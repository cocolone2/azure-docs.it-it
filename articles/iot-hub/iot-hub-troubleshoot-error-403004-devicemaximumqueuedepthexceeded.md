---
title: Risoluzione dei problemi dell'hub Azure Internet Error 403004 DeviceMaximumQueueDepthExceeded
description: Informazioni su come correggere l'errore 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d48fd9aa9ba52c850a514d392f25b980d0219470
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960906"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

Questo articolo descrive le cause e le soluzioni per gli errori di **403004 DeviceMaximumQueueDepthExceeded** .

## <a name="symptoms"></a>Sintomi

Quando si tenta di inviare un messaggio da cloud a dispositivo, la richiesta ha esito negativo con l'errore **403004** o **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Causa

La ragione principale è che il numero di messaggi accodati per il dispositivo supera il [limite della coda (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

Il motivo più probabile per cui si sta verificando questo limite è dato dal fatto che si sta usando HTTPS per ricevere il messaggio, che conduce al polling continuo con `ReceiveAsync`, con conseguente limitazione della richiesta da parte dell'hub Internet.

## <a name="solution"></a>Soluzione

Il modello supportato per i messaggi da cloud a dispositivo con HTTPS è rappresentato da dispositivi collegati in modo intermittente che controllano raramente la presenza di messaggi (meno di ogni 25 minuti). Per ridurre la probabilità di esecuzione nel limite della coda, passare a AMQP o MQTT per i messaggi da cloud a dispositivo.

In alternativa, migliorare la logica lato dispositivo per completare, rifiutare o abbandonare i messaggi in coda in modo rapido, abbreviare la durata o prendere in considerazione l'invio di un minor numero di messaggi. Vedere [Durata dei messaggi C2D](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Infine, provare a usare l' [API della coda di ripulitura](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) per pulire periodicamente i messaggi in sospeso prima che venga raggiunto il limite.