---
title: Panoramica delle unità di calcolo di Azure | Documentazione Microsoft
description: Panoramica del concetto di unità di calcolo di Azure. Le unità di calcolo di Azure rappresentano un modo per confrontare le prestazioni della CPU tra gli SKU di Azure.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 65d83e76d464da997c6933610ae97dc176df7964
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919788"
---
# <a name="azure-compute-unit-acu"></a>Unità di calcolo di Azure

Il concetto di unità di calcolo di Azure offre un modo per confrontare le prestazioni di calcolo (CPU) degli SKU di Azure. In questo modo, sarà più semplice identificare lo SKU più adatto a soddisfare le proprie esigenze di prestazioni. L'unità ACU adotta come standard una macchina virtuale Small (Standard_A1), a cui attribuisce il valore 100. Per tutte le altre SKU sarà quindi possibile valutare la maggiore velocità di elaborazione con cui sono in grado di eseguire un benchmark standard.

> [!IMPORTANT]
> L'unità ACU costituisce solo un'indicazione di riferimento. I risultati possono variare in base al carico di lavoro.
<br>

| Famiglia SKU | ACU\vCPU | vCPU: Core |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 - A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 - A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 - A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 - A11](sizes-previous-gen.md) |225* | 1:1 |
| [D1 - D14](sizes-previous-gen.md) |160-250 | 1:1 |
| [D1_v2 - D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 - DS14](sizes-previous-gen.md) |160-250 | 1:1 |
| [DS1_v2 - DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](fsv2-series.md) |195-210 * | 2:1\*\*\* |
| [F1 - F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s - F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1 - G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 - GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [H](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199-216 * * | 1:1 |
| [HC](hc-series.md) |297-315 * | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 - L80s_v2](lsv2-series.md) |150 - 175** | 2:1 |
| [M](m-series.md) | 160 - 180 | 2:1\*\*\* |

*Le unità ACU usano la tecnologia Intel® Turbo per aumentare la frequenza della CPU e offrire un miglioramento delle prestazioni.  L'entità del miglioramento delle prestazioni può variare in base alle dimensioni della macchina virtuale, al carico di lavoro e agli altri carichi di lavoro in esecuzione sullo stesso host.
**Le unità ACU usano la tecnologia AMD® Boost per aumentare la frequenza della CPU e offrire un miglioramento delle prestazioni.  L'entità del miglioramento delle prestazioni può variare in base alle dimensioni della macchina virtuale, al carico di lavoro e agli altri carichi di lavoro in esecuzione sullo stesso host.
***Con hyperthreading e in grado di eseguire la virtualizzazione annidata

Per altre informazioni sulle diverse dimensioni, leggere quanto segue:

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
