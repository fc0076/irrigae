# irrigae

Gestione completa dell'irrigazione facilmente adattabile a qualsiasi installazione di Home Assistant.

![home](examples/home.png)
![cycle_config](examples/cycle_config.png)


## Funzionalità:
 - Fino a 6 zone di irrigazione
 - Fino a 3 programmi di irrigazione automatici
 - Selezione giorni di irrigazione basati su sequenza fino a 8 giorni
 - Irrigazione manuale tutte le zone
 - Irrigazione manuale singola zona
 - Sospensione irrigazione automatica fino a qualche giorno
 - Ritardo irrigazione automatica fino a qualche ora
 - Riduzione irrigazione basata su previsioni pioggia ore successive
 - Riduzione irrigazione basata su precipitazioni pioggia ora precedenti
 - Invio notifiche su cellulare/alexa/etc.
 - Report ultime irrigazioni
 - Spegnimento di sicurezza per periodo invernale

### Funzionalità avanzate per utenti esperti:
 - Aggiunta di propri sensori per previsioni pioggia/precipitazioni/umidità terreno 
 - Aggiunta di un sensore per ogni zona per il controllo dei minuti irrigazione (utile per cisterne, umidità terreno, ecc)
 - Aggiunta di un proprio script per la notifica delle attività


## Installazione
Irrigae è un insieme di entità, script, timer, automazioni e cards che devono essere aggiunte manualmente alla vostra installazione di HomeAssistant.
Per farlo è sufficiente copiare le cartelle all'interno di /config e riavviare Home Assistant. 
Irrigae è composto di due parti fondamentali: il motore di contollo dell'irrigazione e l'interfaccia grafica. Gli utenti avanzati possono creare/modificare l'interfaccia grafica a piacere per adattarla alla propria dashboard o al proprio stile. 

### Prerequisiti
- Home assistant versione minima 2024.3
- Database standard di Home Assistant (il funzionamento dei sensori SQL non è garantito se avete cambiato il database)
- Integrazione standard Meteorologisk institutt (Met.no) (il funzionamento non è garantito se avete intallato altre integrazione meteo)

Per l'interfaccia grafica devono essere installati e funzionanti i seguenti componenti di terze parti:
- lovelace_gen          (https://github.com/thomasloven/hass-lovelace_gen)
- browser_mod           (https://github.com/thomasloven/hass-browser_mod)
- button_card           (https://github.com/custom-cards/button-card)
- config-template-card  (https://github.com/iantrich/config-template-card) 
- hui-element           (https://github.com/thomasloven/lovelace-hui-element)

Fate riferimento alle rispettive documentazioni per una corretta installazione. Questi componenti sono fondamentali per l'interfaccia grafica, se qualcosa non funziona significa che sono installati in modo scorretto. Se pensate di creare la vostra interfaccia grafica non ne avrete bisogno.

### Cartelle
Scaricate e aggiungete le seguenti cartelle:
- i files in packages/irrigae vanno scaricati e copiati in /config/packages/irrigae (create i percorsi se non esistono)
- i files in custom_templates/irrigae vanno scaricati e copiati in /config/custom_templates/irrigae (create i percorsi se non esistono)
- i files in irrigae_ui vanno scaricati e copiati in /config/www/irrigae_ui (create i percorsi se non esistono)

### configuration.yaml
Nel file di configurazione principale di HomeAssistant (configuration.yaml) dovete aggiungere se non sono già presenti le seguenti configurazioni:


```yaml

homeassistant:
  packages: !include_dir_named packages

# Lovelace new dashboards
lovelace_gen:

lovelace:
  mode: storage
  dashboards:
    lovelace-irrigation:
      mode: yaml
      title: Irrigation
      icon: mdi:watering-can
      show_in_sidebar: true
      filename: www/irrigae_ui/dashboard.yaml

```
Al termine dell'installazione riavviare Home Assistant. Dopo il riavvio nella Sidebar troverete la nuova voce Irrigation.

## Configurazione iniziale sistema (utenti basic)
Selezionando Irrigation dalla Sidebar entrerete nella Home dell'irrigazione. Tutte le etichette e gli stati saranno visualizzati come "Unknown". 
E' normale, dovete ancora configurare il sistema. Per farlo selezionate la tab "CONFIG"

![cycle_config](examples/config.png)

- Scegliete quanti cicli di irrigazione automatica volete gestire (da 1 a 3), consiglio 3. 
- Scegliete il numero di zone che il vostro sistema di irrigazione gestisce (da 1 a 6)
- Inserite per ogni zona il nome dell'entità di tipo switch che controlla la valvola
- Se volete che il vostro sistema consideri le previsioni di pioggia aggiungete l'entità weather della vostra zona geografica
- Inserite quindi il numero di ore che volete tenere in considerazione per le previsioni di pioggia e per le piogge precedenti. 
  Dai test risulta che 15h e 18h siano valori ottimali, ma devono tener conto della zona in cui vivete e del tempo di evaporazione.

## Attivazione irrigazione 
Selezionando la tab "CONTROL" potrete ora attivare e disattivare le farie funzionalità del sistema di irrigazione

![cycle_config](examples/control.png)




