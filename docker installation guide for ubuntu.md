Removing old Docker versions and conflicting packages..."
    
    # Remove old Docker packages
    sudo apt remove -y docker docker-engine docker.io containerd runc 2>/dev/null || true
    
    # Remove old Docker Compose standalone if exists
    if [ -f /usr/local/bin/docker-compose ]; then
        sudo rm -f /usr/local/bin/docker-compose
        log "Removed old standalone docker-compose binary"
    fi
    
    # Clean up old repositories
    sudo rm -f /etc/apt/sources.list.d/docker.list 2>/dev/null || true
    sudo rm -f /usr/share/keyrings/docker-archive-keyring.gpg 2>/dev/null || true
    
    log "Old Docker installations cleaned up"
}

# Update system packages
update_system() {
    log "Updating system package repositories..."
    sudo apt update
    
    log "Upgrading existing packages..."
    sudo apt upgrade -y
    
    log "Installing essential build tools..."
    sudo apt install -y build-essential
}

# Install prerequisites
install_prerequisites() {
    log "Installing Docker and Docker Compose prerequisites..."
    sudo apt install -y \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg \
        lsb-release \
        software-properties-common \
        wget \
        unzip \
        git
    
    log "Prerequisites installed successfully"
}

# Add Docker repository
add_docker_repository() {
    log "Setting up Docker's official repository..."
    
    # Create keyrings directory if it doesn't exist
    sudo mkdir -p /usr/share/keyrings
    
    # Download and install Docker's GPG key
    log "Adding Docker's official GPG key..."
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    
    # Verify GPG key
    if [ -f /usr/share/keyrings/docker-archive-keyring.gpg ]; then
        log "Docker GPG key added successfully"
    else
        error "Failed to add Docker GPG key"
        exit 1
    fi
    
    # Add Docker repository
    log "Adding Docker repository to sources..."
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    
    # Set appropriate permissions
    sudo chmod a+r /usr/share/keyrings/docker-archive-keyring.gpg
    
    log "Docker repository configured successfully"
}

# Install Docker Engine
install_docker() {
    log "Updating package index with Docker repository..."
    sudo apt update
    
    log "Installing Docker Engine and components..."
    sudo apt install -y \
        docker-ce \
        docker-ce-cli \
        containerd.io \
        docker-buildx-plugin \
        docker-compose-plugin
    
    log "Docker Engine installation completed"
}

# Configure Docker service and user permissions
configure_docker() {
    log "Configuring Docker service..."
    
    # Start Docker service
    log "Starting Docker daemon..."
    sudo systemctl start docker
    
    # Enable Docker to start on boot
    log "Enabling Docker service for system startup..."
    sudo systemctl enable docker
    
    # Add current user to docker group
    log "Adding user '$USER' to docker group..."
    sudo usermod -aG docker $USER
    
    # Configure Docker daemon for optimal performance
    log "Configuring Docker daemon settings..."
    sudo mkdir -p /etc/docker
    
    # Create daemon.json with recommended settings
    sudo tee /etc/docker/daemon.json > /dev/null <<EOF
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    },
    "storage-driver": "overlay2",
    "userland-proxy": false,
    "experimental": false
}
EOF
    
    # Restart Docker to apply configuration
    log "Restarting Docker to apply configuration..."
    sudo systemctl restart docker
    
    log "Docker service configured successfully"
}

# Verify Docker installation
verify_docker() {
    log "Verifying Docker installation..."
    
    # Wait for Docker daemon to be ready
    sleep 3
    
    # Check Docker version
    if DOCKER_VERSION=$(sudo docker --version 2>/dev/null); then
        success "Docker installed: $DOCKER_VERSION"
    else
        error "Docker installation verification failed"
        exit 1
    fi
    
    # Check Docker Compose plugin version
    if COMPOSE_VERSION=$(sudo docker compose version 2>/dev/null); then
        success "Docker Compose plugin installed: $COMPOSE_VERSION"
    else
        error "Docker Compose plugin verification failed"
        exit 1
    fi
    
    # Test Docker with hello-world container
    log "Testing Docker installation with hello-world container..."
    if sudo docker run --rm hello-world > /tmp/docker_test.log 2>&1; then
        success "Docker test completed successfully!"
        info "Test output saved to /tmp/docker_test.log"
    else
        error "Docker test failed. Check logs at /tmp/docker_test.log"
        cat /tmp/docker_test.log
        exit 1
    fi
    
    # Verify service status
    if systemctl is-active --quiet docker; then
        success "Docker service is running and active"
    else
        error "Docker service is not running properly"
        sudo systemctl status docker
        exit 1
    fi
}

