---
title: Usare i segreti di Azure Key Vault nelle attività della pipeline
description: Informazioni su come recuperare le credenziali archiviate da Azure Key Vault e usarle durante data factory esecuzioni di pipeline.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 09051ad3633ddc720cb34d3d145ccf649fa9cb08
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200113"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Usare i segreti di Azure Key Vault nelle attività della pipeline

È possibile archiviare le credenziali o i valori dei segreti in un Azure Key Vault e usarli durante l'esecuzione della pipeline per passare alle attività.

## <a name="prerequisites"></a>Prerequisites

Questa funzionalità si basa sull'identità gestita data factory.  Scopri come funziona da [identità gestita per data factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) e assicurati che i data factory ne abbiano uno associato.

## <a name="steps"></a>Passaggi

1. Aprire le proprietà del data factory e copiare il valore di ID applicazione identità gestita.

    ![Valore identità gestita](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Aprire i criteri di accesso di Key Vault e aggiungere le autorizzazioni di identità gestite per ottenere ed elencare i segreti.

    ![Criteri di accesso di Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Criteri di accesso di Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Fare clic su **Aggiungi**e quindi su **Salva**.

3. Passare al segreto Key Vault e copiare l'identificatore del segreto.

    ![Identificatore del segreto](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Prendere nota dell'URI del segreto che si desidera ottenere durante l'esecuzione del data factory pipeline.

4. Nella pipeline Data Factory aggiungere una nuova attività Web e configurarla come indicato di seguito.  

    |Proprietà  |valore  |
    |---------|---------|
    |Output sicuro     |True         |
    |URL     |[Valore dell'URI del segreto]? API-Version = 7.0         |
    |Metodo     |GET         |
    |Authentication     |Identità del servizio gestita         |
    |Risorsa        |https://vault.azure.net       |

    ![Attività Web](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > È necessario aggiungere **? API-Version = 7.0** alla fine dell'URI del segreto.  

    > [!CAUTION]
    > Impostare l'opzione di output sicuro su true per impedire che il valore del segreto venga registrato come testo normale.  Qualsiasi altra attività che utilizza questo valore deve avere l'opzione di input sicuro impostata su true.

5. Per usare il valore in un'altra attività, usare la seguente espressione di codice **@activity(' Web1'). output. Value**.

    ![Espressione codice](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare Azure Key Vault per archiviare le credenziali per gli archivi dati e i calcoli, vedere [archiviare le credenziali in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
