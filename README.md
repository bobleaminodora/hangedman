# hangman
/*Jucătorul trebuie să ghicească cuvinte ascunse (ca în jocul spânzurătoarea) pentru a „evada” din fiecare nivel al escape room-ului. Fiecare nivel are un set de cuvinte care trebuie rezolvate pentru a trece mai departe.

🧩 Structura Nivelurilor:

Nivel	Tematică	Număr cuvinte	Dificultate
1	Filme polițiste	5	Ușoară
2	Detectivi celebri + concepte	10	Medie
3	Termeni criminalistică	15	Medie-Greuață
4	Scene celebre din filme	20	Greu
5	Cuvinte criptate (combinații logice)	25	Foarte greu
Se afișează un cuvânt criptat cu * pentru fiecare literă necunoscută.

Jucătorul introduce o literă.

Dacă litera există, se completează pozițiile.

Dacă nu, se contorizează o greșeală.

Max 6 greșeli per cuvânt.

Dacă jucătorul ratează de 6 ori, reia acel cuvânt.
💾 Salvare progres:
Progresul este salvat într-un fișier local progres.dat.

Se salvează:

Nivelul curent

Cuvintele ghicite

Numărul de încercări

La pornirea jocului, jucătorul este întrebat dacă vrea să continue progresul anterior.
 Logica de progresie:
Dacă un cuvânt este ghicit, se trece la următorul.

După toate cuvintele unui nivel => progresul este salvat și se trece automat la nivelul următor.

După finalizarea nivelului 5, jocul se termină cu un mesaj de „Evadare reușită!”

Interfață (text, consolă):
Afișează:

Progresul curent (nivel, cuvinte ghicite)

Numărul de greșeli rămase

Literele deja introduse

Cuvântul actual criptat

La finalul fiecărui nivel:

Mesaj de încurajare și salvare automată

*/
///////////////////////////////////////////////////////////////////////[nivel1.txt](https://github.com/user-attachments/files/19954737/nivel1.txt)detectiv arma criminal ancheta pistol martor procuror coruptie gangster sirena arest judecator tribunal crima dovada santaj urmarire suspans mister agent infiltrat coruptia arestat tribunalul pedeapsa echipa interceptare droguri mafie politie arma crime marturie tinta urmarit rapire cautare scotocire santaj acuzat avocat legitima jurnalist informator filmare laborator ADN patrula secventa criminalisti anchetator captura perchezitie victima agresor raportru masina masina politie revolver pistolero catuse penitenciar evadare evadat indiciu proces inchisoare politie locotenent comisar inspector procuror general ancheta speciala dubla crima caz neelucidat suspect scena probe armament detectivul arma alba arma foc glont urme mobil identificare amprente hacker banci seif jaf masca vanzator complice strain

nivel1.txt



////////////////////////////////////////////////////////////////////////
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <time.h>

#define MAX_CUVANT 30
#define MAX_CUVINTE 150
#define MAX_GRESITE 6
#define CUVINTE_PE_NIVEL 5

typedef struct {
    int nivel;
    int cuvinte_ghicite;
    int selectie[CUVINTE_PE_NIVEL];
} ProgresJoc;

void afiseazaSpanzuratoare(int gresite) {
    const char *etape[] = {
        "  _______\n"
        " |/      |\n"
        " |\n"
        " |\n"
        " |\n"
        " |\n"
        "_|___\n",

        "  _______\n"
        " |/      |\n"
        " |      (_)\n"
        " |\n"
        " |\n"
        " |\n"
        "_|___\n",

        "  _______\n"
        " |/      |\n"
        " |      (_)\n"
        " |       |\n"
        " |       |\n"
        " |\n"
        "_|___\n",

        "  _______\n"
        " |/      |\n"
        " |      (_)\n"
        " |      \\|\n"
        " |       |\n"
        " |\n"
        "_|___\n",

        "  _______\n"
        " |/      |\n"
        " |      (_)\n"
        " |      \\|/\n"
        " |       |\n"
        " |\n"
        "_|___\n",

        "  _______\n"
        " |/      |\n"
        " |      (_)\n"
        " |      \\|/\n"
        " |       |\n"
        " |      /\n"
        "_|___\n",

        "  _______\n"
        " |/      |\n"
        " |      (_)\n"
        " |      \\|/\n"
        " |       |\n"
        " |      / \\\n"
        "_|___\n"
    };
    printf("%s\n", etape[gresite]);
}

void afiseazaCuvant(const char *cuvant, int descoperite[]) {
    for (int i = 0; cuvant[i] != '\0'; i++) {
        if (descoperite[i])
            printf("%c ", cuvant[i]);
        else
            printf("_ ");
    }
    printf("\n");
}