# Install Docker Compose standalone (for compatibility)
install_compose_standalone() {
    log "Installing standalone Docker Compose for legacy compatibility..."
    
    # Get latest version if not specified
    if [ -z "$DOCKER_COMPOSE_VERSION" ]; then
        DOCKER_COMPOSE_VERSION=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d'"' -f4)
        log "Latest Docker Compose version detected: $DOCKER_COMPOSE_VERSION"
    fi
    
    # Download Docker Compose binary
    log "Downloading Docker Compose $DOCKER_COMPOSE_VERSION..."
    sudo curl -L "https://github.com/docker/compose/releases/download/$DOCKER_COMPOSE_VERSION/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    
    # Make it executable
    sudo chmod +x /usr/local/bin/docker-compose
    
    # Create symlink for easier access
    sudo ln -sf /usr/local/bin/docker-compose /usr/bin/docker-compose 2>/dev/null || true
    
    # Verify standalone installation
    if /usr/local/bin/docker-compose --version > /dev/null 2>&1; then
        success "Standalone Docker Compose installed: $(/usr/local/bin/docker-compose --version)"
    else
        warn "Standalone Docker Compose installation may have issues"
    fi
}

# Create useful Docker Compose examples
create_examples() {
    log "Creating Docker Compose example configurations..."
    
    # Create examples directory
    mkdir -p $HOME/docker-examples
    
    # Example 1: Simple web application
    cat > $HOME/docker-examples/web-app-example.yml <<EOF
version: '3.8'

services:
  web:
    image: nginx:alpine
    container_name: example-web
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
    restart: unless-stopped
    networks:
      - web-network

  app:
    image: node:18-alpine
    container_name: example-app
    working_dir: /app
    volumes:
      - ./app:/app
    command: npm start
    networks:
      - web-network
    depends_on:
      - db

  db:
    image: postgres:15-alpine
    container_name: example-db
    environment:
      POSTGRES_DB: exampledb
      POSTGRES_USER: exampleuser
      POSTGRES_PASSWORD: examplepass
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - web-network
    restart: unless-stopped

volumes:
  db-data:

networks:
  web-network:
    driver: bridge
EOF

    # Example 2: Development stack
    cat > $HOME/docker-examples/dev-stack.yml <<EOF
version: '3.8'

services:
  redis:
    image: redis:7-alpine
    container_name: dev-redis
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    restart: unless-stopped

  postgres:
    image: postgres:15-alpine
    container_name: dev-postgres
    environment:
      POSTGRES_DB: devdb
      POSTGRES_USER: devuser
      POSTGRES_PASSWORD: devpass
    ports:
      - "5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data
    restart: unless-stopped

  adminer:
    image: adminer:latest
    container_name: dev-adminer
    ports:
      - "8081:8080"
    depends_on:
      - postgres
    restart: unless-stopped

volumes:
  redis-data:
  postgres-data:
EOF

    # Create README for examples
    cat > $HOME/docker-examples/README.md <<EOF
# Docker Compose Examples

This directory contains example Docker Compose configurations.

## Usage

### Web Application Example
\`\`\`bash
cd docker-examples
docker compose -f web-app-example.yml up -d
\`\`\`

### Development Stack
\`\`\`bash
cd docker-examples
docker compose -f dev-stack.yml up -d
\`\`\`

### Common Commands
- Start services: \`docker compose up -d\`
- Stop services: \`docker compose down\`
- View logs: \`docker compose logs\`
- Restart services: \`docker compose restart\`
EOF

    log "Docker Compose examples created in $HOME/docker-examples/"
}

# Display post-installation information
post_installation_info() {
    echo
    success "============================================"
    success "Docker and Docker Compose installation completed!"
    success "============================================"
    
    echo
    info "Installed Components:"
    info "• Docker Engine: $(sudo docker --version)"
    info "• Docker Compose Plugin: $(sudo docker compose version)"
    if command -v docker-compose &> /dev/null; then
        info "• Docker Compose Standalone: $(docker-compose --version)"
    fi
    
    echo
    warn "IMPORTANT NEXT STEPS:"
    warn "1. Log out and log back in for Docker group changes to take effect"
    warn "2. Or run 'newgrp docker' to apply group changes in current session"
    
    echo
    log "Basic Docker Commands:"
    echo "  docker --version           # Check Docker version"
    echo "  docker info               # Display system information"
    echo "  docker run hello-world    # Test Docker installation"
    echo "  docker images             # List downloaded images"
    echo "  docker ps                 # List running containers"
    echo "  docker ps -a              # List all containers"
    echo "  docker system prune       # Clean up unused resources"
    
    echo
    log "Docker Compose Commands:"
    echo "  docker compose --version  # Check Compose version"
    echo "  docker compose up -d      # Start services in background"
    echo "  docker compose down       # Stop and remove services"
    echo "  docker compose ps         # List running compose services"
    echo "  docker compose logs       # View service logs"
    echo "  docker compose pull       # Pull latest images"
    echo "  docker compose restart    # Restart services"
    
    echo
    log "Example configurations created in: $HOME/docker-examples/"
    
    echo
    log "Useful File Locations:"
    echo "  Docker config: /etc/docker/daemon.json"
    echo "  Docker service: systemctl status docker"
    echo "  Docker logs: journalctl -u docker.service"
    
    success "============================================"
}

# Main installation function
main() {
    print_banner
    
    log "Starting Docker and Docker Compose installation process..."
    
    check_requirements
    remove_old_docker
    update_system
    install_prerequisites
    add_docker_repository
    install_docker
    configure_docker
    verify_docker
    install_compose_standalone
    create_examples
    post_installation_info
    
    success "Installation script completed successfully!"
    warn "Please log out and back in to use Docker without sudo"
}

# Error handling
trap 'error "An error occurred. Installation may be incomplete."; exit 1' ERR

# Run main function
main "$@"
```

### Running the Installation Script

1. **Create and prepare the script:**
```bash
nano install-docker-compose.sh
# Paste the script content and save (Ctrl+X, Y, Enter)
chmod +x install-docker-compose.sh
```

2. **Run the installation:**
```bash
./install-docker-compose.sh
```

3. **Apply group changes:**
```bash
# Apply immediately without logout
newgrp docker

# Or log out and back in for permanent effect
exit
```

## Detailed Script Explanation

### Enhanced System Validation
```bash
check_requirements() {
    # Comprehensive system compatibility checks
    # Architecture detection for proper package selection
    # Disk space validation (minimum 10GB recommended)
    # Existing installation detection with reinstall option
}
```

### Comprehensive Cleanup
```bash
remove_old_docker() {
    # Removes conflicting packages: docker, docker-engine, docker.io
    # Cleans old standalone docker-compose binaries
    # Removes old GPG keys and repository configurations
}
```

### Docker Repository Setup
The script performs secure repository configuration:
- Downloads official Docker GPG key with verification
- Sets proper file permissions for security
- Adds architecture-specific repository sources
- Validates GPG key installation

### Enhanced Docker Installation
```bash
install_docker() {
    # Installs complete Docker suite:
    # - docker-ce: Community Edition engine
    # - docker-ce-cli: Command-line interface
    # - containerd.io: Container runtime
    # - docker-buildx-plugin: Advanced build features
    # - docker-compose-plugin: Integrated Compose functionality
}
```

### Optimized Docker Configuration
```bash
configure_docker() {
    # Creates /etc/docker/daemon.json with:
    # - Log rotation (10MB max, 3 files)
    # - overlay2 storage driver for performance
    # - Disabled userland-proxy for better networking
    # - Production-ready settings
}
```

### Dual Docker Compose Installation
The script installs both:
1. **Docker Compose Plugin** (integrated with Docker CLI)
2. **Standalone Docker Compose** (for legacy compatibility)

This ensures compatibility with both `docker compose` and `docker-compose` commands.

### Example Configurations
Creates ready-to-use examples in `~/docker-examples/`:
- **Web Application Stack**: Nginx, Node.js, PostgreSQL
- **Development Stack**: Redis, PostgreSQL, Adminer
- **README with usage instructions**

## Method 2: Manual Installation

### Step 1: System Preparation
```bash
# Remove old versions
sudo apt remove docker docker-engine docker.io containerd runc

# Update system
sudo apt update && sudo apt upgrade -y

# Install build essentials
sudo apt install -y build-essential
```

### Step 2: Install Prerequisites
```bash
sudo apt install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release \
    software-properties-common \
    wget \
    unzip \
    git
```

### Step 3: Add Docker Repository
```bash
# Create keyrings directory
sudo mkdir -p /usr/share/keyrings

# Add Docker's GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Set permissions
sudo chmod a+r /usr/share/keyrings/docker-archive-keyring.gpg
```

### Step 4: Install Docker and Docker Compose
```bash
# Update package index
sudo apt update

# Install Docker with Compose plugin
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Step 5: Configure Docker
```bash
# Start and enable service
sudo systemctl start docker
sudo systemctl enable docker

# Add user to docker group
sudo usermod -aG docker $USER

# Configure daemon
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<EOF
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    },
    "storage-driver": "overlay2"
}
EOF

