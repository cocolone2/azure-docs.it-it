---
title: "Guida introduttiva: Creare un'istanza con il portale di Azure"
titleSuffix: Azure Database Migration Service
description: Usare il portale di Azure per creare un'istanza del Servizio Migrazione del database di Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/08/2020
ms.openlocfilehash: efbf7d9db91b8c23151ca4337bd4d58f814a7096
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254969"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Guida introduttiva: Creare un'istanza del Servizio Migrazione del database di Azure usando il portale di Azure

In questa guida di avvio rapido viene usato il portale di Azure per creare un'istanza del Servizio Migrazione del database di Azure.  Dopo aver creato il servizio, è possibile usarlo per eseguire la migrazione dei dati da SQL Server locale a un database SQL di Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Nel Web browser passare al [portale di Microsoft Azure](https://portal.azure.com/) e quindi immettere le credenziali per accedere al portale.

La visualizzazione predefinita è il dashboard del servizio.

## <a name="register-the-resource-provider"></a>Registrare il provider di risorse

Prima di creare la prima istanza del servizio Migrazione del database, registrare il provider di risorse Microsoft.DataMigration.

1. Nel portale di Azure selezionare **Tutti i servizi** e quindi **Sottoscrizioni**.

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Creare un'istanza del servizio

1. Selezionare **+ Create a resource** (+ Crea una risorsa) per creare un'istanza del Servizio Migrazione del database di Azure.

2. Cercare "migrazione" nel marketplace, selezionare **Servizio Migrazione del database di Azure** e quindi nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

3. Nella schermata **Crea servizio Migrazione**:

    - Per **Nome servizio** scegliere un nome facile da ricordare e univoco per identificare l'istanza del Servizio Migrazione del database di Azure.
    - Selezionare la **sottoscrizione** di Azure in cui creare l'istanza.
    - Selezionare un gruppo esistente in **Gruppo di risorse** oppure creare un nuovo gruppo.
    - Scegliere la **Posizione** più vicina al server di origine o di destinazione.
    - Selezionare una **Rete virtuale** esistente oppure crearne una nuova.

        La rete virtuale consente al Servizio Migrazione del database di Azure di accedere al database di origine e all'ambiente di destinazione.

        Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale con il portale di Azure](https://aka.ms/vnet).

    - Selezionare Basic: 1 vCore come **piano tariffario**.

        ![Creare il servizio migrazione](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Selezionare **Create** (Crea).

    Dopo qualche istante, viene creata l'istanza del Servizio Migrazione del database di Azure che può essere quindi usata. Il Servizio Migrazione del database di Azure viene visualizzato come illustrato nell'immagine seguente:

    ![Creare il servizio migrazione](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile pulire le risorse create in questa guida introduttiva eliminando il [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md). Per eliminare il gruppo di risorse, passare all'istanza del servizio Migrazione del database di Azure creata. Selezionare il nome **Gruppo di risorse** e quindi selezionare **Elimina gruppo di risorse**. Questa azione elimina tutti gli asset nel gruppo di risorse, nonché il gruppo stesso.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la migrazione di SQL Server locale nel database SQL di Azure](tutorial-sql-server-to-azure-sql.md)