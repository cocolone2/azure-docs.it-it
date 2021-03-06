---
title: Acquisire eventi di streaming - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica della funzionalità Acquisizione, che consente di acquisire gli eventi in streaming attraverso Hub eventi di Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: c166f4cace6a8cc25b36a84f4614033801e69a51
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265012"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Acquisire eventi tramite Hub eventi di Azure in Archiviazione BLOB di Azure o Azure Data Lake Storage
Hub eventi di Azure consente di acquisire automaticamente i dati in streaming in hub eventi in un [Archivio BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/) o Azure Data Lake storage account di [generazione 1 o gen 2](https://azure.microsoft.com/services/data-lake-store/) di propria scelta, con la flessibilità aggiuntiva di specificare un intervallo di tempo o di dimensioni. La configurazione di Acquisizione è rapida, non sono previsti costi amministrativi per l'esecuzione e viene ridimensionata automaticamente con le [unità elaborate](event-hubs-scalability.md#throughput-units) in Hub eventi. Acquisizione di Hub eventi è il modo più semplice per caricare i dati in streaming in Azure e consente di concentrarsi sull'elaborazione dei dati anziché sull'acquisizione.

Acquisizione di Hub eventi consente di elaborare pipeline in tempo reale e basate su batch nello stesso flusso. Ciò significa che è possibile compilare soluzioni che si adattano alle esigenze nel corso del tempo. Sia che si debbano compilare oggi sistemi basati su batch con lo sguardo rivolto alla futura elaborazione in tempo reale o che si voglia aggiungere un percorso a freddo efficiente a una soluzione in tempo reale esistente, Acquisizione di Hub eventi semplifica l'uso dei dati in streaming.


## <a name="how-event-hubs-capture-works"></a>Come funziona Acquisizione di Hub eventi

Hub eventi è un buffer permanente di conservazione nel tempo per l'ingresso della telemetria, simile a un log distribuito. La chiave per ridurre il numero di istanze di Hub eventi è il [modello di consumer partizionato](event-hubs-scalability.md#partitions). Ogni partizione è un segmento di dati indipendente e viene utilizzata in modo indipendente. Nel corso del tempo questi dati diventano obsoleti, a seconda del periodo di conservazione configurabile. Di conseguenza, un determinato hub eventi non sarà mai "troppo pieno".

Acquisizione di hub eventi consente di specificare il proprio account di archiviazione BLOB di Azure e il contenitore o l'account Azure Data Lake Storage, che vengono usati per archiviare i dati acquisiti. Questi account possono trovarsi nella stessa area dell'hub eventi o in un'altra area, aumentando così la flessibilità della funzionalità Acquisizione di Hub eventi.

I dati acquisiti vengono scritti in formato [Apache avro][Apache Avro] , un formato compatto, veloce e binario che offre strutture di dati avanzate con lo schema inline. Questo formato è largamente usato nell'ecosistema Hadoop, dall'analisi di flusso e da Azure Data Factory. Altre informazioni sull'uso di Avro sono disponibili più avanti in questo articolo.

### <a name="capture-windowing"></a>Acquisire windowing

Acquisizione di Hub eventi consente di configurare una finestra per controllare l'acquisizione. Questa finestra è una dimensione minima e una configurazione a tempo con un criterio basato sulla precedenza, ovvero il primo trigger rilevato avvia un'operazione di acquisizione. Se si ha una finestra di acquisizione di quindici minuti/100 MB e si invia 1 MB al secondo, la finestra della dimensione viene attivata prima della finestra temporale. Ogni partizione acquisisce in modo indipendente e scrive un BLOB in blocchi completo al momento dell'acquisizione, denominato in base all'ora in cui è stato rilevato l'intervallo di acquisizione. La convenzione di denominazione dell'archiviazione è la seguente:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Si noti che i valori di data vengono riempiti con zeri, come illustrato nel nome di file di esempio seguente:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Nel caso in cui il BLOB di archiviazione di Azure sia temporaneamente non disponibile, l'acquisizione di hub eventi manterrà i dati per il periodo di conservazione dei dati configurato nell'hub eventi e riempirà i dati quando l'account di archiviazione sarà nuovamente disponibile.

### <a name="scaling-to-throughput-units"></a>Ridimensionamento alle unità elaborate

Il traffico di Hub eventi è controllato dalle [unità elaborate](event-hubs-scalability.md#throughput-units). Una singola unità elaborata consente 1 MB al secondo o 1000 eventi al secondo in ingresso e il doppio in uscita. Hub eventi standard può essere configurato con 1-20 unità di velocità effettiva ed è possibile acquistare altro con una [richiesta di supporto][support request]per l'aumento della quota. L'uso superiore rispetto alle unità elaborate acquistate è limitato. Acquisizione di Hub eventi copia i dati direttamente dalla memoria di Hub eventi interna, ignorando le quote in uscita di unità elaborate e salvando l'uscita per altri lettori di elaborazione, ad esempio l'analisi di flusso o Spark.

Acquisizione di Hub eventi, dopo essere stata configurata, viene eseguita automaticamente quando si invia il primo evento e continua l'esecuzione. Per comunicare facilmente all'elaborazione downstream che il processo è funzionante, Hub eventi scrive file vuoti quando non sono presenti dati. Questo processo ottiene una cadenza prevedibile e un marcatore che possono alimentare i processori batch.

## <a name="setting-up-event-hubs-capture"></a>Configurazione di Acquisizione di Hub eventi

È possibile configurare Acquisizione al momento della creazione dell'hub eventi usando il [portale di Azure](https://portal.azure.com) o i modelli di Azure Resource Manager. Per altre informazioni, vedere gli articoli seguenti:

- [Abilitare Acquisizione di Hub eventi usando il portale di Azure](event-hubs-capture-enable-through-portal.md)
- [Creare uno spazio dei nomi di Hub eventi con un hub eventi e abilitare l'acquisizione con un modello di Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Esplorazione dei file acquisiti e dell'uso di Avro

Acquisizione di Hub eventi crea i file in formato Avro, come specificato nell'intervallo di tempo configurato. È possibile visualizzare questi file con qualsiasi strumento, ad esempio [Azure Storage Explorer][Azure Storage Explorer]. È possibile scaricare i file in locale per usarli.

I file generati da Acquisizione di Hub eventi hanno lo schema Avro seguente:

![Schema Avro][3]

Per esplorare facilmente i file di Avro, è possibile usare il file JAR [Avro Tools][Avro Tools] di Apache. È anche possibile usare [Apache drill][Apache Drill] per un'esperienza Lightweight basata su SQL o [Apache Spark][Apache Spark] per eseguire un'elaborazione distribuita complessa sui dati inseriti. 

### <a name="use-apache-drill"></a>Usare Apache Drill

[Apache drill][Apache Drill] è un motore di query SQL open source per l'esplorazione di Big data, che può eseguire query su dati strutturati e semistrutturati ovunque si trovino. Il motore può essere eseguito come un nodo autonomo o come un cluster di grandi dimensioni per prestazioni ottimali.

È disponibile un supporto nativo per Archiviazione BLOB di Azure, che rende più semplice eseguire query sui dati in un file Avro, come descritto nella documentazione:

[Apache Drill: plug-in di archiviazione BLOB di Azure][Apache Drill: Azure Blob Storage Plugin]

Per eseguire con facilità query sui file acquisiti, è possibile creare ed eseguire una macchina virtuale con Apache Drill abilitata tramite un contenitore per l'accesso ad Archiviazione BLOB di Azure:

https://github.com/yorek/apache-drill-azure-blob

Un esempio completo end-to-end è disponibile nel repository Flussi scalabili:

[Streaming su larga scala: acquisizione di hub eventi]

### <a name="use-apache-spark"></a>Usare Apache Spark

[Apache Spark][Apache Spark] è un motore di analisi unificato per l'elaborazione di dati su larga scala. Supporta diversi linguaggi, tra cui SQL, e può accedere facilmente ad Archiviazione BLOB di Azure. Sono disponibili alcune opzioni per eseguire Apache Spark in Azure e ognuna consente di accedere facilmente all'archivio BLOB di Azure:

- [HDInsight: indirizzare i file in archiviazione di Azure][HDInsight: Address files in Azure storage]
- [Azure Databricks: archiviazione BLOB di Azure][Azure Databricks: Azure Blob Storage]
- [Servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/spark-job) 

### <a name="use-avro-tools"></a>Usare Avro Tools

[Gli strumenti avro][Avro Tools] sono disponibili come pacchetto jar. Dopo avere scaricato il file JAR, è possibile visualizzare lo schema di un file Avro specifico eseguendo il comando seguente:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

Questo comando restituisce

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

È anche possibile usare Avro Tools per convertire il file in formato JSON ed eseguire altre operazioni di elaborazione.

Per eseguire operazioni di elaborazione più avanzate, scaricare e installare Avro per la propria piattaforma. Al momento della stesura di questo articolo, sono disponibili implementazioni per C, C++, C\#, Java, NodeJS, Perl, PHP, Python e Ruby.

Apache avro offre guide Introduzione complete per [Java][Java] e [Python][Python]. È anche possibile leggere l'articolo [Acquisizione di Hub eventi di Azure](event-hubs-capture-python.md).

## <a name="how-event-hubs-capture-is-charged"></a>Come viene addebitato l'uso di Acquisizione di Hub eventi

L'uso di Acquisizione di Hub eventi viene registrato in modo simile a quello delle unità elaborate, come tariffa oraria. L'addebito è direttamente proporzionale al numero di unità elaborate acquistate per lo spazio dei nomi. Quando le unità elaborate aumentano o diminuiscono, anche Acquisizione di Hub eventi aumenta o diminuisce per offrire prestazioni corrispondenti. Le misurazioni vengono eseguite in parallelo. Per i dettagli sui prezzi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/). 

Si noti che l'acquisizione non utilizza la quota in uscita perché viene fatturata separatamente. 

## <a name="integration-with-event-grid"></a>Integrazione con Griglia di eventi 

È possibile creare una sottoscrizione di Griglia di eventi di Azure con uno spazio dei nomi di Hub eventi come origine. L'esercitazione seguente illustra come creare una sottoscrizione di Griglia di eventi con un hub eventi come origine e un'app di Funzioni di Azure come sink: [Elaborare i dati di Hub eventi acquisiti ed eseguirne la migrazione a SQL Data Warehouse con Griglia di eventi e Funzioni di Azure](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Passaggi successivi
Acquisizione di Hub eventi rappresenta il modo più facile per ottenere i dati in Azure. Con Azure Data Lake, Azure Data Factory e Azure HDInsight, è possibile eseguire l'elaborazione batch e altre analisi usando strumenti e piattaforme familiari a scelta con la scalabilità necessaria.

Informazioni su come abilitare questa funzionalità usando il modello di portale di Azure e di Azure Resource Manager:

- [Usare il portale di Azure per abilitare l'acquisizione di hub eventi](event-hubs-capture-enable-through-portal.md)
- [Usare un modello di Azure Resource Manager per abilitare l'acquisizione di hub eventi](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www.apache.org/dist/avro/stable/java/avro-tools-1.9.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Streaming su larga scala: acquisizione di hub eventi]: https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
