---
title: Visualizza le valutazioni degli aggiornamenti di Azure Gestione aggiornamenti
description: Questo articolo descrive come visualizzare le valutazioni degli aggiornamenti per le distribuzioni degli aggiornamenti.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 58d3cf6261456c09195ad6dafaeb781b55d9e5ee
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278415"
---
# <a name="view-azure-update-management-update-assessments"></a>Visualizza le valutazioni degli aggiornamenti di Azure Gestione aggiornamenti

Nell'account di automazione di Azure selezionare **Gestione aggiornamenti** per visualizzare lo stato dei computer.

Questa visualizzazione contiene informazioni sui computer, sugli aggiornamenti mancanti, sulle distribuzioni degli aggiornamenti e sulle distribuzioni degli aggiornamenti pianificate. Nella colonna **conformità** è possibile visualizzare l'ultima volta in cui il computer è stato valutato. Nella colonna **Aggiorna conformità agente** è possibile visualizzare lo stato dell'agente di aggiornamento. Se si verifica un problema, selezionare il collegamento per passare alla documentazione per la risoluzione dei problemi che consente di risolvere il problema.

Per eseguire una ricerca log che restituisce informazioni sul computer, l'aggiornamento o la distribuzione, selezionare l'elemento corrispondente nell'elenco. Si apre il riquadro **Ricerca log** con una query per l'elemento selezionato:

![Visualizzazione predefinita di Gestione aggiornamenti](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Visualizzare gli aggiornamenti mancanti

Selezionare **Aggiornamenti mancanti** per visualizzare l'elenco di aggiornamenti mancanti nei computer. Ogni aggiornamento viene inserito nell'elenco e può essere selezionato. Vengono visualizzate tutte le informazioni sul numero di computer che richiedono l'aggiornamento, i dettagli del sistema operativo e un collegamento per ulteriori informazioni. Il riquadro **Ricerca log** Mostra anche altri dettagli sugli aggiornamenti.

![Aggiornamenti mancanti](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Classificazioni degli aggiornamenti

Nelle tabelle seguenti sono elencate le classificazioni di aggiornamento supportate in Gestione aggiornamenti, con una definizione per ogni classificazione.

### <a name="windows"></a>Windows

|Classificazione  |Descrizione  |
|---------|---------|
|Aggiornamenti critici     | Un aggiornamento per un problema specifico che risolve un bug critico non correlato alla sicurezza.        |
|Aggiornamenti per la sicurezza     | Un aggiornamento per un problema specifico del prodotto correlato alla sicurezza.        |
|Aggiornamenti cumulativi     | Un set cumulativo di aggiornamenti rapidi, contenuti nello stesso pacchetto per facilitarne la distribuzione.        |
|Feature Pack     | Nuove funzionalità del prodotto distribuite di fuori di una versione del prodotto.        |
|Service Pack     | Un set cumulativo di aggiornamenti rapidi applicati a un'applicazione.        |
|Aggiornamenti della definizione     | Un aggiornamento per un virus o altri file di definizione.        |
|Strumenti     | Utilità o funzionalità che consente di completare una o più attività.        |
|Aggiornamenti     | Un aggiornamento di un'applicazione o un file attualmente installati.        |

### <a name="linux-2"></a>Linux

|Classificazione  |Descrizione  |
|---------|---------|
|Aggiornamenti critici e della sicurezza     | Aggiornamenti per un problema specifico o specifico del prodotto, correlato alla sicurezza.         |
|Altri aggiornamenti     | Tutti gli altri aggiornamenti che non sono critici per natura o che non sono aggiornamenti della sicurezza.        |

Per Linux, Gestione aggiornamenti possibile distinguere tra gli aggiornamenti critici e gli aggiornamenti della sicurezza nel cloud durante la visualizzazione dei dati di valutazione. Questa granularità è possibile grazie all'arricchimento dei dati nel cloud. Per l'applicazione di patch, Gestione aggiornamenti si basa sui dati di classificazione disponibili nel computer. A differenza di altre distribuzioni, CentOS non ha queste informazioni disponibili nelle versioni RTM del prodotto. Se i computer CentOS sono configurati per restituire i dati di sicurezza per il comando seguente, Gestione aggiornamenti possibile applicare una patch in base alle classificazioni:

```bash
sudo yum -q --security check-update
```

Attualmente non è disponibile alcun metodo supportato per abilitare la classificazione nativa: disponibilità dei dati in CentOS. A questo punto, è disponibile solo il supporto per i clienti che hanno abilitato questa funzionalità autonomamente.

Per classificare gli aggiornamenti in Red Hat Enterprise versione 6, è necessario installare il plug-in yum-Security. In Red Hat Enterprise Linux 7 il plug-in fa già parte di yum, non è necessario installare nulla. Per ulteriori informazioni, vedere l' [articolo della Knowledge](https://access.redhat.com/solutions/10021)base di Red Hat.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver visualizzato eventuali valutazioni degli aggiornamenti, è possibile pianificare una distribuzione degli aggiornamenti seguendo i passaggi descritti in [gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
