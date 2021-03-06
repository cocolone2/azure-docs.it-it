---
title: Spostare l'app Web che esegue l'accesso degli utenti all'ambiente di produzione-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'app Web per l'accesso degli utenti (passare alla produzione)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6c486e59f32afd09a9934ae2298172ccb4ee2414
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768098"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>App Web che esegue l'accesso agli utenti: passa alla produzione

Ora che si è appreso come ottenere un token per chiamare le API Web, si apprenderà come spostarlo in produzione.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

### <a name="same-site"></a>Stesso sito

Assicurarsi di comprendere i possibili problemi con le nuove versioni del browser Chrome

> [!div class="nextstepaction"]
> [Come gestire le modifiche ai cookie navigava sullostesso sito nel browser Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Scenario per la chiamata di API Web

Dopo che l'app Web ha effettuato l'accesso agli utenti, può chiamare le API Web per conto degli utenti connessi. La chiamata di API Web dall'app Web è l'oggetto dello scenario seguente:

> [!div class="nextstepaction"]
> [App Web che chiama le API Web](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Approfondimento: esercitazione sull'app Web ASP.NET Core

Altre informazioni su altri modi per accedere agli utenti con questa ASP.NET Core esercitazione: 

> [!div class="nextstepaction"]
> [Consentire alle app Web di eseguire l'accesso agli utenti e chiamare le API con la piattaforma di identità Microsoft per gli sviluppatori](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Questa esercitazione progressiva include codice pronto per la produzione per un'app Web, incluso come aggiungere l'accesso con gli account in:

- Organizzazione
- Più organizzazioni
- Account aziendali o dell'Istituto di istruzione o account Microsoft personali
- [Azure AD B2C](https://aka.ms/aadb2c)
- Cloud nazionali

## <a name="sample-code-java-web-app"></a>Codice di esempio: app Web Java

Per altre informazioni sull'app Web Java, vedere questo esempio in GitHub: 

> [!div class="nextstepaction"]
> [Applicazione Web Java che esegue l'accesso agli utenti con la piattaforma di identità Microsoft e chiama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
