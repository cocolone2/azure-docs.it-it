---
title: Come trasferire un payload tra il dispositivo e il servizio Device provisioning di Azure
description: Questo documento descrive come trasferire un payload tra il dispositivo e il servizio Device provisioning (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3eec39e975b1e782eafe16205623c625f462a865
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209410"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Come trasferire un payload tra il dispositivo e il DPS
A volte DPS necessita di più dati dai dispositivi per eseguirne correttamente il provisioning nell'hub degli elementi appropriati e che i dati devono essere forniti dal dispositivo. Viceversa, DPS può restituire i dati al dispositivo per semplificare le logiche lato client. 

## <a name="when-to-use-it"></a>Quando usarli
Questa funzionalità può essere usata come miglioramento per l' [allocazione personalizzata](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Ad esempio, si desidera allocare i dispositivi in base al modello di dispositivo senza intervento umano. In questo caso, si utilizzerà l' [allocazione personalizzata](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). È possibile configurare il dispositivo in modo che segnali le informazioni del modello come parte della [chiamata del dispositivo Register](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). DPS passerà il payload del dispositivo al webhook di allocazione personalizzato. E la funzione può decidere a quale Hub di tutto il dispositivo andrà a ricevere informazioni sul modello di dispositivo. Analogamente, se il webhook vuole restituire alcuni dati al dispositivo, i dati vengono passati di nuovo come stringa nella risposta del webhook.  

## <a name="device-sends-data-payload-to-dps"></a>Il dispositivo invia il payload dei dati a DPS
Quando il dispositivo invia una [chiamata al dispositivo di registrazione](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) a DPS, la chiamata al registro può essere migliorata per prendere altri campi nel corpo. Il corpo ha un aspetto simile al seguente: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS restituisce i dati al dispositivo
Se il webhook dei criteri di allocazione personalizzati vuole restituire alcuni dati al dispositivo, i dati vengono passati come stringa nella risposta del webhook. La modifica si trova nella sezione payload riportata di seguito. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Supporto SDK
Questa funzionalità è disponibile negli SDK del C# [client](https://docs.microsoft.com/azure/iot-dps/)C,, Java e node. js.  

## <a name="next-steps"></a>Passaggi successivi
* Sviluppare usando [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) per hub IoT di Azure e il servizio Device Provisioning in hub IoT di Azure
