# Discovery desktop — B8

I tool `discovery_resolve`, `discovery_search`, `discovery_status`, `discovery_run` e `discovery_cancel` sono registrati nell'assistente. Le chiamate restituiscono subito un requestId o uno snapshot. Il client conserva chiavi di idempotenza, risorse e autorizzazioni in QSettings, separate per utente, endpoint e workspace. Le credenziali vengono applicate soltanto alle richieste API; non vengono salvate negli snapshot o inviate all'URL firmato del kit.

La scheda nella chat mostra requisiti, lacune, fonti, licenze, motivazioni, modalità, dimensioni, destinazione e budget. Nessun candidato è selezionato automaticamente. Il pulsante di conferma autorizza una selezione precisa; il tool non espone un argomento con cui il modello possa simulare quella conferma. L'area della mappa viene trasformata in EPSG:4326 prima del resolver quando l'utente sceglie di usarla.

Il download è asincrono e usa verifiche condivise con download_file per URL, hash e destinazione, senza un secondo dialogo. Dopo l'hash dello ZIP si verificano indice, percorsi, collisioni, dimensioni, CRC, manifest e hash di ciascun file. I layer devono appartenere alla selezione autorizzata. Le verifiche dei file e dei provider GIS vengono eseguite fuori dal thread UI. Solo layer validi vengono aggiunti al gruppo Discovery; si conserva il CRS nativo e la trasformazione del progetto.

`desktop-review.json` mantiene esito, controlli e copertura. Il report è inviato all'endpoint di review del run. Riproiezione e clip raster non eseguiti sono dichiarati separatamente. Il pulsante di annullamento interrompe il download e impedisce l'importazione di risultati successivi; richieste e ID permettono la ripresa, senza ripetere l'autorizzazione già registrata.

## Verifica

**Sul Mac di sviluppo le build e i relativi output devono essere soltanto sull'SSD.** Build esistenti: `/Volumes/LLM_MODELS/strata_core-build-pyqgis` e `/Volumes/LLM_MODELS/QGIS_AI`. Usare una directory separata sull'SSD per questo checkout isolato; preservare le build e le modifiche locali dell'utente.

Test Qt indipendenti da QGIS:

```sh
cmake -S tests/discovery -B /Volumes/LLM_MODELS/strata-discovery-validation/qt -G Ninja -DCMAKE_PREFIX_PATH=/opt/homebrew
cmake --build /Volumes/LLM_MODELS/strata-discovery-validation/qt -j4
ctest --test-dir /Volumes/LLM_MODELS/strata-discovery-validation/qt --output-on-failure
```

Nel normale ambiente QGIS configurato sull'SSD, compilare `test_app_aidiscoveryworkflow`, `provider_wfs` e `provider_wms`, quindi eseguire il test CTest corrispondente. OGR e GDAL sono inclusi in `qgis_core`, dipendenza del test. Il test copre resolver → ricerca → anteprima → selezione → download → manifest → provider OGR → gruppo QGIS → review, inclusa copertura parziale e conferma duplicata. Un'assenza del provider deve fallire; non saltare il controllo.

Il backend deve essere distribuito prima del desktop. La discovery resta disabilitata sul backend fino ai gate end-to-end e allo smoke staging sui due profili verde urbano. Le prove sintetiche non sostituiscono la verifica su fonti reali.
