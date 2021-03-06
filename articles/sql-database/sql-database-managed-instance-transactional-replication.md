---
title: Replica transazionale
description: Informazioni sull'uso della replica transazionale di SQL Server con database singoli, in pool e dell'istanza nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 41dd336bdb74fbe745ab48ebd3c168af0492ae2c
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75691007"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Replica transazionale con database singoli, in pool e dell'istanza nel database SQL di Azure

La replica transazionale è una funzionalità del database SQL di Azure e di SQL Server che consente di replicare i dati da una tabella nel database SQL di Azure o in un SQL Server verso tabelle inserite in database remoti. Questa funzionalità consente di sincronizzare più tabelle in database diversi.

## <a name="when-to-use-transactional-replication"></a>Quando usare la replica transazionale

La replica transazionale è utile negli scenari seguenti:
- Pubblicare le modifiche apportate in una o più tabelle in un database e distribuirle in uno o più SQL Server o database SQL di Azure che hanno sottoscritto le modifiche.
- Mantenere più database distribuiti in stato sincronizzato.
- Eseguire la migrazione dei database da una SQL Server o da un'istanza gestita a un altro database pubblicando continuamente le modifiche.

## <a name="overview"></a>Overview

I componenti chiave della replica transazionale sono illustrati nell'immagine seguente:  

![Replica con database SQL](media/replication-to-sql-database/replication-to-sql-database.png)

Il **server di pubblicazione** è un'istanza o un server che pubblica le modifiche apportate in alcune tabelle (articoli) inviando gli aggiornamenti al database di distribuzione. La pubblicazione in un database SQL di Azure qualsiasi da un SQL Server locale è supportata dalle versioni seguenti di SQL Server:

