---
title: Configurare WinRM dopo la creazione della macchina virtuale di Azure | Azure Marketplace
description: Illustra come configurare Gestione remota Windows (WinRM) dopo la creazione di una macchina virtuale ospitata in Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pabutler
ms.openlocfilehash: 7d050b32b212f66623a24bcf87d40111fc5973a5
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77481375"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Configurare WinRM dopo la creazione della macchina virtuale

Questo articolo illustra come configurare una macchina virtuale (VM) esistente ospitata in Azure per abilitare WinRM tramite HTTPS.  Questa configurazione si applica solo alle macchine virtuali basate su Windows e richiede il processo in due passaggi riportato di seguito:

1. Abilitare il traffico di porta per WinRM tramite il protocollo HTTPS.  Questa impostazione per la macchina virtuale verrà configurata nel portale di Azure.
2. Configurare la macchina virtuale per abilitare WinRM eseguendo gli script di PowerShell forniti.


## <a name="enabling-port-traffic"></a>Abilitazione del traffico di porta

Il protocollo WinRM over HTTPS utilizza la porta 5986, che non è abilitata per impostazione predefinita nelle VM Windows preconfigurate offerte in Azure Marketplace. Per abilitare questo protocollo, usare la procedura seguente per aggiungere una nuova regola al gruppo di sicurezza di rete (NSG) con il [portale di Azure](https://portal.azure.com).  Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Passare al pannello **Macchine virtuali >**   <*nome-vm*>   **> Impostazioni/Rete**.
2.  Fare clic sul nome del gruppo sicurezza di rete (in questo esempio **testvm11002**) per visualizzare le relative proprietà:

    ![Proprietà del gruppo di sicurezza di rete](./media/nsg-properties.png)
 
3. In **Impostazioni** selezionare **Regole di sicurezza in ingresso** per visualizzare questo pannello.
4. Fare clic su **+ Aggiungi** per creare una nuova regola denominata `WinRM_HTTPS` per la porta TCP 5986.

    ![Aggiungere una regola di sicurezza di rete in ingresso](./media/nsg-new-rule.png)

5. Fare clic su **OK** dopo aver terminato di specificare i valori.  L'elenco delle regole di sicurezza in ingresso dovrebbe contenere le nuove voci seguenti.

    ![Elenco delle regole di sicurezza di rete in ingresso](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Configurare la macchina virtuale per abilitare WinRM 

Usare la procedura seguente per abilitare e configurare la funzionalità Gestione remota Windows nella macchina virtuale Windows.   

1. Stabilire una connessione Desktop remoto alla macchina virtuale ospitata in Azure.  Per altre informazioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  I passaggi rimanenti verranno eseguiti nella macchina virtuale.
2. Scaricare i file seguenti e salvarli in una cartella nella macchina virtuale:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Aprire la **Console di PowerShell** con privilegi elevati (**Esegui come amministratore**). 
4. Eseguire il comando seguente, specificando il parametro obbligatorio: nome di dominio completo (FQDN) per la macchina virtuale: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Script di configurazione di PowerShell 1](./media/powershell-file1.png)

    Questo script dipende dagli altri due file nella stessa cartella.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato WinRM, si è pronti per [distribuire la macchina virtuale dai dischi rigidi virtuali che la costituiscono](./cpp-deploy-vm-vhd.md).
