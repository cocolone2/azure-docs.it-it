---
title: Guida alla migrazione da ADAL a MSAL per Android | Azure
description: Informazioni su come eseguire la migrazione dell'app Android ADAL (Azure Active Directory Authentication Library) a Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084047"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Guida alla migrazione da ADAL a MSAL per Android

Questo articolo evidenzia le modifiche che è necessario eseguire per eseguire la migrazione di un'app che usa la libreria di autenticazione Azure Active Directory (ADAL) per usare Microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Evidenziazioni differenze

ADAL funziona con l'endpoint Azure Active Directory versione 1.0. Microsoft Authentication Library (MSAL) funziona con la piattaforma di identità Microsoft, nota in precedenza come endpoint Azure Active Directory v 2.0. La piattaforma di identità Microsoft differisce da Azure Active Directory v 1.0 in quanto:

Supporto:
  - Identità organizzativa (Azure Active Directory)
  - Identità non organizzative, ad esempio Outlook.com, Xbox Live e così via
  - (Solo B2C) Accesso federato con Google, Facebook, Twitter e Amazon

- È compatibile con gli standard:
  - OAuth v 2.0
  - OpenID Connect (OIDC)

L'API pubblica MSAL introduce importanti modifiche, tra cui:

- Un nuovo modello per l'accesso ai token:
  - ADAL consente di accedere ai token tramite il `AuthenticationContext`, che rappresenta il server. MSAL consente di accedere ai token tramite il `PublicClientApplication`, che rappresenta il client. Gli sviluppatori client non devono creare una nuova istanza di `PublicClientApplication` per ogni autorità con cui devono interagire. È necessaria solo una configurazione `PublicClientApplication`.
  - Supporto per la richiesta di token di accesso usando gli ambiti oltre agli identificatori di risorsa.
  - Supporto per il consenso incrementale. Gli sviluppatori possono richiedere gli ambiti quando l'utente accede a più funzionalità nell'app, incluse quelle non incluse durante la registrazione dell'app.
  - Le autorità non vengono più convalidate in fase di esecuzione. Lo sviluppatore dichiara invece un elenco di "autorità note" durante lo sviluppo.
- Modifiche API token:
  - In ADAL `AcquireToken()` per prima cosa esegue una richiesta invisibile all'utente. In caso contrario, esegue una richiesta interattiva. Questo comportamento ha comportato la relyinging di alcuni sviluppatori solo su `AcquireToken`, che ha comportato la richiesta di credenziali da parte dell'utente in modo imprevisto. MSAL richiede che gli sviluppatori siano intenzionali quando l'utente riceve un prompt dell'interfaccia utente.
    - `AcquireTokenSilent` restituisce sempre una richiesta invisibile all'utente che ha esito positivo o negativo.
    - `AcquireToken` restituisce sempre una richiesta che richiede all'utente tramite l'interfaccia utente.
- MSAL supporta l'accesso da un browser predefinito o da una visualizzazione Web incorporata:
  - Per impostazione predefinita, viene usato il browser predefinito nel dispositivo. Questo consente a MSAL di usare lo stato di autenticazione (cookie) che può essere già presente per uno o più account di accesso. Se non è presente alcuno stato di autenticazione, l'autenticazione durante l'autorizzazione tramite MSAL comporta la creazione dello stato di autenticazione (cookie) per il vantaggio di altre applicazioni Web che verranno utilizzate nello stesso browser.
- Nuovo modello di eccezione:
  - Le eccezioni definiscono in modo più chiaro il tipo di errore che si è verificato e ciò che lo sviluppatore deve eseguire per risolverlo.
- MSAL supporta oggetti Parameter per le chiamate `AcquireToken` e `AcquireTokenSilent`.
- MSAL supporta la configurazione dichiarativa per:
  - ID client, URI di reindirizzamento.
  - Browser incorporato rispetto a quello predefinito
  - autorità
  - Impostazioni HTTP come il timeout di lettura e connessione

