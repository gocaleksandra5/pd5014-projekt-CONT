## Konteneryzacja w bioinformatyce (CONT)


# Temat projektu: Mikroserwis do analizy plików FASTQ za pomocą FastQC i wyświetlania wyników przez NGINX.

**Utworzyłam katalog projektu pd5014-projekt-CONT**

mkdir pd5014-projekt-CONT
cd pd5014-projekt-CONT

**Utworzyłam katalog input**

mkdir input

**Utworzyłam w nim potrzebne pliki**

touch Dockerfile docker-compose.yml nginx.conf README.md

**Pobrany plik FASTA przeniosłam do katalogu input bo był w folderze pobrane**

mv ~/SRR8786200_1.fastq.gz input/

**Do pliku Dockerfile, nginx.conf i docker-compose wstawiłam kod z pliku**

**Uruchomiłam projekt komendą:**

sudo docker compose up --bild  

**W przeglądarce użyłam linku z pliku http://localhost:8080**

Wyskoczyła strona FASTQS


