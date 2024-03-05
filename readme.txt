linux acl:

- am inceput prin a da cat pe toate script-urile si strings pe robot-sudo
pentru a vedea ce se intampla in fiecare.

- in robot-sudo am gasit o cale (/usr/share/vim/talent/r0b0t3rs.conf) si am
dat cat pe acel fisier si am vazut ca este o lista de comenzi permise unor
utilizatori.

- am incercat sa modific acel fisier to no avail.

- am folosit hint-urile aflate in /var/.hints.txt si am dat ltrace pe robot-sudo
incercand diverse comenzi. am vazut ca citeste fisierul acela .conf si il
parseaza/compara bucati din continutul lui cu comanda primita ca argument.

- am rulat mai multe comenzi fara niciun efect, primind aceeasi eroare. m-am
gandit ca trebuie sa introduc cod de-al meu in fisierul vacuum-control dar am
vazut ca nu am cum.

- testand cu ltrace ce se intampla cand se da /usr/local/bin/vacuum-control
ca argument la robot-sudo, am vazut ca exista un apel la strncmp care nu
schimba parametrul aferent lungimii in functie de input. am incercat apoi
sa rulez cu o comanda gen /usr/local/bin/vacuum-control1 ca sa vad ce se
intampla si am vazut ca nu mai primesc eroare de permisiuni.

- vazand asta, am facut vacuum-control1 sa fie un script cu urmatorul
continut:

#!/bin/bash
bash

- am folosit bash ca si unica comanda pentru a obtine un shell ca utilizatorul
roombax. upon further review puteam sa dau direct comanda de robot-sudo pentru
a obtine direct flag-ul dar la inceput mi-am zis ca e mai util sa am un shell.

- am incercat sa rulez binarul acela din /etc/X11/not_for_your_eyes in toate
modurile posibile timp de doua ore, pana m-a dus capul sa il rulez cu un argument
si folosind ltrace, ca sa vad un strcmp care compara argumentul dat de mine
cu un sir de caractere random pe care il vazusm cand am dat strings pe el.

- pana la urma am rulat executabilul cu argumentul care trebuie si am obtinut flag-ul.

- am gasit un fisier /.you.are.never/.gonnafindthis/dar_ceavem_noi_aicia.txt
cu urmatorul continut care cred ca este inutil, pare a fi un hash de la ceva.
o sa banuiesc ca e hash-ul aferent docker-ului pentru ca la conectari succesive
este diferit.

- urasc vim

binary exploit:

- am incarcat programul in ghidra si am luat la rand fiecare functie ca sa
inteleg ce face codul.

- am vazut ca loop este apelata din main si ca win nu este apelata nicaieri,
lucru care m-a facut sa ma gandesc ca ar trebui sa apelez functia asta cum
faceam la iocla.

- am verificat fiecare loc din cod unde se primeste input de la utilizator
pentru a vedea daca si unde exista o vulnerabilitate pe care o pot exploata

- am vazut in loop un while true in care se citesc elemente numere intregi
si se introduc intr-un vector declarat local, fara sa fie vreun bounds check,
lucru care mi-a indicat ca aici este vulnerabilitatea pe care o cautam.

- am facut niste matematica adunand dimensiunea vectorului si numarul de bytes/4
al variabilelor declarate deasupra buffer-ului pentru a stabili de cate numere
este nevoie ca sa suprascriu adresa de retur, folosind cunostintele si amintirile
de la iocla.

- am mers in ghidra si am luat adresa primei instructiuni din win si am
convertit-o in baza 10 pentru a o putea trimite programului.

- am reusit sa apelez functia dar am primit mesajul cu you shall not pass, semn
ca numarul pe care il dadeam ca parametru nu corespundea cu lucky_number.

- urmarind flow-ul programului, am realizat ca trebuie sa rulez o data in loop
pentru a primi un numar random pe care apoi sa il introduc in payload-ul meu
ca argument pentru apelul fortat al functiei win.

- am facut asta si am primit flag-ul.

- mi-a placut iocla

crypto-attack:

- am inceput prin a trimite continutul din message la server si am primit
mesajul "The message contains the valid flag, please proceed!", lucru
care m-a bucurat si m-a confuzat in acelasi timp.

- m-am uitat nitel la formatul mesajului si mi-am zis ca ar fi worthwhile
sa incerc sa dau base64 -d pe el si chiar a fost o idee buna, rezultatul
fiind unul coerent.

- am folosit numerele n si e din mesajul decodat in script-ul de python din
arhiva si am pus continutul din "flag" din json in message.

- am folosit instructiunile din link-ul asta:
https://crypto.stackexchange.com/questions/2323/how-does-a-chosen-plaintext-attack-on-rsa-work
pentru a face un mesaj care contine flag-ul meu * 2 ^ e mod n, ca apoi sa
pot decrpita flag-ul.

- practic am decodat mesajul din base64, din rezultatul operatiei asteia
am facut un numar cu ajutorul bibliotecii gmpy2, numarul asta l-am inmultit
cu 2^e mod n tot cu ajutorul bibliotecii gmpy2, am dat mod n la tot rezultatul
asta, si am reencodat mesajul in base64.

- rezultatul paragrafului de mai sus l-am pus in campul flag al unui json
care respecta formatul din message.txt, am encodat noul fisier in base64
si am transmis rezultatul la server.

- serverul mi-a dat inapoi un sir de bytes pe care l-am bagat intr-un alt
script de python ca sa scot flag-ul.

- am transformat sirul de bytes intr-un numar, am impartit numarul la 2
si apoi am transformat rezultatul inapoi in sir si asta e flag-ul.

- am inclus script-urile folosite in arhiva si am scos din ele chestiile inutile

- urasc matematica si urasc python