int literaGasita(char litera, const char *cuvant, int descoperite[]) {
    int gasit = 0;
    for (int i = 0; cuvant[i] != '\0'; i++) {
        if (tolower(cuvant[i]) == tolower(litera)) {
            descoperite[i] = 1;
            gasit = 1;
        }
    }
    return gasit;
}

int cuvantComplet(const char *cuvant, int descoperite[]) {
    for (int i = 0; cuvant[i] != '\0'; i++) {
        if (!descoperite[i])
            return 0;
    }
    return 1;
}

void salveazaProgres(ProgresJoc progres) {
    FILE *f = fopen("progres.dat", "wb");
    if (f) {
        fwrite(&progres, sizeof(ProgresJoc), 1, f);
        fclose(f);
    }
}

void incarcaProgres(ProgresJoc *progres) {
    FILE *f = fopen("progres.dat", "rb");
    if (f) {
        fread(progres, sizeof(ProgresJoc), 1, f);
        fclose(f);
    } else {
        progres->nivel = 1;
        progres->cuvinte_ghicite = 0;
        for (int i = 0; i < CUVINTE_PE_NIVEL; i++) progres->selectie[i] = -1;
    }
}

void genereazaSelectieAleatorie(int selectie[], int totalCuvinte) {
    int i = 0;
    while (i < CUVINTE_PE_NIVEL) {
        int idx = rand() % totalCuvinte;
        int unic = 1;
        for (int j = 0; j < i; j++) {
            if (selectie[j] == idx) {
                unic = 0;
                break;
            }
        }
        if (unic)
            selectie[i++] = idx;
    }
}

void joacaNivel1(ProgresJoc *progres) {
    FILE *f = fopen("nivel1.txt", "r");
    if (!f) {
        printf("Eroare: fisierul 'nivel1.txt' nu a fost gasit.\n");
        return;
    }

    char cuvinte[MAX_CUVINTE][MAX_CUVANT];
    int total = 0;
    while (fscanf(f, "%s", cuvinte[total]) == 1 && total < MAX_CUVINTE)
        total++;
    fclose(f);

    if (total < CUVINTE_PE_NIVEL) {
        printf("Eroare: fisierul contine prea putine cuvinte!\n");
        return;
    }

    if (progres->selectie[0] == -1) {
        genereazaSelectieAleatorie(progres->selectie, total);
        salveazaProgres(*progres);
    }

    printf("\n== Nivel 1: Filme Polițiste ==\n\n");

    for (int i = progres->cuvinte_ghicite; i < CUVINTE_PE_NIVEL; i++) {
        char *cuvant = cuvinte[progres->selectie[i]];
        int descoperite[MAX_CUVANT] = {0};
        char litere_incercate[30] = {0};
        int gresite = 0;

        printf("Cuvantul %d din %d:\n", i + 1, CUVINTE_PE_NIVEL);

        while (gresite < MAX_GRESITE && !cuvantComplet(cuvant, descoperite)) {
            afiseazaSpanzuratoare(gresite);
            afiseazaCuvant(cuvant, descoperite);
            printf("Greseli: %d/%d | Incercari: %s\n", gresite, MAX_GRESITE, litere_incercate);
            printf("Litera: ");

            char lit;
            scanf(" %c", &lit);
            lit = tolower(lit);

            if (strchr(litere_incercate, lit)) {
                printf("Ai incercat deja aceasta litera!\n");
                continue;
            }

            int len = strlen(litere_incercate);
            litere_incercate[len] = lit;
            litere_incercate[len + 1] = '\0';

            if (!literaGasita(lit, cuvant, descoperite)) {
                gresite++;
                printf("Gresit!\n");
            } else {
                printf("Corect!\n");
            }
        }

        if (cuvantComplet(cuvant, descoperite)) {
            printf("Felicitari! Cuvantul era: %s\n\n", cuvant);
            progres->cuvinte_ghicite++;
            salveazaProgres(*progres);
        } else {
            printf("Ai pierdut! Cuvantul era: %s\n", cuvant);
            printf("Reincepem acest cuvant...\n\n");
            i--;
        }
    }

    printf("Bravo! Ai trecut nivelul 1!\n");
    progres->nivel = 2;
    progres->cuvinte_ghicite = 0;
    for (int i = 0; i < CUVINTE_PE_NIVEL; i++) progres->selectie[i] = -1;
    salveazaProgres(*progres);
}

int main() {
    srand(time(NULL));
    ProgresJoc progres;
    incarcaProgres(&progres);

    if (progres.nivel == 1) {
        joacaNivel1(&progres);
    } else {
        printf("Ai trecut deja de nivelul 1. (Nivel curent: %d)\n", progres.nivel);
    }

    return 0;
}