# Restart Docker
sudo systemctl restart docker
```

### Step 6: Install Standalone Docker Compose (Optional)
```bash
# Get latest version
DOCKER_COMPOSE_VERSION=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d'"' -f4)

# Download and install
sudo curl -L "https://github.com/docker/compose/releases/download/$DOCKER_COMPOSE_VERSION/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# Make executable
sudo chmod +x /usr/local/bin/docker-compose

# Create symlink
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

## Docker Compose Usage Examples

### Basic Commands
```bash
# Using Docker Compose Plugin (recommended)
docker compose up -d          # Start services in detached mode
docker compose down           # Stop and remove services
docker compose ps            # List running services
docker compose logs         # View all service logs
docker compose logs web     # View specific service logs
docker compose restart      # Restart all services
docker compose pull         # Pull latest images
docker compose build        # Build custom images

# Using Standalone Docker Compose (legacy)
docker-compose up -d
docker-compose down
docker-compose ps
```

### Sample docker-compose.yml
```yaml
version: '3.8'

services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
    depends_on:
      - app
    restart: unless-stopped

  app:
    image: node:18-alpine
    working_dir: /app
    volumes:
      - ./app:/app
    command: npm start
    environment:
      - NODE_ENV=production
    depends_on:
      - db

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypass
    volumes:
      - db_data:/var/lib/postgresql/data
    restart: unless-stopped

volumes:
  db_data:
```

