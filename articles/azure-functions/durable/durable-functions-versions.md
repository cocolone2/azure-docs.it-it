---
title: Panoramica delle versioni di Durable Functions-funzioni di Azure
description: Informazioni sulle versioni Durable Functions.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152891"
---
# <a name="durable-functions-versions-overview"></a>Panoramica sulle versioni di Durable Functions

*Funzioni permanenti* è un'estensione di [Funzioni di Azure](../functions-overview.md) e [Processi Web di Azure](../../app-service/web-sites-create-web-jobs.md) che consente di scrivere funzioni con stato in un ambiente senza server. L'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii. Se non si ha già familiarità con Durable Functions, vedere la [documentazione di panoramica](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Nuove funzionalità in 2. x

In questa sezione vengono descritte le funzionalità di Durable Functions aggiunte nella versione 2. x.

### <a name="durable-entities"></a>Entità durevoli

In Durable Functions 2. x è stato introdotto un nuovo concetto di [funzioni di entità](durable-functions-entities.md) .

Le funzioni di entità definiscono le operazioni per la lettura e l'aggiornamento di piccole parti di stato, note come *entità durevoli*. Come le funzioni dell'agente di orchestrazione, le funzioni di entità sono funzioni con un tipo di trigger speciale, il *trigger di entità*. Diversamente dalle funzioni dell'agente di orchestrazione, le funzioni di entità non hanno vincoli di codice specifici. Le funzioni di entità, inoltre, gestiscono lo stato in modo esplicito anziché in modo implicito, rappresentandolo tramite flusso di controllo.

Per altre informazioni, vedere l'articolo sulle [entità durevoli](durable-functions-entities.md) .

### <a name="durable-http"></a>HTTP durevole

In Durable Functions 2. x è stata introdotta una nuova funzionalità [http durevole](durable-functions-http-features.md#consuming-http-apis) che consente di:

* Chiamare le API HTTP direttamente dalle funzioni di orchestrazione (con alcune limitazioni documentate).
* Implementare il polling dello stato HTTP 202 sul lato client automatico.
* Supporto incorporato per le [identità gestite di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Per altre informazioni, vedere l'articolo sulle [funzionalità http](durable-functions-http-features.md#consuming-http-apis) .

## <a name="migrate-from-1x-to-2x"></a>Eseguire la migrazione da 1. x a 2. x

In questa sezione viene descritto come eseguire la migrazione della versione 1. x Durable Functions esistente alla versione 2. x per sfruttare le nuove funzionalità.

### <a name="upgrade-the-extension"></a>Aggiornare l'estensione

Installare la versione 2. x dell' [estensione Durable Functions bindings](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) nel progetto. Per altre informazioni, vedere [registrare le estensioni di binding di funzioni di Azure](../functions-bindings-register.md) .

### <a name="update-your-code"></a>Aggiornare il codice

Durable Functions 2. x introduce diverse modifiche di rilievo. Le applicazioni Durable Functions 1. x non sono compatibili con Durable Functions 2. x senza modifiche al codice. In questa sezione sono elencate alcune delle modifiche che è necessario apportare durante l'aggiornamento delle funzioni versione 1. x a 2. x.

#### <a name="hostjson-schema"></a>Schema host. JSON

Durable Functions 2. x usa un nuovo schema host. JSON. Di seguito sono riportate le modifiche principali da 1. x:

* `"storageProvider"` (e la sottosezione `"azureStorage"`) per la configurazione specifica dell'archiviazione.
* `"tracing"` per la configurazione della traccia e della registrazione.
* `"notifications"` (e la sottosezione `"eventGrid"`) per la configurazione delle notifiche di griglia di eventi.

Per informazioni dettagliate, vedere la [documentazione di riferimento di Durable Functions host. JSON](durable-functions-bindings.md#durable-functions-2-0-host-json) .

#### <a name="default-taskhub-name-changes"></a>Modifiche al nome predefinito di taskhub

Nella versione 1. x, se non è stato specificato il nome di un hub attività in host. JSON, il valore predefinito è "DurableFunctionsHub". Nella versione 2. x, il nome predefinito dell'hub attività è ora derivato dal nome dell'app per le funzioni. Per questo motivo, se non è stato specificato un nome di hub attività quando si esegue l'aggiornamento a 2. x, il codice funzionerà con un nuovo hub attività e tutte le orchestrazioni in corso non avranno più l'elaborazione di un'applicazione. Per risolvere il problema, è possibile impostare in modo esplicito il nome dell'hub attività sul valore predefinito V1. x di "DurableFunctionsHub" oppure è possibile seguire le linee guida per la distribuzione con tempo di [inattività zero](durable-functions-zero-downtime-deployment.md) per informazioni dettagliate su come gestire le modifiche di rilievo per le orchestrazioni in corso.

#### <a name="public-interface-changes-net-only"></a>Modifiche all'interfaccia pubblica (solo .NET)

Nella versione 1. x, i vari oggetti di _contesto_ supportati da Durable Functions hanno classi di base astratte destinate all'uso nel testing unità. Come parte di Durable Functions 2. x, queste classi di base astratte vengono sostituite con le interfacce.

La tabella seguente rappresenta le modifiche principali:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` o `IDurableClient` |
| `DurableOrchestrationContext` o `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` o `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

Nel caso in cui una classe base astratta contenesse metodi virtuali, questi metodi virtuali sono stati sostituiti dai metodi di estensione definiti in `DurableContextExtensions`.

#### <a name="functionjson-changes-javascript-and-c-script"></a>modifiche di Function. JSON (JavaScript C# e script)

In Durable Functions 1. x, l'associazione del client di orchestrazione utilizza una `type` di `orchestrationClient`. La versione 2. x usa invece `durableClient`.
