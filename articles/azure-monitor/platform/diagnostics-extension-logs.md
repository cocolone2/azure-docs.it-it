---
title: Usare l'archiviazione BLOB per IIS e l'archiviazione tabelle per gli eventi in monitoraggio di Azure | Microsoft Docs
description: Monitoraggio di Azure è in grado di leggere i log per i servizi di Azure che scrivono dati di diagnostica nell'archivio tabelle o nei log di IIS scritti nell'archivio BLOB.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672396"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Raccogliere i dati dall'estensione diagnostica di Azure ai log di monitoraggio di Azure
L'estensione diagnostica di Azure è un [agente di monitoraggio di Azure](agents-overview.md) che raccoglie i dati di monitoraggio dal sistema operativo guest delle risorse di calcolo di Azure, incluse le macchine virtuali. Questo articolo descrive come raccogliere i dati raccolti dall'estensione di diagnostica da archiviazione di Azure ai log di monitoraggio di Azure.

> [!NOTE]
> Il Log Analytics Agent in monitoraggio di Azure è in genere il metodo preferito per raccogliere dati dal sistema operativo guest nei log di monitoraggio di Azure. Per un confronto dettagliato degli agenti, vedere [Panoramica degli agenti di monitoraggio di Azure](agents-overview.md) .

## <a name="supported-data-types"></a>Tipi di dati supportati
L'estensione diagnostica di Azure archivia i dati in un account di archiviazione di Azure. Per raccogliere questi dati, i log di monitoraggio di Azure devono trovarsi nei percorsi seguenti:

| Tipo di log | Tipo di risorsa | Location |
| --- | --- | --- |
| Log IIS |Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro |wad-iis-logfiles (archivio BLOB) |
| Syslog |Macchine virtuali |LinuxsyslogVer2v0 (archivio tabelle) |
| Eventi operativi di Service Fabric |Nodi di Service Fabric |WADServiceFabricSystemEventTable |
| Eventi di Reliable Actor di Service Fabric |Nodi di Service Fabric |WADServiceFabricReliableActorEventTable |
| Eventi di Reliable Service di Service Fabric |Nodi di Service Fabric |WADServiceFabricReliableServiceEventTable |
| Log eventi di Windows |Nodi di Service Fabric <br> Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro |WADWindowsEventLogsTable (archivio tabelle) |
| Log di Windows ETW |Nodi di Service Fabric <br> Macchine virtuali <br> Ruoli Web <br> Ruoli di lavoro |WADETWEventTable (archivio tabelle) |

## <a name="data-types-not-supported"></a>Tipi di dati non supportati

- Dati sulle prestazioni del sistema operativo guest
- Log di IIS da siti Web di Azure


## <a name="enable-azure-diagnostics-extension"></a>Abilitare l'estensione diagnostica di Azure
Per informazioni dettagliate sull'installazione e la configurazione dell'estensione di diagnostica, vedere [installare e configurare l'estensione diagnostica di Microsoft Azure](diagnostics-extension-windows-install.md) o [usare l'estensione di diagnostica Linux per monitorare le metriche e i log](../../virtual-machines/extensions/diagnostics-linux.md) . In questo modo si consentire di specificare l'account di archiviazione e di configurare la raccolta dei dati che si vuole inviare ai log di monitoraggio di Azure.


## <a name="collect-logs-from-azure-storage"></a>Raccogliere i log da archiviazione di Azure
Usare la procedura seguente per abilitare la raccolta di dati dell'estensione di diagnostica da un account di archiviazione di Azure:

1. Nella portale di Azure passare a **log Analytics aree di lavoro** e selezionare l'area di lavoro.
1. Fare clic su **account di archiviazione log** nella sezione **origini dati dell'area di lavoro** del menu.
2. Fare clic su **Aggiungi**.
3. Selezionare l' **account di archiviazione** che contiene i dati da raccogliere.
4. Selezionare il **tipo di dati** che si desidera raccogliere.
5. Il valore per l'origine viene popolato automaticamente in base al tipo di dati.
6. Fare clic su **OK** per salvare la configurazione.
7. Ripetere per i tipi di dati aggiuntivi.

In circa 30 minuti è possibile visualizzare i dati dall'account di archiviazione nell'area di lavoro Log Analytics. Verranno visualizzati solo i dati scritti nell'archivio dopo l'applicazione della configurazione. L'area di lavoro non legge i dati preesistenti dall'account di archiviazione.

> [!NOTE]
> Il portale non verifica che l'origine esista nell'account di archiviazione o se sono in corso la scrittura di nuovi dati.



## <a name="next-steps"></a>Passaggi successivi

* [Raccogliere i log e le metriche per i servizi di Azure](collect-azure-metrics-logs.md) per i servizi supportati di Azure.
* [Abilitare soluzioni](../../azure-monitor/insights/solutions.md) per fornire informazioni dettagliate sui dati.
* [Usare query di ricerca](../../azure-monitor/log-query/log-query-overview.md) per analizzare i dati.
