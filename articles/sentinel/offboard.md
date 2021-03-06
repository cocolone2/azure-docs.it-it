---
title: Rimuovere Sentinel di Azure | Microsoft Docs
description: Come eliminare l'istanza di Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77581685"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Rimuovere Azure Sentinel dall'area di lavoro

Se non si vuole più usare Azure Sentinel, questo articolo illustra come rimuoverlo dall'area di lavoro.

## <a name="how-to-remove-azure-sentinel"></a>Come rimuovere Azure Sentinel

Seguire questa procedura per rimuovere Azure Sentinel dall'area di lavoro:

1. Passare ad **Azure Sentinel**, seguito da **Impostazioni**e selezionare la scheda **Rimuovi Azure Sentinel**.

1. Prima di rimuovere Azure Sentinel, usare le caselle di controllo per indicare il motivo per cui si sta rimuovendo.

1. Selezionare **Rimuovi Sentinel di Azure dall'area di lavoro**.
    
    ![Eliminare la soluzione SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Cosa accade dietro le quinte?

Quando si rimuove la soluzione, Azure Sentinel impiega fino a 48 ore per completare la prima fase del processo di eliminazione.

Una volta identificata la disconnessione, viene avviato il processo offboarding.

**La configurazione di questi connettori è stata rimossa:**
-   Office 365

-   AWS

-   Avvisi di sicurezza dei servizi Microsoft (Azure ATP, Microsoft Cloud App Security inclusi Cloud Discovery segnalazione Shadow IT, Azure AD Identity Protection, Microsoft Defender ATP, Centro sicurezza di Azure)

-   Intelligence per le minacce

-   Log di sicurezza comuni (inclusi i log basati su CEF, Barracuda e syslog). Se si dispone del Centro sicurezza di Azure, questi log continueranno a essere raccolti.

-   Eventi di sicurezza di Windows. Se si dispone del Centro sicurezza di Azure, questi log continueranno a essere raccolti.

Nelle prime 48 ore, i dati e le regole analitiche (inclusa la configurazione di automazione in tempo reale) non saranno più accessibili o disponibili per le query in Sentinel di Azure.

**Dopo 30 giorni, queste risorse vengono rimosse:**

-   Eventi imprevisti (inclusi i metadati di analisi)

-   Regole analitiche

-   Segnalibri

I PlayBook, le cartelle di lavoro salvate, le query di ricerca salvate e i notebook non vengono rimossi. **Alcune potrebbero interrompersi a causa dei dati rimossi. È possibile rimuoverli manualmente.**

Dopo aver rimosso il servizio, si verifica un periodo di tolleranza di 30 giorni durante il quale è possibile riabilitare la soluzione e i dati e le regole analitiche verranno ripristinati, ma i connettori configurati che erano disconnessi devono essere riconnessi.

> [!NOTE]
> Se si rimuove la soluzione, la sottoscrizione continuerà a essere registrata con il provider di risorse Sentinel di Azure. **È possibile rimuoverlo manualmente.**




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come rimuovere il servizio Sentinel di Azure. Se si cambia idea e si vuole installarlo di nuovo:
- Introduzione [all'onboarding di Azure Sentinel](quickstart-onboard.md).
