---
title: Accesso condizionale
description: Informazioni su come configurare l'accesso condizionale per il database SQL di Azure e la sinapsi di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: f2431ee7c62079a3691a5ea99e562460df8f9309
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197573"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Accesso condizionale (multi-factor authentication) con database SQL di Azure e Azure sinapsi Analytics

Il [database SQL](sql-database-technical-overview.md)di azure, [istanza gestita](sql-database-managed-instance.md)e la [sinapsi di Azure](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) supportano l'accesso condizionale Microsoft. 

> [!NOTE]
> Questo argomento si applica al server SQL di Azure e al database SQL e alla sinapsi di Azure creati nel server SQL di Azure. Per semplicità, il database SQL viene usato quando si fa riferimento sia al database SQL che alla sinapsi di Azure.

La procedura seguente mostra come configurare il database SQL per applicare un criterio di accesso condizionale.  

## <a name="prerequisites"></a>Prerequisites  
- È necessario configurare il database SQL o il pool SQL in sinapsi di Azure per supportare l'autenticazione Azure Active Directory. Per i passaggi specifici, vedere [configurare e gestire l'autenticazione di Azure Active Directory con il database SQL o la sinapsi di Azure](sql-database-aad-authentication-configure.md).  
- Quando è abilitata l'autenticazione a più fattori, è necessario connettersi con uno strumento supportato, ad esempio la versione più recente di SQL Server Management Studio. Per altre informazioni, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurare l'autorità di certificazione per il database SQL di Azure o Azure SQL Data Warehouse  
1. Accedere al portale, selezionare **Azure Active Directory**e quindi selezionare **accesso condizionale**. Per altre informazioni, vedere [Documentazione tecnica sull'accesso condizionale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   pannello accesso condizionale ![](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Nel pannello **Accesso condizionale - Criteri** fare clic su **Nuovo criterio**, specificare un nome e quindi fare clic su **Configura regole**.  
3. In **assegnazioni**selezionare **utenti e gruppi**, **selezionare utenti e gruppi**, quindi selezionare l'utente o il gruppo per l'accesso condizionale. Fare clic su **Seleziona** e quindi su **Fatto** per accettare la selezione.  
   ![seleziona utenti e gruppi](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Selezionare **App cloud** e fare clic su **Seleziona app**. Vengono visualizzate tutte le app disponibili per l'accesso condizionale. Selezionare **Database SQL di Azure**, nella parte inferiore fare clic su **Seleziona** e quindi fare clic su **Fatto**.  
   ![selezionare il database SQL](./media/sql-database-conditional-access/select-sql-database.png)  
   Se non è possibile trovare il **database SQL di Azure** elencato nel terzo screenshot seguente, completare i passaggi seguenti:   
   - Accedere all'istanza del database SQL di Azure o di Azure SQL Data Warehouse usando SQL Server Management Studio con un account amministratore di AAD.  
   - Eseguire `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Accedere ad AAD e verificare che il database SQL di Azure e la sinapsi di Azure siano elencati nelle applicazioni di AAD.  

5. Selezionare **Controlli di accesso**, selezionare **Concedi** e quindi selezionare il criterio che si vuole applicare. Per questo esempio, selezionare **Richiedi autenticazione a più fattori**.  
   ![selezionare l'opzione per concedere l'accesso](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Summary  
L'applicazione selezionata (database SQL di Azure) che consente di connettersi al database SQL di Azure o ad Azure SQL Data Warehouse con Azure AD Premium, applica ora il criterio di accesso condizionale selezionato, **Richiedi autenticazione a più fattori**  
Per domande sul database SQL di Azure e sulle sinapsi di Azure per la funzionalità di autenticazione a più fattori, contattare MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Passaggi successivi  

Per un'esercitazione, vedere [Proteggere il database SQL di Azure](sql-database-security-tutorial.md).
