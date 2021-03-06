---
title: Sviluppare un endpoint SCIM per il provisioning degli utenti nelle app da Azure AD
description: System for Cross-Domain Identity Management (SCIM) standardizza il provisioning utenti automatico. Informazioni su come sviluppare un endpoint SCIM, integrare l'API SCIM con Azure Active Directory e avviare l'automazione del provisioning di utenti e gruppi nelle applicazioni cloud.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42fc10c1e7e88e36e4d2174671702e043fb96538
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2020
ms.locfileid: "78926853"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Compilare un endpoint SCIM e configurare il provisioning utenti con Azure Active Directory (Azure AD)

Gli sviluppatori di applicazioni possono usare l'API di gestione degli utenti di System for Cross-Domain Identity Management (SCIM) per abilitare il provisioning automatico di utenti e gruppi tra l'applicazione e Azure AD. Questo articolo descrive come creare un endpoint SCIM e integrarsi con il servizio di provisioning di Azure AD. La specifica SCIM fornisce uno schema utente comune per il provisioning. Se usato in combinazione con gli standard di federazione come SAML o OpenID Connect, SCIM offre agli amministratori una soluzione end-to-end basata sugli standard per la gestione degli accessi.

SCIM è una definizione standardizzata di due endpoint: un endpoint/Users e un endpoint/groups. Usa verbi REST comuni per creare, aggiornare ed eliminare oggetti e uno schema predefinito per attributi comuni come nome del gruppo, nome utente, nome, cognome e indirizzo di posta elettronica. Le app che offrono un'API REST SCIM 2,0 possono ridurre o eliminare il dolore dovuto all'uso di un'API proprietaria per la gestione degli utenti. Ad esempio, tutti i client SCIM conformi sanno come creare un POST HTTP di un oggetto JSON nell'endpoint/Users per creare una nuova voce utente. Anziché richiedere un'API leggermente diversa per le stesse azioni di base, le app conformi allo standard SCIM possono sfruttare immediatamente i vantaggi di client, strumenti e codice preesistenti. 

![Provisioning da Azure AD a un'app con SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Lo schema dell'oggetto utente standard e le API REST per la gestione definite in SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) consentono a provider di identità e app di integrarsi più facilmente tra loro. Gli sviluppatori di applicazioni che compilano un endpoint SCIM possono integrarsi con qualsiasi client conforme a SCIM senza dover eseguire operazioni personalizzate.

