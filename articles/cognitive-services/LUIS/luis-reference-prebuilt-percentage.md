---
title: Percentuale di entità predefinita-LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita percentuale in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 31ea1c36139abcb1e102161ad76a203073ba4dfd
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270503"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Entità predefinita percentuale per un'app LUIS
I numeri in percentuale possono essere visualizzati come frazioni `3 1/2`, o come percentuale, `2%`. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti la percentuale per le finalità dell'applicazione. L'entità percentuale è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md).

## <a name="types-of-percentage"></a>Tipi di percentuale
La percentuale viene gestita nel repository GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Risoluzione per l'entità percentuale predefinita

Per la query vengono restituiti gli oggetti entità seguenti:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[Risposta V3](#tab/V3)

Il codice JSON seguente è con il parametro `verbose` impostato su `false`:

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Risposta dettagliata V3](#tab/V3-verbose)
Il codice JSON seguente è con il parametro `verbose` impostato su `true`:

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[Risposta V2](#tab/V2)

L'esempio seguente illustra la risoluzione dell'entità **builtin.percentage**.

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

Informazioni sulle entità [ordinali](luis-reference-prebuilt-ordinal.md), [numeri](luis-reference-prebuilt-number.md) e [temperatura](luis-reference-prebuilt-temperature.md).