## <a name="your-app-registration-and-migration-to-msal"></a>Registrazione e migrazione dell'app a MSAL

Non è necessario modificare la registrazione dell'app esistente per usare MSAL. Se si desidera sfruttare il consenso incrementale/progressivo, potrebbe essere necessario esaminare la registrazione per identificare gli ambiti specifici che si desidera richiedere in modo incrementale. Di seguito sono riportate ulteriori informazioni sugli ambiti e sul consenso incrementale.

Nella registrazione dell'app nel portale viene visualizzata la scheda **autorizzazioni API** . Viene visualizzato un elenco di API e autorizzazioni (ambiti) a cui l'app è attualmente configurata per richiedere l'accesso. Viene inoltre visualizzato un elenco dei nomi di ambito associati a ogni autorizzazione API.

### <a name="user-consent"></a>Consenso dell'utente

Con ADAL e l'endpoint AAD V1, il consenso dell'utente per le risorse di cui è proprietario è stato concesso al primo utilizzo. Con MSAL e la piattaforma di identità Microsoft, è possibile richiedere il consenso in modo incrementale. Il consenso incrementale è utile per le autorizzazioni che un utente può prendere in considerazione con privilegi elevati o in caso contrario, se non viene fornito con una spiegazione chiara del motivo per cui è necessaria l'autorizzazione. In ADAL queste autorizzazioni potrebbero avere comportato l'abbandono dell'accesso all'app da parte dell'utente.

> [!TIP]
> Si consiglia di usare il consenso incrementale negli scenari in cui è necessario fornire un contesto aggiuntivo all'utente per informazioni sul motivo per cui l'app necessita di un'autorizzazione.

### <a name="admin-consent"></a>Consenso dell'amministratore

Gli amministratori dell'organizzazione possono fornire il consenso alle autorizzazioni richieste dall'applicazione per conto di tutti i membri della propria organizzazione. Alcune organizzazioni consentono solo agli amministratori di dare il consenso alle applicazioni. Il consenso dell'amministratore richiede l'inclusione di tutte le autorizzazioni e gli ambiti dell'API usati dall'applicazione nella registrazione dell'app.

> [!TIP]
> Anche se è possibile richiedere un ambito usando MSAL per un elemento non incluso nella registrazione dell'app, è consigliabile aggiornare la registrazione dell'app in modo da includere tutte le risorse e gli ambiti a cui un utente può concedere l'autorizzazione.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migrazione dagli ID risorsa agli ambiti

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Autenticare e richiedere l'autorizzazione per tutte le autorizzazioni al primo utilizzo

Se attualmente si usa ADAL e non è necessario usare il consenso incrementale, il modo più semplice per iniziare a usare MSAL consiste nel creare una richiesta `acquireToken` usando il nuovo oggetto `AcquireTokenParameter` e impostando il valore dell'ID della risorsa.

> [!CAUTION]
> Non è possibile impostare entrambi gli ambiti e un ID di risorsa. Il tentativo di impostare entrambi comporterà un `IllegalArgumentException`.

 Questo comporterà lo stesso comportamento V1 usato. Tutte le autorizzazioni richieste nella registrazione dell'app vengono richieste dall'utente durante la prima interazione.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Eseguire l'autenticazione e richiedere le autorizzazioni solo se necessario

Per sfruttare i vantaggi del consenso incrementale, creare un elenco di autorizzazioni (ambiti) che l'app usa dalla registrazione dell'app e organizzarle in due elenchi in base a:

- Quali ambiti si desidera richiedere durante la prima interazione dell'utente con l'app durante l'accesso.
- Le autorizzazioni associate a una funzionalità importante dell'app che dovrà essere spiegata anche all'utente.

Una volta organizzati gli ambiti, organizzare ogni elenco con la risorsa (API) per cui si vuole richiedere un token. Così come qualsiasi altro ambito che si desidera venga autorizzato dall'utente nello stesso momento.

L'oggetto Parameters usato per eseguire la richiesta a MSAL supporta:

