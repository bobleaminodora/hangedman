# hangman
/*1. Descrierea proiectului
Hangman Multiplayer este un joc implementat în limbajul C, care permite doi jucători să participe într-un mod competitiv. Unul introduce un cuvânt secret, iar celălalt încearcă să îl ghicească. Jocul poate fi jucat local (pe același computer) sau prin rețea folosind sockets.

2. Funcționalități principale
Modul Local (Offline)

Un jucător introduce cuvântul secret, iar celălalt încearcă să-l ghicească.

Limită de încercări configurabilă.

Posibilitatea de a selecta o categorie de cuvinte prestabilite.

Scor bazat pe numărul de încercări reușite.

Modul Online (Cu Sockets)

Un jucător creează un lobby (server), iar celălalt se conectează (client).

Datele sunt transmise între jucători prin rețea (TCP/IP).

Comunicare prin mesaje (de ex. „Ai ghicit litera X!”).

Sistem de Punctaj și Clasament

Fiecare jucător acumulează puncte pe baza timpului și numărului de încercări.

La final, scorul este salvat într-un fișier și afișat.

Timp Limită per Încercare (Opțional)

Dacă jucătorul nu introduce o literă într-un anumit timp, pierde automat o viață.

3. Biblioteci utilizate


stdio.h și stdlib.h (operare fișiere, afișare)


string.h (manipulare șiruri de caractere)

time.h (pentru sistemul de cronometrare)

sys/socket.h și arpa/inet.h (pentru multiplayer în rețea)*/




////////////////////////////////////////////////////////////////////////


#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <time.h>

#define MAX_TRIES 6
#define WORD_LENGTH 100

void display_hangman(int tries) {
    const char *stages[] = {
        "  _______\n"
        " |/      |\n"
        " |      (_)\n"
        " |      \\|/\n"
        " |       |\n"
        " |      / \\\n"
        " |\n"
        "_|___\n",

        "  _______\n"
        " |/      |\n"
        " |      (_)\n"
        " |      \\|/\n"
        " |       |\n"
        " |      / \n"
        " |\n"
        "_|___\n",

        "  _______\n"
        " |/      |\n"
        " |      (_)\n"
        " |      \\|/\n"
        " |       |\n"
        " |        \n"
        " |\n"
        "_|___\n",

        "  _______\n"
        " |/      |\n"
        " |      (_)\n"
        " |      \\|\n"
        " |       |\n"
        " |        \n"
        " |\n"
        "_|___\n",

        "  _______\n"
        " |/      |\n"
        " |      (_)\n"
        " |       |\n"
        " |       |\n"
        " |        \n"
        " |\n"
        "_|___\n",

        "  _______\n"
        " |/      |\n"
        " |      (_)\n"
        " |        \n"
        " |        \n"
        " |        \n"
        " |\n"
        "_|___\n",

        "  _______\n"
        " |/      |\n"
        " |        \n"
        " |        \n"
        " |        \n"
        " |        \n"
        " |\n"
        "_|___\n"
    };
    printf("%s\n", stages[MAX_TRIES - tries]);
}

int main() {
    const char *word_list[] = {
        "elephant", "computer", "hangman", "giraffe", "umbrella",
        "keyboard", "planet", "language", "teacher", "bicycle"
    };
    const int word_count = sizeof(word_list) / sizeof(word_list[0]);

    srand(time(NULL));
    const char *word = word_list[rand() % word_count];

    char guessed[WORD_LENGTH];
    int tries = MAX_TRIES;
    int correct = 0;

    for (int i = 0; i < strlen(word); i++) {
        guessed[i] = '_';
    }

    guessed[strlen(word)] = '\0';

    printf("Welcome to Hangman!\n");

    while (tries > 0 && correct < strlen(word)) {
        display_hangman(tries);
        printf("Word: ");
        for (int i = 0; i < strlen(word); i++) {
            printf("%c ", guessed[i]);
        }
        printf("\n");

        printf("Guess a letter: ");
        char letter;
        scanf(" %c", &letter);
        letter = tolower(letter);

        int found = 0;
        for (int i = 0; i < strlen(word); i++) {
            if (word[i] == letter && guessed[i] == '_') {
                guessed[i] = letter;
                found = 1;
                correct++;
            }
        }

        if (!found) {
            tries--;
            printf("Wrong guess! You have %d tries left.\n", tries);
        } else {
            printf("Good guess!\n");
        }

        printf("\n");
    }

    if (correct == strlen(word)) {
        printf("Congratulations! You guessed the word: %s\n", word);
    } else {
        display_hangman(0);
        printf("Game over! The word was: %s\n", word);
    }

    return 0;
}

