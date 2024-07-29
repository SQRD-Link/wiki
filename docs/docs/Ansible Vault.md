https://medium.com/t%C3%BCrk-telekom-bulut-teknolojileri/ansible-vault-with-awx-80b603617798

maak vault file 

````
ansible-vault create vault_file_name.yml
````

Let erop dat je gelijk een entry maakt anders kan je de vault later niet meer editen.

edit vault

````
ansible-vault edit vault_file_name.yml
````

geef in playbook mee


````
vars_files:  
- vault_file_name.yml
````

````
"{{variablele}}"
````