- `Scope`: elenco degli ambiti per i quali si desidera richiedere l'autorizzazione e ricevere un token di accesso.
- `ExtraScopesToConsent`: un elenco aggiuntivo di ambiti per i quali si desidera richiedere l'autorizzazione mentre si richiede un token di accesso per un'altra risorsa. Questo elenco di ambiti consente di ridurre al minimo il numero di volte in cui è necessario richiedere l'autorizzazione dell'utente. Il che significa un minor numero di richieste di autorizzazione utente o di consenso.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Eseguire la migrazione da AuthenticationContext a PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Creazione di PublicClientApplication

Quando si usa MSAL, si crea un'istanza di un `PublicClientApplication`. Questo oggetto modella l'identità dell'app e viene usato per eseguire richieste a una o più autorità. Con questo oggetto verrà configurata l'identità del client, l'URI di reindirizzamento, l'autorità predefinita, se usare il browser del dispositivo e la visualizzazione Web incorporata, il livello di log e altro ancora.

È possibile configurare in modo dichiarativo questo oggetto con JSON, che è possibile fornire come file o archiviare come una risorsa all'interno dell'APK.

Sebbene questo oggetto non sia un singleton, internamente USA `Executors` condivise per le richieste interattive e non automatiche.

### <a name="business-to-business"></a>Business to business

In ADAL ogni organizzazione richiesta da token di accesso richiede un'istanza separata del `AuthenticationContext`. In MSAL non è più un requisito. È possibile specificare l'autorità da cui si desidera richiedere un token come parte della richiesta interattiva o invisibile all'utente.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Eseguire la migrazione dalla convalida dell'autorità alle autorità note

MSAL non dispone di un flag per abilitare o disabilitare la convalida dell'autorità. La convalida dell'autorità è una funzionalità di ADAL e nelle prime versioni di MSAL, che impedisce al codice di richiedere token da un'autorità potenzialmente dannosa. MSAL Recupera ora un elenco di autorità note a Microsoft e unisce tale elenco con le autorità specificate nella configurazione.

> [!TIP]
> Se si è un utente di Azure Business to consumer (B2C), questo significa che non è più necessario disabilitare la convalida dell'autorità. In alternativa, includere tutti i criteri di Azure AD B2C supportati come autorità nella configurazione di MSAL.

Se si tenta di usare un'autorità che non è nota a Microsoft e non è inclusa nella configurazione, si otterrà un `UnknownAuthorityException`.

### <a name="logging"></a>Registrazione
È ora possibile configurare la registrazione in modo dichiarativo come parte della configurazione, come indicato di seguito:

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>Eseguire la migrazione da UserInfo a account

In ADAL, il `AuthenticationResult` fornisce un oggetto `UserInfo` utilizzato per recuperare le informazioni sull'account autenticato. Il termine "User", che significava un agente software o umano, è stato applicato in modo da rendere difficile la comunicazione che alcune app supportano un singolo utente, che si tratti di un agente umano o software, che dispone di più account.

Prendere in considerazione un conto bancario. È possibile avere più di un account in più di un istituto finanziario. Quando si apre un account, l'utente riceve le credenziali, ad esempio una scheda bancomat & PIN, utilizzate per accedere al saldo, i pagamenti e così via, per ogni account. Tali credenziali possono essere utilizzate solo nell'istituto finanziario che le ha rilasciate.

Per analogie, ad esempio gli account di un istituto finanziario, è possibile accedere agli account della piattaforma Microsoft Identity usando le credenziali. Queste credenziali sono registrate con o emesse da Microsoft. O da Microsoft per conto di un'organizzazione.

Laddove la piattaforma di identità Microsoft differisce da un istituto finanziario, in questa analogia, la piattaforma di gestione delle identità Microsoft fornisce un Framework che consente a un utente di usare un account e le credenziali associate per accedere alle risorse che appartengono a più utenti e organizzazioni. Questo è come poter usare una scheda rilasciata da una banca, a un altro istituto finanziario. Questa operazione funziona perché tutte le organizzazioni in questione usano la piattaforma di identità Microsoft, che consente l'uso di un account in più organizzazioni. Di seguito è riportato un esempio:

