---
title: Configurare indirizzi IP privati per le VM-portale di Azure
description: Informazioni su come configurare indirizzi IP privati per le VM mediante il portale di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: kumud
ms.openlocfilehash: b1019b15463a03282c5d1bd8f0a878433d7f488e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199564"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Configurare indirizzi IP privati per una VM mediante il portale di Azure

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

La procedura di esempio seguente prevede che un ambiente semplice sia già creato. Se si vuole eseguire la procedura illustrata in questo documento, creare prima l'ambiente di testing descritto in [Creare una rete virtuale](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Come creare una VM per il test degli indirizzi IP statici privati
Non è possibile impostare un indirizzo IP statico privato durante la creazione di una VM in modalità di distribuzione di Gestione risorse tramite il portale di Azure. È necessario creare innanzitutto la VM, quindi impostare il relativo IP privato come statico.

Per creare una VM denominata *DNS01* nel subnet *FrontEnd* di una VNet denominata *TestVNet*, seguire questa procedura:

1. In un browser passare a https://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **Crea una risorsa** > **Calcolo** > **Windows Server 2012 R2 Datacenter** (notare che l'elenco **Selezionare un modello di distribuzione** mostra già **Gestione risorse**) e quindi fare clic su **Crea**, come mostrato nella figura seguente.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. Nel riquadro **Nozioni di base**, immettere il nome della VM da creare (*DNS01* nello scenario), l'account amministratore locale e la password, come illustrato nella figura seguente.
   
    ![Riquadro Informazioni di base](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Assicurarsi che la **Località** selezionata sia *Stati Uniti centrali*, fare clic su **Seleziona esistente** in **Gruppo di risorse**, fare clic nuovamente su **Gruppo di risorse**, fare clic su *TestRG* e infine fare clic su **OK**.
   
    ![Riquadro Informazioni di base](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. Nel riquadro **Scegli una dimensione** selezionare **Standard A1** e quindi fare clic su **Seleziona**.
   
    ![Riquadro Scegli una dimensione](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. Nel riquadro **Impostazioni** assicurarsi che le proprietà siano impostate con i valori seguenti e quindi fare clic su **OK**.
   
    -**Account di archiviazione**: *vnetstorage*
   
   * **Rete**: *TestVNet*
   * **Subnet**: *FrontEnd*
     
     ![Riquadro Scegli una dimensione](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. Nel riquadro **Riepilogo** fare clic su **OK**. Si noti il riquadro visualizzato di seguito nel dashboard.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

È consigliabile non assegnare staticamente l'indirizzo IP privato assegnato alla macchina virtuale di Azure all'interno del sistema operativo di una macchina virtuale, se non necessario, ad esempio durante l'[assegnazione di più indirizzi IP a una macchina virtuale Windows](virtual-network-multiple-ip-addresses-portal.md). Se si imposta manualmente l'indirizzo IP privato all'interno del sistema operativo, assicurarsi che sia uguale all'indirizzo IP privato assegnato all'[interfaccia di rete](virtual-network-network-interface-addresses.md#change-ip-address-settings) di Azure. In caso contrario, si può perdere la connettività alla macchina virtuale. Altre informazioni sulle impostazioni dell'[indirizzo IP privato](virtual-network-network-interface-addresses.md#private). Non assegnare mai manualmente l'indirizzo IP pubblico assegnato a una macchina virtuale di Azure all'interno del sistema operativo della macchina virtuale.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Come recuperare le informazioni relative all'indirizzo IP privato statico per una VM
Per visualizzare le informazioni dell'indirizzo IP privato statico per la macchina virtuale creata con la procedura descritta sopra, eseguire la procedura seguente.

1. Dal portale fare clic su **ESPLORA TUTTO** > **Macchine virtuali** > **DNS01l** > **Tutte le impostazioni** > **Interfacce di rete** e quindi fare clic sull'unica interfaccia di rete elencata.
   
    ![Distribuzione di un riquadro VM](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. Nel riquadro **Interfaccia di rete** fare clic su **Tutte le impostazioni** > **Indirizzi IP** e notare i valori di **Assegnazione** e **Indirizzo IP**.
   
    ![Distribuzione di un riquadro VM](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Come aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
Per aggiungere un indirizzo IP statico privato per la macchina virtuale creata in precedenza, seguire questa procedura:

1. Dal riquadro **Indirizzi IP** mostrato in precedenza, fare clic su **Statico** in **Assegnazione**.
2. Digitare *192.168.1.101* per **Indirizzo IP** e quindi fare clic su **Salva**.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Se dopo aver fatto clic su **Salva** si nota che l'assegnazione è ancora impostata su **Dinamico**, significa che l'indirizzo IP immesso è già in uso. Provare un indirizzo IP diverso.
> 
> 

È consigliabile non assegnare staticamente l'indirizzo IP privato assegnato alla macchina virtuale di Azure all'interno del sistema operativo di una macchina virtuale, se non necessario, ad esempio durante l'[assegnazione di più indirizzi IP a una macchina virtuale Windows](virtual-network-multiple-ip-addresses-portal.md). Se si imposta manualmente l'indirizzo IP privato all'interno del sistema operativo, assicurarsi che sia uguale all'indirizzo IP privato assegnato all'[interfaccia di rete](virtual-network-network-interface-addresses.md#change-ip-address-settings) di Azure. In caso contrario, si può perdere la connettività alla macchina virtuale. Altre informazioni sulle impostazioni dell'[indirizzo IP privato](virtual-network-network-interface-addresses.md#private). Non assegnare mai manualmente l'indirizzo IP pubblico assegnato a una macchina virtuale di Azure all'interno del sistema operativo della macchina virtuale.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Come rimuovere un indirizzo IP statico privato da una macchina virtuale
Per rimuovere l'indirizzo IP privato statico dalla VM creata in precedenza, attenersi alla procedura seguente:

Dal riquadro **Indirizzi IP** mostrato in precedenza, fare clic su **Dinamico** in **Assegnazione** e quindi fare clic su **Salva**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Impostare gli indirizzi IP all'interno del sistema operativo

È consigliabile non assegnare staticamente l'indirizzo IP privato assegnato alla macchina virtuale di Azure all'interno del sistema operativo di una macchina virtuale, se non necessario, ad esempio durante l'[assegnazione di più indirizzi IP a una macchina virtuale Windows](virtual-network-multiple-ip-addresses-portal.md). Se si imposta manualmente l'indirizzo IP privato all'interno del sistema operativo, assicurarsi che sia uguale all'indirizzo IP privato assegnato all'[interfaccia di rete](virtual-network-network-interface-addresses.md#change-ip-address-settings) di Azure. In caso contrario, si può perdere la connettività alla macchina virtuale. Altre informazioni sulle impostazioni dell'[indirizzo IP privato](virtual-network-network-interface-addresses.md#private). Non assegnare mai manualmente l'indirizzo IP pubblico assegnato a una macchina virtuale di Azure all'interno del sistema operativo della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire le [impostazioni dell'indirizzo IP](virtual-network-network-interface-addresses.md).

