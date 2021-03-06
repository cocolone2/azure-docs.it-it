---
title: Account di accesso e utenti
description: Informazioni sul modo in cui il database SQL di Azure e l'analisi delle sinapsi di Azure autenticano gli utenti per l'accesso tramite account di accesso e account utente e usano ruoli e autorizzazioni esplicite per autorizzare gli account di accesso e gli utenti a eseguire azioni all'interno di database e a livello di server.
keywords: sicurezza del database sql, gestione della sicurezza del database, sicurezza degli account di accesso, sicurezza del database, accesso al database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2020
ms.openlocfilehash: 7c70d5dd19ec0495fe09152b5653363ad369347c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268912"
---
# <a name="granting-database-access-and-authorization-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Concessione dell'accesso al database e dell'autorizzazione per il database SQL e l'analisi delle sinapsi di Azure tramite account utente e account di accesso

L'accesso autenticato ai database nel database SQL di Azure e in Azure sinapsi Analytics (in precedenza Azure SQL Data Warehouse) viene gestito con account utente e account di accesso. L' [**autenticazione**](sql-database-security-overview.md#authentication) è il processo che consente di dimostrare che l'utente è quello che afferma.

- Un account di accesso è un singolo account nel database master
- Un account utente è un account singolo in qualsiasi database e non deve essere associato a un account di accesso

> [!IMPORTANT]
> I database nel database SQL di Azure e in Azure sinapsi Analytics (in precedenza Azure SQL Data Warehouse) sono definiti collettivamente nella parte restante di questo articolo come database SQL di Azure (per semplicità).

Un utente del database si connette a un database SQL di Azure usando un account utente ed è autenticato mediante uno dei due metodi seguenti:

- [Autenticazione SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication), che è costituita da un nome di account di accesso o un nome di account utente e una password associata archiviati nel database SQL di Azure.
- [Azure Active Directory autenticazione](sql-database-aad-authentication.md), che usa le credenziali di accesso archiviate in Azure Active Directory

L'autorizzazione per accedere ai dati ed eseguire varie azioni all'interno del database SQL di Azure viene gestita con i ruoli del database e le autorizzazioni esplicite. L' [**autorizzazione**](sql-database-security-overview.md#authorization) si riferisce alle autorizzazioni assegnate a un utente all'interno di un database SQL di Azure e determina le operazioni che l'utente è autorizzato a eseguire. L'autorizzazione viene controllata dalle [appartenenze ai ruoli](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) del database e dalle [autorizzazioni a livello di oggetto](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)dell'account utente. Come procedura consigliata, è opportuno concedere agli utenti i privilegi minimi necessari.

Contenuto dell'articolo:

- Configurazione dell'accesso e dell'autorizzazione dopo la creazione iniziale di un nuovo database SQL di Azure
- Come aggiungere account di accesso e account utente nel database master e negli account utente, quindi concedere a questi account le autorizzazioni amministrative
- Come aggiungere account utente nei database utente, associati ad account di accesso o come account utente indipendenti
- Configurare gli account utente con le autorizzazioni nei database utente usando i ruoli del database e le autorizzazioni esplicite

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Account di accesso e account utente esistenti dopo la creazione di un nuovo database

Quando si crea la prima distribuzione del database SQL di Azure, è necessario specificare un account di accesso amministratore e una password associata per tale account di accesso. Questo account amministrativo è denominato **amministratore del server**. La seguente configurazione di account di accesso e utenti nei database master e utente si verifica durante la distribuzione:

- Viene creato un account di accesso SQL con privilegi amministrativi usando il nome dell'account di accesso specificato. Un account di [accesso](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) è un singolo account utente per l'accesso al database SQL.
- A questo account di accesso vengono concesse autorizzazioni amministrative complete per tutti i database come [entità di livello server](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Questo account di accesso dispone di tutte le autorizzazioni disponibili nel database SQL e non può essere limitato. In un'istanza gestita, questo account di accesso viene aggiunto al ruolo predefinito del [server sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (questo ruolo non esiste con i database singoli o in pool).
- Viene creato un [account utente](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) denominato `dbo` per questo account di accesso in ogni database utente. L'utente [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) dispone di tutte le autorizzazioni di database nel database e viene mappato al ruolo predefinito del database `db_owner`. Ulteriori ruoli predefiniti del database sono descritti più avanti in questo articolo.

Per identificare gli account amministratore di SQL Server, aprire il portale di Azure e passare alla scheda **Proprietà** di SQL Server o del database SQL.

![Amministratori del server SQL](media/sql-database-manage-logins/sql-admins.png)

> [!IMPORTANT]
> Il nome dell'account di accesso dell'amministratore non può essere modificato dopo che è stato creato. Per reimpostare la password per l'amministratore del server logico, passare alla [portale di Azure](https://portal.azure.com), fare clic su **SQL Server**, selezionare il server dall'elenco e quindi fare clic su **Reimposta password**. Per reimpostare la password per un server di istanza gestita, passare alla portale di Azure, fare clic sull'istanza e quindi fare clic su **Reimposta password**. È anche possibile usare PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Creare altri account di accesso e utenti con autorizzazioni amministrative

A questo punto, il database SQL è configurato per l'accesso solo usando un unico account di accesso SQL e un account utente. Per creare account di accesso aggiuntivi con autorizzazioni amministrative complete o parziali, sono disponibili le opzioni seguenti (a seconda della modalità di distribuzione):

- **Creazione di un account amministratore Azure Active Directory con autorizzazioni amministrative complete**

  Abilitare l'autenticazione Azure Active Directory e creare un account di accesso Azure AD amministratore. Un account di Azure Active Directory può essere configurato come amministratore della distribuzione del database SQL con autorizzazioni amministrative complete. Questo account può essere un account utente singolo o di gruppo di sicurezza. È **necessario** configurare un amministratore Azure ad se si desidera utilizzare account Azure ad per la connessione al database SQL. Per informazioni dettagliate sull'abilitazione dell'autenticazione Azure AD per tutti i tipi di distribuzione del database SQL, vedere gli articoli seguenti:

  - [Usare l'autenticazione Azure Active Directory per l'autenticazione con SQL](sql-database-aad-authentication.md)
  - [Configurare e gestire l'autenticazione di Azure Active Directory con SQL](sql-database-aad-authentication-configure.md)

- **In una distribuzione di istanza gestita, creare account di accesso SQL con autorizzazioni amministrative complete**

  - Creazione di un account di accesso SQL Server aggiuntivo nell'istanza gestita
  - Aggiungere l'account di accesso al ruolo predefinito del [server sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) utilizzando l'istruzione [ALTER Server Role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) . Questo account di accesso avrà autorizzazioni amministrative complete.
  - In alternativa, creare un [account di accesso Azure ad](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) usando la sintassi <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create Login</a> .

- **In una distribuzione singola o in pool, creare account di accesso SQL con autorizzazioni amministrative limitate**

  - Creazione di un account di accesso SQL aggiuntivo nel database master per la distribuzione di un database singolo o in pool o per la distribuzione di un'istanza gestita
  - Creazione di un account utente nel database master associato a questo nuovo account di accesso
  - Aggiungere l'account utente al `dbmanager`, il ruolo `loginmanager` o entrambi nel database `master` usando l'istruzione [ALTER Server Role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (per Azure sinapsi Analytics, usare l'istruzione [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ).

  > [!NOTE]
  > i ruoli `dbmanager` e `loginmanager` **non** riguardano le distribuzioni di istanze gestite.

  I membri di questi [speciali ruoli del database master](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) per i database singoli o in pool consentono agli utenti di disporre dell'autorità per creare e gestire i database o per creare e gestire gli account di accesso. Nei database creati da un utente membro del ruolo `dbmanager`, il membro viene mappato al ruolo predefinito del database `db_owner` e può accedere a tale database e gestirlo utilizzando l'account utente `dbo`. Questi ruoli non dispongono di autorizzazioni esplicite al di fuori del database master.

  > [!IMPORTANT]
  > Non è possibile creare un account di accesso SQL aggiuntivo con autorizzazioni amministrative complete in un database singolo o in pool.

## <a name="create-accounts-for-non-administrator-users"></a>Creare account per utenti non amministratori

È possibile creare account per utenti non amministrativi usando uno dei due metodi seguenti:

- **Creazione di un account di accesso**

  Creare un account di accesso SQL nel database master. Creare quindi un account utente in ogni database a cui l'utente deve accedere e associare l'account utente a tale account. Questo approccio è preferibile quando l'utente deve accedere a più database e si desidera che le password vengano sincronizzate. Questo approccio, tuttavia, presenta complessità quando viene usato con la replica geografica perché è necessario creare l'account di accesso sia nel server primario che nel server secondario. Per altre informazioni, vedere [configurare e gestire la sicurezza del database SQL di Azure per il ripristino geografico o il failover](sql-database-geo-replication-security-config.md).
- **Creazione di un account utente**

  Creare un account utente nel database a cui un utente deve accedere (detto anche [utente indipendente](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - Con un database singolo o in pool, è sempre possibile creare questo tipo di account utente.
  - Con un database di istanza gestita che non supporta [Azure ad entità server](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities), è possibile creare questo tipo di account utente solo in un [database indipendente](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Con l'istanza gestita che supporta [Azure ad entità server](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities), è possibile creare account utente per eseguire l'autenticazione all'istanza gestita senza richiedere la creazione di utenti del database come utente di database indipendente.

  Con questo approccio, le informazioni di autenticazione dell'utente vengono archiviate in ogni database e replicate automaticamente nei database con replica geografica. Tuttavia, se lo stesso account è presente in più database e si usa l'autenticazione SQL, è necessario tenere sincronizzate manualmente le password. Inoltre, se un utente dispone di un account in database diversi con password diverse, la memorizzazione di tali password può costituire un problema.

> [!IMPORTANT]
> Per creare utenti indipendenti con mapping a Azure AD identità, è necessario effettuare l'accesso con un account di Azure AD che sia un amministratore nel database SQL. In istanza gestita, un account di accesso SQL con autorizzazioni `sysadmin` può creare anche un account di accesso Azure AD o un utente.

Per esempi che illustrano come creare account di accesso e utenti, vedere:

- [Creazione dell'account di accesso per database singoli o in pool](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Creare l'account di accesso per il database dell'istanza gestita](https://docs.microsoft.com/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Creare un account di accesso per il database di analisi delle sinapsi](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Crea utente](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Creazione di Azure AD utenti indipendenti](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Per un'esercitazione sulla sicurezza che include la creazione di SQL Server un utente indipendente in un database singolo o in pool, vedere [esercitazione: proteggere un database singolo o in pool](sql-database-security-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Utilizzo di ruoli di database fissi e personalizzati

Dopo aver creato un account utente in un database, in base a un account di accesso o come utente indipendente, è possibile autorizzare l'utente a eseguire varie azioni e ad accedere ai dati in un database specifico. Per autorizzare l'accesso, è possibile usare i metodi seguenti:

- **Ruoli predefiniti del database**

  Aggiungere l'account utente a un [ruolo predefinito del database](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). Sono disponibili 9 ruoli predefiniti del database, ognuno con un set di autorizzazioni definito. I ruoli predefiniti del database più comuni sono: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**e **db_denydatareader**. Per concedere autorizzazioni complete a un numero limitato di utenti viene usato comunemente **db_owner**. Gli altri ruoli predefiniti del database sono utili per ottenere rapidamente un database semplice nello sviluppo, ma non sono consigliabili per la maggior parte dei database di produzione. Ad esempio, il ruolo predefinito del database **db_datareader** concede l'accesso in lettura a ogni tabella del database, che è più che strettamente necessario.

  - Per aggiungere un utente a un ruolo predefinito del database:

    - Nel database SQL di Azure usare l'istruzione [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) . Per esempi, vedere [ALTER ROLE examples](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples) .
    - Analisi delle sinapsi di Azure, usare l'istruzione [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) . Per esempi, vedere [sp_addrolemember esempi](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Ruolo del database personalizzato**

  Creare un ruolo del database personalizzato utilizzando l'istruzione [create Role](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) . Un ruolo personalizzato consente di creare ruoli del database definiti dall'utente e concedere con attenzione a ogni ruolo le autorizzazioni minime necessarie per le esigenze aziendali. È quindi possibile aggiungere utenti al ruolo personalizzato. Quando un utente è membro di più ruoli, vengono aggregate le autorizzazioni di tutti.
- **Concedi direttamente le autorizzazioni**

  Concedere direttamente le [autorizzazioni](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) per l'account utente. Nel database SQL possono essere concesse o negate singolarmente oltre 100 autorizzazioni. Molte di queste autorizzazioni sono annidate. L'autorizzazione `UPDATE` per uno schema, ad esempio, include l'autorizzazione `UPDATE` per ogni tabella all'interno di tale schema. Come nella maggior parte dei sistemi di autorizzazioni, la negazione di un'autorizzazione determina l'override di una concessione. A causa dell'annidamento e del numero delle autorizzazioni, progettare un sistema di autorizzazioni appropriato per proteggere correttamente il database può richiedere un attento studio. Per iniziare, vedere l'elenco di autorizzazioni in [Autorizzazioni (Motore di database)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) e la [grafica in formato di poster](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) relativa alle autorizzazioni.

## <a name="using-groups"></a>Uso di gruppi

Una gestione efficiente degli accessi usa le autorizzazioni assegnate a Active Directory i gruppi di sicurezza e i ruoli predefiniti o personalizzati anziché ai singoli utenti.

- Quando si usa l'autenticazione Azure Active Directory, inserire gli utenti Azure Active Directory in un gruppo di sicurezza di Azure Active Directory. Creare un utente di database indipendente per il gruppo. Inserire uno o più utenti di database in un ruolo del database personalizzato con autorizzazioni specifiche appropriate per il gruppo di utenti.

- Quando si usa l'autenticazione SQL, creare utenti di database indipendente nel database. Inserire uno o più utenti di database in un ruolo del database personalizzato con autorizzazioni specifiche appropriate per il gruppo di utenti.

  > [!NOTE]
  > È anche possibile usare i gruppi per gli utenti di database non indipendenti.

È consigliabile acquisire familiarità con le funzionalità seguenti, utili per limitare o elevare le autorizzazioni:

- È possibile usare la [rappresentazione](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) e la [firma del modulo](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) per elevare temporaneamente le autorizzazioni in modo sicuro.
- [Sicurezza a livello di riga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) può essere utilizzato come limite alle cui righe un utente può accedere.
- [Mascheramento dei dati](sql-database-dynamic-data-masking-get-started.md) per limitare l'esposizione dei dati sensibili
- [Stored procedure](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) per limitare le operazioni che possono essere eseguite nel database.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica di tutte le funzionalità di sicurezza del database SQL, vedere la [panoramica della sicurezza in SQL](sql-database-security-overview.md).
