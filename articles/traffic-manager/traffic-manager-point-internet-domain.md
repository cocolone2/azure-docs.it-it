---
title: Puntare un dominio Internet a gestione traffico-gestione traffico di Azure
description: In questo articolo vengono fornite istruzioni per scegliere il nome di dominio aziendale per un nome di dominio di Gestione traffico.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: rohink
ms.openlocfilehash: d56e3fe759d2c9dbee9a8f19a6f1a030565c8e4e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938483"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico di Azure

Quando si crea un profilo di Gestione traffico, Azure assegna automaticamente un nome DNS per tale profilo. Per usare un nome dalla zona DNS, creare un record DNS CNAME che esegua il mapping al nome di dominio del profilo di Gestione traffico. È possibile visualizzare il nome di dominio di Gestione traffico nella sezione **Generale** nella pagina di configurazione del profilo di Gestione traffico.

Ad esempio, per scegliere il nome `www.contoso.com` come nome DNS di Gestione traffico `contoso.trafficmanager.net` si crea il record di risorse DNS seguente:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Tutte le richieste di traffico a *www\.contoso.com* vengono indirizzate a *contoso.trafficmanager.NET*.

> [!IMPORTANT]
> Non è possibile scegliere un dominio di secondo livello, come *contoso.com*, per il dominio di Gestione traffico. Gli standard di protocollo DNS non consentono record CNAME per i nomi di dominio di secondo livello.

## <a name="next-steps"></a>Passaggi successivi

* [Metodi di routing di Gestione traffico](traffic-manager-routing-methods.md)
* [Gestione traffico: disabilitare, abilitare o eliminare un profilo](disable-enable-or-delete-a-profile.md)
* [Gestione traffico: disabilitare o abilitare un endpoint](disable-or-enable-an-endpoint.md)
