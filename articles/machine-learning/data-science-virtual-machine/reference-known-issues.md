---
title: 'Informazioni di riferimento: problemi noti & risoluzione dei problemi'
titleSuffix: Azure Data Science Virtual  Machine
description: Ottenere un elenco dei problemi noti, delle soluzioni alternative e della risoluzione dei problemi per Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206521"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problemi noti e risoluzione dei problemi relativi alla Data Science Virtual Machine di Azure

Questo articolo consente di individuare e correggere gli errori o gli errori che potrebbero verificarsi quando si usa il Data Science Virtual Machine di Azure.

## <a name="python-package-installation-issues"></a>Problemi di installazione dei pacchetti Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>L'installazione di pacchetti con pip interrompe le dipendenze in Linux

Utilizzare `sudo pip install` anziché `pip install` quando si installano i pacchetti.

## <a name="disk-encryption-issues"></a>Problemi di crittografia dischi

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>La crittografia del disco non riesce in Ubuntu DSVM

Crittografia dischi di Azure (ADE) non è attualmente supportata nella DSVM Ubuntu. Come soluzione alternativa, è consigliabile configurare [la crittografia lato server di Azure Managed disks](../../virtual-machines/windows/disk-encryption.md).

## <a name="tool-appears-disabled"></a>Lo strumento appare disabilitato

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V non funziona in Windows DSVM

Hyper-V inizialmente non funziona in Windows è il comportamento previsto. Per le prestazioni di avvio, i servizi sono stati disabilitati. Per abilitare Hyper-V:

1. Aprire la barra di ricerca in Windows DSVM
1. Digitare "Services",
1. Impostare tutti i servizi Hyper-V su "Manual"
1. Impostare "gestione macchine virtuali Hyper-V" su "automatico"

La schermata finale dovrebbe essere simile alla seguente:

   ![Abilitare Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

