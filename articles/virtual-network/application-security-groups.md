---
title: Panoramica sui gruppi di sicurezza delle applicazioni di Azure
titlesuffix: Azure Virtual Network
description: Informazioni sull'uso dei gruppi di sicurezza delle applicazioni.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 775ef92a0ca486d1f8a6c44c78a4df04cd5ef467
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274710"
---
# <a name="application-security-groups"></a>Gruppi di sicurezza delle applicazioni

I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione, raggruppando le macchine virtuali e definendo i criteri di sicurezza di rete in base a tali gruppi. È possibile riusare i criteri di sicurezza su larga scala senza gestire manualmente indirizzi IP espliciti. La piattaforma gestisce la complessità degli indirizzi IP espliciti e di più set di regole, consentendo all'utente di concentrarsi sulla logica di business. Per comprendere meglio i gruppi di sicurezza delle applicazioni, considerare l'esempio seguente:

![Gruppi di sicurezza delle applicazioni](./media/security-groups/application-security-groups.png)

Nell'immagine precedente, *NIC1* e *NIC2* sono membri del gruppo di sicurezza delle applicazioni *AsgWeb*. *NIC3* è un membro del gruppo di sicurezza delle applicazioni *AsgLogic*. *NIC4* è un membro del gruppo di sicurezza delle applicazioni *AsgDb*. Anche se in questo esempio ogni interfaccia di rete è membro di un solo gruppo di sicurezza delle applicazioni, un'interfaccia di rete può essere membro di più gruppi di sicurezza delle applicazioni, fino ai [limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Nessuna delle interfacce di rete ha un gruppo di sicurezza di rete associato. *NSG1* è associato a entrambe le subnet e contiene le regole seguenti:

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Questa regola è necessaria per consentire il traffico da Internet verso i server Web. Dato che il traffico in ingresso da Internet viene negato dalla regola di sicurezza predefinita **DenyAllInbound**, non sono necessarie regole aggiuntive per i gruppi di sicurezza delle applicazioni *AsgLogic* o *AsgDb*.

|Priorità|Source (Sorgente)|Porte di origine| Destination | Porte di destinazione | Protocollo | Accesso |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Allow |

## <a name="deny-database-all"></a>Deny-Database-All

Dato che la regola di sicurezza predefinita **AllowVNetInBound** consente tutte le comunicazioni tra le risorse nella stessa rete virtuale, questa regola è necessaria per negare il traffico da tutte le risorse.

|Priorità|Source (Sorgente)|Porte di origine| Destination | Porte di destinazione | Protocollo | Accesso |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Qualsiasi | Nega |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Questa regola consente il traffico dal gruppo di sicurezza delle applicazioni *AsgLogic* al gruppo di sicurezza delle applicazioni *AsgDb*. La priorità di questa regola è superiore a quella della regola *Deny-Database-All*. Di conseguenza, questa regola viene elaborata prima della regola *Deny-Database-All*, quindi il traffico dal gruppo di sicurezza delle applicazioni *AsgLogic* è consentito, mentre tutto il resto del traffico è bloccato.

|Priorità|Source (Sorgente)|Porte di origine| Destination | Porte di destinazione | Protocollo | Accesso |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Allow |

Le regole che specificano un gruppo di sicurezza delle applicazioni come origine o destinazione vengono applicate solo alle interfacce di rete che sono membri del gruppo di sicurezza delle applicazioni. Se l'interfaccia di rete non è membro di un gruppo di sicurezza delle applicazioni, la regola non viene applicata all'interfaccia di rete, anche se il gruppo di sicurezza di rete è associato alla subnet.

I gruppi di sicurezza delle applicazioni hanno i vincoli seguenti:

-    Esistono limiti al numero di gruppi di sicurezza delle applicazioni in una sottoscrizione, oltre ad altri limiti correlati ai gruppi di sicurezza delle applicazioni. Per informazioni dettagliate, vedere [Limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Si può specificare un unico gruppo di sicurezza delle applicazioni come origine e destinazione in una regola di sicurezza. Non è possibile specificare più gruppi di sicurezza delle applicazioni nell'origine o nella destinazione.
- Tutte le interfacce di rete assegnate a un gruppo di sicurezza delle applicazioni devono esistere nella stessa rete virtuale in cui si trova la prima interfaccia di rete assegnata al gruppo di sicurezza delle applicazioni. Se ad esempio la prima interfaccia di rete assegnata a un gruppo di sicurezza delle applicazioni denominato *AsgWeb* si trova nella rete virtuale denominata *VNet1*, tutte le interfacce di rete successive assegnate a *ASGWeb* devono trovarsi in *VNet1*. Non è possibile aggiungere interfacce di rete da reti virtuali diverse allo stesso gruppo di sicurezza delle applicazioni.
- Se si specifica un gruppo di sicurezza delle applicazioni come origine e destinazione in una regola di sicurezza, le interfacce di rete in entrambi i gruppi di sicurezza delle applicazioni devono trovarsi nella stessa rete virtuale. Se ad esempio *AsgLogic* contenesse interfacce di rete di *VNet1* e *AsgDb* contenesse interfacce di rete di *VNet2*, non sarebbe possibile assegnare *AsgLogic* come origine e *AsgDb* come destinazione in una regola. Tutte le interfacce di rete per i gruppi di sicurezza delle applicazioni di origine e di destinazione devono esistere nella stessa rete virtuale.

> [!TIP]
> Per ridurre al minimo il numero di regole di sicurezza e la necessità di modificarle, pianificare i gruppi di sicurezza delle applicazioni necessari e creare regole usando tag di servizio o gruppi di sicurezza delle applicazioni, anziché singoli indirizzi IP o intervalli di indirizzi IP, quando possibile.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un gruppo di sicurezza di rete](tutorial-filter-network-traffic.md).
