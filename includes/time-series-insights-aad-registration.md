---
title: File di inclusione
description: File di inclusione
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 5be6e7937a6e1f710b8e2576a9058963413fb6c2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984579"
---
1. Nel [portale di Azure](https://ms.portal.azure.com/) selezionare **Azure Active Directory** > **Registrazioni app** > **Nuova registrazione**.

   [![Nuova registrazione dell'applicazione in Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    L'app verrà elencata in questa posizione dopo la registrazione.

1. Assegnare all'applicazione un nome e selezionare **Account solo in questa directory organizzativa** per specificare i valori per **Tipi di account supportati** a cui è consentito l'accesso all'API. Scegliere un URI valido per reindirizzare gli utenti dopo l'autenticazione e quindi scegliere **Registra**.

   [![Creare l'applicazione in Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Le informazioni importanti sull'app Azure Active Directory vengono visualizzate nel pannello **Panoramica** dell'app elencata. Selezionare l'app in **Applicazioni di cui si è proprietari** e quindi selezionare **Panoramica**.

   [![Copiare l'ID applicazione](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Copiare il valore di **ID applicazione (client)** da usare nell'applicazione client.

1. Il pannello **Autenticazione** specifica importanti impostazioni di configurazione dell'autenticazione. 

    1. Aggiungere gli **URI di reindirizzamento** e configurare i token di **accesso** selezionando **+ Aggiungi una piattaforma**.

    1. Determinare se l'app è un **client pubblico** o meno selezionando **Sì** o **No**.

    1. Verificare quali account e tenant sono supportati.

    [![configurare la concessione implicita](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Dopo aver selezionato la piattaforma appropriata, configurare gli **URI di reindirizzamento** e i token di **accesso** nel pannello laterale a destra dell'interfaccia utente.

    1. È necessario che quanto specificato per **URI di reindirizzamento** corrisponda all'indirizzo fornito dalla richiesta di autenticazione:

        * Per le app ospitate in un ambiente di sviluppo locale, selezionare **Client pubblico (per dispositivi mobili e desktop)** . Assicurarsi di impostare **client pubblico** su **Sì**.
        * Per le app a singola pagina ospitate nel servizio app Azure, selezionare **Web**.

    1. Determinare se un **URL di disconnessione** è appropriato.

    1. Abilitare il flusso di concessione implicita controllando i **token di accesso** o i **token ID**.

    [![creare gli URI di Reindirizzamento](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Fare clic su **Configura**, quindi su **Salva**.

1. Selezionare **certificati & segreti** , quindi **nuovo segreto client** per creare una password dell'applicazione che l'app client può usare per dimostrare la propria identità.

   [![Creare un nuovo segreto client](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Verrà quindi visualizzata la password del segreto client. Copiare la chiave in un editor di testo.

   > [!NOTE]
   > È invece possibile scegliere di importare un certificato. Per una maggiore sicurezza, è consigliabile un certificato. Per usare un certificato, selezionare **Carica certificato**.

1. Associare l'app Azure Active Directory Azure Time Series Insights. Selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione** > **API usate dall'organizzazione**. 

    [![Associare un'API all'app Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Digitare `Azure Time Series Insights` nella barra di ricerca e quindi selezionare `Azure Time Series Insights`.

1. Specificare quindi il tipo di autorizzazione API richiesto dall'app. Per impostazione predefinita sarà evidenziata l'opzione **Autorizzazioni delegate**. Scegliere un tipo di autorizzazione e quindi selezionare **Aggiungi autorizzazioni**.

    [![Specificare il tipo di autorizzazione API richiesto dall'app](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
