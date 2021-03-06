---
title: "Esercitazione: Associare un'istanza di Azure Cosmos DB all'applicazione Azure Spring Cloud"
description: Questa esercitazione mostra come associare un'istanza di Azure Cosmos DB all'applicazione Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 1566b6ab59e858217adcf6818e1d62f851f37eb1
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277556"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Associare un database di Azure Cosmos DB all'applicazione Azure Spring Cloud

Anziché configurare manualmente le applicazioni Spring Boot, è possibile associare automaticamente determinati servizi di Azure alle applicazioni tramite Azure Spring Cloud. Questo articolo illustra come associare l'applicazione a un database di Azure Cosmos DB.

Prerequisiti:

* Un'istanza di Azure Spring Cloud distribuita. Per iniziare, seguire la guida di [avvio rapido sulla distribuzione tramite l'interfaccia della riga di comando di Azure](spring-cloud-quickstart-launch-app-cli.md).
* Un account Azure Cosmos DB con un livello minimo di autorizzazioni di Collaboratore.

## <a name="bind-azure-cosmos-db"></a>Associare un'istanza di Azure Cosmos DB

Azure Cosmos DB dispone di cinque tipi diversi di API che supportano l'associazione. La procedura seguente mostra come usarli:

1. Crea un database di Azure Cosmos DB. Per informazioni, vedere la guida di avvio rapido sulla [creazione di un database](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal). 

1. Prendere nota del nome del database. Per questa procedura, il nome del database è **testdb**.

1. Aggiungere una delle dipendenze seguenti al file pom.xml dell'applicazione Azure Spring Cloud. Scegliere la dipendenza appropriata per il tipo di API.

    * Tipo di API: Core (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * Tipo di API: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * Tipo di API: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * Tipo di API: Gremlin (graph)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * Tipo di API: tabella di Azure

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Usare `az spring-cloud app update` per aggiornare la distribuzione corrente oppure usare `az spring-cloud app deployment create` per crearne una nuova. Questi comandi aggiornano o creano l'applicazione con la nuova dipendenza.

1. Passare alla pagina del servizio Azure Spring Cloud nel portale di Azure. Passare al **Dashboard dell'applicazione** e selezionare l'applicazione da associare ad Azure Cosmos DB. Si tratta della stessa applicazione aggiornata o distribuita nel passaggio precedente.

1. Selezionare **Service binding** (Associazione al servizio), quindi selezionare **Create service binding** (Crea associazione al servizio). Per compilare il modulo, selezionare:
   * Per **Tipo di associazione** specificare il valore **Azure Cosmos DB**.
   * Il tipo di API.
   * Il nome del database.
   * L'account Azure Cosmos DB.

    > [!NOTE]
    > Se si usa Cassandra, usare uno spazio delle chiavi per il nome del database.

1. Riavviare l'applicazione selezionando **Riavvia** nella pagina dell'applicazione.

1. Per verificare la corretta associazione del servizio, selezionare il nome dell'associazione e controllarne i dettagli. Il campo `property` sarà simile all'esempio seguente:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come associare l'applicazione Azure Spring Cloud a un database di Azure Cosmos DB. Per informazioni su come associare l'applicazione a una cache di Azure Cache for Redis, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Come eseguire l'associazione a una cache di Azure Cache for Redis](spring-cloud-tutorial-bind-redis.md)
