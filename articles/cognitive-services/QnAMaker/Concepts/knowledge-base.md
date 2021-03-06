---
title: Importazione da origini dati-QnA Maker
description: Una QnA Maker Knowledge base è costituita da un set di set di domande e risposte (QnA) e metadati facoltativi associati a ogni coppia di QnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d47d994366a8057521c1cc2ab1ab8a7ec3393965
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219687"
---
# <a name="importing-from-data-sources"></a>Importazione da origini dati

Una Knowledge base è costituita da set di domande e risposte portati da URL e file pubblici.

## <a name="data-source-locations"></a>Percorsi di origine dati

Il contenuto viene inserito in una Knowledge base da un'origine dati. I percorsi delle origini dati sono **URL o file pubblici**, che non richiedono l'autenticazione.

I [file di SharePoint](../how-to/add-sharepoint-datasources.md), protetti con l'autenticazione, costituiscono l'eccezione. Le risorse di SharePoint devono essere file, non pagine Web. Se l'URL termina con un'estensione Web, ad esempio. ASPX, non verrà importato in QnA Maker da SharePoint.

## <a name="chit-chat-content"></a>Contenuto della chat Chit

Il set di contenuti QnA Chit Chat è disponibile come origine dati di contenuto completa in diversi linguaggi e stili di conversazione. Può trattarsi di un punto di partenza per la personalità del bot e consentirà di risparmiare tempi e costi di scrittura da zero. Informazioni [su come aggiungere](../how-to/chit-chat-knowledge-base.md) questo set di contenuti alla Knowledge base.

## <a name="structured-data-format-through-import"></a>Formato di dati strutturati tramite l'importazione

L'importazione di una Knowledge Base sostituisce il contenuto della Knowledge Base esistente. L'importazione richiede un file strutturato `.tsv` contenente domande e risposte. Queste informazioni consentono QnA Maker raggruppare i set di domande e risposte e di assegnarli a una determinata origine dati.

| Domanda  | Risposta  | Origine| Metadati (1 chiave: 1 valore) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editoriale|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Formato a più turni strutturato tramite l'importazione

È possibile creare conversazioni a più turni in un formato di file `.tsv`. Il formato offre la possibilità di creare le conversazioni a più turni analizzando i log delle chat precedenti (con altri processi, non usando QnA Maker), quindi creare il file di `.tsv` tramite l'automazione. Importare il file per sostituire la Knowledge Base esistente.

> [!div class="mx-imgBorder"]
> ![modello concettuale di 3 livelli di domande a più turni](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

La colonna per un `.tsv`a più turni, specifica della funzionalità a più turni, è **richiesta**. Un esempio `.tsv`, illustrato in Excel, Mostra le informazioni da includere per definire gli elementi figlio a più turni:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**Displayorder** è numerico e il **DisplayText** è un testo che non deve includere Markdown.

> [!div class="mx-imgBorder"]
> ![esempio di domande a più turni come illustrato in Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Esporta come esempio

Se non si è certi di come rappresentare il set di QnA nel file di `.tsv`, creare il set nel portale di QnA Maker, salvare ed esportare la Knowledge base per un esempio di come rappresentare il set.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ciclo di vita di sviluppo di una knowledge](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Vedere anche

Usare le informazioni di riferimento su QnA Maker [Markdown](../reference-markdown-format.md) per formattare le risposte.

[Panoramica di QnA Maker](../Overview/overview.md)

Creare e modificare una Knowledge base con:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Generare una risposta con:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
