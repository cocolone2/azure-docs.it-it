---
title: Usare Gestione aggiornamenti di Azure con client di Configuration Manager
description: Questo articolo ha lo scopo di semplificare la configurazione di Microsoft endpoint Configuration Manager con questa soluzione per distribuire gli aggiornamenti software ai client di ConfigMgr.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: f0ca836e3b53c3cce755d45b50fe168073f0bbaa
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513134"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Distribuire gli aggiornamenti ai client Microsoft endpoint Configuration Manager con Gestione aggiornamenti

I clienti che hanno investito in Microsoft endpoint Configuration Manager per gestire PC, server e dispositivi mobili si affidano anche alla sua forza e maturità nella gestione degli aggiornamenti software nell'ambito del ciclo di gestione degli aggiornamenti software (SUM).

È possibile creare report e aggiornare i server Windows gestiti tramite la creazione e il pre-staging di distribuzioni di aggiornamenti software in Configuration Manager, nonché ottenere lo stato dettagliato delle distribuzioni di aggiornamenti completate usando la [soluzione Gestione aggiornamenti](automation-update-management.md). Se si usa Configuration Manager per la creazione di report di conformità degli aggiornamenti, ma non per la gestione delle distribuzioni di aggiornamenti con i server Windows, è possibile continuare a creare report per Configuration Manager mentre gli aggiornamenti della sicurezza vengono gestiti con la soluzione Gestione aggiornamenti.

## <a name="prerequisites"></a>Prerequisiti

* È necessario aver aggiunto la [soluzione Gestione aggiornamenti](automation-update-management.md) al proprio account di Automazione.
* I server Windows attualmente gestiti dall'ambiente di Configuration Manager devono anche segnalare all'area di lavoro Log Analytics in cui è abilitata anche la soluzione Gestione aggiornamenti.
* Questa funzionalità è abilitata in Configuration Manager Current Branch versione 1606 e successive. Per integrare il Configuration Manager sito di amministrazione centrale o un sito primario autonomo con i log di monitoraggio di Azure e le raccolte di importazione, vedere [connettere Configuration Manager ai log di monitoraggio di Azure](../azure-monitor/platform/collect-sccm.md).  
* Gli agenti Windows devono essere configurati per comunicare con un server Windows Server Update Services (WSUS) o avere accesso a Microsoft Update, se non ricevono gli aggiornamenti della sicurezza da Configuration Manager.   

Le modalità di gestione dei client ospitati nella soluzione IaaS di Azure con l'ambiente Configuration Manager esistente dipendono principalmente dalla connessione tra i data center di Azure e l'infrastruttura. Questa connessione influisce su eventuali modifiche di progettazione che potrebbe essere necessario apportare all'infrastruttura di Configuration Manager e sui costi correlati per supportare le modifiche necessarie. Per comprendere le considerazioni sulla pianificazione da valutare prima di procedere, vedere [Configuration Manager in Azure: domande frequenti](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Configurazione

### <a name="manage-software-updates-from-configuration-manager"></a>Gestire gli aggiornamenti software da Configuration Manager 

Se si vuole continuare a gestire le distribuzioni di aggiornamenti da Configuration Manager, eseguire la procedura illustrata di seguito. Automazione di Azure si connette a Configuration Manager per applicare gli aggiornamenti ai computer client connessi all'area di lavoro Log Analytics. Il contenuto degli aggiornamenti è disponibile dalla cache del computer client, come se la distribuzione fosse stata gestita da Configuration Manager.

1. Creare una distribuzione degli aggiornamenti software dal sito di livello superiore nella gerarchia di Configuration Manager usando la procedura descritta in [distribuire gli aggiornamenti software](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). L'unica impostazione che deve essere configurata in modo diverso rispetto a una distribuzione standard è l'opzione **Non installare aggiornamenti software** per controllare il comportamento di download del pacchetto di distribuzione. Questo comportamento viene gestito dalla soluzione Gestione aggiornamenti creando una distribuzione di aggiornamenti pianificata nel passaggio successivo.

1. Selezionare **Gestione aggiornamenti** in Automazione di Azure. Creare una nuova distribuzione seguendo i passaggi descritti nella sezione relativa alla [creazione di una distribuzione degli aggiornamenti](automation-tutorial-update-management.md#schedule-an-update-deployment) e scegliere **Gruppi importati** nell'elenco a discesa **Tipo** per selezionare la raccolta appropriata di Configuration Manager. Tenere presente questi punti importanti: a. Se è definita una finestra di manutenzione per la raccolta di dispositivi di Configuration Manager selezionata, i membri della raccolta rispettano questa finestra invece dell'impostazione **Durata** definita nella distribuzione pianificata.
    b. I membri della raccolta di destinazione devono avere una connessione Internet diretta, tramite un server proxy o il gateway di Log Analytics.

Dopo aver completato la distribuzione di aggiornamenti tramite Automazione di Azure, i computer di destinazione che sono membri del gruppo di computer selezionato installeranno gli aggiornamenti all'ora pianificata dalla cache client locale. È possibile [visualizzare lo stato della distribuzione di aggiornamenti](automation-tutorial-update-management.md#view-results-of-an-update-deployment) per monitorare i risultati della distribuzione.

### <a name="manage-software-updates-from-azure-automation"></a>Gestire gli aggiornamenti software da Automazione di Azure

Per gestire gli aggiornamenti per macchine virtuali Windows Server che sono client di Configuration Manager, è necessario configurare i criteri client per disabilitare la funzionalità Gestione aggiornamenti software per tutti i client gestiti da questa soluzione. Per impostazione predefinita, le impostazioni client si applicano a tutti i dispositivi nella gerarchia. Per ulteriori informazioni su questa impostazione dei criteri e su come configurarla, vedere [come configurare le impostazioni client in Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

Dopo avere apportato questa modifica alla configurazione, creare una nuova distribuzione seguendo i passaggi descritti nella sezione relativa alla [creazione di una distribuzione degli aggiornamenti](automation-tutorial-update-management.md#schedule-an-update-deployment) e scegliere **Gruppi importati** nell'elenco a discesa **Tipo** per selezionare la raccolta appropriata di Configuration Manager.

## <a name="next-steps"></a>Passaggi successivi

