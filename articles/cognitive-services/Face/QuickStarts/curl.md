---
title: "Avvio rapido: Rilevare i visi in un'immagine con l'API REST di Azure e cURL"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si userà l'API REST Viso di Azure con cURL per rilevare i visi in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 6a1a6d1fdce4853a2ac73f10eb4cf0a0505fa4c7
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165898"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Avvio rapido: Rilevare i visi in un'immagine con l'API REST Viso e cURL

In questa guida introduttiva si userà l'API REST Viso di Azure con cURL per rilevare i visi umani in un'immagine.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisites

- Una chiave di sottoscrizione di Viso. È possibile ottenere una chiave di sottoscrizione della versione di valutazione gratuita da [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api). In alternativa, seguire le istruzioni riportate in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per sottoscrivere il servizio Viso e ottenere la chiave.

## <a name="write-the-command"></a>Scrivere il comando
 
Si userà un comando come il seguente per chiamare l'API Viso e ottenere i dati dell'attributo viso da un'immagine. In primo luogo, copiare il codice in un editor di testo. È necessario apportare modifiche ad alcune parti prima di poterlo eseguire.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://<My Endpoint String>.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Chiave della sottoscrizione
Sostituire `<Subscription Key>` con la propria chiave di sottoscrizione Viso valida.

### <a name="face-endpoint-url"></a>URL endpoint Viso

L'URL `https://<My Endpoint String>.com/face/v1.0/detect` indica l'endpoint Viso di Azure su cui eseguire la query. Potrebbe essere necessario modificare la prima parte dell'URL in modo che equivalga all'endpoint che corrisponde alla chiave di sottoscrizione.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="url-query-string"></a>Stringa di query URL

La stringa di query dell'URL endpoint Viso specifica gli attributi del viso da recuperare. È possibile modificare questa stringa in base all'uso previsto.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>URL origine immagine
L'URL di origine indica l'immagine da usare come input. È possibile modificarlo in modo che punti a un'immagine che si vuole analizzare.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Eseguire il comando

Dopo aver apportato le modifiche, aprire un prompt dei comandi e immettere il nuovo comando. Le informazioni sul viso dovrebbero essere visualizzate come dati JSON nella finestra della console. Ad esempio:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è scritto un comando cURL che chiama il servizio Viso di Azure per rilevare i visi in un'immagine e restituirne gli attributi. Successivamente, esplorare la documentazione di riferimento dell'API Viso per altre informazioni.

> [!div class="nextstepaction"]
> [API Viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
