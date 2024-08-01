## irrigae 
Sistema di irrigazione avanzato per Home Assistant.

Si adatta a qualsiasi configurazione home assistant, è sufficiente avere delle entità di tipo switch che controllano le valvole di irrigazione.
Non sono richieste particolari abilità per l'installazione. 

![home](examples/home.png)

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
Nel file di configurazione principale di HomeAssistant (configuration.yaml) dovete aggiungere, se non sono già presenti, le seguenti configurazioni:


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

![config](examples/config.png)

- Scegliete quanti cicli di irrigazione automatica volete gestire (da 1 a 3), consiglio 3. 
- Scegliete il numero di zone che il vostro sistema di irrigazione gestisce (da 1 a 6).
- Inserite per ogni zona il nome dell'entità di tipo switch che controlla la valvola.
- Se volete che il vostro sistema consideri le previsioni di pioggia aggiungete l'entità weather della vostra zona geografica.
- Inserite quindi il numero di ore che volete tenere in considerazione per le previsioni di pioggia e per le piogge precedenti. 
  Dai test risulta che 15h e 18h siano valori ottimali, ma devono tener conto della zona in cui vivete e del tempo di evaporazione.
- Se volete essere avvisati sulle attività del sistema di irrigazione, inserite quindi il servizio di notifica per il vostro cellulare/alexa,ecc.
- E' possibile quindi inserire anche il tempo di preavviso con cui volete ricevere la notifica prima dell'avvio di un'irrigazione automatica. </br>
  Questo vi consente per esempio di avere il tempo per bloccarla o spostare eventuali oggetti che non devono essere bagnati. Un valore di 30 per esempio invierà la notifica 30 secondi prima dell'avvio effettivo dell'irrigazione.

I parametri non presenti in questo elenco sono per utenti esperti, in grado di creare sensori e script. Vedere sezione apposita.

## Attivazione sistema e controllo 
Selezionando la tab "CONTROL" potrete ora attivare e disattivare le varie funzionalità del sistema di irrigazione.

![cycle_config](examples/control.png)

- Abilitare il sistema. Con il sistema disabilitato, nessuna zona di irrigazione funzionerà. La disattivazione del sistema serve (per esempio) quando si chiude l'acqua durante l'inverno e permette di evitare avvi involontari dell'irrigazione che potrebbero danneggiare l'impianto.
- Selezionare il tipo di notifiche che si desidera ricevere. Al momento della scrittura di questo documento non c'è molta differenza, selezionare ALL per ricevere tutte le notifiche o No, per non ricevere nulla.
- Abilitare i sistemi di riduzione dell'irrigazione per previsioni pioggia o per precipitazioni precedenti a seconda delle proprie necessità.
- In caso di utilizzo della riduzione irrigazione per pioggia configurare i valori Reduce Irrigation e Stop Irrigation sia per precedenti precipitazioni che per precipitazioni previste. I valori 2mm e 5mm danno buoni risultati. 
  Il comportamento dei due campi è il seguente:
  Il sistema comincia a ridurre i minuti di irrigazione per ogni singola zona quando la pioggia supera il primo limite, quando la pioggia supera il secondo limite il sistema non irriga. Un valore di pioggia compreso tra i due limiti riduce l'irrigazione in proporzione. Esempio pratico:
  Supponiamo di configurare i due limiti cosi':
  Limite basso: 2mm
  Limite alto: 6mm
  Se le previsioni pioggia fossero di 4 millimetri, l'irrigazione sarebbe ridotta al 50% per ogni singola zona. Se le previsioni piogga fossero di 1mm l'irrigazione sarebbe normale. Se l'irrigazione fosse 7mm l'irrigazione sarebbe annullata.
  I valori sono da inserire sia per le precipitazioni precedenti, sia per le precipitazioni previste. Le rispettive riduzioni vengono moltiplicate.
  Quindi il sistema tiene conto sia delle piogge cadute ieri che delle piogge previste oggi. </br>
  Attenzione: Irrigae aggiorna il sensore di previone pioggia ogni ora. Se volete un aggiornamento instantantaneo disabilitate e abilitate il campo "Enable rain forecast reduction". Il sensore delle precipitazioni precedenti viene aggiornato costantemente, ma il suo valore sarà attendibile solo dopo 24 ore.
  

