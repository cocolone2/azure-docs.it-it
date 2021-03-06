---
title: Trasformare i dati usando l'attività Spark
description: Informazioni su come trasformare dati tramite l'esecuzione di programmi Spark da una pipeline di Azure Data Factory con l'attività Spark.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/31/2018
ms.openlocfilehash: eb887a7d9081875c28964ddb1e3d1b2e609862fd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912963"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Trasformare dati tramite l'attività Spark in Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-spark.md)
> * [Versione corrente](transform-data-using-spark.md)

L'attività Spark in una [pipeline](concepts-pipelines-activities.md) di Data Factory esegue un programma Spark nel cluster HDInsight [personale](compute-linked-services.md#azure-hdinsight-linked-service) o [on demand](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](transform-data.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate. Quando si usa un servizio Spark collegato on demand, Data Factory crea automaticamente un cluster Spark JIT per elaborare i dati e quindi elimina il cluster al termine dell'elaborazione. 


## <a name="spark-activity-properties"></a>Proprietà dell'attività Spark
Ecco la definizione JSON di esempio di un'attività Spark:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

La tabella seguente fornisce le descrizioni delle proprietà JSON usate nella definizione JSON:

| Proprietà              | Description                              | Obbligatoria |
| --------------------- | ---------------------------------------- | -------- |
| name                  | Nome dell'attività nella pipeline.    | SÌ      |
| description           | Testo che descrive l'attività.  | No       |
| type                  | Per l'attività Spark il tipo corrisponde a HDInsightSpark. | SÌ      |
| linkedServiceName     | Nome del servizio collegato di HDInsight Spark in cui viene eseguito il programma Spark. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). | SÌ      |
| SparkJobLinkedService | Il servizio collegato di archiviazione di Azure che contiene il file di processo, le dipendenze e i log di Spark.  Se non si specifica un valore per questa proprietà, viene usato lo spazio di archiviazione associato al cluster HDInsight. Il valore di questa proprietà può essere solo un servizio collegato di Archiviazione di Azure. | No       |
| rootPath              | Contenitore BLOB di Azure e cartella che contiene il file Spark. Il nome del file distingue tra maiuscole e minuscole. Per informazioni dettagliate sulla struttura della cartella, fare riferimento alla prossima sezione, relativa alla struttura delle cartelle. | SÌ      |
| entryFilePath         | Percorso relativo alla cartella radice del pacchetto/codice Spark. Il file di ingresso deve essere un file Python o un file JAR. | SÌ      |
| className             | Classe principale Java/Spark dell'applicazione      | No       |
| arguments             | Elenco di argomenti della riga di comando del programma Spark. | No       |
| proxyUser             | Account utente da rappresentare per eseguire il programma Spark | No       |
| sparkConfig           | Specificare i valori delle proprietà di configurazione di Spark elencati nell'argomento: [Configurazione di SparK: proprietà dell'applicazione](https://spark.apache.org/docs/latest/configuration.html#available-properties). | No       |
| getDebugInfo          | Specifica quando i file di log di Spark vengono copiati nell'archiviazione di Azure usata dal cluster HDInsight (o) specificata da sparkJobLinkedService. Valori consentiti: None, Always o Failure. Valore predefinito: None. | No       |

## <a name="folder-structure"></a>Struttura di cartelle
I processi Spark sono più estendibili dei processi Pig/Hive. Per i processi Spark è possibile offrire più dipendenze, ad esempio pacchetti jar (posizionati in CLASSPATH di java), file python (posizionati in PYTHONPATH) e qualsiasi altro file.

Creare la struttura seguente di cartelle nell'archivio BLOB di Azure a cui fa riferimento il servizio collegato HDInsight. Caricare i file dipendenti nelle sottocartelle appropriate all'interno della cartella radice rappresentata da **entryFilePath**. Ad esempio, caricare i file python nella sottocartella pyFiles e i file jar nella sottocartella jars della cartella radice. In fase di esecuzione, il servizio Data Factory prevede la struttura di cartelle seguente nell'archivio BLOB di Azure:     

| path                  | Description                              | Obbligatoria | Type   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (radice)            | Percorso radice del processo Spark nel servizio collegato di archiviazione | SÌ      | Cartella |
| &lt;definito dall'utente &gt; | Percorso che punta al file di ingresso del processo Spark | SÌ      | File   |
| ./jars                | Tutti i file in questa cartella vengono caricati e inseriti nel classpath java del cluster | No       | Cartella |
| ./pyFiles             | Tutti i file in questa cartella vengono caricati e inseriti nel PYTHONPATH del cluster | No       | Cartella |
| ./files               | Tutti i file in questa cartella vengono caricati e inseriti nella directory di lavoro executor | No       | Cartella |
| ./archives            | Tutti i file in questa cartella sono decompressi | No       | Cartella |
| ./logs                | Cartella contenente i log del cluster Spark. | No       | Cartella |

Di seguito è riportato un esempio per una risorsa di archiviazione che contiene due file di processo Spark nell'archivio BLOB di Azure a cui fa riferimento il servizio collegato HDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti che illustrano come trasformare i dati in altri modi: 

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di streaming di Hadoop](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Attività di esecuzione batch di Machine Learning)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
