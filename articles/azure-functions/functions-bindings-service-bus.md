---
title: Associazioni del bus di servizio di Azure per Funzioni di Azure
description: Informazioni su come inviare trigger e associazioni del bus di servizio di Azure in funzioni di Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277414"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Associazioni del bus di servizio di Azure per Funzioni di Azure

Funzioni di Azure si integra con il [bus di servizio di Azure](https://azure.microsoft.com/services/service-bus) tramite [trigger e associazioni](./functions-triggers-bindings.md). L'integrazione con il bus di servizio consente di compilare funzioni che reagiscono e inviano messaggi di coda o di argomento.

| Azione | Type |
|---------|---------|
| Esegue una funzione quando viene creato un messaggio di coda o argomento del bus di servizio | [Trigger](./functions-bindings-service-bus-trigger.md) |
| Inviare messaggi del bus di servizio di Azure |[Binding di output](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Aggiungi all'app funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2. x e versioni successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene usato per le librerie di classi .NET mentre il bundle di estensione viene usato per tutti gli altri tipi di applicazioni.

| Linguaggio                                        | Aggiungi da...                                   | Osservazioni 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet]versione 4. x | |
| C#Script, Java, JavaScript, Python, PowerShell | Registrazione del [bundle di estensione]          | L' [estensione degli strumenti di Azure] è consigliata per l'uso con Visual Studio Code. |
| C#Script (solo online in portale di Azure)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di binding esistenti senza dover ripubblicare l'app per le funzioni, vedere [aggiornare le estensioni]. |

[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[Bundle di estensione]: ./functions-bindings-register.md#extension-bundles
[Aggiornare le estensioni]: ./install-update-binding-extensions-manual.md
[Estensione degli strumenti di Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Le app di funzioni 1. x hanno automaticamente un riferimento al pacchetto NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2. x.

## <a name="next-steps"></a>Passaggi successivi

- [Esegue una funzione quando viene creato un messaggio di coda o argomento del bus di servizio (trigger)](./functions-bindings-service-bus-trigger.md)
- [Inviare messaggi del bus di servizio di Azure da funzioni di Azure (associazione di output)](./functions-bindings-service-bus-output.md)