- Nelle sezioni 'Global irrigation percentage' e 'Zone irrigation percentage' vengono mostrate le reali percentuali di irrigazione che il sistema adotterà se l'irrigazione partisse in questo momento. Valori del 100% indicano che l'irrigazione sarà completa. Valori inferiori indicano che i minuti di irrigazione saranno ridotti della corripondente percentuale.
La percentuale finale di irrigazione è Result (il dato più importante), ovvero il risultato delle due riduzioni per pioggia (precipitazioni precenti * precipitaizioni previste).
Anche le percentuali per zone sono soggette al valore di Result. Quindi se per esempio result è 30% e la percentuale zona1 è 100%, la zona1 verrà irrigata solo al 30%, cioè la moltiplicazione di result * zona1. Le correzioni per zona sono comunque dedicate ad utenti esperti, in grado cioè di creare i propri sensori per controllare il tempo di irrigazione. 
 
## Home irrigazione
Terminate le due configurazioni precedenti siete pronti per provare il sistema di irrigazione. Selezionate quindi la pagina HOME per cominciare i test di irrigazione.

![home](examples/home.png)

Nella prima riga vengono visualizzati i cicli automatici che avete deciso di utilizzare (da 1 a 3). Premendo il pulsante entrerete in configurazione dei cicli.
Attenzione, se premendo sui pulsanti della pagina Home non si apre alcuna finestra di popup avete problemi con l'add-on browser_mod. Verificate che sia correttamente installato e attivo. Consultate la relativa documentazione per la giusta configurazione di browser_mod. Ogni altro problema con la grafica della pagina Home è dovuto esclusivamente alla scorretta installazione degli add-on elencati nei prerequisiti. 

![cycle_config](examples/cycle_config.png)

- Enabled: Potete Abilitare o disabilitare questo ciclo
- Name: Potete Assegnare un nome a piacere al vostro ciclo
- Start time: Ora di avvio del ciclo
- Apply rain reduction: </br>
  Vi consente di decidere se abilitare la riduzione per pioggia per questo ciclo. La riduzione per pioggia può essere disabilitata per esempio se il ciclo si riferisce a zone protette dagli agenti atmosferici
- Apply zone correction: </br>
  Abilita la correzione dei tempi di irrigazione delle zone per altri sensori, come per esempio cisterne o sensori di umidità del terreno (utenti avanzati)
- Irrigation days sequence: </br>
  Questa è la sequenza di giorni in cui irrigare o non irrigare. Dimenticate le classiche configurazione a giorni della settimana. La natura non comprende i nostri giorni della settimana e deve essere irrigata con periodicità costante. Questo sistema vi consente di irrigare alternando i giorni di irrigazione a vostro piacere, con una combinazione massima di 8 giorni. </br>
  Per prima cosa dovrete scegliere di quanti giorni è la vostra sequenza, quindi per ogni giorno abilitare o disabilitare l'irrigazione.
   I giorni evidenziati sono quelli in cui il sistema farà partire l'irrigazione, nei giorni non evidenziati l'irrigazione non partirà. Fate un singolo click sul giorno per attivarlo/disattivarlo. </br>
  Esempio1: Se volete irrigare a giorni alterni, selezionate 2 giorni di sequenza e abilitate il primo giorno e disabilitate il secondo giorno.</br>
  Esempio2: Se volete irrigare tutti i giorni tranne la domenica selezionate 7 giorni di sequenza e abilitate i primi 6 giorni e l'ultimo no. (fate attenzione dovrete anche fare doppio click sul giorno della sequenza che corrisponde a oggi). </br>
  Nell'esempio di figura invece è stato scelto di irrigare con la sequenza giorni Si-No-Si-Si-No. </br>
  Il giorno corrente è rappresentato dal riquadro verde. Il giorno corrente si sposta automaticamente a mezzanotte. Se volete cambiarlo manualmente fate doppio click. Si chiuderà la scheda, se la riaprite vedrete che il giorno corrente è stato aggiornato.</br>
  Importante. Quando il ciclo di irrigazione viene annullato per pioggia, il giorno corrente avanza ugualmente anche se l'irrigazione non è partita. Il giorno corrente quindi avanza indipendentemente da tutto alla mezzanotte di ogni giorno.
