---
title: Domande frequenti sulle Azure NetApp Files | Microsoft Docs
description: Risposte alle domande più frequenti su Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: b-juche
ms.openlocfilehash: 0713f59889962960b4f3ad5eba58ddb7b32e95ff
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79369744"
---
# <a name="faqs-about-azure-netapp-files"></a>Domande frequenti sulla Azure NetApp Files

Questo articolo risponde alle domande frequenti su Azure NetApp Files. 

## <a name="networking-faqs"></a>Domande frequenti sulla rete

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Il percorso dati NFS passa attraverso Internet?  

No. Il percorso dati NFS non passa attraverso Internet. Azure NetApp Files è un servizio nativo di Azure distribuito nella rete virtuale di Azure (VNet) in cui è disponibile il servizio. Azure NetApp Files usa una subnet delegata ed effettua il provisioning di un'interfaccia di rete direttamente nel VNet. 

Per informazioni dettagliate, vedere [linee guida per la pianificazione della rete Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) .  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>È possibile connettere un VNet già creato al servizio Azure NetApp Files?

Sì, è possibile connettere reti virtuali creato al servizio. 

Per informazioni dettagliate, vedere [linee guida per la pianificazione della rete Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) .  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>È possibile montare un volume NFS di Azure NetApp Files usando il nome FQDN DNS?

Sì, è possibile, se si creano le voci DNS obbligatorie. Azure NetApp Files fornisce l'IP del servizio per il volume di cui è stato effettuato il provisioning. 

> [!NOTE] 
> Azure NetApp Files possibile distribuire indirizzi IP aggiuntivi per il servizio in base alle esigenze.  Potrebbe essere necessario aggiornare periodicamente le voci DNS.

## <a name="security-faqs"></a>Domande frequenti sulla sicurezza

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>È possibile crittografare il traffico di rete tra la macchina virtuale di Azure e l'archiviazione?

Il traffico dati (traffico dal client NFSv3, NFSv 4.1 o SMBv3 a volumi Azure NetApp Files) non è crittografato. Tuttavia, il traffico da una macchina virtuale di Azure (che esegue un client NFS o SMB) a Azure NetApp Files è sicuro come qualsiasi altro traffico da macchina virtuale a macchina virtuale di Azure. Questo traffico è locale per la rete del Data Center di Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Lo spazio di archiviazione può essere crittografato a riposo?

Tutti i volumi Azure NetApp Files vengono crittografati con lo standard FIPS 140-2. Tutte le chiavi vengono gestite dal servizio Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Modalità di gestione delle chiavi di crittografia 

La gestione delle chiavi per Azure NetApp Files viene gestita dal servizio. Viene generata una chiave di crittografia dei dati XTS-AES-256 univoca per ogni volume. Una gerarchia della chiave di crittografia viene utilizzata per crittografare e proteggere tutte le chiavi del volume. Queste chiavi di crittografia non vengono mai visualizzate o segnalate in un formato non crittografato. Le chiavi di crittografia vengono eliminate immediatamente quando viene eliminato un volume.

Il supporto per le chiavi gestite dall'utente (Bring your own key) con il modulo di protezione hardware dedicato di Azure è disponibile in base a una regola controllata nelle aree Stati Uniti orientali, Stati Uniti West2 e Stati Uniti centro-meridionali.  Il cliente può richiedere l'accesso a anffeedback@microsoft.come, con la capacità disponibile, le richieste verranno approvate.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>È possibile configurare le regole dei criteri di esportazione NFS per controllare l'accesso alla destinazione di montaggio del servizio Azure NetApp Files?


Sì, è possibile configurare fino a cinque regole in un singolo criterio di esportazione NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp Files supporta i gruppi di sicurezza di rete?

No, attualmente non è possibile applicare gruppi di sicurezza di rete alla subnet delegata di Azure NetApp Files o alle interfacce di rete create dal servizio.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>È possibile usare Azure IAM con Azure NetApp Files?

Sì, Azure NetApp Files supporta le funzionalità RBAC con Azure IAM.

## <a name="performance-faqs"></a>Domande frequenti sulle prestazioni

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Cosa devo fare per ottimizzare o ottimizzare le prestazioni di Azure NetApp Files?

È possibile eseguire le azioni seguenti in base ai requisiti di prestazioni: 
- Verificare che la macchina virtuale sia dimensionata in modo appropriato.
- Abilitare la rete accelerata per la macchina virtuale.
- Selezionare il livello di servizio e le dimensioni desiderati per il pool di capacità.
- Creare un volume con le dimensioni di quota desiderate per la capacità e le prestazioni.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Ricerca per categorie convertire i livelli di servizio basati sulla velocità effettiva di Azure NetApp Files a IOPS?

