---
title: Panoramica sulla trasformazione del flusso di dati
description: Panoramica delle diverse trasformazioni disponibili per il mapping del flusso di dati
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086932"
---
# <a name="mapping-data-flow-transformation-overview"></a>Panoramica sulla trasformazione del flusso di dati

Di seguito è riportato un elenco delle trasformazioni attualmente supportate nel flusso di dati di mapping. Fare clic su ogni trasformazione per apprendere i dettagli di configurazione.

| Nome | Category | Descrizione |
| ---- | -------- | ----------- |
| [Aggregata](data-flow-aggregate.md) | Modificatore schema | Definire tipi diversi di aggregazione, ad esempio SUM, MIN, MAX e COUNT raggruppati in base a colonne esistenti o calcolate. | 
| [Modifica riga](data-flow-alter-row.md) | Modificatore di riga | Impostare i criteri INSERT, DELETE, Update e Upsert sulle righe. |
| [Suddivisione condizionale](data-flow-conditional-split.md) | Input/output multipli | Indirizzare righe di dati a flussi diversi in base alle condizioni di corrispondenza. |
| [Colonna derivata](data-flow-derived-column.md) | Modificatore schema | generare nuove colonne o modificare i campi esistenti utilizzando il linguaggio delle espressioni del flusso di dati. | 
| [Exists](data-flow-exists.md) | Input/output multipli | Controllare se i dati esistono in un'altra origine o in un altro flusso. | 
| [Filter](data-flow-filter.md) | Modificatore di riga | Filtrare una riga in base a una condizione. |
| [Rendere flat](data-flow-flatten.md) | Modificatore schema |  Accetta valori di matrice all'interno di strutture gerarchiche, ad esempio JSON, e ne esegue il rollback in singole righe. |
| [Join](data-flow-join.md) | Input/output multipli |  Combinare dati da due origini o flussi. |
| [Ricerca](data-flow-lookup.md) | Input/output multipli | Dati di riferimento da un'altra origine. |
| [Nuovo ramo](data-flow-new-branch.md) | Input/output multipli | Applicare più set di operazioni e trasformazioni a fronte dello stesso flusso di dati. |
| [Pivot](data-flow-pivot.md) | Modificatore schema | Aggregazione in cui una o più colonne di raggruppamento presentano valori di riga distinti trasformati in singole colonne. |
| [Select](data-flow-select.md) | Modificatore schema | Colonne alias e nomi di flusso, colonne drop o riordina |
| [Sink](data-flow-sink.md) | - | Una destinazione finale per i dati |
| [Sort](data-flow-sort.md) | Modificatore di riga | Ordina righe in ingresso nel flusso di dati corrente |
| [Origine](data-flow-source.md) | - | Origine dati per il flusso di dati |
| [Chiave surrogata](data-flow-surrogate-key.md) | Modificatore schema | Aggiungere un valore di chiave arbitraria non business incrementale |
| [Union](data-flow-union.md) | Input/output multipli | Combinare più flussi di dati verticalmente |
| [UnPivot](data-flow-unpivot.md) | Modificatore schema | Colonne pivot in valori di riga |
| [Finestra](data-flow-window.md) | Modificatore schema |  Definire le aggregazioni basate su finestra di colonne nei flussi di dati. |
