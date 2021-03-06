---
title: Collegamenti alla documentazione per l'arricchimento tramite intelligenza artificiale
titleSuffix: Azure Cognitive Search
description: Elenco con annotazioni di articoli, esercitazioni, esempi e post di blog relativi ai carichi di lavoro di arricchimento tramite intelligenza artificiale in Ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: d2b25fb93a1e35ffa82cf49c60d181b841b1692d
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616186"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Risorse di documentazione per l'arricchimento tramite intelligenza artificiale in Ricerca cognitiva di Azure

L'arricchimento tramite intelligenza artificiale è una funzionalità dell'indicizzazione di Ricerca cognitiva di Azure che consente di trovare informazioni latenti in origini non di testo e testo indifferenziato, trasformandolo in contenuto abilitato per la ricerca full-text in Ricerca cognitiva di Azure.

Gli articoli seguenti rappresentano la documentazione completa per l'arricchimento tramite intelligenza artificiale.

## <a name="getting-started"></a>Introduzione
+ [Introduzione all'intelligenza artificiale nella ricerca cognitiva di Azure](cognitive-search-concept-intro.md)
+ [Guida introduttiva: creare un di competenze cognitive nell'portale di Azure](cognitive-search-quickstart-blob.md)
+ [Esercitazione: indicizzazione arricchita con intelligenza artificiale](cognitive-search-tutorial-blob.md)
+ [Esempio: creazione di un'abilità personalizzata per l'arricchimento di intelligenza artificiale](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Guida alle procedure
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Come fare riferimento alle annotazioni in un insieme di competenze](cognitive-search-concept-annotations-syntax.md)
+ [Come eseguire la mappatura dei campi a un indice](cognitive-search-output-field-mapping.md)
+ [Come elaborare ed estrarre informazioni dalle immagini](cognitive-search-concept-image-scenarios.md)
+ [Come ricompilare un indice di Ricerca cognitiva di Azure](search-howto-reindex.md)
+ [Come definire un'interfaccia di competenze personalizzata](cognitive-search-custom-skill-interface.md)
+ [Suggerimenti per la risoluzione dei problemi](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Riferimento

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Competenze personalizzate
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Competenze deprecate](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Creare un set di competenze (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Creare un indicizzatore (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Vedere anche

+ [API REST di Ricerca cognitiva di Azure](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indicizzatori in Ricerca cognitiva di Azure](search-indexer-overview.md)
+ [Che cos'è la ricerca cognitiva di Azure?](search-what-is-azure-search.md)