### Advanced Compose Features
```yaml
# Environment files
env_file:
  - .env
  - .env.local

# Health checks
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost/health"]
  interval: 30s
  timeout: 10s
  retries: 3

# Resource limits
deploy:
  resources:
    limits:
      cpus: '0.5'
      memory: 512M
    reservations:
      memory: 256M

# Networks
networks:
  frontend:
    driver: bridge
  backend:
    internal: true
```

## Troubleshooting

### Common Issues

1. **Permission denied after installation:**
```bash
# Check if user is in docker group
groups $USER

# Apply group changes
newgrp docker
# OR logout and back in
```

2. **Docker Compose command not found:**
```bash
# Check plugin installation
docker compose version

# Check standalone installation
docker-compose --version

# Reinstall if needed
sudo apt install --reinstall docker-compose-plugin
```

3. **Service startup issues:**
```bash
# Check Docker service status
sudo systemctl status docker

# View detailed logs
sudo journalctl -u docker.service -f

# Restart Docker service
sudo systemctl restart docker
```

4. **Docker Compose file validation:**
```bash
# Validate compose file syntax
docker compose config

# Validate and view merged configuration
docker compose config --services
```

### Performance Optimization

```bash
# Clean up unused resources
docker system prune -a

# Check disk usage
docker system df

# Configure log rotation
sudo tee /etc/docker/daemon.json <<EOF
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
EOF
```

## Security Best Practices

1. **User Management:**
   - Only add trusted users to docker group
   - Consider using rootless Docker for enhanced security

2. **Image Security:**
   - Use official images from Docker Hub
   - Regularly update base images
   - Scan images for vulnerabilities: `docker scout`

3. **Network Security:**
   - Use custom networks instead of default bridge
   - Implement proper firewall rules
   - Avoid exposing unnecessary ports

4. **Secrets Management:**
   - Use Docker secrets for sensitive data
   - Avoid hardcoding credentials in compose files
   - Use environment files with proper permissions

## Complete Uninstallation

```bash
# Stop all containers and services
docker stop $(docker ps -aq) 2>/dev/null || true
docker compose down --remove-orphans 2>/dev/null || true

# Remove all containers, images, volumes, networks
docker system prune -a --volumes -f

# Remove Docker packages
sudo apt remove -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Remove standalone docker-compose
sudo rm -f /usr/local/bin/docker-compose /usr/bin/docker-compose

# Remove Docker data
sudo rm -rf /var/lib/docker /var/lib/containerd

# Remove configuration and keys
sudo rm -rf /etc/docker
sudo rm -f /etc/apt/sources.list.d/docker.list
sudo rm -f /usr/share/keyrings/docker-archive-keyring.gpg

# Remove user from docker group
sudo deluser $USER docker
```

## Conclusion

This comprehensive guide provides automated installation of Docker and Docker Compose with enterprise-grade configuration and extensive examples. The script includes robust error handling, system validation, and creates ready-to-use example configurations for immediate productivity.

Both the plugin-based and standalone versions of Docker Compose are installed for maximum compatibility with existing projects and workflows.