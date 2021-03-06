---
title: Reazione a eventi di Archiviazione BLOB di Azure | Microsoft Docs
description: Usare la Griglia di eventi di Azure per sottoscrivere eventi di archiviazione BLOB.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: 5281dab8fd42326d88964614fd20a81621b5e9dd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268496"
---
# <a name="reacting-to-blob-storage-events"></a>Reazione a eventi di Archiviazione BLOB di Azure

Gli eventi di archiviazione di Azure consentono alle applicazioni di reagire agli eventi, ad esempio la creazione e l'eliminazione di BLOB. e senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti.

Viene eseguito il push degli eventi usando [griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) per Sottoscrittori, ad esempio funzioni di Azure, app per la logica di Azure o anche il listener HTTP. La parte migliore è pagare solo per le risorse usate.

L'archiviazione BLOB invia eventi a griglia di eventi, che fornisce recapito di eventi affidabili alle applicazioni tramite criteri di ripetizione dei tentativi avanzati e messaggi non recapitabili.

Tra gli scenari comuni di eventi di Archiviazione BLOB sono inclusi l'elaborazione di immagini o video, l'indicizzazione delle ricerche o qualsiasi flusso di lavoro orientato ai file. I caricamenti asincroni di file sono operazioni perfette per gli eventi. Quando le modifiche non sono frequenti, ma lo scenario richiede tempi di risposta immediata, un'architettura basata su eventi può essere particolarmente efficiente.

Per provare a eseguire questa operazione, vedere gli articoli introduttivi seguenti:

|Se si desidera utilizzare questo strumento:    |Vedere questo articolo: |
|--|-|
|Portale di Azure    |[Guida introduttiva: indirizzare gli eventi di archiviazione BLOB all'endpoint Web con il portale di Azure](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Guida introduttiva: indirizzare gli eventi di archiviazione all'endpoint Web con PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Interfaccia della riga di comando di Azure    |[Guida introduttiva: indirizzare gli eventi di archiviazione all'endpoint Web con l'interfaccia della riga](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Per visualizzare esempi dettagliati di reagire agli eventi di archiviazione BLOB tramite funzioni di Azure, vedere gli articoli seguenti:

- [Esercitazione: usare eventi Azure Data Lake storage Gen2 per aggiornare una tabella Delta di databricks](data-lake-storage-events.md).
- [Esercitazione: automatizzare il ridimensionamento delle immagini caricate con griglia di eventi](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Solo gli account di archiviazione di tipo **archiviazione V2 (utilizzo generico v2)** e **BlobStorage** supportano l'integrazione degli eventi. Il tipo **Archiviazione (utilizzo generico V1)** *non* supporta l'integrazione con Griglia di eventi.

## <a name="the-event-model"></a>Modello di eventi

Griglia di eventi usa le [sottoscrizioni](../../event-grid/concepts.md#event-subscriptions) di eventi per indirizzare i messaggi di evento ai sottoscrittori. Questa immagine illustra la relazione tra autori di eventi, sottoscrizioni di eventi e gestori eventi.

![Modello di Griglia di eventi di Azure](./media/storage-blob-event-overview/event-grid-functional-model.png)

Prima di tutto, sottoscrivere un endpoint per un evento. Quindi, quando viene attivato un evento, il servizio griglia di eventi invierà i dati relativi all'evento all'endpoint.

Vedere l'articolo [dello schema degli eventi di archiviazione BLOB](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) da visualizzare:

> [!div class="checklist"]
> * Elenco completo degli eventi di archiviazione BLOB e modalità di attivazione di ogni evento.
> * Un esempio dei dati che la griglia di eventi invierà per ognuno di questi eventi.
> * Scopo di ogni coppia chiave-valore visualizzata nei dati.

## <a name="filtering-events"></a>Filtro di eventi

[Gli eventi BLOB possono essere filtrati](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) in base al tipo di evento, al nome del contenitore o al nome dell'oggetto che è stato creato/eliminato. I filtri in griglia di eventi corrispondono all'inizio o alla fine dell'oggetto in modo che gli eventi con un oggetto corrispondente vadano al Sottoscrittore.

Per altre informazioni su come applicare i filtri, vedere [filtrare gli eventi per griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

L'oggetto di eventi di archiviazione BLOB usa il formato:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Per trovare la corrispondenza di tutti gli eventi per un account di archiviazione, è possibile non impostare alcun filtro dell'oggetto.

Per trovare la corrispondenza di eventi da BLOB creati in un set di contenitori che condividono un prefisso, usare un filtro `subjectBeginsWith` come:

```
/blobServices/default/containers/containerprefix
```

Per trovare la corrispondenza di eventi da BLOB creati in un contenitore specifico, usare un filtro `subjectBeginsWith` come:

```
/blobServices/default/containers/containername/
```

Per trovare la corrispondenza di eventi da BLOB creati in un contenitore specifico che condividono un prefisso di nome di BLOB, usare un filtro `subjectBeginsWith` come:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Per trovare la corrispondenza di eventi da BLOB creati in un contenitore specifico che condividono un suffisso di BLOB, usare un filtro `subjectEndsWith`, ad esempio ".log" o ".jpg". Per altre informazioni, vedere [Concetti di Griglia di eventi di Azure](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi

Per le applicazioni che gestiscono gli eventi di archiviazione BLOB è consigliabile seguire alcune procedure:
> [!div class="checklist"]
> * Dal momento che vi possono essere più sottoscrizioni configurate per instradare gli eventi allo stesso gestore eventi, è importante non presupporre che gli eventi derivino da un'origine specifica, ma controllare l'argomento del messaggio per assicurarsi che provengano dall'account di archiviazione previsto.
> * Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
> * Poiché i messaggi possono arrivare dopo un certo ritardo, usare i campi eTag per capire se le informazioni sugli oggetti sono ancora aggiornate. Per informazioni su come usare il campo ETag, vedere [gestione della concorrenza nell'archivio BLOB](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage). 
> * Poiché i messaggi possono arrivare senza ordine, utilizzare i campi di Sequencer per comprendere l'ordine degli eventi in un oggetto specifico. Il campo sequencer è un valore stringa che rappresenta la sequenza logica di eventi per un nome di BLOB particolare. È possibile usare il confronto di stringhe standard per comprendere la sequenza relativa di due eventi nello stesso nome di BLOB.
> * Usare il campo blobType per capire il tipo di operazioni consentite sul BLOB e quali tipi di librerie client è consigliabile usare per accedere al BLOB. I valori validi sono `BlockBlob` o `PageBlob`. 
> * Usare il campo URL con i costruttori `CloudBlockBlob` e `CloudAppendBlob` per accedere al BLOB.
> * Ignorare i campi che non si conoscono. Questa procedura consentirà di rimanere flessibili alle nuove funzionalità che potrebbero essere aggiunte in futuro.
> * Per assicurarsi che l'evento **Microsoft. storage. BlobCreated** venga attivato solo quando viene eseguito il commit completo di un BLOB in blocchi, filtrare l'evento per le chiamate all'API REST di `CopyBlob`, `PutBlob`, `PutBlockList` o `FlushWithClose`. Queste chiamate API attivano l'evento **Microsoft. storage. BlobCreated** solo dopo che è stato eseguito il commit completo dei dati in un BLOB in blocchi. Per informazioni su come creare un filtro, vedere [filtrare gli eventi per griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni su Griglia di eventi e la possibilità di provare a usare gli eventi di archiviazione BLOB:

- [Informazioni sulla griglia di eventi](../../event-grid/overview.md)
- [Route Blob storage Events to a custom web endpoint](storage-blob-event-quickstart.md) (Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato)
