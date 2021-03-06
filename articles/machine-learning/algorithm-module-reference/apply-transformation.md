---
title: 'Applica trasformazione: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Apply Transformation in Azure Machine Learning per modificare un set di dati di input in base a una trasformazione calcolata in precedenza.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395256"
---
# <a name="apply-transformation-module"></a>Modulo Apply Transformation

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Utilizzare questo modulo per modificare un set di dati di input in base a una trasformazione calcolata in precedenza.

Se, ad esempio, sono stati usati i punteggi z per normalizzare i dati di training usando il modulo **Normalize data** , è necessario usare il valore del punteggio z calcolato per il training anche durante la fase di assegnazione del punteggio. In Azure Machine Learning, è possibile salvare il metodo di normalizzazione come trasformazione, quindi utilizzare **Applica trasformazione** per applicare il punteggio z ai dati di input prima del punteggio.

## <a name="how-to-save-transformations"></a>Come salvare le trasformazioni

La finestra di progettazione consente di salvare le trasformazioni dei dati come **set** di dati in modo da poterle utilizzare in altre pipeline.

1. Selezionare un modulo di trasformazione dei dati che sia stato eseguito correttamente.

1. Selezionare la scheda **output + log** .

1. Selezionare l' **icona Salva** per salvare la **trasformazione risultato**.

## <a name="how-to-use-apply-transformation"></a>Come utilizzare Applica trasformazione  
  
1. Aggiungere il modulo **Apply Transformation** alla pipeline. È possibile trovare questo modulo nella sezione **valutazione del punteggio &** della tavolozza dei moduli. 
  
1. Trovare la trasformazione salvata che si vuole usare nei **set di impostazioni** > i set di **impostazioni personali** nella tavolozza dei moduli.

1. Connettere l'output della trasformazione salvata alla porta di input sinistra del modulo **Apply Transformation** .

    Il set di dati deve avere esattamente lo stesso schema (numero di colonne, nomi di colonna e tipi di dati) come set di dati per cui la trasformazione è stata progettata per la prima volta.  
  
1. Connettere l'output del set di dati del modulo desiderato alla porta di input destra del modulo **Apply Transformation** .
  
1. Per applicare una trasformazione al nuovo set di dati, eseguire la pipeline.  

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 