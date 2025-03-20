import datetime
import json
import os
import random

class Libro:
    def __init__(self, titolo, autore, isbn, genere, data_pubblicazione):
        self.titolo = titolo
        self.autore = autore
        self.isbn = isbn
        self.genere = genere
        self.data_pubblicazione = data_pubblicazione
        self.disponibile = True

    def __str__(self):
        return f"{self.titolo} di {self.autore} ({self.isbn}) - {self.genere}, pubblicato il {self.data_pubblicazione}"

class Utente:
    def __init__(self, nome, cognome, id_utente, data_registrazione):
        self.nome = nome
        self.cognome = cognome
        self.id_utente = id_utente
        self.data_registrazione = data_registrazione
        self.libri_presi = []

    def __str__(self):
        return f"{self.nome} {self.cognome} (ID: {self.id_utente}), registrato il {self.data_registrazione}"

class Biblioteca:
    def __init__(self, nome, indirizzo, file_libri="libri.json", file_utenti="utenti.json"):
        self.nome = nome
        self.indirizzo = indirizzo
        self.file_libri = file_libri
        self.file_utenti = file_utenti
        self.libri = self.carica_libri()
        self.utenti = self.carica_utenti()

    def carica_libri(self):
        if os.path.exists(self.file_libri):
            with open(self.file_libri, 'r') as f:
                libri_data = json.load(f)
            return [Libro(**libro) for libro in libri_data]
        return []

    def salva_libri(self):
        with open(self.file_libri, 'w') as f:
            json.dump([libro.__dict__ for libro in self.libri], f, indent=4)

    def carica_utenti(self):
        if os.path.exists(self.file_utenti):
            with open(self.file_utenti, 'r') as f:
                utenti_data = json.load(f)
            return [Utente(**utente) for utente in utenti_data]
        return []

    def salva_utenti(self):
        with open(self.file_utenti, 'w') as f:
            json.dump([utente.__dict__ for utente in self.utenti], f, indent=4)

    def aggiungi_libro(self, libro):
        self.libri.append(libro)
        self.salva_libri()

    def aggiungi_utente(self, utente):
        self.utenti.append(utente)
        self.salva_utenti()

    def cerca_libro(self, titolo=None, autore=None, isbn=None):
        risultati = []
        for libro in self.libri:
            if titolo and titolo.lower() not in libro.titolo.lower():
                continue
            if autore and autore.lower() not in libro.autore.lower():
                continue
            if isbn and isbn != libro.isbn:
                continue
            risultati.append(libro)
        return risultati

    def cerca_utente(self, nome=None, cognome=None, id_utente=None):
        risultati = []
        for utente in self.utenti:
            if nome and nome.lower() not in utente.nome.lower():
                continue
            if cognome and cognome.lower() not in utente.cognome.lower():
                continue
            if id_utente and id_utente != utente.id_utente:
                continue
            risultati.append(utente)
        return risultati

    def prendi_in_prestito(self, libro, utente):
        if libro.disponibile:
            libro.disponibile = False
            utente.libri_presi.append(libro)
            self.salva_libri()
            self.salva_utenti()
            return True
        return False

    def restituisci_libro(self, libro, utente):
        if libro in utente.libri_presi:
            libro.disponibile = True
            utente.libri_presi.remove(libro)
            self.salva_libri()
            self.salva_utenti()
            return True
        return False

    def genera_report_libri_piu_popolari(self, n=5):
        # Implementazione complessa per generare report sui libri più popolari
        pass

    def invia_promemoria_scadenze(self):
        # Implementazione per inviare promemoria agli utenti sui libri in scadenza
        pass

# Esempio di utilizzo
biblioteca = Biblioteca("Biblioteca Comunale", "Via Roma 1")

# Aggiungi libri
biblioteca.aggiungi_libro(Libro("Il Signore degli Anelli", "J.R.R. Tolkien", "978-0-618-26026-3", "Fantasy", "1954-07-29"))
biblioteca.aggiungi_libro(Libro("Orgoglio e Pregiudizio", "Jane Austen", "978-0-14-143951-8", "Romanzo", "1813-01-28"))

# Aggiungi utenti
biblioteca.aggiungi_utente(Utente("Mario", "Rossi", "MR123", datetime.date.today()))
biblioteca.aggiungi_utente(Utente("Laura", "Verdi", "LV456", datetime.date.today()))

# Cerca libri
risultati_ricerca = biblioteca.cerca_libro(titolo="Signore")
for libro in risultati_ricerca:
    print(libro)

# Prendi in prestito un libro
libro_da_prestare = biblioteca.libri[0]
utente_prestito = biblioteca.utenti[0]
biblioteca.prendi_in_prestito(libro_da_prestare, utente_prestito)

# Restituisci un libro
biblioteca.restituisci_libro(libro_da_prestare, utente_prestito)




