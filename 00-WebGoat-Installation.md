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
