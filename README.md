#Konteneryzacja w bioinformatyce (CONT)


##Część 1


**Utworzyłam katalog projektu pd5014-projekt-CONT**
mkdir pd5014-projekt-CONT
cd pd5014-projekt-CONT

**Utworzyłam katalog input**
mkdir input

**Utworzyłam w nim potrzebne pliki**
touch Dockerfile docker-compose.yml nginx.conf README.md

**Pobrany plik FASTA przeniosłam do katalogu input bo był w folderze pobrane**
mv ~/SRR8786200_1.fastq.gz input/

**Do pliku Dockerfile wstawiłam kod z pliku**