Sam funziona per Contoso.com, ma gestisce macchine virtuali di Azure appartenenti a Fabrikam.com. Per gestire le macchine virtuali di Fabrikam, Sam deve essere autorizzato ad accedervi. È possibile concedere l'accesso aggiungendo l'account di Sam a Fabrikam.com e concedendo al suo account un ruolo che consenta di lavorare con le macchine virtuali. Questa operazione viene eseguita con il portale di Azure.

L'aggiunta dell'account Contoso.com di Sam come membro di Fabrikam.com comporterebbe la creazione di un nuovo record in Fabrikam. com Azure Active Directory per Sam. Il record di Sam nel Azure Active Directory è noto come oggetto utente. In questo caso, tale oggetto utente punterà di nuovo all'oggetto utente di Sam in Contoso.com. L'oggetto utente Fabrikam di Sam è la rappresentazione locale di Sam e viene usato per archiviare le informazioni sull'account associato a Sam nel contesto di Fabrikam.com. In Contoso.com, il titolo di Sam è Senior DevOps Consultant. In Fabrikam, il titolo di Sam è un appaltatore-macchine virtuali. In Contoso.com, Sam non è responsabile, né autorizzato, per gestire le macchine virtuali. In Fabrikam.com, si tratta dell'unica funzione del processo. Tuttavia, Sam ha ancora un solo set di credenziali di cui tenere traccia, ovvero le credenziali emesse da Contoso.com.

Una volta eseguita una chiamata `acquireToken` riuscita, verrà visualizzato un riferimento a un oggetto `IAccount` che può essere utilizzato nelle richieste `acquireTokenSilent` successive.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Se si dispone di un'app che accede a attestazioni relative a un account di ogni tenant in cui è rappresentato l'account, è possibile eseguire il cast di oggetti `IAccount` `IMultiTenantAccount`. Questa interfaccia fornisce una mappa di `ITenantProfiles`, con chiave in base all'ID tenant, che consente di accedere alle attestazioni che appartengono all'account in ognuno dei tenant da cui è stato richiesto un token rispetto all'account corrente.

Le attestazioni nella radice del `IAccount` e `IMultiTenantAccount` contengono sempre le attestazioni del tenant Home. Se non è stata ancora effettuata una richiesta per un token all'interno del tenant principale, questa raccolta sarà vuota.

## <a name="other-changes"></a>Altre modifiche

### <a name="use-the-new-authenticationcallback"></a>Usa il nuovo AuthenticationCallback

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>Eseguire la migrazione alle nuove eccezioni

In ADAL è presente un tipo di eccezione, `AuthenticationException`, che include un metodo per il recupero del valore enum `ADALError`.
In MSAL è presente una gerarchia di eccezioni e ognuna ha un proprio set di codici di errore specifici associati.

Elenco di eccezioni MSAL

|Eccezione  | Descrizione  |
|---------|---------|
| `MsalException`     | Eccezione controllata predefinita generata da MSAL.  |
| `MsalClientException`     | Viene generata se l'errore è lato client. |
| `MsalArgumentException`     | Generata se uno o più argomenti di input non sono validi. |
| `MsalClientException`     | Viene generata se l'errore è lato client. |
| `MsalServiceException`     | Viene generata se l'errore è lato server. |
| `MsalUserCancelException`     | Generata se l'utente ha annullato il flusso di autenticazione.  |
| `MsalUiRequiredException`     | Viene generata se il token non può essere aggiornato in modo invisibile all'utente.  |
| `MsalDeclinedScopeException`     | Generata se uno o più ambiti richiesti sono stati rifiutati dal server.  |
| `MsalIntuneAppProtectionPolicyRequiredException` | Generata se per la risorsa sono abilitati i criteri di protezione MAMCA. |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError MsalException ErrorCode

### <a name="adal-logging-to-msal-logging"></a>Registrazione ADAL alla registrazione MSAL

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
