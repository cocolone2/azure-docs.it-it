---
title: Parole chiave SQL per Azure Cosmos DB
description: Informazioni sulle parole chiave SQL per Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 711e961bd5eb1607e2e6f11b0b5762423d78c0e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246578"
---
# <a name="keywords-in-azure-cosmos-db"></a>Parole chiave in Azure Cosmos DB
Questo articolo descrive in dettaglio le parole chiave che possono essere usate nelle query SQL Azure Cosmos DB.

## <a name="between"></a>BETWEEN

Come in ANSI SQL, è possibile usare la parola chiave BETWEEN per esprimere query sugli intervalli di valori stringa o numerici. Ad esempio, la query seguente restituisce tutti gli elementi in cui la qualità del primo figlio è 1-5, inclusi.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Diversamente da SQL ANSI, è anche possibile usare la clausola BETWEEN nella clausola FROM, come nell'esempio seguente.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Diversamente da ANSI SQL, nell'API SQL è possibile esprimere query di intervallo su proprietà di tipi misti. Ad esempio, `grade` potrebbe essere un numero come `5` in alcuni elementi e una stringa come `grade4` in altri. In questi casi, come in JavaScript, il confronto tra i due tipi diversi restituisce `Undefined`, quindi l'elemento viene ignorato.

> [!TIP]
> Per velocizzare i tempi di esecuzione delle query, creare un criterio di indicizzazione che utilizza un tipo di indice di intervallo rispetto a qualsiasi proprietà numerica o percorsi che la clausola BETWEEN filtra.

## <a name="distinct"></a>DISTINCT

La parola chiave DISTINCT elimina i duplicati nella proiezione della query.

In questo esempio la query proietta i valori per ogni cognome:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

I risultati sono:

```json
[
    "Andersen"
]
```

È anche possibile proiettare oggetti univoci. In questo caso, il campo lastName non esiste in uno dei due documenti, quindi la query restituisce un oggetto vuoto.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

I risultati sono:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT può essere usato anche nella proiezione all'interno di una sottoquery:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Questa query proietta una matrice che contiene il valore specificato di ogni figlio con i duplicati rimossi. Questa matrice viene sottoposta a alias come ChildNames e proiettata nella query esterna.

I risultati sono:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Le query con una funzione di sistema di aggregazione e una sottoquery con DISTINCT non sono supportate. Ad esempio, la query seguente non è supportata:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Usare la parola chiave IN per verificare se un valore specificato corrisponde a qualsiasi valore in un elenco. Ad esempio, la query seguente restituisce tutti gli elementi della famiglia in cui la `id` è `WakefieldFamily` o `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Nell'esempio seguente vengono restituiti tutti gli elementi in cui lo stato è uno dei valori specificati:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

L'API SQL fornisce supporto per l' [iterazione su matrici JSON](sql-query-object-array.md#Iteration), con un nuovo costrutto aggiunto tramite la parola chiave in nell'origine da.

Se si include la chiave di partizione nel filtro `IN`, la query verrà automaticamente filtrata solo per le partizioni pertinenti.

## <a name="top"></a>TORNA ALL'INIZIO

La parola chiave TOP restituisce il primo `N` numero di risultati della query in un ordine non definito. Come procedura consigliata, utilizzare TOP con la clausola ORDER BY per limitare i risultati al primo `N` numero di valori ordinati. La combinazione di queste due clausole è l'unico modo per indicare in modo prevedibile quali righe hanno effetto.

È possibile utilizzare TOP con un valore costante, come nell'esempio seguente, oppure con un valore di variabile utilizzando query con parametri.

```sql
    SELECT TOP 1 *
    FROM Families f
```

I risultati sono:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Join](sql-query-join.md)
- [Sottoquery](sql-query-subquery.md)