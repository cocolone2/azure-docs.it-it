---
title: Amministratori della sottoscrizione classica di Azure | Microsoft Docs
description: Illustra come aggiungere o modificare i ruoli di coamministratore o amministratore del servizio di Azure e come visualizzare l'amministratore dell'account.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2493e893f9afda0642bd838c94538dd0b984bce5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243484"
---
# <a name="azure-classic-subscription-administrators"></a>Amministratori della sottoscrizione classica di Azure

Microsoft consiglia di gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo. Tuttavia, se si usa ancora il modello di distribuzione classica, è necessario usare un ruolo di amministratore della sottoscrizione classico, ovvero amministratore del servizio e coamministratore. Per altre informazioni, vedere [Distribuzione Azure Resource Manager o classica](../azure-resource-manager/management/deployment-models.md).

Questo articolo descrive come aggiungere o modificare i ruoli di coamministratore o amministratore del servizio di Azure e come visualizzare l'amministratore dell'account.

## <a name="add-a-co-administrator"></a>Aggiungere un coamministratore

> [!TIP]
> È necessario aggiungere un coamministratore solo se l'utente deve gestire distribuzioni classiche di Azure tramite il [modulo PowerShell di gestione del servizio Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Se l'utente usa il portale di Azure solo per gestire le risorse classiche, non è necessario aggiungere il ruolo amministratore classico per l'utente.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore del servizio o coamministratore.

