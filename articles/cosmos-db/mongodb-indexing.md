---
title: Indicizzazione nell'API di Azure Cosmos DB per MongoDB
description: Presenta una panoramica delle funzionalità di indicizzazione nell'API di Azure Cosmos DB per MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: c8879884cf3d882e6a6b441244ed139072bedeeb
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029470"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indicizzazione con l'API di Azure Cosmos DB per MongoDB

L'API di Azure Cosmos DB per MongoDB sfrutta le funzionalità di gestione automatica degli indici di Cosmos DB. Di conseguenza, gli utenti hanno accesso ai criteri di indicizzazione predefiniti di Cosmos DB. Se l'utente non ha definito indici o non sono stati eliminati indici, tutti i campi verranno automaticamente indicizzati per impostazione predefinita quando vengono inseriti in una raccolta. Per la maggior parte degli scenari è consigliabile usare i criteri di indicizzazione predefiniti impostati per l'account.

## <a name="indexing-for-version-36"></a>Indicizzazione per la versione 3,6

Gli account che dispongono del protocollo wire versione 3,6 forniscono criteri di indicizzazione predefiniti diversi rispetto ai criteri forniti dalle versioni precedenti. Per impostazione predefinita, solo il campo _id è indicizzato. Per indicizzare campi aggiuntivi, l'utente deve applicare i comandi di gestione degli indici MongoDB. Per applicare un ordinamento a una query, attualmente è necessario creare un indice sui campi utilizzati nell'operazione di ordinamento.

### <a name="dropping-the-default-indexes-36"></a>Eliminazione degli indici predefiniti (3,6)

Per gli account che dispongono del protocollo wire versione 3,6, l'unico indice predefinito è _id, che non può essere eliminato.

### <a name="creating-a-compound-index-36"></a>Creazione di un indice composto (3,6)

Gli indici composti reali sono supportati per gli account che usano il protocollo Wire 3,6. Con il comando seguente viene creato un indice composto sui campi "a" e "b": `db.coll.createIndex({a:1,b:1})`

Gli indici composti possono essere usati per eseguire l'ordinamento in modo efficiente su più campi contemporaneamente, ad esempio: `db.coll.find().sort({a:1,b:1})`

### <a name="track-the-index-progress"></a>Tenere traccia dello stato di avanzamento dell'indice

La versione 3,6 dell'API Azure Cosmos DB per gli account MongoDB supporta il comando `currentOp()` per tenere traccia dello stato di avanzamento dell'indice in un'istanza del database. Questo comando restituisce un documento che contiene informazioni sulle operazioni in corso su un'istanza del database. Il comando `currentOp` viene usato per tenere traccia di tutte le operazioni in corso in MongoDB nativo, mentre nell'API Azure Cosmos DB per MongoDB, questo comando supporta solo il rilevamento dell'operazione sull'indice.

Di seguito sono riportati alcuni esempi che illustrano come usare il comando `currentOp` per tenere traccia dello stato di avanzamento dell'indice:

• Ottenere lo stato di avanzamento dell'indice per una raccolta:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

• Ottenere lo stato di avanzamento dell'indice per tutte le raccolte in un database:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

• Ottenere lo stato di avanzamento dell'indice per tutti i database e le raccolte in un account Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

I dettagli sullo stato dell'indice contengono la percentuale di avanzamento per l'operazione sull'indice corrente. Nell'esempio seguente viene illustrato il formato del documento di output per diverse fasi di avanzamento dell'indice:

1. Se l'operazione sull'indice in una raccolta ' foo ' e nel database ' barra ' con l'indicizzazione del 60% è stata completata, avrà il seguente documento di output. `Inprog[0].progress.total` Visualizza 100 come completamento della destinazione.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

2. Per un'operazione sugli indici appena iniziata in una raccolta ' foo ' e in un database ' bar ', il documento di output può mostrare lo stato dello 0% fino a raggiungere un livello misurabile.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

3. Quando l'operazione sull'indice in corso viene completata, il documento di output Mostra le operazioni inprog vuote.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="indexing-for-version-32"></a>Indicizzazione per la versione 3,2

### <a name="dropping-the-default-indexes-32"></a>Eliminazione degli indici predefiniti (3,2)

Il comando seguente consente di eliminare gli indici predefiniti per una raccolta ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>Creazione di un indice composto (3,2)

Gli indici composti contengono riferimenti a più campi di un documento. Dal punto di vista logico equivalgono alla creazione di più indici per ogni campo. Per sfruttare le ottimizzazioni fornite dalle tecniche di indicizzazione di Cosmos DB, è consigliabile creare più indici anziché un singolo indice composto non univoco.

## <a name="common-indexing-operations"></a>Operazioni di indicizzazione comuni

Le operazioni seguenti sono comuni sia per gli account che dispongono di protocollo wire versione 3,6 che per gli account che eseguono versioni precedenti del protocollo wire. 

## <a name="creating-unique-indexes"></a>Creazione di indici univoci

Gli [indici univoci](unique-keys.md) sono utili per impedire che due o più documenti contengano lo stesso valore per i campi indicizzati.

>[!Important]
> Attualmente, gli indici univoci possono essere creati solo quando la raccolta è vuota, ovvero non contiene alcun documento.

Il comando seguente consente di creare un indice univoco sul campo "student_id":

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Per le raccolte partizionate, in base al comportamento di MongoDB è necessario fornire la chiave di partizione per creare un indice univoco. In altre parole, tutti gli indici univoci in una raccolta partizionata sono indici composti in cui uno dei campi è la chiave di partizione.

I comandi seguenti creano una raccolta partizionata ```coll``` (la chiave di partizione è ```university```) con un indice univoco sui campi student_id e university:

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

Nell'esempio precedente, se la clausola ```"university":1``` venisse omessa verrebbe restituito un errore con il messaggio seguente:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>Indici TTL

Per abilitare la scadenza dei documenti in una determinata raccolta, è necessario creare un ["indice TTL" (time-to-live)](../cosmos-db/time-to-live.md). Un indice TTL è un indice sul campo _ts con un valore "expireAfterSeconds".

Esempio:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Il comando precedente causerà l'eliminazione di tutti i documenti della raccolta ```db.coll``` che non sono stati modificati negli ultimi 10 secondi.

> [!NOTE]
> **_ts** è un campo specifico di Cosmos DB e non è accessibile dai client di MongoDB. Si tratta di una proprietà di sistema riservata che contiene il timestamp dell'ultima modifica del documento.

## <a name="migrating-collections-with-indexes"></a>Migrazione di raccolte con indici

Attualmente, gli indici univoci possono essere creati solo quando la raccolta non contiene alcun documento. Gli strumenti di migrazione più diffusi di MongoDB provano a creare gli indici univoci dopo l'importazione dei dati. Per aggirare questo problema, si consiglia agli utenti di creare manualmente le raccolte e gli indici univoci corrispondenti, anziché consentire lo strumento di migrazione (per ```mongorestore``` questo comportamento viene eseguito utilizzando il flag `--noIndexRestore` nella riga di comando).

## <a name="next-steps"></a>Passaggi successivi

* [Indicizzazione in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Impostare la scadenza automatica dei dati in Azure Cosmos DB con la durata (TTL)](../cosmos-db/time-to-live.md)
