---
title: Scaricare i profili VPN globali o basati su hub della rete WAN virtuale di Azure | Microsoft Docs
description: Informazioni sulla connettività scalabile da ramo a ramo, sulle aree disponibili e sui partner.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965229"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Scaricare un profilo globale o basato su hub per i client VPN utente

La rete WAN virtuale di Azure offre due tipi di connettività per gli utenti remoti: globale e basato su Hub. Usare le sezioni seguenti per ottenere informazioni su un profilo e scaricarlo.

## <a name="global-profile"></a>Profilo globale

Il profilo punta a un servizio di bilanciamento del carico che include tutti gli hub VPN utente attivi. L'utente viene indirizzato all'hub più vicino alla posizione geografica dell'utente. Questo tipo di connettività è utile quando gli utenti si spostano spesso in posizioni diverse. Per scaricare il profilo **globale** :

1. Passare alla rete WAN virtuale.
2. Fare clic su **configurazione VPN utente**.
3. Evidenziare la configurazione per cui si vuole scaricare il profilo.
4. Fare clic su **Scarica profilo VPN utente WAN virtuale**.

   ![Profilo globale](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Profilo basato su Hub

Il profilo punta a un singolo hub. L'utente può connettersi solo a un hub specifico usando questo profilo. Per scaricare il profilo **basato su Hub** :

1. Passare alla rete WAN virtuale.
2. Fare clic su **Hub** nella pagina panoramica.

    ![Profilo hub 1](./media/global-hub-profile/hub1.png)
3. Fare clic su **VPN utente (da punto a sito)** .
4. Fare clic su **Scarica profilo VPN utente dell'hub virtuale**.

   ![Profilo Hub 2](./media/global-hub-profile/hub2.png)
5. Controllare **eaptls**.
6. Fare clic su **genera e Scarica profilo**.

   ![Profilo Hub 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).
