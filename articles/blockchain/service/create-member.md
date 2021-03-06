---
title: Creare un membro del servizio Azure Blockchain - Portale di Azure
description: Creare un membro del servizio Azure Blockchain per un consorzio blockchain con il portale di Azure.
ms.date: 01/23/2020
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 03cd4be1228fcf6a501203e6efb40a8b45c30d0c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760777"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Avvio rapido: Creare un membro della blockchain del servizio Azure Blockchain usando il portale di Azure

In questo argomento di avvio rapido si distribuisce un nuovo membro e un consorzio blockchain nel servizio Azure Blockchain usando il portale di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Creare un membro della blockchain

Un membro del servizio Azure Blockchain è un nodo blockchain in una rete blockchain del consorzio privato. Quando si effettua il provisioning di un membro, è possibile creare o partecipare alla rete di un consorzio. Per una rete del consorzio è necessario almeno un membro. Il numero di membri di blockchain necessari per i partecipanti dipende dallo scenario. I partecipanti del consorzio possono avere uno o più membri di blockchain oppure possono condividere i membri con altri partecipanti. Per altre informazioni sui consorzi, vedere [Consorzio del servizio Azure Blockchain](consortium.md).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Crea risorsa** nell'angolo superiore sinistro del portale di Azure.
1. Selezionare **Blockchain** > **Servizio Azure Blockchain (anteprima)** .

    ![Creazione di un servizio](./media/create-member/create-member.png)

    Impostazione | Descrizione
    --------|------------
    Subscription | Selezionare la sottoscrizione di Azure da usare per il servizio. Se si hanno più sottoscrizioni, scegliere quella in cui viene fatturata la risorsa.
    Resource group | Creare un nuovo nome di gruppo di risorse o sceglierne uno esistente nella sottoscrizione.
    Region | Scegliere un'area per creare il membro. La località deve essere la stessa per tutti i membri del consorzio.
    Protocollo | La versione di anteprima del servizio Azure Blockchain supporta il protocollo Quorum.
    Consorzio | Immettere un nome univoco per un nuovo consorzio. Se si accede a un consorzio tramite invito, scegliere il consorzio a cui si sta accedendo. Per altre informazioni sui consorzi, vedere [Consorzio del servizio Azure Blockchain](consortium.md).
    Nome | Scegliere un nome univoco per il membro del servizio Azure Blockchain. Il nome del membro della blockchain può contenere solo lettere minuscole e numeri. Il primo carattere deve essere una lettera. Il valore deve avere una lunghezza compresa tra 2 e 20 caratteri.
    Member account password (Password dell'account del membro) | La password dell'account del membro viene usata per crittografare la chiave privata per l'account Ethereum creato per il membro. L'account del membro account e la password dell'account del membro si usano per la gestione del consorzio.
    Prezzi | Configurazione del nodo e costo del nuovo servizio. Selezionare il collegamento **Cambia** per scegliere tra i livelli **Standard** e **Basic**.
    Node password (Password del nodo) | La password per il nodo della transazione predefinito del membro. Usare la password per l'autenticazione di base quando ci si connette all'endpoint pubblico del nodo della transazione predefinito per il membro della blockchain.

1. Selezionare **Rivedi e crea** per convalidare le impostazioni. Selezionare **Create** (Crea) per effettuare il provisioning del servizio. Il provisioning richiede circa 10 minuti.
1. Per monitorare il processo di distribuzione, selezionare **Notifications** (Notifiche) sulla barra degli strumenti.
1. Dopo la distribuzione, passare al membro della blockchain.

Selezionare **Overview** (Panoramica), in cui è possibile visualizzare le informazioni di base sul servizio tra cui account del membro e indirizzo RootContract.

![Panoramica del membro della blockchain](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il membro creato per l'esercitazione o la guida di avvio rapido successiva. Quando non sono più necessarie, è possibile eliminare le risorse eliminando il gruppo di risorse `myResourceGroup` creato per l'avvio rapido.

Per eliminare il gruppo di risorse:

1. Nel portale di Azure passare a **Gruppo di risorse** nel riquadro di spostamento a sinistra e selezionare il gruppo di risorse da eliminare.
2. Selezionare **Elimina gruppo di risorse**. Confermare l'eliminazione immettendo il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati distribuiti un membro del servizio Azure Blockchain e un nuovo consorzio. Provare l'avvio rapido successivo per usare Azure Blockchain Development Kit per Ethereum per collegarsi a un membro del servizio Azure Blockchain.

> [!div class="nextstepaction"]
> [Usare Visual Studio Code per connettersi al servizio Azure Blockchain](connect-vscode.md)
