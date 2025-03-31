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