- Minuti di irrigazione: </br>
  Per ogni zona del vostro impianto potrete decidere i minuti di irrigazione, se lasciate il valore a zero, quella zona non verrà irrigata.

La parte sucessiva della HOME consente di controllare l'irrigazione.
- Suspend: </br>
  Consente di sospendere l'irrigazione automatica per un po' di giorni (tutti i cicli). Se per esempio dalle vostre osservazioni notate che il terreno è già troppo bagnato potete sospendere l'irrigazione per tot giorni.
Lo stesso effetto si ottiene ovviamente anche disabilitando i cicli di irrigazione, ma il vantaggio della sospensione sta nel fatto che non dovrete ricordarvi di riattivarli, infatti con la sospensione al termine del  periodo di stop, l'irrigazione torna a funzionare autonomamente.
- Delay: </br>
  Questa funziona consente di ritardare l'irrigazione. Supponiamo che durante il ciclo di irrigazione dobbiate attraversare il giardino. Grazie alla funzione di delay potrete ritardare l'irrigazione per tutto il tempo che vi serve per uscire dalla zona di irrigazione. La funzione di delay funziona sia prima dell'avvio dell'irrigazione (nel qual caso il timer di ritardo parte esattamente all'ora prevista dal ciclo), sia mentre gli irrigatori stanno andando (nel qual caso l'irrigazione si blocca immediatamente e riprende dopo il tempo che gli avete indicato).
- Manual cycle:</br>
  Permette di avviare un ciclo manuale di tutte le zone con tempi da voi selezionati.
- Stop irrigation:</br>
  Arresta immediatamente qualsiasi tipo di irrigazione (manuale o automatica o zone).

L'ultima parte della HOME consente di avviare gli irrigatori di zona manualmente per un tempo da voi scelto.

## Funzionalità avanzate (utenti esperti)
Questa sezione è riservata a utenti in grado di creare sensori e script. Nella pagina CONFIG potrete aggiungere i seguenti sensori/script per variare il comportamento standard del sistema di irrigazione.

- Sensore precipitazioni pioggia ore precedenti.
Il sensore nativo di Irrigae si basa esclusivamente sulle previsioni pioggia delle ore precedenti. Non è quindi un vero sensore che misura la pioggia realmente caduta, in quanto fa affidamento esclusivamente alla bontà delle previsioni meteo precedenti. Se invece avete una stazione meteo, un sensore pioggia o un qualsiasi altro sensore di umidità terreno potrete creare il vostro sensore personale che indichi quanta pioggia è realmente caduta nelle ultime ore.
Dovrete quindi creare un sensore che ritorni i millimetri di pioggia caduta e inserire il nome nel campo 'Rain Precipitation Sensor'. Il sistema di irrigazione escluderà quindi il suo sensore nativo e userà il vostro.
  Esempio di sensore pioggia:

  ```yaml
  - template:
      - sensor:
          - name: Rain last 24 hours
            unique_id: rain_last_24h
            icon: mdi:weather-pouring
            unit_of_measurement: "mm"
            device_class: precipitation
            state: > 
              {% if has_value('sensor.station_daily_rain_rate') %}
                 {{states('sensor.station_daily_rain_rate')|float}}
              {% else %}
                {{0.0}}
              {% endif %}
  ```

