---
title: Serie DC-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie DC.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: fd8f01f0fc7180d271404ffee4496ff9cbac7222
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205926"
---
# <a name="preview-dcv2-series"></a>Anteprima: serie DCv2


La serie DCv2 può aiutare a proteggere la riservatezza e l'integrità dei dati e del codice durante l'elaborazione nel cloud pubblico. Questi computer sono supportati dall'ultima generazione del processore Intel XEON E-2288G con la tecnologia SGX. Con la tecnologia Intel Turbo Boost, questi computer possono arrivare fino a 5.0 GHz. Le istanze della serie DCv2 consentono ai clienti di creare applicazioni sicure basate su enclave per proteggere il codice e i dati in uso.

I casi d'uso di esempio includono la condivisione di dati multiparte riservati, il rilevamento delle frodi, il riciclaggio Anti-Money, blockchain, l'analisi dell'utilizzo riservato, l'analisi Intelligence e l'apprendimento automatico riservato.

Archiviazione Premium: supportato *

Caching archiviazione Premium: supportato *

Live Migration: non supportato

Aggiornamenti con mantenimento della memoria: non supportato

\* Ad eccezione di Standard_DC8_v2



| Dimensione             | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | NIC massimo/larghezza di banda di rete prevista (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- Le macchine virtuali della serie DCv2 sono [macchine virtuali di seconda generazione](./linux/generation-2.md#creating-a-generation-2-vm) e supportano solo immagini `Gen2`.
- Attualmente disponibile solo in Regno Unito meridionale.
- Generazione precedente di macchine virtuali di calcolo riservate: [serie DC](sizes-previous-gen.md)
- Creare VM DCv2 usando il portale [di Azure creare una VM-portale](./linux/quick-create-portal.md)



## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
