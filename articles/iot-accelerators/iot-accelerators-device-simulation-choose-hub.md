---
title: Usare l'hub Internet esistente con la soluzione simulazione dispositivi-Azure | Microsoft Docs
description: Questo articolo descrive come configurare l'acceleratore di soluzione Simulazione dispositivi per un hub IoT esistente.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889188"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Usare un hub IoT esistente con l'acceleratore della soluzione Simulazione dispositivi

Quando si distribuisce l'opzione Simulazione dispositivi, è anche possibile scegliere di distribuire un hub IoT da usare nella simulazione. Questa opzione consente di distribuire un [hub IoT di livello S2 con una singola unità di scala](../iot-hub/iot-hub-scaling.md). Se si distribuisce questo hub IoT facoltativo, è possibile scegliere come destinazione un altro hub IoT per un'esecuzione della simulazione.

Se si sceglie di non distribuire l'hub IoT facoltativo, è necessario usare il proprio hub per le simulazioni eseguite.

Se non è disponibile un hub IoT, è sempre possibile crearne uno nuovo dal [portale di Azure](https://portal.azure.com).

Per usare un hub IoT pre-esistente, è necessaria una stringa di connessione per i criteri di accesso condiviso **iothubowner**. È possibile ottenere la stringa di connessione dal [portale di Azure](https://portal.azure.com):

1. Nella pagina di configurazione dell'hub nel portale, fare clic su **Criteri di accesso condiviso**.

1. Fare clic su **iothubowner**.

1. Copiare la stringa di connessione primaria o secondaria.

[![Ottenere una stringa di connessione](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Usare la stringa di connessione copiata quando si configura la simulazione:

![Configurare una simulazione](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Passaggi successivi

In questa guida pratica si è appreso come usare un hub IoT esistente in una simulazione. Successivamente sarà possibile vedere come [creare un modello di dispositivo avanzato](iot-accelerators-device-simulation-advanced-device.md) per una simulazione.
