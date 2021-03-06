---
title: Configurare l'autenticazione Microsoft
description: Informazioni su come configurare l'autenticazione dell'account Microsoft come provider di identità per l'app del servizio app.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 95c603d4a10eb0e4d0817e20755c0f9b36baa96f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842334"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>Configurare l'app del servizio app per l'uso dell'account di accesso Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In questo argomento viene illustrato come configurare app Azure servizio per l'utilizzo di AAD per supportare gli account di accesso personali account Microsoft.

> [!NOTE]
> Sia gli account Microsoft personali che gli account aziendali usano il provider di identità AAD. A questo punto, non è possibile configurare questo provider di identità per supportare entrambi i tipi di accesso.

## <a name="register-microsoft-account"> </a>Registrare l'app con l'account Microsoft

1. Passare a [**registrazioni app**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) nel portale di Azure. Se necessario, accedere con l'account Microsoft.
1. Selezionare **nuova registrazione**, quindi immettere un nome di applicazione.
1. In **tipi di account supportati**selezionare **account in qualsiasi directory organizzativa (qualsiasi Azure ad directory-multi-tenant) e account Microsoft personali (ad esempio Skype, Xbox)**
1. In **URI di reindirizzamento**selezionare **Web**, quindi immettere `https://<app-domain-name>/.auth/login/aad/callback`. Sostituire *\<app-Domain-name >* con il nome di dominio dell'app.  Ad esempio: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Assicurarsi di usare lo schema HTTPS nell'URL.

1. Selezionare **Registra**.
1. Copiare l' **ID applicazione (client)** . Sarà necessario più avanti.
1. Nel riquadro sinistro selezionare **certificati & segreti** > **nuovo segreto client**. Immettere una descrizione, selezionare la durata della validità e selezionare **Aggiungi**.
1. Copiare il valore visualizzato nella pagina **certificati & segreti** . Una volta lasciata, la pagina non verrà più visualizzata.

    > [!IMPORTANT]
    > Il valore del segreto client (password) è una credenziale di sicurezza importante. Non condividerla con altri e non distribuirla all'interno di un'applicazione client.

## <a name="secrets"> </a>Aggiungere le informazioni sull'account Microsoft all'applicazione del servizio app

1. Passare all'applicazione nella [Azure portal].
1. Selezionare **impostazioni** > **autenticazione/autorizzazione**e assicurarsi che **l'autenticazione del servizio app** sia **attiva**.
1. In **Provider di autenticazione** fare clic su **Azure Active Directory**. Selezionare **Avanzate** in **modalità di gestione**. Incollare l'ID dell'applicazione (client) e il segreto client ottenuti in precedenza. Usare **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** per il campo **URL autorità di certificazione** .
1. Selezionare **OK**.

   Il servizio app fornisce l'autenticazione ma non limita l'accesso autorizzato al contenuto del sito e alle API. È necessario autorizzare gli utenti nel codice dell'app.

1. Opzionale Per limitare l'accesso agli utenti account Microsoft, impostare l' **azione da eseguire quando la richiesta non viene autenticata** per l'accesso **con Azure Active Directory**. Quando si imposta questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza anche tutte le richieste non autenticate per l'uso di AAD per l'autenticazione. Si noti che poiché l'URL dell' **autorità emittente** è stato configurato per l'uso del tenant di account Microsoft, solo i account personali avranno esito positivo.

   > [!CAUTION]
   > La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere utili per le app con un home page disponibile pubblicamente, come in molte applicazioni a singola pagina. Per queste applicazioni, **consentire le richieste anonime (nessuna azione)** potrebbe essere preferibile, in modo che l'app avvii manualmente l'autenticazione. Per altre informazioni, vedere [flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Selezionare **Salva**.

È ora possibile usare l'account Microsoft per l'autenticazione nell'app.

## <a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure portal]: https://portal.azure.com/