- SQL Server 2019 (anteprima)
- SQL Server da 2016 a 2017
- SQL Server 2014 SP1 CU3 o versione successiva (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 o versione successiva (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Per altre versioni di SQL Server che non supportano la pubblicazione in oggetti in Azure, è possibile usare il metodo di [ripubblicazione dei dati](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) per spostare i dati in versioni più recenti di SQL Server. 

Il **database di distribuzione** è un'istanza o un server che raccoglie le modifiche negli articoli da un server di pubblicazione e li distribuisce ai sottoscrittori. Il server di distribuzione può essere un'istanza gestita di database SQL di Azure o SQL Server (qualsiasi versione purché sia uguale o superiore alla versione del server di pubblicazione). 

Il **sottoscrittore** è un'istanza o un server che riceve le modifiche apportate nel server di pubblicazione. I sottoscrittori possono essere database singoli, in pool e dell'istanza del database SQL di Azure o di database SQL Server. Un sottoscrittore in un database singolo o in pool deve essere configurato come sottoscrittore push. 

| Ruolo | Database singoli e in pool | Database dell'istanza |
| :----| :------------- | :--------------- |
| **Autore** | No | Sì | 
| **Database di distribuzione** | No | Sì|
| **Sottoscrittore pull** | No | Sì|
| **Sottoscrittore push**| Sì | Sì|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Una sottoscrizione pull non è supportata quando il server di distribuzione è un database di istanza e il Sottoscrittore non lo è. 

Esistono diversi [tipi di replica](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replica | Database singoli e in pool | Database in istanza|
| :----| :------------- | :--------------- |
| [**Transazionale standard**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Sì (solo come sottoscrittore) | Sì | 
| [**Snapshot**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Sì (solo come sottoscrittore) | Sì|
| [**Replica di tipo merge**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | No | No|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | No | No|
| [**Bidirezionale**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | No | Sì|
| [**Sottoscrizioni aggiornabili**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | No | No|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Se si cerca di configurare la replica usando una versione meno recente, potrebbero essere generati gli errori MSSQL_REPL20084. (Il processo non è riuscito a connettersi al sottoscrittore) e MSSQ_REPL40532 (Impossibile aprire il server \<nome> richiesto dall'account di accesso. Accesso non riuscito).
  > - Per usare tutte le funzionalità del database SQL di Azure, è necessario usare le versioni più recenti di [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Matrice di supporto per i database di istanza e i sistemi locali
  La matrice di supporto della replica per i database dell'istanza è identica a quella per SQL Server in locale. 
  
| **Autore**   | **Database di distribuzione** | **Sottoscrittore** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Requisiti

- Per la connettività viene usata l'autenticazione SQL tra i partecipanti alla replica. 
- Una condivisione di account di archiviazione di Azure per la directory di lavoro usata dalla replica. 
- Per accedere alla condivisione file di Azure, è necessario aprire la porta 445 (TCP in uscita) nelle regole di sicurezza della subnet dell'istanza gestita. 
- È necessario aprire la porta 1433 (TCP in uscita) se il server di pubblicazione/distribuzione si trova in un'istanza gestita e il Sottoscrittore è in locale.
- Tutti i tipi di partecipanti alla replica (server di pubblicazione, server di distribuzione, sottoscrittore pull e Sottoscrittore push) possono essere inseriti in istanze gestite, ma il server di pubblicazione e il server di distribuzione devono trovarsi sia nel cloud sia in locale.
- Se il server di pubblicazione, il server di distribuzione e/o il Sottoscrittore sono presenti in reti virtuali diverse, è necessario stabilire il peering VPN tra ciascuna entità, in modo che esista il peering VPN tra il server di pubblicazione e il server di distribuzione e/o il peering VPN tra il server di distribuzione e il Sottoscrittore. 


>[!NOTE]
> - È possibile che si verifichi l'errore 53 durante la connessione a un file di archiviazione di Azure se la porta 445 del gruppo di sicurezza di rete (NSG) in uscita è bloccata quando il server di distribuzione è un database di istanza e il Sottoscrittore è in locale. [Aggiornare il NSG di vNet](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) per risolvere il problema. 


### <a name="compare-data-sync-with-transactional-replication"></a>Confrontare la sincronizzazione dati con la replica transazionale

| | Sincronizzazione dei dati | Replica transazionale |
|---|---|---|
| Vantaggi | - Supporto attivo/attivo<br/>- Bidirezionale tra database locali e database SQL di Azure | - Latenza inferiore<br/>- Coerenza delle transazioni<br/>- Riutilizzo topologia esistente dopo la migrazione |
| Svantaggi | - Latenza 5 min o superiore<br/>- Nessuna coerenza delle transazioni<br/>- Maggiore impatto sulle prestazioni | - Impossibilità di pubblicare da database singolo o in pool di Database SQL di Azure<br/>- Alti costi di manutenzione |
| | | |

## <a name="common-configurations"></a>Configurazioni comuni

In generale, il server di pubblicazione e il database di pubblicazione devono entrambi essere nel cloud o locali. Le configurazioni seguenti sono supportate: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Server di pubblicazione con server di distribuzione locale in un'istanza gestita

![Singola istanza come server di pubblicazione e database di distribuzione](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Il server di pubblicazione e il server di distribuzione sono configurati all'interno di una singola istanza gestita e distribuiscono le modifiche ad altra istanza gestita, database singolo, database in pool o SQL Server in locale. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Server di pubblicazione con server di distribuzione remoto in un'istanza gestita

In questa configurazione, un'istanza gestita pubblica le modifiche apportate al server di distribuzione in un'altra istanza gestita che può gestire molte istanze gestite di origine e distribuisce le modifiche a una o più destinazioni in istanza gestita, database singolo, database in pool o SQL Server.

![Istanze separate per server di pubblicazione e database di distribuzione](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

I database di pubblicazione e distribuzione sono configurati in due istanze gestite. Questa configurazione presenta alcuni vincoli: 

- Entrambe le istanze gestite si trovano nello stesso vNet.
- Le due istanze gestite sono nella stessa posizione.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Server di pubblicazione e database di distribuzione locali con sottoscrittore in un database singolo, in pool e dell'istanza 

![Database SQL di Azure come sottoscrittore](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
In questa configurazione, un database SQL di Azure (database singolo, in pool e dell'istanza) è un sottoscrittore. Questa configurazione supporta la migrazione dal database locale al database di Azure. Se un sottoscrittore si trova in un database in pool o singolo, deve essere in modalità push.  

## <a name="with-failover-groups"></a>Con gruppi di failover

Se la replica geografica è abilitata in un'istanza del **server di pubblicazione** o del **server di distribuzione** in un gruppo di [failover](sql-database-auto-failover-group.md), l'amministratore dell'istanza gestita deve eliminare tutte le pubblicazioni nel database primario precedente e riconfigurarle sul nuovo database primario dopo un failover. In questo scenario sono necessarie le attività seguenti:

1. Arrestare tutti i processi di replica in esecuzione nel database, se presenti.
2. Eliminare i metadati della sottoscrizione dal server di pubblicazione eseguendo lo script seguente nel database del server di pubblicazione:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Rilasciare i metadati della sottoscrizione dal Sottoscrittore. Eseguire lo script seguente nel database di sottoscrizione dell'istanza del Sottoscrittore:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Eliminare in modo forzato tutti gli oggetti di replica dal server di pubblicazione eseguendo lo script seguente nel database pubblicato:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Eliminare in modo forzato il server di distribuzione precedente dall'istanza primaria originale (se si esegue il failover in un database primario precedente utilizzato per avere un server di distribuzione). Eseguire lo script seguente nel database master nell'istanza gestita del server di distribuzione precedente:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Se la replica geografica è abilitata in un'istanza del **Sottoscrittore** in un gruppo di failover, la pubblicazione deve essere configurata per la connessione all'endpoint del listener del gruppo di failover per l'istanza gestita del Sottoscrittore. In caso di failover, l'azione successiva da parte dell'amministratore dell'istanza gestita dipende dal tipo di failover che si è verificato: 

- Per un failover senza perdita di dati, la replica continuerà a funzionare dopo il failover. 
- Per un failover con perdita di dati, funzionerà anche la replica. La replica delle modifiche perse verrà nuovamente eseguita. 
- Per un failover con perdita di dati, ma la perdita di dati non rientra nel periodo di memorizzazione del database di distribuzione, l'amministratore dell'istanza gestita dovrà reinizializzare il database di sottoscrizione. 

## <a name="next-steps"></a>Passaggi successivi

- [Configurare la replica tra un server di pubblicazione MI e un Sottoscrittore](replication-with-sql-database-managed-instance.md)
- [Configurare la replica tra un server di pubblicazione MI, un server di distribuzione MI e un Sottoscrittore SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [Creare una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) usando il nome del server di database SQL di Azure come sottoscrittore (ad esempio `N'azuresqldbdns.database.windows.net`) e il nome del database SQL di Azure come database di destinazione (ad esempio **AdventureWorks**). )


Per ulteriori informazioni sulla configurazione della replica transazionale, vedere le esercitazioni seguenti:



## <a name="see-also"></a>Vedere anche  

- [Replica con MI e un gruppo di failover](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replica nel database SQL](replication-to-sql-database.md)
- [Replica nell'istanza gestita](replication-with-sql-database-managed-instance.md)
- [Creare una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipi di replica](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitoraggio (replica)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inizializzare una sottoscrizione](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
