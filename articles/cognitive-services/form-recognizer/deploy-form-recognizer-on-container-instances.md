---
title: Esegui il contenitore del riconoscitore del modulo nelle istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore Recognizer form in un'istanza di contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 9a3456eb4d30aa8d163488f558b571dd97c73bf4
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605109"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Distribuire il contenitore dei form Recognizer in istanze di contenitore di Azure

Informazioni su come distribuire il contenitore del sistema di [riconoscimento dei moduli](form-recognizer-container-howto.md) di servizi cognitivi in [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure. Questa procedura illustra la creazione di una risorsa di riconoscimento di un modulo di Azure. Viene quindi illustrato il pull dell'immagine del contenitore associata. Infine, viene evidenziata la possibilità di eseguire l'orchestrazione dei due da un browser. L'uso dei contenitori può spostare l'attenzione degli sviluppatori fuori dalla gestione dell'infrastruttura per concentrarsi invece sullo sviluppo di applicazioni.

> [!IMPORTANT]
> I contenitori di riconoscimento form utilizzano attualmente la versione 1,0 dell'API di riconoscimento form. È possibile accedere alla versione più recente dell'API usando invece il servizio gestito.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

È necessario prima di tutto completare e inviare il [modulo di richiesta dei contenitori di Riconoscimento modulo in Servizi cognitivi](https://aka.ms/FormRecognizerContainerRequestAccess) per richiedere l'accesso al contenitore. In questo modo verrà ance effettuata l'iscrizione a Visione artificiale. Non è necessario iscriversi con il modulo di richiesta di Visione artificiale separatamente. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
