# Alex Hormozi Summarizer

Cron GitHub Actions che ogni ora controlla il canale YouTube @AlexHormozi (offerta, pricing, vendita, lead generation, crescita aziendale), riassume i nuovi video via Claude Sonnet 4.5 (trascrizione EN, output IT) e manda la sintesi strutturata via mail a Pierpaolo.

Stessa architettura di [davie-fogarty-personal-summarizer](https://github.com/PierpaoloMaggio/davie-fogarty-personal-summarizer), con canale e prompt diversi. Channel ID: `UCUyDOdBWhC1MCxEjC46d-zw`.

## Architettura

```
GitHub Actions cron (orario)
  → fetch RSS canale YouTube
  → diff vs state.json
  → per ogni nuovo videoId non Short (controllo URL /shorts/<id>):
      Apify transcript → Claude Sonnet 4.5 → SMTP Gmail
  → commit state.json aggiornato
```

Il canale pubblica soprattutto Shorts: vengono saltati prima della chiamata Apify. I video con trascrizione sotto 1500 caratteri vengono saltati e marcati come processati; una trascrizione vuota (errore Apify) non viene marcata e si ritenta al run successivo.

## Secrets richiesti

`APIFY_TOKEN`, `OPENROUTER_KEY`, `GMAIL_USER`, `GMAIL_APP_PASSWORD`.

## Primo avvio

Actions → "Alex Hormozi Summarizer" → Run workflow. La prima run è il seed: registra i videoId attuali senza mandare mail.

## Health check

Il run risulta verde anche quando fallisce (gli errori sono catturati per video). Per verificare davvero: leggere il log dello step "Run summarizer" cercando `RSS entries`, `New videos`, `ERROR`, `mail sent`.
