---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474152"
---
Le query sul contenitore vengono fatturate secondo il piano tariffario della risorsa di Azure usata per `ApiKey`.

I contenitori di servizi cognitivi di Azure non sono concessi in licenza per l'esecuzione senza essere connessi all'endpoint di misurazione/fatturazione. È necessario consentire ai contenitori di comunicare sempre le informazioni di fatturazione all'endpoint di fatturazione. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

### <a name="connect-to-azure"></a>Connettersi ad Azure

Per eseguire il contenitore, sono necessari i valori dell'argomento di fatturazione. Questi valori consentono al contenitore di connettersi all'endpoint di fatturazione. Il contenitore segnala l'utilizzo ogni 10-15 minuti. Se il contenitore non si connette ad Azure entro la finestra temporale consentita, continuerà a essere eseguito ma non fornirà query finché l'endpoint di fatturazione non verrà ripristinato. Il tentativo di connessione viene effettuato 10 volte nello stesso intervallo di tempo di 10-15 minuti. Se non è possibile connettersi all'endpoint di fatturazione entro 10 tentativi, il contenitore smette di soddisfare le richieste.

### <a name="billing-arguments"></a>Argomenti di fatturazione

Il <a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank">comando <span class="docon docon-navigate-external x-hidden-focus"></span>`docker run`</a> avvierà il contenitore quando vengono fornite tutte e tre le opzioni seguenti con valori validi:

| Opzione | Descrizione |
|--------|-------------|
| `ApiKey` | Chiave API della risorsa di Servizi cognitivi usata per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato su una chiave API per la risorsa di cui è stato effettuato il provisioning specificata in `Billing`. |
| `Billing` | Endpoint della risorsa di Servizi cognitivi usata per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato sull'URI dell'endpoint di una risorsa di Azure di cui è stato effettuato il provisioning.|
| `Eula` | Indica che è la licenza per il contenitore è stata accettata.<br/>Il valore di questa opzione deve essere impostato su **accept**. |