È possibile convertire MB/s in IOPS usando la formula seguente:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Ricerca per categorie modificare il livello di servizio di un volume?

La modifica del livello di servizio di un volume non è attualmente supportata.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Ricerca per categorie monitorare le prestazioni di Azure NetApp Files?

Azure NetApp Files fornisce le metriche delle prestazioni del volume. È anche possibile usare monitoraggio di Azure per monitorare le metriche di utilizzo per Azure NetApp Files.  Per un elenco delle metriche delle prestazioni per Azure NetApp Files, vedere [metriche per Azure NetApp files](azure-netapp-files-metrics.md) .

## <a name="nfs-faqs"></a>Domande frequenti su NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Si vuole che un volume venga montato automaticamente quando una macchina virtuale di Azure viene avviata o riavviata.  Ricerca per categorie configurare l'host per i volumi NFS permanenti?

Per il montaggio automatico di un volume NFS all'avvio o al riavvio della macchina virtuale, aggiungere una voce al file di `/etc/fstab` nell'host. 

Per informazioni dettagliate, vedere [montare o smontare un volume per le macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) .  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Perché il comando DF sul client NFS non Mostra le dimensioni del volume di cui è stato effettuato il provisioning?

Le dimensioni del volume segnalate in DF corrispondono alla dimensione massima consentita per la crescita del volume Azure NetApp Files. La dimensione del volume Azure NetApp Files nel comando DF non riflette la quota o le dimensioni del volume.  È possibile ottenere la dimensione o la quota del volume Azure NetApp Files tramite la portale di Azure o l'API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Quale versione di NFS supporta Azure NetApp Files?

Azure NetApp Files supporta NFSv3 e NFSv 4.1. È possibile creare un volume utilizzando una delle due versioni di NFS. 

> [!IMPORTANT] 
> L'accesso alla funzionalità NFSv4.1 richiede l'inserimento nell'elenco di elementi consentiti.  Per ottenerlo, inviare una richiesta a <anffeedback@microsoft.com>. 


### <a name="how-do-i-enable-root-squashing"></a>Ricerca per categorie abilitare la squash radice?

La squash radice non è attualmente supportata.

## <a name="smb-faqs"></a>Domande frequenti su SMB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>È necessaria una connessione Active Directory per l'accesso SMB? 

Sì, è necessario creare una connessione Active Directory prima di distribuire un volume SMB. I controller di dominio specificati devono essere accessibili dalla subnet delegata di Azure NetApp Files per una connessione corretta.  Per informazioni dettagliate, vedere [creare un volume SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) . 

### <a name="how-many-active-directory-connections-are-supported"></a>Quante connessioni di Active Directory sono supportate?

Azure NetApp Files non supporta più connessioni di Active Directory (AD) in una singola *area*, anche se le connessioni ad si trovano in account NetApp diversi. Tuttavia, è possibile disporre di più connessioni AD in un'unica *sottoscrizione*, purché le connessioni ad si trovino in aree diverse. Se sono necessarie più connessioni AD in una singola area, è possibile usare sottoscrizioni separate. 

Per ogni account NetApp è configurata una connessione AD. la connessione AD è visibile solo tramite l'account NetApp in cui è stata creata.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp Files supporta Azure Active Directory? 