1. Aprire [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selezionare una sottoscrizione.

    I coamministratori possono essere assegnati solo nell'ambito della sottoscrizione.

1. Fare clic su **Controllo di accesso (IAM)** .

1. Fare clic sulla scheda **Amministratori (versione classica)** .

    ![Screenshot che apre Amministratori (versione classica)](./media/classic-administrators/classic-administrators.png)

1. Fare clic su **Aggiungi** > **Aggiungi coamministratore** per aprire il riquadro Aggiungi coamministratori.

    Se l'opzione Aggiungi coamministratore è disabilitata, non dispone dell'autorizzazione.

1. Selezionare l'utente che si desidera aggiungere e fare clic su **Aggiungi**.

    ![Schermata per l'aggiunta di un coamministratore](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Aggiungere un utente guest come coamministratore

Per aggiungere un utente guest come coamministratore, seguire la stessa procedura descritta nella sezione precedente [aggiungere un coamministratore](#add-a-co-administrator) . L'utente guest deve soddisfare i criteri seguenti:

- L'utente guest deve essere presente nella directory. Questo significa che l'utente è stato invitato alla directory e ha accettato l'invito.

Per ulteriori informazioni su come aggiungere un utente guest alla directory, vedere [aggiungere Azure Active Directory utenti di collaborazione B2B nel portale di Azure](../active-directory/b2b/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Differenze per gli utenti Guest

Gli utenti Guest a cui è stato assegnato il ruolo di coamministratore potrebbero riscontrare alcune differenze rispetto agli utenti membri con il ruolo di coamministratore. Si consideri lo scenario seguente:

- Un utente con un account Azure AD (aziendale o dell'Istituto di istruzione) è un amministratore del servizio per una sottoscrizione di Azure.
- L'utente B ha un account Microsoft.
- L'utente A assegna il ruolo di coamministratore all'utente B.
- L'utente B può eseguire quasi tutte le operazioni, ma non può registrare le applicazioni o cercare gli utenti nella directory di Azure AD.

Ci si aspetta che l'utente B possa gestire tutto. Il motivo di questa differenza è che l'account Microsoft viene aggiunto alla sottoscrizione come utente guest invece di utente membro. Gli utenti guest hanno diverse autorizzazioni predefinite in Azure AD rispetto agli utenti membri. Ad esempio, gli utenti membri possono leggere gli altri utenti di Azure AD mentre gli utenti guest non possono farlo. Gli utenti membri possono registrare nuove entità servizio in Azure AD mentre gli utenti guest non possono farlo.

Se un utente guest deve essere in grado di eseguire queste attività, una possibile soluzione consiste nell'assegnare i ruoli di amministratore di Azure AD specifici necessari all'utente guest. Ad esempio, nello scenario precedente è possibile assegnare i [ruoli con autorizzazioni di lettura nella directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) per leggere altri utenti e assegnare il ruolo [sviluppatore di applicazioni](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) per poter creare le entità servizio. Per altre informazioni sugli utenti membri e guest e le relative autorizzazioni, vedere [Autorizzazioni utente predefinite in Azure Active Directory](../active-directory/fundamentals/users-default-permissions.md). Per altre informazioni sulla concessione dell'accesso per gli utenti guest, vedere [gestire l'accesso alle risorse di Azure per gli utenti Guest esterni con RBAC](role-assignments-external-users.md).

Si noti che i [ruoli predefiniti per le risorse di Azure](../role-based-access-control/built-in-roles.md) sono diversi dai [ruoli di amministratore di Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). I ruoli predefiniti non concedono l'accesso ad Azure AD. Per altre informazioni, vedere [Informazioni sui diversi ruoli](../role-based-access-control/rbac-and-directory-admin-roles.md).

Per informazioni sul confronto tra utenti membri e utenti guest, vedere [quali sono le autorizzazioni utente predefinite in Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="remove-a-co-administrator"></a>Rimuovere un coamministratore

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore del servizio o coamministratore.

1. Aprire [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selezionare una sottoscrizione.

1. Fare clic su **Controllo di accesso (IAM)** .

1. Fare clic sulla scheda **Amministratori (versione classica)** .

1. Aggiungere un segno di spunta accanto al coamministratore che si desidera rimuovere.

1. Scegliere **Rimuovi**.

1. Nella finestra di messaggio visualizzata fare clic su **Sì**.

    ![Schermata per la rimozione di un coamministratore](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Modificare l'amministratore del servizio

Solo l'amministratore account può modificare l'amministratore del servizio per una sottoscrizione. Per impostazione predefinita, al momento della sottoscrizione di un abbonamento di Azure, l'amministratore del servizio corrisponde all'amministratore dell'account. L'amministratore con il ruolo di amministratore dell'account non ha accesso al portale di Azure. L'amministratore con il ruolo di amministratore del servizio ha pieno accesso al portale di Azure. Se l'amministratore dell'account e l'amministratore del servizio corrispondono allo stesso utente e si assegna l'amministratore del servizio a un altro utente, l'amministratore dell'account perde l'accesso al portale di Azure. Tuttavia, l'amministratore account può sempre usare il Centro account per reimpostare se stesso come amministratore del servizio.

Attenersi alla seguente procedura per modificare l'amministratore del servizio nel **centro account**.

### <a name="account-center"></a>Centro account

1. Verificare che lo scenario sia supportato controllando le [limitazioni per la modifica dell'amministratore del servizio](#limitations-for-changing-the-service-administrator).

1. Accedere al [Centro account](https://account.windowsazure.com/subscriptions) come amministratore account.

1. Fare clic su una sottoscrizione.

1. Sul lato destro fare clic su **Modifica i dettagli della sottoscrizione**.

    ![Screenshot che mostra il pulsante di modifica della sottoscrizione nel Centro account](./media/classic-administrators/editsub.png)

1. Nel casella **AMMINISTRATORE DEL SERVIZIO** immettere l'indirizzo di posta elettronica del nuovo amministratore del servizio.

    ![Screenshot che mostra la casella per modificare l'indirizzo e-mail dell'amministratore del servizio](./media/classic-administrators/change-service-admin.png)

1. Fare clic sul segno di spunta per salvare la modifica.

### <a name="limitations-for-changing-the-service-administrator"></a>Limitazioni per la modifica degli amministratori del servizio

Può essere presente un solo amministratore del servizio per ogni sottoscrizione di Azure. La modifica dell'amministratore del servizio si comporterà in modo diverso a seconda che l'amministratore dell'account sia un account Microsoft o che si tratti di un account Azure AD (account aziendale o dell'Istituto di istruzione).

| Account amministratore account | È possibile modificare l'amministratore del servizio in un account Microsoft diverso? | È possibile modificare l'amministratore del servizio in un account Azure AD nella stessa directory? | È possibile modificare l'amministratore del servizio in un account Azure AD in una directory diversa? |
| --- | --- | --- | --- |
| Account Microsoft | Sì | No | No |
| Account Azure AD | Sì | Sì | No |

Se l'amministratore dell'account è un account di Azure AD, è possibile modificare l'amministratore del servizio in un account di Azure AD nella stessa directory, ma non in una directory diversa. Ad esempio, abby@contoso.com possibile modificare l'amministratore del servizio per bob@contoso.com, ma non può modificare l'amministratore del servizio in john@notcontoso.com a meno che john@notcontoso.com non abbia una presenza nella directory contoso.com

Per ulteriori informazioni sugli account Microsoft e Azure AD, vedere [che cos'è Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="view-the-account-administrator"></a>Visualizzare l'amministratore dell'account

L'amministratore dell'account è l'utente che inizialmente ha effettuato la sottoscrizione ad Azure ed è il responsabile della fatturazione della sottoscrizione. Per modificare l'amministratore account di una sottoscrizione, vedere [Trasferimento della proprietà di una sottoscrizione di Azure a un altro account](../cost-management-billing/manage/billing-subscription-transfer.md).

Seguire questi passaggi per visualizzare l'amministratore account.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Aprire [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selezionare una sottoscrizione.

1. Scegliere **Proprietà**.

    L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account**.

    ![Screenshot che mostra l'amministratore dell'account](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Passaggi successivi

* [Comprendere i diversi ruoli in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md)
* [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)
