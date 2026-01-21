# Konteneryzacja w bioinformatyce (CONT)


## Temat projektu: Mikroserwis do analizy plików FASTQ za pomocą FastQC i wyświetlania wyników przez NGINX.

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

____________________________________________________________________________________________________________


## Plik Dockerfile: ##

Ten kod buduje obraz oparty na Ubuntu 22.04, instaluje środowisko Java oraz narzędzie FastQC, konfiguruje zmienne środowiskowe i definiuje FastQC jako domyślne polecenie uruchamiane w kontenerze

- FROM ubuntu:22.04
- RUN apt-get update && apt-get install -y \ **(Sprawdzanie aktualizacji i automatyczna ich instalacja)**  
- openjdk-11-jre-headless unzip perl wget && \   **(Uruchamia system Java, unzip - rozpakowanie plików zip, weget - pobiera treści z internetu )**
- wget https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.12.1.zip && \  **(Pobiera archiwum ZIP z oficjalnej strony projektu FastQC)**
- unzip fastqc_v0.12.1.zip && \ **(Rozpakowuje pobrane archiwum ZIP i tworzy katalog FastQC zawierający pliki programu)**
- chmod +x FastQC/fastqc && \ **(Powoduje, że FastQC jest w stanie się uruchomić)**
- mv FastQC /usr/local/ && \ **(Przenosi katalog FastQC do lokalizacji dla oprogramowania)**
- ln -s /usr/local/FastQC/fastqc /usr/local/bin/fastqc && \ **(Tworzy link symboliczny do pliku wykonywalnego FastQC. Dzięki temu polecenie fastqc jest dostępne globalnie w systemie. Umożliwia uruchamianie FastQC bez podawania pełnej ścieżki)**
- apt-get clean **(usuwa lokalne pliki instalacyjne pakietów)** && rm -rf /var/lib/apt/lists/* **(usuwa cache list pakietów)** fastqc_v0.12.1.zip **(usuwa niepotrzebne archiwum ZIP)**

- ENV JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64 **(Określa lokalizacje zainstalowanego programu Java)**
- ENV PATH=$JAVA_HOME/bin:$PATH **(Umożliwia uruchamianie poleceń Java z dowolnego miejsca)**
- ENV CLASSPATH=/usr/local/FastQC:/usr/local/FastQC/htsjdk.jar:/usr/local/FastQC/jbzip2-0.9.jar:/usr/local/FastQC/cisd-jhdf5.jar **(Określa ścieżki do bibliotek Javy wymaganych przez FastQC)**

- ENTRYPOINT ["fastqc"] **(Każde uruchomienie kontenera wywołuje polecenie fastqc)**
- CMD ["--help"]

___________________________________________________________________________________________________________________

## Plik nginx.conf: ##

Serwer NGINX działa w osobnym kontenerze i udostępnia statyczny raport FastQC zapisany w wolumenie Dockera, dzięki czemu wyniki analizy wykonanej w innym kontenerze są dostępne przez przeglądarkę.

- server {
    - listen 80; **(Określa port, na którym serwer NGINX nasłuchuje połączeń HTTP. Port 80 to domyślny port protokołu HTTP)**
    - server_name localhost;
    - root /usr/share/nginx/html;
  - index SRR8786200_1_fastqc.html; **(Określa domyślny plik, który NGINX ma wyświetlić po wejściu na stronę. Jest to główny raport HTML wygenerowany przez FastQC)**
    - location / {
        - try_files $uri $uri/ =404;  **(Sprawdza czy istnieje taki plik ($uri) i katalog ($uri/), jeśli nie to pokaże się błąd)**
    }
}


___________________________________________________________________________________________________________

## Plik docker-compose: ##

Docker Compose zarządza dwoma kontenerami: FastQC wykonuje analizę FASTQ i zapisuje wyniki w wolumenie, natomiast NGINX udostępnia raport HTML przez przeglądarkę, korzystając z tego samego wolumenu.

- version: '3.8'
- services: 
 - fastqc:
   - build:
     - context: .
    -  dockerfile: Dockerfile
   - container_name: fastqc_container
   - volumes:
     - - fastqc_results:/results
    -  - ./input:/input:ro
   - networks:
    -  - fastqc_network
   - environment:
    -  JAVA_HOME: /usr/lib/jvm/java-11-openjdk-amd64
   -   CLASSPATH: /usr/local/FastQC:/usr/local/FastQC/htsjdk.jar:/usr/local/FastQC/jbzip2-0.9.jar:/usr/local/FastQC/cisd-jhdf5.jar
  -  command: /input/SRR8786200_1.fastq.gz -o /results

 - nginx:
   - image: nginx:latest
   - container_name: nginx_container
   - volumes:
     - - fastqc_results:/usr/share/nginx/html:ro
    -  - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
   - ports:
  - - "8080:80"
   - networks:
     - - fastqc_network

- volumes:
  - fastqc_results:

- networks:
  - fastqc_network:







