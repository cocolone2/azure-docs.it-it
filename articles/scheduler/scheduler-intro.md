---
title: Che cos'è l'Utilità di pianificazione di Azure?
description: Creare pianificazioni ed eseguire processi automatizzati che chiamano servizi all'interno o all'esterno di Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e851da0013cf3a9ff6bb1a0fc1c073b5b796c54d
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898544"
---
# <a name="what-is-azure-scheduler"></a>Che cos'è l'Utilità di pianificazione di Azure?

> [!IMPORTANT]
> [App](../logic-apps/logic-apps-overview.md) per la logica di Azure sostituisce l'utilità di pianificazione di Azure, che sta per [essere ritirata](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Per continuare a usare i processi configurati nell'utilità di pianificazione, [eseguire la migrazione alle app per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) il prima possibile. 
>
> L'utilità di pianificazione non è più disponibile nella portale di Azure, ma i cmdlet di [PowerShell](scheduler-powershell-reference.md) per l' [API REST](/rest/api/scheduler) e l'utilità di pianificazione di Azure restano disponibili in questo momento, in modo da poter gestire processi e raccolte di processi.

L'[Utilità di pianificazione di Azure](https://azure.microsoft.com/services/scheduler/) consente di creare [processi](../scheduler/scheduler-concepts-terms.md) eseguiti nel cloud descrivendo le azioni in modo dichiarativo. Quindi, il servizio pianifica ed esegue tali azioni automaticamente. È possibile, ad esempio, chiamare servizi quali endpoint HTTP o HTTPS all'interno e all'esterno di Azure, e inserire messaggi nelle code di Archiviazione di Azure e nelle code o negli argomenti del bus di servizio di Azure. È possibile eseguire i processi immediatamente o in un secondo momento. L’Utilità di pianificazione supporta con facilità [pianificazioni complesse e operazioni ricorrenti avanzate](../scheduler/scheduler-advanced-complexity.md). L'Utilità di pianificazione specifica quando eseguire i processi, mantiene una cronologia dei risultati dei processi che è possibile esaminare e pianifica quindi l'esecuzione dei carichi di lavoro in modo prevedibile e affidabile.

Altre funzionalità di pianificazione di Azure utilizzano l'Utilità di pianificazione in background, come ad esempio [Processi Web di Azure](../app-service/webjobs-create.md), una funzionalità [app Web](https://azure.microsoft.com/services/app-service/web/) nel Servizio app di Azure. È possibile gestire la comunicazione per queste azioni usando l' [API REST dell'utilità di pianificazione](https://docs.microsoft.com/rest/api/scheduler/), che consente di gestire la comunicazione per queste azioni.

Ecco alcuni scenari in cui l'Utilità di pianificazione consente di:

* Eseguire azioni ricorrenti dell'app: ad esempio, raccogliere periodicamente i dati da Twitter in un feed.

* Eseguire interventi di manutenzione giornalieri, ad esempio l'eliminazione giornaliera dei log, l'esecuzione di backup e altre attività di manutenzione.

  Ad esempio, come amministratore, è possibile eseguire il backup del database ogni giorno all'1:00 per i nove mesi successivi.

Sebbene sia possibile usare l'Utilità di pianificazione per creare, gestire ed eseguire carichi di lavoro pianificati, l'utilità di pianificazione non ospita i carichi di lavoro né esegue il codice. Il servizio si limita a *richiamare* i servizi o il codice ospitato altrove, ad esempio in Azure, in locale o presso un altro provider. L'Utilità di pianificazione può richiamare tramite HTTP, HTTPS, una coda di archiviazione, una coda o un argomento del bus di servizio.

Per creare, pianificare, gestire, aggiornare o eliminare processi e [raccolte](../scheduler/scheduler-concepts-terms.md)di processi, è possibile usare il codice, l' [API REST dell'utilità di pianificazione](https://docs.microsoft.com/rest/api/scheduler/)o i cmdlet di PowerShell per l'utilità di pianificazione di [Azure](scheduler-powershell-reference.md).

## <a name="next-steps"></a>Passaggi successivi

* [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)
* [Piani e fatturazione per l'utilità di pianificazione di Azure](scheduler-plans-billing.md)
* [Creare pianificazioni complesse e ricorrenze avanzate con l'utilità di pianificazione di Azure](scheduler-advanced-complexity.md)
* [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](/rest/api/scheduler)
* [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)