Sono supportati sia [servizi di dominio Azure Active Directory (ad)](https://docs.microsoft.com/azure/active-directory-domain-services/overview) che [Active Directory Domain Services (ad DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) . Con Azure NetApp Files è possibile usare controller di dominio Active Directory esistenti. I controller di dominio possono risiedere in Azure come macchine virtuali o in locale tramite la VPN ExpressRoute o S2S. Azure NetApp Files non supporta il join AD per [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) in questo momento.

Se si usa Azure NetApp Files con Azure Active Directory Domain Services, il percorso dell'unità organizzativa viene `OU=AADDC Computers` quando si configura Active Directory per l'account NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Quali versioni di Windows Server Active Directory sono supportate?

Azure NetApp Files supporta le versioni di Active Directory Domain Services di Windows Server 2008r2SP1-2019.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Perché lo spazio disponibile nel client SMB non Mostra le dimensioni di cui è stato effettuato il provisioning?

Le dimensioni del volume segnalate dal client SMB sono le dimensioni massime consentite per l'aumento del volume Azure NetApp Files. Le dimensioni del volume Azure NetApp Files come illustrato nel client SMB non riflettono la quota o le dimensioni del volume. È possibile ottenere la dimensione o la quota del volume Azure NetApp Files tramite la portale di Azure o l'API.
<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Domande frequenti sulla gestione della capacità

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Ricerca per categorie monitorare l'utilizzo per il pool di capacità e il volume di Azure NetApp Files? 

Azure NetApp Files fornisce le metriche relative al pool di capacità e all'utilizzo del volume. È anche possibile usare monitoraggio di Azure per monitorare l'utilizzo per Azure NetApp Files. Per informazioni dettagliate, vedere [metriche per Azure NetApp files](azure-netapp-files-metrics.md) . 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>È possibile gestire Azure NetApp Files tramite Azure Storage Explorer?

No. Azure NetApp Files non è supportato da Azure Storage Explorer.

## <a name="data-migration-and-protection-faqs"></a>Domande frequenti sulla migrazione e la protezione dei dati

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Ricerca per categorie migrare i dati Azure NetApp Files?
Azure NetApp Files fornisce volumi NFS e SMB.  È possibile utilizzare qualsiasi strumento di copia basato su file per eseguire la migrazione dei dati al servizio. 

NetApp offre una soluzione basata su SaaS, la [sincronizzazione cloud NetApp](https://cloud.netapp.com/cloud-sync-service).  La soluzione consente di replicare i dati NFS o SMB in Azure NetApp Files le esportazioni NFS o le condivisioni SMB. 

È anche possibile usare un'ampia gamma di strumenti gratuiti per la copia dei dati. Per NFS, è possibile usare strumenti di carico di lavoro come [rsync](https://rsync.samba.org/examples.html) per copiare e sincronizzare i dati di origine in un volume Azure NetApp files. Per SMB, è possibile utilizzare [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) dei carichi di lavoro nello stesso modo.  Questi strumenti possono anche replicare le autorizzazioni per file o cartelle. 

I requisiti per la migrazione dei dati da locale a Azure NetApp Files sono i seguenti: 

- Assicurarsi che Azure NetApp Files sia disponibile nell'area di Azure di destinazione.
- Convalidare la connettività di rete tra l'origine e il Azure NetApp Files indirizzo IP del volume di destinazione. Il trasferimento dei dati tra locale e il servizio Azure NetApp Files è supportato tramite ExpressRoute.
- Creare il volume di Azure NetApp Files di destinazione.
- Trasferire i dati di origine nel volume di destinazione usando lo strumento di copia file preferito.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Ricerca per categorie creare una copia di un volume di Azure NetApp Files in un'altra area di Azure?
    
Azure NetApp Files fornisce volumi NFS e SMB.  Qualsiasi strumento di copia basato su file può essere usato per replicare i dati tra le aree di Azure. 

NetApp offre una soluzione basata su SaaS, la [sincronizzazione cloud NetApp](https://cloud.netapp.com/cloud-sync-service).  La soluzione consente di replicare i dati NFS o SMB in Azure NetApp Files le esportazioni NFS o le condivisioni SMB. 

È anche possibile usare un'ampia gamma di strumenti gratuiti per la copia dei dati. Per NFS, è possibile usare strumenti di carico di lavoro come [rsync](https://rsync.samba.org/examples.html) per copiare e sincronizzare i dati di origine in un volume Azure NetApp files. Per SMB, è possibile utilizzare [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) dei carichi di lavoro nello stesso modo.  Questi strumenti possono anche replicare le autorizzazioni per file o cartelle. 

I requisiti per la replica di un volume Azure NetApp Files in un'altra area di Azure sono i seguenti: 
- Assicurarsi che Azure NetApp Files sia disponibile nell'area di Azure di destinazione.
- Convalidare la connettività di rete tra reti virtuali in ogni area. Attualmente il peering globale tra reti virtuali non è supportato.  È possibile stabilire la connettività tra reti virtuali collegandosi a un circuito ExpressRoute o usando una connessione VPN S2S. 
- Creare il volume di Azure NetApp Files di destinazione.
- Trasferire i dati di origine nel volume di destinazione usando lo strumento di copia file preferito.

### <a name="is-migration-with-azure-data-box-supported"></a>La migrazione con Azure Data Box è supportata?

No. Azure Data Box non supporta attualmente Azure NetApp Files. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>La migrazione con il servizio importazione/esportazione di Azure è supportata?

No. Il servizio importazione/esportazione di Azure non supporta attualmente Azure NetApp Files.

## <a name="next-steps"></a>Passaggi successivi  

- [Domande frequenti Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Domande frequenti Rete virtuale di Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [Domande frequenti sulle prestazioni SMB per Azure NetApp Files](azure-netapp-files-smb-performance.md)
