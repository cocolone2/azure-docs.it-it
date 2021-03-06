---
title: Come usare il rilevamento automatico della lingua per il riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Speech SDK supporta il rilevamento automatico della lingua per il riconoscimento vocale. Quando si usa questa funzionalità, l'audio fornito viene confrontato con un elenco di lingue specificato e viene determinata la corrispondenza più probabile. Il valore restituito può quindi essere usato per selezionare il modello di lingua usato per la sintesi vocale.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-seven
ms.openlocfilehash: bc438c3e606fefc10e9ffbb64c79f7167d9af062
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122050"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Rilevamento automatico della lingua per il riconoscimento vocale

Il rilevamento automatico della lingua viene usato per determinare la corrispondenza più probabile per l'audio passato all'SDK di riconoscimento vocale rispetto a un elenco di lingue fornite. Il valore restituito dal rilevamento automatico della lingua viene quindi usato per selezionare il modello di lingua per la sintesi vocale, offrendo una trascrizione più accurata. Per visualizzare le lingue disponibili, vedere [supporto](language-support.md)per le lingue.

In questo articolo si apprenderà come usare `AutoDetectSourceLanguageConfig` per costruire un oggetto `SpeechRecognizer` e recuperare il linguaggio rilevato.

> [!IMPORTANT]
> Questa funzionalità è disponibile solo per l'SDK di riconoscimento C#vocale C++ per e Java.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Rilevamento automatico della lingua con l'SDK di riconoscimento vocale

Il rilevamento automatico della lingua dispone attualmente di un limite lato servizi di due lingue per rilevamento. Tenere presente questa limitazione quando si costruisce l'oggetto `AudoDetectSourceLanguageConfig`. Negli esempi seguenti verrà creato un `AutoDetectSourceLanguageConfig`, che verrà quindi usato per costruire una `SpeechRecognizer`.

> [!TIP]
> È anche possibile specificare un modello personalizzato da usare quando si esegue il riconoscimento vocale in un testo. Per altre informazioni, vedere [usare un modello personalizzato per il rilevamento automatico della lingua](#use-a-custom-model-for-automatic-language-detection).

I frammenti di codice seguenti illustrano come usare il rilevamento automatico della lingua nelle app:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "de-DE" });
using (var recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Usare un modello personalizzato per il rilevamento automatico della lingua

Oltre al rilevamento del linguaggio con i modelli di servizio vocale, è possibile specificare un modello personalizzato per il riconoscimento migliorato. Se non viene fornito un modello personalizzato, il servizio utilizzerà il modello di lingua predefinito.

Nei frammenti di codice riportati di seguito viene illustrato come specificare un modello personalizzato nella chiamata al servizio di riconoscimento vocale. Se il linguaggio rilevato è `en-US`, viene utilizzato il modello predefinito. Se il linguaggio rilevato è `fr-FR`, viene utilizzato l'endpoint per il modello personalizzato:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione di riferimento per l'SDK vocale](speech-sdk.md)
