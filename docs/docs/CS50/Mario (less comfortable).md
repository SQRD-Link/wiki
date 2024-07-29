```c
// CS50 Mario

  

#include <cs50.h>

#include <stdio.h>

  

int main(void)

{

// Define- variable to hold size

int height, spaces, hashes, iterate;

  

// prompt user for value

do

{

height = get_int("give me a number between 1 and 8\n");

}

//make sure number is between 1 and 8

while (height > 8 || height < 1);

  

//set main loop to generate each row printed on screen

for (iterate = 0; iterate < height; iterate ++)

{

//subloop for printing the right amount of spaces

for (spaces = height - 1 - iterate; spaces > 0; spaces--)

{

printf(" ");

}

  

// subloop for printing the right amount of hashes

for (hashes = iterate + 1; hashes > 0; hashes--)

{

printf("#");

}

  

//print newline after each iteration

printf("\n");

}

  
  

}
```

## Uitleg

```c
int height, spaces, hashes, iterate;
```

Hier worden alle gebruikte variabelen gedefinieerd en de waardes opgeslagen.

```c
do

{

height = get_int("give me a number between 1 and 8\n");

}

//make sure number is between 1 and 8

while (height > 8 || height < 1);
```

'do' vraagt een waarde aan de gebruiker, 'while' zorgt ervoor dat er zolang er geen gewenst antwoord word gegeven (tussen de 1 en de 8 in dit geval) continue opnieuw word gevraagd een waarde op te geven. 


```c
//set main loop to generate each row printed on screen

for (iterate = 0; iterate < height; iterate ++)
```

De hoofdloop van het programma, Hier wordt de waarde voor de variabele iteratie op 0 gezet, 

```c 
iterate = 0
```

hierna checkt het programma of de loop aan de gedefinieerde voorwaarde voldoet.

```c
iterate < height
```

Is iterate kleiner dan height? iterate is 0 dus ja, de code tussen de brackets mag worden uitgevoerd.

```c
{
//subloop for printing the right amount of spaces
for (spaces = height - 1 - iterate; spaces > 0; spaces--)
{
printf(" ");
}
// subloop for printing the right amount of hashes
for (hashes = iterate + 1; hashes > 0; hashes--)
{
printf("#");
}
//print newline after each iteration
printf("\n");
}
```

Dit begint met een volgende loop, 

```c
//subloop for printing the right amount of spaces
for (spaces = height - 1 - iterate; spaces > 0; spaces--)
{
printf(" ");
}
```


```c
spaces - height - 1 - iterate;
```

Hier word de waarde 'spaces' gedefinieerd,  height -1 -iterate, wanneer de gebruiker bijvoorbeeld 5 als waarde opgeeft word de beginwaarde van spaces dus 5-1 is 4. De toevoeging - iterate zorgt ervoor dat de waarde spaces iedere keer dat de loop is voltooid en opnieuw begint 1 kleiner word. dus 4, 3, 2, 1
Hierna checkt het programma of aan de voorwaarde van de loop wordt voldaan, 

```c
spaces > 0;
```

Is de waarde van spaces groter dan 0? We hebben hierboven bepaald dat de waarde 4 is dus ja. de code tussen de volgende set brackets mag worden uitgevoerd. 

```c
printf(" ");
```

print spatie, 

de code werkt in dit geval als volgt. de loop voldoet aan de gestelde voorwaarde van spaces is groter dan 0, de waarde is immers 4, dan voert deze de code uit en print een spatie. hiernaa keert het programma terug naar de loop en voert het derde en laatste deel van de for loop uit, 
```c
spaces--
```
dit betekent verminder de waarde van spaces met 1. 

de loop begint opnieuw, 
```c
spaces - height - 1 - iterate;
```

spaces - height - 1 - iterate; is nu 3. 

```c
spaces > 0;
```

is spaces groter dan 0? ja voer de code tussen de brackets uit. 

```c
printf(" ");
```

print nog een spatie. nu zijn er dus 2 spaties uitgeprint. Na de opdracht wordt het laatste deel van de for loop uitgevoerd en de waarde van spaces weer met 1 verminderd. 

de loop begint opnieuw.



