---
title: Elemento SizeSelector dell'interfaccia utente
description: Illustra l'elemento Microsoft.Compute.SizeSelector dell'interfaccia utente per il portale di Azure. Consente di selezionare le dimensioni di una macchina virtuale.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d98ddbb09ac4f1f933237b3288db7a0cb78380b5
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652047"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Elemento Microsoft.Compute.SizeSelector dell'interfaccia utente

Controllo per la selezione di una dimensione per una o più istanze di macchina virtuale.

## <a name="ui-sample"></a>Esempio di interfaccia utente

L'utente visualizza un selettore con i valori predefiniti dalla definizione dell'elemento.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Dopo aver selezionato il controllo, l'utente visualizza una vista estesa delle dimensioni disponibili.

![Microsoft.Compute.SizeSelector esteso](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

## <a name="schema"></a>SCHEMA

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Output di esempio

```json
"Standard_D1"
```

## <a name="remarks"></a>Osservazioni

- `recommendedSizes` deve contenere almeno una dimensione. La prima dimensione consigliata viene usata come impostazione predefinita. L'elenco delle dimensioni disponibili non è ordinato in base allo stato consigliato. L'utente può selezionare quella colonna per ordinare in base allo stato consigliato.
- Se una dimensione consigliata non è disponibile nella località selezionata, viene ignorata automaticamente e viene usata la dimensione consigliata successiva.
- `constraints.allowedSizes` e `constraints.excludedSizes` sono entrambi facoltativi, ma non possono essere usati contemporaneamente. Per determinare l'elenco delle dimensioni disponibili è possibile chiamare l'[elenco di dimensioni di macchina virtuale disponibili per una sottoscrizione](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Le dimensioni non specificate in `constraints.allowedSizes` vengono nascoste e quelle non specificate in `constraints.excludedSizes` vengono visualizzate.
- È necessario specificare `osPlatform`, che può essere **Windows** o **Linux**. Viene usato per determinare i costi hardware delle macchine virtuali.
- `imageReference` viene omesso per le immagini proprietarie e viene specificato per le immagini di terze parti. Viene usato per determinare i costi software delle macchine virtuali.
- `count` viene usato per impostare il moltiplicatore appropriato per l'elemento. Supporta un valore statico, ad esempio **2**, o un valore dinamico da un altro elemento, ad esempio `[steps('step1').vmCount]`. Il valore predefinito è **1**.
- `numAvailabilityZonesRequired` può essere 1, 2 o 3.
- L'impostazione predefinita di `hideDiskTypeFilter` è **false**. Il filtro del tipo di disco consente all'utente di visualizzare tutti i tipi di dischi o solo unità SSD.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
