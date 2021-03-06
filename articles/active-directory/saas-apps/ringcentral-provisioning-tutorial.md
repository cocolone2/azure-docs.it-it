---
title: 'Esercitazione: configurare RingCentral per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a RingCentral.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 976ac071-fa7e-4f31-aed1-d174942860d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2019
ms.author: Zhchia
ms.openlocfilehash: 052223f69fc1c1d59ec5f1bcbeb3746ef7122c86
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087060"
---
# <a name="tutorial-configure-ringcentral-for-automatic-user-provisioning"></a>Esercitazione: configurare RingCentral per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire sia in RingCentral che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in [RingCentral](https://www.ringcentral.com/office/plansandpricing.html) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in RingCentral
> * Rimuovere gli utenti in RingCentral quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e RingCentral
> * [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial) per RingCentral (scelta consigliata)

## <a name="prerequisites"></a>Prerequisites

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l' [autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per la configurazione del provisioning, ad esempio amministratore dell'applicazione, amministratore di applicazioni cloud, proprietario dell'applicazione o amministratore globale. 
* [Tenant di RingCentral](https://www.ringcentral.com/office/plansandpricing.html)
* Un account utente in RingCentral con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Informazioni sul [funzionamento del servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare chi sarà nell' [ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e RingCentral](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-ringcentral-to-support-provisioning-with-azure-ad"></a>Passaggio 2. Configurare RingCentral per supportare il provisioning con Azure AD

1. Accedere a [RingCentral Admin Console](https://login.ringcentral.com/sw.html). Passare a **strumenti > integrazione della directory**.

    ![Console di amministrazione di RingCentral](media/ringcentral-provisioning-tutorial/admin.png)

2.  Scegliere **scim** in **Seleziona provider di directory**. In futuro sarà disponibile un'opzione denominata Azure Active Directory. Fare clic su **Abilita servizio scim**.

    ![RingCentral aggiungere SCIM](media/ringcentral-provisioning-tutorial/scim.png)

3.  Contattare il team di supporto di RingCentral all'matthew.hunt@ringcentral.com per un **token di autenticazione scim**. Questo valore verrà immesso nel campo token segreto nella scheda provisioning dell'applicazione RingCentral nel portale di Azure.

> [!NOTE]
> Per assegnare licenze agli utenti, fare riferimento al collegamento video [qui](https://support.ringcentral.com/s/article/5-10-Adding-Extensions-via-Web?language).

## <a name="step-3-add-ringcentral-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere RingCentral dalla raccolta di applicazioni Azure AD

Aggiungere RingCentral dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in RingCentral. Se in precedenza è stato configurato RingCentral per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'applicazione separata quando si testa inizialmente l'integrazione. Altre informazioni sull'aggiunta di un'applicazione dalla raccolta sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire chi sarà nell'ambito per il provisioning 

Il servizio Azure AD provisioning consente di definire l'ambito di chi verrà eseguito il provisioning in base all'assegnazione all'applicazione e o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile usare i [passaggi](../manage-apps/assign-user-or-group-access-portal.md) seguenti per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi a RingCentral, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Avviare Small. Eseguire il test con un piccolo set di utenti e gruppi prima di distribuirlo a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-ringcentral"></a>Passaggio 5. Configurare il provisioning utenti automatico in RingCentral 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ringcentral-in-azure-ad"></a>Per configurare il provisioning utenti automatico per RingCentral in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **RingCentral**.

    ![Collegamento a RingCentral nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://platform.ringcentral.com/scim/v2` in **URL tenant**. Immettere il valore del **token di autenticazione scim** recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a RingCentral. Se la connessione non riesce, verificare che l'account RingCentral disponga delle autorizzazioni di amministratore e riprovare.

    ![Scheda Provisioning](./media/ringcentral-provisioning-tutorial/provisioning.png)

6. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che deve ricevere le notifiche degli errori di provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a RingCentral**.

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a RingCentral nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in RingCentral per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), sarà necessario assicurarsi che l'API RingCentral supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |userName|string|
   |externalId|string|
   |active|Boolean|
   |displayName|string|
   |title|string|
   |emails[type eq "work"].value|string|
   |indirizzi [digitare EQ "Work"]. Country|string|
   |indirizzi [digitare EQ "Work"]. Region|string|
   |indirizzi [digitare EQ "Work"]. località|string|
   |addresses[type eq "work"].postalCode|string|
   |addresses[type eq "work"].streetAddress|string|
   |name.givenName|string|
   |name.familyName|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: Department|string|
   |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: Manager|Riferimento|

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per RingCentral, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in RingCentral selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti nell' **ambito** nella sezione **Impostazioni** . Il ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano approssimativamente ogni 40 minuti, a condizione che sia in esecuzione il servizio di provisioning Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato effettuato il provisioning con esito positivo o negativo
2. Controllare l' [indicatore di stato per visualizzare](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) lo stato del ciclo di provisioning e il modo in cui terminarlo
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entra in quarantena. Altre informazioni sugli stati di quarantena sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)