---
title: Dynamics CRM | Azure Marketplace
description: Configurare la gestione dei lead per Dynamics CRM.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 4ccc8b85e72a4da3b0e640abcc70d24b7cdc54af
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825251"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Configurare la gestione dei lead per Dynamics CRM online

Questo articolo descrive come configurare Dynamics CRM Online per l'elaborazione di lead per la vendita.

## <a name="prerequisites"></a>Prerequisiti

Per completare le procedure descritte in questo articolo, sono necessarie le seguenti autorizzazioni utente:
- Essere amministratore all'interno dell'istanza di Dynamics CRM Online per installare una soluzione.
- Essere amministratore del tenant per creare un nuovo account del servizio lead.

<a name="install-the-solution"></a>Installare la soluzione
--------------------

1.  Scaricare la [soluzione Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e salvarla in locale.

2.  Aprire Dynamics CRM Online e scegliere Impostazioni.
    >[!NOTE]
    >Se non vengono visualizzate le opzioni all'interno dell'acquisizione di schermata successiva, significa che non si possiedono le autorizzazioni necessarie.
 
       ![Vista di configurazione di Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Fare clic su **Importa** e selezionare la soluzione scaricata nel passaggio 1.
 
    ![Opzione di importazione di Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Completare l'installazione della soluzione.

## <a name="configure-user-permissions"></a>Configurare le autorizzazioni utente

Per scrivere i lead nell'istanza Dynamics CRM è necessario condividere un account di servizio e configurarne i permessi.

Usare la procedura seguente per creare l'account del servizio e assegnare le autorizzazioni. Usare **Azure Active Directory** oppure **Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Questa è l'opzione consigliata poiché consente di ottenere l'ulteriore vantaggio di non dover aggiornare nome utente o password per continuare a ricevere i lead. Per usare l'opzione Azure Active Directory, fornire ID app, chiave applicazione e ID directory dall'applicazione Active Directory.

Usare la procedura seguente per configurare Azure Active Directory per Dynamics CRM.

1.  Accedere al [portale di Azure](https://portal.azure.com/), quindi selezionare il servizio Azure Active Directory.

2.  Selezionare **Proprietà** , quindi copiare l' **ID directory**. Si tratta dell'identificazione dell'account tenant che è necessario usare nella portale Cloud Partner.

    ![Ottieni ID directory](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Selezionare **Registrazioni app** e quindi **Registrazione nuova applicazione**.
4.  Immettere il nome dell'applicazione.
5.  Per Tipo selezionare **App/API Web**.
6.  Specificare un URL. Questo campo è necessario per creare un'applicazione, ma non lo è per i lead.
7. Selezionare **Crea**.
8.  Ora che l'applicazione è stata registrata, selezionare **Proprietà** , quindi selezionare **Copia ID applicazione**. Queste informazioni di connessione verranno utilizzate nel portale Cloud Partner.
9.  In Proprietà, impostare l'applicazione come Multi-tenant e quindi selezionare **Salva**.

10. Selezionare **Chiave** per creare una nuova chiave con durata impostata su *Nessuna scadenza*. Selezionare **Salva** per creare la chiave. 
11. Nel menu delle chiavi, selezionare **Copiare il valore della chiave.** Salvare una copia di questo valore poiché necessario per il portale Cloud Partner.
    
    ![Dynamics: registrare la chiave](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Selezionare **Autorizzazioni necessarie** e quindi **Aggiungi**. 
13. Selezionare **Dynamics CRM Online** come nuova API e controllare le autorizzazioni per *Accesso a CRM Online come utenti dell'organizzazione*.

14. In Dynamics CRM, passare a Utenti e selezionare "Utenti abilitati"dall'elenco a discesa per passare a **Utenti applicazione**.
    
    ![Utenti applicazione](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Selezionare **Nuovo** per creare un nuovo utente. Selezionare **UTENTE: UTENTE APPLICAZIONE** dall'elenco a discesa.
    
    ![Aggiungi nuovo utente applicazione](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. In **Nuovo utente**, specificare nome e indirizzo di posta elettronica da utilizzare con questa connessione. Incollare nel portale di Azure l'**ID applicazione** per l'app creata.

     ![Configura nuovo utente](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Passare alla sezione"Impostazioni di sicurezza" di questo articolo per completare la configurazione della connessione per l'utente corrente.

### <a name="office-365"></a>Office 365

Se non si vuole usare Azure Active Directory, è possibile registrare un nuovo utente nell'interfaccia di *amministrazione di Microsoft 365*. Per continuare a ottenere lead, è necessario aggiornare il nome utente o la password ogni 90 giorni.

Utilizzare la procedura seguente per configurare Office 365 per Dynamics CRM.

1. Accedere al centro di [amministrazione Microsoft 365](https://admin.microsoft.com).

2. Selezionare il riquadro **amministratore** .

    ![Amministratore di Office Online](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Selezionare **Aggiungi utente**.

    ![Aggiungere un utente](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Creare un nuovo utente per il servizio Lead Writer. Configurare le seguenti impostazioni:

    -   Fornire una password e deselezionare l'opzione "Chiedi all'utente di cambiare la password al primo accesso".
    -   Selezionare il ruolo "Utente - nessun accesso amministratore" come ruolo per l'utente.
    -   Selezionare la licenza del prodotto mostrata nella successiva acquisizione di schermata. L'addebito avverrà in base alla licenza scelta. La soluzione funzionerà anche con la licenza di base di Dynamics CRM Online.
    
    ![Configurare le autorizzazioni utente e la licenza](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Impostazioni di sicurezza

Il passaggio finale consente all'utente creato di scrivere i lead.

1.  Accedere a Dynamics CRM Online.
2.  Su **Impostazioni**, selezionare **Sicurezza**.
    
    ![Impostazioni di sicurezza](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Selezionare l'utente creato in **Autorizzazioni utente**, quindi selezionare **Gestisci ruoli utente**. Controllare **Microsoft Marketplace Lead Writer** per l'assegnazione del ruolo.

    ![Assegna ruolo utente](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Questo ruolo viene creato dalla soluzione importata. Dispone solo di autorizzazioni per la scrittura dei lead e dell'individuazione della versione della soluzione per garantirne la compatibilità.

4.  In Sicurezza, selezionare **Ruoli di sicurezza** e individuare il ruolo per Microsoft Marketplace Lead Writer.
    
    ![Configurare il writer del lead di sicurezza](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Selezionare la scheda **record principali** . abilitare Crea/lettura/scrittura per l'interfaccia utente dell'entità utente.

    ![Abilitare le opzioni di creazione, lettura e scrittura per l'utente](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Eseguire il wrapping

Completare la configurazione di Dynamics CRM per la gestione dei lead aggiungendo le informazioni sull'account generato al portale Cloud Partner. Ad esempio:

-   **Azure Active Directory** - **ID applicazione** (esempio: *23456052-aaaa-bbbb-8662-1234df56788f*), **ID directory** (esempio: *12345678-8af1-4asf-1234-12234d01db47*) e **Chiave applicazione**  (esempio: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=* ).
-   **Office 365** - **URL** (esempio: *https://contoso.crm4.dynamics.com* ), **Nome utente** (esempio: *contoso\@contoso.onmicrosoft.com*) e **Password** (esempio: *P\@ssw0rd*).
