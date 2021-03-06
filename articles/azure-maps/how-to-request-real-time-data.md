---
title: Richiedere dati di transito in tempo reale | Mappe Microsoft Azure
description: Richiedi dati in tempo reale usando il servizio Mobility Microsoft Azure maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9710366bdb7d8e86c8abb54b29b8dde3cc315692
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209903"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Richiedi dati in tempo reale usando il servizio Mobility di Azure Maps

Questo articolo illustra come usare il [servizio Mobility](https://aka.ms/AzureMapsMobilityService) di Azure Maps per richiedere dati di transito in tempo reale.

In questo articolo viene spiegato come:


 * Richiedi arrivi successivi in tempo reale per tutte le righe in arrivo a una determinata interruzione
 * Richiedere informazioni in tempo reale per una determinata stazione di ancoraggio della bicicletta.


## <a name="prerequisites"></a>Prerequisiti

Prima di tutto è necessario avere un account Azure Maps e una chiave di sottoscrizione per effettuare chiamate alle API di transito pubblico di Azure maps. Per informazioni, seguire le istruzioni riportate in [creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps) per creare un account Azure maps. Seguire i passaggi in [ottenere la chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account) per ottenere la chiave primaria per l'account. Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).


Questo articolo usa l'[app Postman](https://www.getpostman.com/apps) per compilare le chiamate REST. È possibile usare qualsiasi ambiente di sviluppo API preferito.


## <a name="request-real-time-arrivals-for-a-stop"></a>Richiedi arrivi in tempo reale per un'interruzione

Per richiedere i dati di arrivo in tempo reale di un particolare arresto di transito pubblico, è necessario effettuare una richiesta all' [API arrivi in tempo reale](https://aka.ms/AzureMapsMobilityRealTimeArrivals) del [servizio Mobility](https://aka.ms/AzureMapsMobilityService)di Azure maps. Per completare la richiesta è necessario disporre di **metroID** e **stopID** . Per altre informazioni su come richiedere questi parametri, vedere la guida su come [richiedere route di transito pubbliche](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Si userà "522" come ID metro, ovvero l'ID metro per l'area "Seattle – Tacoma – Bellevue, WA". Usare "522---2060603" come ID di arresto, questo arresto del bus si trova in "ne 24 St & 162nd Ave ne, Bellevue WA". Per richiedere i cinque dati di arrivo in tempo reale successivi, per tutti gli arrivi Live successivi a questa interruzione, seguire questa procedura:

1. Aprire l'app postazione e creare una raccolta per archiviare le richieste. Nella parte superiore dell'app post, selezionare **nuovo**. Nella finestra **Crea nuova** selezionare **raccolta**.  Assegnare un nome alla raccolta e selezionare il pulsante **Crea** .

2. Per creare la richiesta, selezionare nuovamente **nuovo** . Nella finestra **Crea nuova** selezionare **Request**. Immettere un **nome di richiesta** per la richiesta. Selezionare la raccolta creata nel passaggio precedente, come percorso in cui salvare la richiesta. Selezionare quindi **Salva**.

    ![Creare una richiesta in post](./media/how-to-request-transit-data/postman-new.png)

3. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente per creare una richiesta GET. Sostituire `{subscription-key}`con la chiave primaria di Azure maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Dopo una richiesta con esito positivo, si riceverà la risposta seguente.  Si noti che il parametro ' scheduleType ' definisce se l'ora di arrivo stimata è basata sui dati in tempo reale o statici.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Dati in tempo reale per la stazione di ancoraggio della bicicletta

L' [API Get Transit Dock info](https://aka.ms/AzureMapsMobilityTransitDock) consente agli utenti di richiedere informazioni statiche e in tempo reale. Ad esempio, gli utenti possono richiedere informazioni sulla disponibilità e sulle offerte per una bicicletta o una stazione di ancoraggio dello scooter. L' [API Get Transit Dock info](https://aka.ms/AzureMapsMobilityTransitDock) è anche parte del [servizio Mobility](https://aka.ms/AzureMapsMobilityService)di Azure maps.

Per effettuare una richiesta all' [API Get Transit Dock info](https://aka.ms/AzureMapsMobilityTransitDock), è necessario **dockId** per tale stazione. È possibile ottenere l'ID Dock effettuando una richiesta di ricerca all' [API di transito vicina](https://aka.ms/AzureMapsMobilityNearbyTransit) con il parametro **ObjectType** assegnato a "bikeDock". Seguire questa procedura per ottenere i dati in tempo reale di una stazione di ancoraggio per le biciclette.


### <a name="get-dock-id"></a>Ottieni ID Dock

Per ottenere **dockID**, seguire questa procedura per effettuare una richiesta all'API di transito vicina:

1. In postazione fare clic su **nuova richiesta** | **Get request** e denominarla **Get Dock ID**.

2.  Nella scheda generatore selezionare il metodo **Get** http, immettere l'URL della richiesta seguente e fare clic su **Send**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Dopo una richiesta con esito positivo, si riceverà la risposta seguente. Si noti che ora è disponibile l' **ID** nella risposta, che può essere usato in seguito come parametro di query nella richiesta all'API Get Transit Dock info.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Ottenere lo stato di docking bike in tempo reale

Attenersi alla procedura seguente per effettuare una richiesta all'API Get Transit Dock info per ottenere i dati in tempo reale per il Dock selezionato.

1. In post, fare clic su **nuova richiesta** | **Get request** e denominarla **Get Real-Time Dock data**.

2.  Nella scheda generatore selezionare il metodo **Get** http, immettere l'URL della richiesta seguente e fare clic su **Send**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Dopo una richiesta con esito positivo, si riceverà una risposta della struttura seguente:

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come richiedere dati di transito tramite il servizio Mobility:

> [!div class="nextstepaction"]
> [Come richiedere dati di transito](how-to-request-transit-data.md)

Esplorare la documentazione dell'API del servizio Mobility di Azure Maps:

> [!div class="nextstepaction"]
> [Documentazione dell'API del servizio Mobility](https://aka.ms/AzureMapsMobilityService)