L'automazione del provisioning in un'applicazione richiede la compilazione e l'integrazione di un endpoint SCIM con il client SCIM Azure AD. Eseguire la procedura seguente per avviare il provisioning di utenti e gruppi nell'applicazione. 
    
  * **[Passaggio 1: progettare lo schema utente e gruppo.](#step-1-design-your-user-and-group-schema)** Identificare gli oggetti e gli attributi necessari per l'applicazione e determinare il modo in cui vengono mappati allo schema utente e gruppo supportato dall'implementazione di Azure AD SCIM.

  * **[Passaggio 2: comprendere l'implementazione di Azure AD SCIM.](#step-2-understand-the-azure-ad-scim-implementation)** Comprendere il modo in cui viene implementato il client di Azure AD SCIM e modellare le risposte e la gestione delle richieste del protocollo SCIM.

  * **[Passaggio 3: compilare un endpoint SCIM.](#step-3-build-a-scim-endpoint)** Un endpoint deve essere compatibile con SCIM 2,0 per l'integrazione con il servizio di provisioning di Azure AD. In alternativa, è possibile usare le librerie di Microsoft Common Language Infrastructure (CLI) ed esempi di codice per compilare l'endpoint. Questi esempi sono solo a fini di riferimento e di testing; è consigliabile evitare di codificare l'app di produzione per prendere una dipendenza.

  * **[Passaggio 4: integrare l'endpoint SCIM con il client SCIM Azure AD.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Se l'organizzazione usa un'applicazione di terze parti che implementa il profilo di SCIM 2,0 supportato da Azure AD, è possibile iniziare immediatamente ad automatizzare il provisioning e il deprovisioning di utenti e gruppi.

  * **[Passaggio 5: pubblicare l'applicazione nella raccolta di applicazioni Azure AD.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Per i clienti è facile individuare l'applicazione e configurare facilmente il provisioning. 

![Passaggi per l'integrazione di un endpoint SCIM con Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Passaggio 1: progettare lo schema utente e gruppo

Ogni applicazione richiede attributi diversi per creare un utente o un gruppo. Avviare l'integrazione identificando gli oggetti (utenti, gruppi) e gli attributi (nome, Manager, titolo del processo e così via) richiesti dall'applicazione. Lo standard SCIM definisce uno schema per la gestione di utenti e gruppi. Lo schema utente di base richiede solo tre attributi: **ID** (identificatore definito dal provider di servizi), **externalID** (identificatore definito dal client) e **meta** (metadati di sola lettura gestiti dal provider di servizi). Tutti gli altri attributi sono facoltativi. Oltre allo schema utente principale, lo standard SCIM definisce un'estensione utente aziendale e un modello per l'estensione dello schema utente per soddisfare le esigenze dell'applicazione. Se, ad esempio, l'applicazione richiede un responsabile dell'utente, è possibile usare lo schema utente aziendale per raccogliere il responsabile dell'utente e lo schema principale per raccogliere la posta elettronica dell'utente. Per progettare lo schema, attenersi alla procedura seguente:
  1. Elencare gli attributi richiesti dall'applicazione. Può essere utile suddividere i requisiti negli attributi necessari per l'autenticazione (ad esempio, LoginName e posta elettronica), gli attributi necessari per gestire il ciclo di vita dell'utente (ad esempio, status/Active) e altri attributi necessari per il funzionamento dell'applicazione specifica (ad esempio, Manager, tag).
  2. Controllare se tali attributi sono già definiti nello schema utente principale o nello schema utente aziendale. Se gli attributi necessari non sono inclusi negli schemi utente core o Enterprise, sarà necessario definire un'estensione per lo schema utente che copre gli attributi necessari. Nell'esempio seguente è stata aggiunta un'estensione all'utente per consentire il provisioning di un "tag" per un utente. È preferibile iniziare solo con gli schemi utente core e Enterprise ed espanderlo in altri schemi personalizzati in un secondo momento.  
  3. Eseguire il mapping degli attributi SCIM agli attributi utente in Azure AD. Se uno degli attributi definiti nell'endpoint SCIM non ha una controparte chiara nello schema utente Azure AD, è probabile che i dati non vengano archiviati nell'oggetto utente nella maggior parte dei tenant. Valutare se questo attributo può essere facoltativo per la creazione di un utente. Se l'attributo è fondamentale per il funzionamento dell'applicazione, guidare l'amministratore tenant per estendere lo schema o usare un attributo di estensione, come illustrato di seguito per la proprietà "Tags".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabella 1: strutturare gli attributi necessari 
| Passaggio 1: determinare gli attributi richiesti dall'app| Passaggio 2: eseguire il mapping dei requisiti dell'app allo standard SCIM| Passaggio 3: eseguire il mapping degli attributi SCIM agli attributi di Azure AD|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|nome. lastName|lastName|
|workMail|Messaggi di posta elettronica [digitare EQ "Work"]. Value|Posta elettronica|
|manager|manager|manager|
|tag|urn: IETF: params: SCIM: schemas: Extension: 2.0: CustomExtension: Tag|extensionAttribute1|
|status|active|isSoftDeleted (valore calcolato non archiviato nell'utente)|

Lo schema definito in precedenza verrebbe rappresentato usando il payload JSON riportato di seguito. Si noti che, oltre agli attributi necessari per l'applicazione, la rappresentazione JSON include gli attributi "ID", "externalId" e "meta" obbligatori.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>Tabella 2: mapping predefinito degli attributi utente
È quindi possibile usare la tabella seguente per comprendere il modo in cui gli attributi richiesti dall'applicazione potrebbero eseguire il mapping a un attributo in Azure AD e SCIM RFC. È possibile [personalizzare](customize-application-attributes.md) la modalità di mapping degli attributi tra Azure ad e l'endpoint SCIM. Si noti che non è necessario supportare utenti e gruppi o tutti gli attributi mostrati di seguito. Si tratta di un riferimento per il modo in cui gli attributi di Azure AD vengono spesso mappati alle proprietà del protocollo SCIM. 

| Utente Azure Active Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |active |
|department|urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: Department|
| displayName |displayName |
|employeeId|urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn: IETF: params: SCIM: schemas: Extension: Enterprise: 2.0: User: Manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabella 3: mapping predefinito degli attributi di gruppo

| Gruppo di Azure Active Directory | urn: IETF: params: SCIM: schemas: Core: 2.0: Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| Membri di |Membri di |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

Esistono diversi endpoint definiti nella RFC SCIM. È possibile iniziare a usare l'endpoint/User e quindi espandersi da questa posizione. L'endpoint/schemas è utile quando si usano attributi personalizzati o se lo schema cambia di frequente. Consente a un client di recuperare automaticamente lo schema più aggiornato. L'endpoint/bulk è particolarmente utile per i gruppi di supporto. La tabella seguente descrive i vari endpoint definiti nello standard SCIM. L'endpoint/schemas è utile quando si usano attributi personalizzati o se lo schema cambia di frequente. Consente a un client di recuperare automaticamente lo schema più aggiornato. L'endpoint/bulk è particolarmente utile per i gruppi di supporto. La tabella seguente descrive i vari endpoint definiti nello standard SCIM. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabella 4: determinare gli endpoint che si desidera sviluppare
|ENDPOINT|DESCRIZIONE|
|--|--|
|/User|Eseguire operazioni CRUD su un oggetto utente.|
|/Group|Eseguire operazioni CRUD su un oggetto Group.|
|/ServiceProviderConfig|Fornisce informazioni dettagliate sulle funzionalità dello standard SCIM supportate, ad esempio le risorse supportate e il metodo di autenticazione.|
|/ResourceTypes|Specifica i metadati relativi a ogni risorsa|
|/Schemas|Il set di attributi supportati da ogni client e provider di servizi può variare. Mentre un provider di servizi può includere "Name", "title" e "emails", mentre un altro provider di servizi utilizza "Name", "title" e "phoneNumbers". L'endpoint degli schemi consente l'individuazione degli attributi supportati.|
|/Bulk|Le operazioni bulk consentono di eseguire operazioni su una raccolta di grandi dimensioni di oggetti risorsa in un'unica operazione, ad esempio l'appartenenza agli aggiornamenti per un gruppo di grandi dimensioni.|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Passaggio 2: comprendere l'implementazione di SCIM Azure AD
> [!IMPORTANT]
> L'ultimo aggiornamento del comportamento dell'implementazione SCIM di Azure AD è stato eseguito il 18 dicembre 2018. Per informazioni sulle modifiche, vedere [Conformità al protocollo SCIM 2.0 del servizio di provisioning utenti di Azure AD](application-provisioning-config-problem-scim-compatibility.md).

Se si compila un'applicazione che supporta un'API di gestione utenti SCIM 2,0, in questa sezione viene descritto in dettaglio il modo in cui viene implementato il client SCIM Azure AD. Viene inoltre illustrato come modellare le risposte e la gestione delle richieste del protocollo SCIM. Una volta implementato l'endpoint SCIM, è possibile testarlo attenendosi alla procedura descritta nella sezione precedente.

All'interno della [specifica del protocollo SCIM 2,0](http://www.simplecloud.info/#Specification), l'applicazione deve soddisfare i requisiti seguenti:

* Supporta la creazione di utenti e, facoltativamente, anche i gruppi, come indicato [nella sezione 3,3 del protocollo scim](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Supporta la modifica di utenti o gruppi con richieste PATCH, come per [la sezione 3.5.2 del protocollo scim](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Supporta il recupero di una risorsa nota per un utente o un gruppo creato in precedenza, come per [la sezione 3.4.1 del protocollo scim](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Supporta l'esecuzione di query su utenti o gruppi, in base alla sezione [3.4.2 del protocollo scim](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Per impostazione predefinita, gli utenti vengono recuperati dal `id` e sottoposti a query in base ai `username` e `externalid`e i gruppi vengono sottoposti a query per `displayName`.  
* Supporta l'esecuzione di query su User by ID e Manager, come per la sezione 3.4.2 del protocollo SCIM.  
* Supporta l'esecuzione di query sui gruppi in base all'ID e al membro, come indicato nella sezione 3.4.2 del protocollo SCIM.  
* Accetta una singola bearer token per l'autenticazione e l'autorizzazione di Azure AD all'applicazione.

Quando si implementa un endpoint SCIM per garantire la compatibilità con Azure AD, attenersi alle linee guida generali seguenti:

* `id` è una proprietà obbligatoria per tutte le risorse. Ogni risposta che restituisce una risorsa deve garantire che ogni risorsa abbia questa proprietà, ad eccezione di `ListResponse` con zero membri.
* La risposta a una richiesta di query/filtro deve essere sempre un `ListResponse`.
* I gruppi sono facoltativi, ma sono supportati solo se l'implementazione di SCIM supporta le richieste PATCH.
* Non è necessario includere l'intera risorsa nella risposta PATCH.
* Microsoft Azure AD utilizza solo gli operatori seguenti:  
    - `eq`
    - `and`
* Non richiedere una corrispondenza con distinzione tra maiuscole e minuscole negli elementi strutturali in SCIM, in particolare PATCH `op` valori dell'operazione, come definito in https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD emette i valori di "op" come `Add`, `Replace`e `Remove`.
* Microsoft Azure AD esegue richieste per recuperare un utente e un gruppo casuali per verificare che l'endpoint e le credenziali siano validi. Viene anche eseguita come parte del flusso di **test della connessione** nella [portale di Azure](https://portal.azure.com). 
* L'attributo su cui è possibile eseguire query sulle risorse deve essere impostato come attributo corrispondente nell'applicazione nel [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [personalizzazione dei mapping degli attributi per il provisioning degli utenti](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>Provisioning e deprovisioning utenti

La figura seguente illustra i messaggi che Azure Active Directory Invia a un servizio SCIM per gestire il ciclo di vita di un utente nell'archivio identità dell'applicazione.  

![Mostra la sequenza di provisioning e deprovisioning utenti](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Sequenza di provisioning e deprovisioning utenti*

### <a name="group-provisioning-and-deprovisioning"></a>Provisioning e deprovisioning di gruppi

Il provisioning e il deprovisioning del gruppo sono facoltativi. Quando implementato e abilitato, nella figura seguente vengono mostrati i messaggi inviati da Azure AD a un servizio SCIM per gestire il ciclo di vita di un gruppo nell'archivio identità dell'applicazione.  Tali messaggi sono diversi dai messaggi sugli utenti in due modi:

* Le richieste di recupero dei gruppi specificano che l'attributo members deve essere escluso da qualsiasi risorsa fornita in risposta alla richiesta.  
* Le richieste per determinare se un attributo reference ha un determinato valore sono richieste relative all'attributo members.  

![Mostra la sequenza di provisioning e deprovisioning del gruppo](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Sequenza di provisioning e deprovisioning del gruppo*

### <a name="scim-protocol-requests-and-responses"></a>Richieste e risposte del protocollo SCIM
Questa sezione fornisce le richieste SCIM di esempio emesse dal client Azure AD SCIM e dalle risposte previste di esempio. Per ottenere risultati ottimali, è necessario codificare l'app in modo che gestisca queste richieste in questo formato e generare le risposte previste.

> [!IMPORTANT]
> Per comprendere come e quando il servizio di provisioning utenti Azure AD emette le operazioni descritte di seguito, vedere la sezione [cicli di provisioning: iniziale e incrementale](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) nel funzionamento del [provisioning](how-provisioning-works.md).

[Operazioni utente](#user-operations)
  - [Crea utente](#create-user) ([richiesta](#request) / [risposta](#response))
  - [Ottieni utente](#get-user) ([richiesta](#request-1) / [risposta](#response-1))
  - [Ottenere l'utente in base alla query](#get-user-by-query) ([richiesta](#request-2) / [risposta](#response-2))
  - [Ottenere l'utente in base alla query: risultati zero](#get-user-by-query---zero-results) ([richiesta](#request-3)
/ [risposta](#response-3))
  - [Aggiornare l'utente [proprietà multivalore]](#update-user-multi-valued-properties) ([richiesta](#request-4) /  [risposta](#response-4))
  - [Aggiornare l'utente [proprietà a valore singolo]](#update-user-single-valued-properties) ([richiesta](#request-5)
/ [risposta](#response-5)) 
  - [Disabilitare l'utente](#disable-user) ([richiesta](#request-14) / 
[risposta](#response-14))
  - [Elimina utente](#delete-user) ([richiesta](#request-6) / 
[risposta](#response-6))


[Operazioni sui gruppi](#group-operations)
  - [Crea gruppo](#create-group) ( [richiesta](#request-7) / [risposta](#response-7))
  - [Ottenere un gruppo](#get-group) ( [richiesta](#request-8) / [risposta](#response-8))
  - [Ottenere Group by DisplayName](#get-group-by-displayname) ([Request](#request-9) / [Response](#response-9))
  - [Gruppo di aggiornamento [attributi non membri]](#update-group-non-member-attributes) ([richiesta](#request-10) /
 [risposta](#response-10))
  - [Gruppo di aggiornamento [Add members]](#update-group-add-members) ( [Request](#request-11) /
[Response](#response-11))
  - [Gruppo di aggiornamento [Remove members]](#update-group-remove-members) ( [Request](#request-12) /
[Response](#response-12))
  - [Elimina gruppo](#delete-group) ([richiesta](#request-13) /
[risposta](#response-13))

### <a name="user-operations"></a>Operazioni utente

* Gli utenti possono eseguire query sugli attributi `userName` o `email[type eq "work"]`.  

#### <a name="create-user"></a>Crea utente

###### <a name="request"></a>Richiesta

*POST/Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Risposta

*HTTP/1.1 201 creato*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Ottieni utente

###### <a name="request-1"></a>Richiesta
*OTTENERE/Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Risposta (utente trovato)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Richiesta
*OTTENERE/Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Risposta (l'utente non è stato trovato. Si noti che i dettagli non sono obbligatori, ma solo dello stato.

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Ottieni utente per query

##### <a name="request-2"></a>Richiesta

*GET/Users? filter = userName EQ "Test_User_dfeef4c5-5681 -4387-B016-bdf221e82081"*

##### <a name="response-2"></a>Risposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Ottenere l'utente in base alla query: risultati zero

##### <a name="request-3"></a>Richiesta

*OTTENERE/Users? filter = userName EQ "utente non esistente"*

##### <a name="response-3"></a>Risposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Aggiornare l'utente [proprietà multivalore]

##### <a name="request-4"></a>Richiesta

*PATCH/Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response-4"></a>Risposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Aggiornare l'utente [proprietà a valore singolo]

##### <a name="request-5"></a>Richiesta

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response-5"></a>Risposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Disabilita utente

##### <a name="request-14"></a>Richiesta

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response-14"></a>Risposta

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Eliminazione di un utente.

##### <a name="request-6"></a>Richiesta

*Elimina/Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Risposta

*HTTP/1.1 204 nessun contenuto*

### <a name="group-operations"></a>Operazioni sui gruppi

* I gruppi devono essere sempre creati con un elenco di membri vuoto.
* I gruppi possono essere sottoposti a query dall'attributo `displayName`.
* L'aggiornamento alla richiesta PATCH di gruppo deve restituire un *contenuto HTTP 204 senza contenuto* nella risposta. Non è consigliabile restituire un corpo con un elenco di tutti i membri.
* Non è necessario supportare la restituzione di tutti i membri del gruppo.

#### <a name="create-group"></a>Creare un gruppo

##### <a name="request-7"></a>Richiesta

*POST/groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response-7"></a>Risposta

*HTTP/1.1 201 creato*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Ottieni gruppo

##### <a name="request-8"></a>Richiesta

*GET/groups/40734ae655284ad3abcc? excludedAttributes = members HTTP/1.1*

##### <a name="response-8"></a>Risposta
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Ottenere Group by displayName

##### <a name="request-9"></a>Richiesta
*GET/groups? excludedAttributes = members & Filter = displayName EQ "displayName" HTTP/1.1*

##### <a name="response-9"></a>Risposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Gruppo di aggiornamento [attributi non membro]

##### <a name="request-10"></a>Richiesta

*PATCH/groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response-10"></a>Risposta

*HTTP/1.1 204 nessun contenuto*

### <a name="update-group-add-members"></a>Gruppo di aggiornamento [Aggiungi membri]

##### <a name="request-11"></a>Richiesta

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-11"></a>Risposta

*HTTP/1.1 204 nessun contenuto*

#### <a name="update-group-remove-members"></a>Gruppo di aggiornamento [Rimuovi membri]

##### <a name="request-12"></a>Richiesta

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-12"></a>Risposta

*HTTP/1.1 204 nessun contenuto*

#### <a name="delete-group"></a>Eliminare un gruppo

##### <a name="request-13"></a>Richiesta

*Elimina/groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Risposta

*HTTP/1.1 204 nessun contenuto*

### <a name="security-requirements"></a>Requisiti di sicurezza
**Versioni del protocollo TLS**

Le uniche versioni del protocollo TLS accettabili sono TLS 1,2 e TLS 1,3. Non sono consentite altre versioni di TLS. Non è consentita alcuna versione di SSL. 
- Le chiavi RSA devono essere di almeno 2.048 bit.
- Le chiavi ECC devono essere di almeno 256 bit, generate usando una curva ellittica approvata


**Lunghezze delle chiavi**

Tutti i servizi devono usare i certificati X. 509 generati usando chiavi crittografiche di lunghezza sufficiente, ovvero:

**Pacchetti di crittografia**

Tutti i servizi devono essere configurati per usare i pacchetti di crittografia seguenti, nell'ordine esatto specificato di seguito. Si noti che se si dispone solo di un certificato RSA, l'installazione dei pacchetti di crittografia ECDSA non ha alcun effetto. </br>

Barra minima suite di crittografia TLS 1,2:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Passaggio 3: creare un endpoint SCIM

Ora che è stato progettato lo schema e si è compreso il Azure AD implementazione di SCIM, è possibile iniziare a sviluppare l'endpoint SCIM. Anziché iniziare da zero e compilare completamente l'implementazione autonomamente, è possibile fare affidamento su una serie di librerie SCIM Open Source pubblicate da SCIM Commuinty.  
Il [codice di riferimento](https://aka.ms/SCIMReferenceCode) di .NET Core open source pubblicato dal team di Azure ad provisioning è una risorsa di questo tipo che consente di avviare lo sviluppo. Dopo aver compilato l'endpoint SCIM, è necessario eseguirne il test. È possibile usare la raccolta di [test di posting](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) forniti come parte del codice di riferimento o eseguire le richieste o le risposte di esempio fornite in [precedenza](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations).  

Nota: il codice di riferimento è utile per iniziare a creare l'endpoint SCIM e viene fornito "così com'è". I contributi della community sono benvenuti per facilitare la compilazione e la gestione del codice. 

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Passaggio 4: integrare l'endpoint SCIM con il client di Azure AD SCIM

Azure AD può essere configurato per eseguire automaticamente il provisioning di utenti e gruppi assegnati ad applicazioni che implementano un profilo specifico del [protocollo SCIM 2,0](https://tools.ietf.org/html/rfc7644). Le specifiche del profilo sono documentate nel [passaggio 2: comprendere l'implementazione di Azure ad scim](#step-2-understand-the-azure-ad-scim-implementation).

Verificare la conformità ai requisiti sopra riportati con il provider dell'applicazione o consultando la documentazione fornita dal provider.

> [!IMPORTANT]
> Il Azure AD implementazione di SCIM si basa sul servizio di provisioning utenti Azure AD, progettato per consentire agli utenti di sincronizzare costantemente gli utenti tra Azure AD e l'applicazione di destinazione e implementa un set molto specifico di operazioni standard. È importante comprendere questi comportamenti per comprendere il comportamento del client di Azure AD SCIM. Per ulteriori informazioni, vedere la sezione [cicli di provisioning: iniziale e incrementale](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) nel funzionamento del [provisioning](how-provisioning-works.md).

### <a name="getting-started"></a>Introduzione

Le applicazioni che supportano il profilo SCIM descritto in questo articolo possono essere connesse ad Azure Active Directory usando la funzionalità "Applicazione non nella raccolta" nella raccolta di applicazioni di Azure AD. Una volta stabilita la connessione, Azure AD esegue un processo di sincronizzazione ogni 40 minuti in cui interroga l'endpoint SCIM dell'applicazione in merito agli utenti e ai gruppi assegnati e li crea o li modifica in base alle istruzioni di assegnazione.

**Per connettere un'applicazione che supporta SCIM:**

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com). Si noti che è possibile ottenere l'accesso a una versione di valutazione gratuita per Azure Active Directory con licenze P2 iscrivendosi al [programma per sviluppatori](https://developer.microsoft.com/office/dev-program)
2. Selezionare **applicazioni aziendali** dal riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
3. Selezionare **+ nuova applicazione** > **tutte** > **applicazione non della raccolta**.
4. Immettere un nome per l'applicazione e selezionare **Aggiungi** per creare un oggetto app. La nuova app viene aggiunta all'elenco di applicazioni aziendali e si apre alla relativa schermata di gestione delle app.

   ![screenshot mostra la raccolta di applicazioni Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Raccolta di applicazioni Azure AD*

5. Nella schermata gestione app selezionare **provisioning** nel riquadro sinistro.
6. Nel menu **Modalità di provisioning** selezionare **Automatica**.

   ![esempio: pagina di provisioning di un'app nel portale di Azure](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Configurazione del provisioning nel portale di Azure*

7. Nel campo **URL tenant** immettere l'URL dell'endpoint SCIM dell'applicazione. Esempio: https://api.contoso.com/scim/
8. Se l'endpoint SCIM richiede un token di connessione OAuth da un'autorità di certificazione diversa da Azure AD, copiare il token di connessione OAuth nel campo **Token segreto** facoltativo. Se questo campo viene lasciato vuoto, Azure AD include un bearer token OAuth emesso da Azure AD a ogni richiesta. Le app che usano Azure AD come provider di identità possono convalidare il token rilasciato da Azure AD. 
   > [!NOTE]
   > ***Non*** è consigliabile lasciare vuoto questo campo e fare affidamento su un token generato da Azure ad. Questa opzione è disponibile principalmente a scopo di test.
9. Selezionare **Test connessione** per fare in modo che Azure Active Directory tenti di connettersi all'endpoint SCIM. Se il tentativo non riesce, vengono visualizzate le informazioni sull'errore.  

    > [!NOTE]
    > **Test connessione** esegue query sull'endpoint SCIM per cercare un utente che non esiste usando un GUID casuale come proprietà corrispondente selezionata nella configurazione di Azure AD. La risposta corretta prevista è HTTP 200 OK con un messaggio ListResponse SCIM vuoto.

10. Se i tentativi di connessione all'applicazione hanno esito positivo, selezionare **Salva** per salvare le credenziali di amministratore.
11. Nella sezione **mapping** sono disponibili due set selezionabili di [mapping degli attributi](customize-application-attributes.md): uno per gli oggetti utente e uno per gli oggetti gruppo. Selezionare ognuno dei due set per esaminare gli attributi sincronizzati da Active Directory di Azure con l'app. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli utenti e i gruppi nell'app per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    > [!NOTE]
    > Facoltativamente, è possibile disattivare la sincronizzazione degli oggetti gruppo disabilitando il mapping relativo ai gruppi.

12. In **Impostazioni** il campo **Ambito** definisce gli utenti e i gruppi che devono essere sincronizzati. Selezionare **Sincronizza solo utenti** e gruppi assegnati (scelta consigliata) per sincronizzare solo gli utenti e i gruppi assegnati nella scheda **utenti e gruppi** .
13. Al termine della configurazione, impostare lo **stato del provisioning** **su on**.
14. Selezionare **Save (Salva** ) per avviare il servizio di provisioning Azure ad.
15. Se si sincronizzano solo utenti e gruppi assegnati (scelta consigliata), assicurarsi di selezionare la scheda **utenti e gruppi** e di assegnare gli utenti o i gruppi che si desidera sincronizzare.

Una volta avviato il ciclo iniziale, è possibile selezionare i **log di provisioning** nel riquadro sinistro per monitorare lo stato di avanzamento, che Mostra tutte le azioni eseguite dal servizio di provisioning nell'app. Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](check-status-user-account-provisioning.md).

> [!NOTE]
> Il ciclo iniziale richiede più tempo delle sincronizzazioni successive, che vengono eseguite approssimativamente ogni 40 minuti, a condizione che il servizio sia in esecuzione.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Passaggio 5: pubblicare l'applicazione nella raccolta di applicazioni Azure AD

Se si sta creando un'applicazione che verrà usata da più di un tenant, è possibile renderla disponibile nella raccolta di applicazioni Azure AD. In questo modo è più semplice per le organizzazioni individuare l'applicazione e configurare il provisioning. La pubblicazione dell'app nella raccolta Azure AD e l'esecuzione del provisioning per altri è facile. Consultare i passaggi [qui](../develop/howto-app-gallery-listing.md) Microsoft collaborerà con l'utente per integrare l'applicazione nella raccolta, testare l'endpoint e rilasciare la [documentazione](../saas-apps/tutorial-list.md) di onboarding per l'uso da parte dei clienti. 

### <a name="gallery-onboarding-checklist"></a>Elenco di controllo per l'onboarding della raccolta
Seguire l'elenco di controllo riportato di seguito per assicurarsi che l'applicazione sia stata caricata rapidamente e che i clienti abbiano un'esperienza di distribuzione senza problemi. Le informazioni verranno raccolte dall'utente durante l'onboarding alla raccolta. 
> [!div class="checklist"]
> * Supportare un endpoint utente e gruppo [SCIM 2,0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) (è necessario solo uno, ma sono consigliati entrambi)
> * Supporta almeno 25 richieste al secondo per ogni tenant (obbligatorio)
> * Stabilire i contatti di progettazione e supporto tecnico per guidare i clienti dopo l'onboarding della raccolta (obbligatorio)
> * 3 credenziali di test non in scadenza per l'applicazione (obbligatorio)
> * Supportare la concessione del codice di autorizzazione OAuth o un token di lunga durata, come descritto di seguito (obbligatorio)
> * Stabilire un punto di contatto tecnico e di supporto per supportare i clienti dopo il caricamento della raccolta (obbligatorio)
> * Supporto dell'aggiornamento di più appartenenze a gruppi con una singola PATCH (scelta consigliata) 
> * Documentare l'endpoint SCIM pubblicamente (scelta consigliata) 
> * [Supporto individuazione schema](https://tools.ietf.org/html/rfc7643#section-6) (scelta consigliata)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorizzazione per il provisioning dei connettori nella raccolta di applicazioni
La specifica SCIM non definisce uno schema specifico di SCIM per l'autenticazione e l'autorizzazione. Si basa sull'uso di standard di settore esistenti. Il client di provisioning Azure AD supporta due metodi di autorizzazione per le applicazioni nella raccolta. 

|Metodo di autorizzazione|Vantaggi|Svantaggi|Supporto|
|--|--|--|--|
|Nome utente e password (non consigliata o supportata da Azure AD)|Facile da implementare|Non sicuro: [la PA $ $Word non è rilevante](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Supportato da caso per caso per le app della raccolta. Non supportato per le app non della raccolta.|
|bearer token di lunga durata|Per i token di lunga durata non è necessario che un utente sia presente. Per gli amministratori è facile usare quando si configura il provisioning.|I token di lunga durata possono essere difficili da condividere con un amministratore senza usare metodi non protetti come la posta elettronica. |Supportato per le app della raccolta e non della raccolta. |
|Concessione del codice di autorizzazione OAuth|I token di accesso hanno una durata molto più breve rispetto alle password e hanno un meccanismo di aggiornamento automatico che non sono disponibili per i token di connessione di lunga durata.  Un utente reale deve essere presente durante l'autorizzazione iniziale, aggiungendo un livello di responsabilità. |Richiede che un utente sia presente. Se l'utente lascia l'organizzazione, il token non è valido ed è necessario completare nuovamente l'autorizzazione.|Supportato per le app della raccolta. Il supporto per le app non della raccolta è in corso.|
|Concessione di credenziali client OAuth|I token di accesso hanno una durata molto più breve rispetto alle password e hanno un meccanismo di aggiornamento automatico che non sono disponibili per i token di connessione di lunga durata. Sia la concessione del codice di autorizzazione che la concessione delle credenziali client creano lo stesso tipo di token di accesso, quindi il passaggio tra questi metodi è trasparente per l'API.  Il provisioning può essere completamente automatizzato e i nuovi token possono essere richiesti automaticamente senza l'intervento dell'utente. ||Non supportato per le app della raccolta e non della raccolta. Il supporto è nel backlog.|

[!NOTE] Non è consigliabile lasciare vuoto il campo token nell'interfaccia utente dell'app personalizzata per la configurazione del provisioning di Azure AD. Il token generato è disponibile principalmente a scopo di test.

**Flusso di concessione del codice di autorizzazione OAuth:** Il servizio di provisioning supporta la [concessione del codice di autorizzazione](https://tools.ietf.org/html/rfc6749#page-24). Dopo aver inviato la richiesta di pubblicazione dell'app nella raccolta, il Team collaborerà con l'utente per raccogliere le informazioni seguenti:
*  URL autorizzazione: URL del client per ottenere l'autorizzazione dal proprietario della risorsa tramite il reindirizzamento dell'agente utente. L'utente viene reindirizzato a questo URL per autorizzare l'accesso. 
*  URL di scambio di token: URL del client per scambiare una concessione di autorizzazione per un token di accesso, in genere con l'autenticazione client.
*  ID client: il server di autorizzazione rilascia al client registrato un identificatore client, ovvero una stringa univoca che rappresenta le informazioni di registrazione fornite dal client.  L'identificatore client non è un segreto. viene esposto al proprietario della risorsa e **non deve** essere usato da solo per l'autenticazione client.  
*  Segreto client: il segreto client è un segreto generato dal server di autorizzazione. Deve essere un valore univoco noto solo al server di autorizzazione. 

Si noti che OAuth V1 non è supportato a causa dell'esposizione del segreto client. OAuth V2 è supportato.  

Procedure consigliate (consigliato ma non obbligatorio):
* Supporta più URL di reindirizzamento. Gli amministratori possono configurare il provisioning da "portal.azure.com" e "aad.portal.azure.com". Il supporto di più URL di reindirizzamento garantisce che gli utenti possano autorizzare l'accesso da uno dei due portale.
* Supporta più segreti per garantire il rinnovo del segreto senza tempi di inattività. 

**Token di porta OAuth di lunga durata:** Se l'applicazione non supporta il flusso di concessione del codice di autorizzazione OAuth, è anche possibile generare un bearer token OAuth di lunga durata che può essere usato da un amministratore per configurare l'integrazione del provisioning. Il token deve essere perpetuo, altrimenti il processo di provisioning viene [messo in quarantena](application-provisioning-quarantine-status.md) quando il token scade. Il token deve essere di dimensioni inferiori a 1 KB.  

Per ulteriori metodi di autenticazione e autorizzazione, informare Microsoft su [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Elenco di controllo di avvio per l'avvio del mercato della raccolta
Per favorire la comprensione e la richiesta dell'integrazione congiunta, è consigliabile aggiornare la documentazione esistente e amplificare l'integrazione nei canali di marketing.  Di seguito è riportato un set di attività di elenco di controllo che è consigliabile completare per supportare l'avvio

* **Conformità a vendite e supporto clienti.** Assicurati che i team di vendita e di supporto siano consapevoli e possano comunicare con le funzionalità di integrazione. Brief Your Sales and Support Team, fornire domande frequenti e includere l'integrazione nei materiali di vendita. 
* **Post di Blog e/o premere release.** Creazione di un post di Blog o di una versione di stampa che descriva l'integrazione congiunta, i vantaggi e le procedure per iniziare. [Esempio: Imprivata e Azure Active Directory premere Release](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Social Media.** Sfrutta i social media come Twitter, Facebook o LinkedIn per promuovere l'integrazione con i tuoi clienti. Assicurarsi di includere @AzureAD per poter reinviare il post. [Esempio: post di Twitter imprivato](https://twitter.com/azuread/status/1123964502909779968)
* **Sito Web di marketing.** Consente di creare o aggiornare le pagine di marketing (ad esempio, pagina di integrazione, pagina dei partner, pagina dei prezzi e così via) per includere la disponibilità dell'integrazione congiunta. [Esempio: pagina di integrazione di Pingboard](https://pingboard.com/org-chart-for), [pagina di integrazione di Smartsheet](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), pagina dei prezzi di [Monday.com](https://monday.com/pricing/) 
* **Documentazione tecnica.** È possibile creare un articolo di Help Center o una documentazione tecnica su come iniziare a usare i clienti. [Esempio: invio + Microsoft Azure Active Directory integrazione.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Comunicazione del cliente.** Avvisa i clienti della nuova integrazione tramite la comunicazione dei clienti (newsletter mensili, campagne di posta elettronica, note sulla versione del prodotto). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Consenti indirizzi IP usati dal servizio di provisioning Azure AD per eseguire richieste SCIM

Determinate app consentono il traffico in ingresso verso l'app. Affinché il servizio di provisioning Azure AD funzioni come previsto, è necessario consentire gli indirizzi IP utilizzati. Per un elenco degli indirizzi IP per ogni tag del servizio/area, vedere il file JSON [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Indirizzi IP di Azure e tag del servizio - Cloud pubblico). È possibile scaricare e programmare questi indirizzi IP nel firewall, se necessario. Gli intervalli IP riservati per il provisioning di Azure AD sono disponibili in "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Articoli correlati

* [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](user-provisioning.md)
* [Personalizzare i mapping degli attributi per il provisioning degli utenti](customize-application-attributes.md)
* [Scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md)
* [Filtri di ambito per il provisioning degli utenti](define-conditional-rules-for-provisioning-user-accounts.md)
* [Notifiche relative al provisioning dell'account](user-provisioning.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](../saas-apps/tutorial-list.md)
