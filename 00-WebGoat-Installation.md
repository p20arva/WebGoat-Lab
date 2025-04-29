# Εγκατάσταση Docker μέσω Αποθετηρίου apt

Πριν εγκαταστήσετε το Docker Engine για πρώτη φορά σε έναν νέο υπολογιστή, πρέπει να ρυθμίσετε το αποθετήριο Docker apt. Στη συνέχεια, μπορείτε να εγκαταστήσετε και να ενημερώσετε το Docker από το αποθετήριο.

## Ρύθμιση του Αποθετηρίου apt του Docker

### Προσθήκη του Επίσημου GPG Κλειδιού του Docker

Αρχικά, ενημερώνετε τα πακέτα του apt και εγκαθιστάτε τις απαραίτητες εξαρτήσεις:

```shell
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### Προσθήκη του αποθετηρίου στο apt sources

```shell
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

## Εγκατάσταση των Πακέτων Docker

Για να εγκαταστήσετε την τελευταία έκδοση του Docker, εκτελέστε την παρακάτω εντολή:

```shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## Επαλήθευση της Εγκατάστασης

Για να επιβεβαιώσετε ότι η εγκατάσταση ολοκληρώθηκε επιτυχώς, εκτελέστε την εικόνα `hello-world`:

```shell
sudo docker run hello-world
```

Αυτή η εντολή κατεβάζει μια εικόνα δοκιμής και την εκτελεί σε ένα container. Όταν ο container εκτελείται, εκτυπώνει ένα μήνυμα επιβεβαίωσης και στη συνέχεια σταματά.

# Οδηγίες Εγκατάστασης και Εκτέλεσης WebGoat και WebWolf με Docker

## Μέθοδος 1 - WebGoat και WebWolf σε Ενιαία Εικόνα (All-in-One Image)
Ο πιο εύκολος τρόπος για να ξεκινήσεις το WebGoat ως Docker container είναι μέσω της all-in-one εικόνας που περιλαμβάνει τα WebGoat, WebWolf και έναν NGINX reverse proxy.

Εκτέλεσε την παρακάτω εντολή για να κατεβάσεις την εικόνα από το DockerHub και να ξεκινήσεις ταυτόχρονα τα WebGoat και WebWolf:

```shell
docker run -it -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 -e TZ=Europe/Amsterdam webgoat/webgoat
```

## Μέθοδος 2 - WebGoat και WebWolf μέσω Docker Compose
Μια πιο προηγμένη μέθοδος είναι μέσω του `docker-compose.yml` από το αποθετήριο του WebGoat στο GitHub. Αυτός ο τρόπος ξεκινάει και τα δύο containers και ρυθμίζει αυτόματα τη σύνδεσή τους.

1. Δημιούργησε έναν φάκελο με όνομα `WASE` στον υπολογιστή σου και μπες σε αυτόν.
2. Εκτέλεσε την παρακάτω εντολή μέσα από τον φάκελο `WASE`:

```shell
curl https://raw.githubusercontent.com/WebGoat/WebGoat/develop/docker-compose.yml | docker-compose -f - up
```

> **Σημαντικό**: Ο τρέχων φάκελος του υπολογιστή σου θα αντιστοιχιστεί στον container για την αποθήκευση κατάστασης.

Η χρήση του αρχείου `docker-compose` απλοποιεί την εκκίνηση των WebGoat και WebWolf.

## Πώς Λειτουργούν Μαζί τα Docker και WebGoat;

### Docker Container
Όταν εκτελείς την εντολή:

```shell
docker run -d -p 8080:8080 webgoat/webgoat-8.0
```

το Docker δημιουργεί ένα container από την εικόνα WebGoat. Το container περιλαμβάνει όλα όσα απαιτούνται για την εκτέλεση της εφαρμογής: κώδικα, runtime, βιβλιοθήκες και εξαρτήσεις.

### Αντιστοίχιση Πόρτας (Port Mapping)
Η σημαία `-p 8080:8080` σημαίνει ότι η πόρτα 8080 του host (π.χ. Windows 11) προωθείται στην πόρτα 8080 του container.

Έτσι, κάθε αίτημα στο `localhost:8080` κατευθύνεται στο container.

### Web Server
Στο εσωτερικό του container, το WebGoat εκτελεί έναν web server που "ακούει" για HTTP αιτήματα στην πόρτα 8080.

## Πρόσβαση στο WebGoat από Φυλλομετρητή

1. Άνοιξε τον αγαπημένο σου browser (Chrome, Firefox, Edge).
2. Πληκτρολόγησε στη γραμμή διευθύνσεων:

```
http://localhost:8080/WebGoat
```

Το `localhost` δείχνει στον τοπικό σου υπολογιστή και η πόρτα `8080` προωθείται στο WebGoat container μέσω Docker.
