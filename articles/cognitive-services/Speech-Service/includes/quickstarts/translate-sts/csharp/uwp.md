---
title: 'Avvio rapido: Tradurre la voce in voce, C# (piattaforma UWP) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 12/09/2019
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: a0f62db319d54c2db71a86f621985a304dbbb4d2
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925058"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

A questo punto aggiungere il codice XAML che definisce l'interfaccia utente dell'applicazione e aggiungere l'implementazione code-behind C#.

1. In **Esplora soluzioni** aprire `MainPage.xaml`.

1. Nella visualizzazione XAML della finestra di progettazione inserire il frammento di codice XAML seguente nel tag **Grid** (tra `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. In **Esplora soluzioni** aprire il file di origine code-behind `MainPage.xaml.cs` (raggruppato in `MainPage.xaml`).

1. Sostituire tutto il codice in esso contenuto con il frammento di codice seguente:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. Nel gestore `SpeechTranslationFromMicrophone_ButtonClicked` di questo file individuare la stringa `YourSubscriptionKey` e sostituirla con la chiave di sottoscrizione.

1. Nel gestore `SpeechTranslationFromMicrophone_ButtonClicked` individuare la stringa `YourServiceRegion` e sostituirla con l'[area](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione. Ad esempio, usare `westus` per la sottoscrizione di valutazione gratuita.

1. Dalla barra dei menu scegliere **File** > **Salva tutto** per salvare le modifiche.

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

A questo punto è possibile compilare e testare l'applicazione.

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione. Verrà visualizzata la finestra **helloworld**.

   ![Esempio di applicazione di traduzione della piattaforma UWP in C# - Avvio rapido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Fare clic su **Enable Microphone** (Abilita microfono) e, quando viene visualizzata la richiesta di autorizzazione di accesso, fare clic su **Sì**.

   ![Richiesta di autorizzazione di accesso al microfono](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selezionare **Translate speech from the microphone input** (Traduzione vocale da input del microfono) e pronunciare una frase o espressione nel microfono del dispositivo. L'applicazione trasmette il parlato al servizio Voce, che effettua la traduzione in testo (in questo caso in tedesco). Il servizio Voce invia nuovamente il testo tradotto all'applicazione, che visualizza la traduzione nella finestra.

   ![Interfaccia utente di traduzione vocale](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