- Sensore previsioni pioggia prossime ore.
Potrete sostituire il sensore navito di Irrigae che calcola le previsioni di pioggia nelle prossime ore. Una volta creato il vostro sensore che ritorni i millimetri di pioggia che cadranno nelle prossime ore potrete inserirne il nome nel campo 'Rain Forecasts Sensor'. Il sistema di irrigazione escluderà quindi il suo sensore nativo e userà il vostro.

  Esempio di sensore previsioni pioggia per le successive 24h aggiornato ogni ora:

  ```yaml
  - template:
     - trigger:
       - platform: time_pattern
         hours: /1
     action:
       - service: weather.get_forecasts
         data:
           type: hourly
         target:
           entity_id: weather.my_home
         response_variable: weather_hourly
     sensor:
       - name: Rain next 24h
         unique_id: rain_next_24h
         icon: hass:weather-rainy
         unit_of_measurement: "mm"
         device_class: precipitation
         state: > 
           {% set var = namespace(total=0) %}
           {% for forecast in weather_hourly['weather.my_home'].forecast %}
           {% if loop.index <= 24 %}
           {% set var.total = var.total + forecast.precipitation %}
           {% endif %} 
           {% endfor %}
           {{ var.total | float | round(2, 'floor')}}  
  ```
- Sensori per il controllo dell'irrigazione delle zone
Un altro modo per controllare i tempi di irrigazione è utilizzare i sensori delle zone. Questi sensori dovranno restituire una percentuale di irrigazione.
100% significa che l'irrigazione sarà normale, valori inferiori o superiori ridurranno o aumenteranno i minuti di irrigazione. Questi sensori possono essere usati per vari scopi. Per esempio se avete dei sensori di umidità del terreno distribuiti nelle varie zone, potrete fare affidamento su questi per ridurre o aumentare il tempo di irrigazione. Potete usare lo stesso sensore su più zone. Un altro classico esempio può essere quello di ridurre l'irrigazione basandosi sulla quantità di acqua che avete nella cisterna. Se la cisterna non è piena potrete ridurre progressivamente i tempi di irrigazione per salvaguardare il consumo di acqua. Una volta creato il vostro sensore potrete inserire il nome nei campi dedicati alla correzione delle zone.

 Esempio di sensore per ridurre l'irrigazione 
  ```yaml
  - template:
     - sensor:
          - name: reduce_irrigation
            unique_id: reduce_irrigation
            unit_of_measurement: '%'
            icon: mdi:water-percent
            state: > 
              {% set sensor = 'sensor.tank_level' %}
              {%if has_value(sensor) %}
                {% set val = states(sensor) | float %}
              {%else%}
                {% set val = 0.0 %}
              {%endif%}
              {%if (val >= 80) %}
                {{100}}
              {%elif (val >= 60) %}
                {{70}}
              {%elif (val >= 40) %}
                {{50}}
              {%else%}
                {{40}}
              {%endif%}
   ```

- Script di notifica attività di irrigazione
  Irrigae mette a disposizione un sistema nativo di notifica. Potete aggiungere uno script personale per notifiche più complesse e inserirlo nel campo "Notification custom script".
  Lo script che create dovrà avere come field di input i seguenti campi:
  -> title: titolo nel messaggio
  -> message: Corpo del messaggio
  -> type: Tipo della notifica

 Esempio di notifica con alexa 
```yaml
notify_irrigation_activity:
  alias: Notify Irrigation activity
  fields:
    title:
      required: false
      description: Title 
    message:
      required: true
      description: Message to notify
      selector:
        text:
          multiline: true
    type:
      required: true
      description: Type of notification
  sequence:
      - service: notify.alexa_media
        data:
          message: '{{message}}'
          target: media_player.echo_sala, media_player.echo_studio
          data:
            type: tts
            method: spoken
  mode: queued
  icon: mdi:bullhorn-variant-outline
  max: 5

```

## Multilanguage
Attualmente irrigae è disponibile solo in lingua inglese.

## Animation
![animated](examples/animated.gif)
