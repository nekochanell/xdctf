# Trivia 7

Quel est le nom de la vulnérabilité illustrée ici?

```
#include <stdio.h>

int main(void)
{
  char buff[15];
  gets(buff);
}
```

Entrez le nom en minuscules.

# Writeup

Flag: `buffer overflow`, `dépassement de tampon`, `débordement de tampon`.
