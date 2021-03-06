---
title: Modelli Aggiungi accuratezza-LUIS
titleSuffix: Azure Cognitive Services
description: Aggiungere modelli modello per migliorare l'accuratezza della stima nelle applicazioni Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311717"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Come aggiungere modelli per migliorare l'accuratezza della stima
Dopo la ricezione di espressioni di endpoint da un'app LUIS, usare un [modello](luis-concept-patterns.md) per migliorare l'accuratezza della stima per le espressioni che rivelano un modello in Word Order e Word Choice. I modelli usano una [sintassi](luis-concept-patterns.md#pattern-syntax) specifica per indicare la posizione di: [entità](luis-concept-entity-types.md), [ruoli](luis-concept-roles.md)di entità e testo facoltativo.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> I modelli includono solo elementi padre di entità apprese dal computer, non sottocomponenti.

## <a name="adding-example-utterances-as-pattern"></a>Aggiunta di espressioni di esempio come modello

Se si desidera aggiungere un modello per un'entità, il modo _più semplice_ consiste nel creare il modello dalla pagina Dettagli finalità. Ciò garantisce che la sintassi corrisponda all'espressione di esempio.

1. Nel [portale di anteprima Luis](https://preview.luis.ai)selezionare l'app dalla pagina **app personali** .
1. Nella pagina dell'elenco **Intents** selezionare il nome dell'espressione di esempio da cui si vuole creare un modello di espressione.
1. Nella pagina Dettagli finalità selezionare la riga per l'espressione di esempio che si vuole usare come espressione modello, quindi selezionare **+ Aggiungi come modello** dalla barra degli strumenti del contesto.

    > [!div class="mx-imgBorder"]
    > ![screenshot della selezione dell'espressione di esempio come modello di modello nella pagina Dettagli finalità.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Nella casella popup selezionare **fatto** nella pagina **conferma modelli** . Non è necessario definire i sottocomponenti, i vincoli o i descrittori delle entità. È sufficiente elencare l'entità Machine-Learned.

    > [!div class="mx-imgBorder"]
    > ![screenshot della conferma dell'espressione di esempio come modello di modello nella pagina Dettagli finalità.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Se è necessario modificare il modello, ad esempio selezionando testo come facoltativo, con le parentesi quadre `[]`, è necessario apportare questa modifica dalla pagina **Patterns (modelli** ).

1. Nella barra di spostamento selezionare **Train** per eseguire il training dell'app con il nuovo modello.

## <a name="add-template-utterance-using-correct-syntax"></a>Aggiungere un'espressione modello usando la sintassi corretta

1. Apri l'applicazione selezionando il corrispettivo nome nella pagina **App personali** e quindi seleziona **Criteri** nel riquadro sinistro, nella sezione **Migliora le prestazioni dell'applicazione**.

    > [!div class="mx-imgBorder"]
    > ![screenshot dell'elenco dei modelli](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Seleziona la finalità corretta per il criterio.

1. Nella casella di testo del modello, digita l'espressione del modello e premi Invio. Quando vuoi inserire il nome dell'entità, usa la sintassi dell'entità corretta del criterio. Inizia la sintassi dell'entità con `{`. Viene visualizzato l'elenco di entità. Selezionare l'entità corretta.

    > [!div class="mx-imgBorder"]
    > ![screenshot dell'entità per pattern](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se l'entità include un [ruolo](luis-concept-roles.md), indicare il ruolo con un solo segno di due punti, `:`, dopo il nome dell'entità, ad esempio `{Location:Origin}`. I ruoli per le entità vengono visualizzati in un elenco. Seleziona il ruolo, quindi fai clic su Invio.

    > [!div class="mx-imgBorder"]
    > ![screenshot dell'entità con](./media/luis-how-to-model-intent-pattern/patterns-4.png) ruolo

    Dopo aver selezionato l'entità corretta, completa l'inserimento del criterio, quindi premi Invio. Al termine dell'inserimento dei criteri [esegui il training](luis-how-to-train.md) dell'app.

    > [!div class="mx-imgBorder"]
    > ![screenshot del modello immesso con entrambi i tipi di entità](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Eseguire il training dell'app dopo la modifica del modello con i criteri
Dopo aver aggiunto, modificato, rimosso o riassegnato un criterio, [esegui il training](luis-how-to-train.md) e [pubblica](luis-how-to-publish-app.md) l'app affinché le modifiche siano attive per le query di endpoint.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Usa barra degli strumenti contestuale

La barra degli strumenti contestuale sopra l'elenco di modelli consente di:

* Ricerca di modelli
* Modificare un criterio
* Riassegnare un criterio singolo a diverse finalità
* Riassegnare diversi criteri a finalità diverse
* Delete-a-singolo modello
* Eliminare diversi criteri
* Filtrare l'elenco dei criteri per entità
* Filter-pattern-list-by-Intent
* Rimuovere il filtro entità o finalità
* Aggiungi un criterio da un'espressione esistente nella pagina delle entità o finalità

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un modello](luis-tutorial-pattern.md) con un modello. tutti i ruoli e con un'esercitazione.
* Informazioni su come [eseguire il training](luis-how-to-train.md) dell'app.
