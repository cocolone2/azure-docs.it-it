---
title: STARTSWITH nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL STARTSWITH in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9ed49c067946186f8b79f67bad0a460113eacb73
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78295709"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 Restituisce un valore booleano che indica se la prima espressione stringa inizia con il secondo.  
  
## <a name="syntax"></a>Sintassi
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr1*  
   Espressione stringa.
  
*str_expr2*  
   Espressione stringa da confrontare con l'inizio della *str_expr1*.

## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente verifica se la stringa "abc" inizia con "b" e "a".  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Questo è il set di risultati.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trarrà vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
