---
title: Dashboard panoramica di Azure Application Insights| Microsoft Docs
description: Monitorare le applicazioni con la funzionalità Dashboard panoramica di Azure Application Insights.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e5188972d9058b85a9765c7d33f6209b37245d7e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669897"
---
# <a name="application-insights-overview-dashboard"></a>Dashboard panoramica di Application Insights

Application Insights ha sempre fornito un riquadro riepilogativo per consentire una valutazione rapida e immediata dello stato e delle prestazioni dell'applicazione. Il nuovo dashboard panoramica offre un'esperienza più flessibile e veloce.

## <a name="how-do-i-test-out-the-new-experience"></a>Come testare la nuova esperienza?

Il nuovo dashboard panoramica ora avvia per impostazione predefinita:

![Riquadro della Panoramica (anteprima)](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Migliori prestazioni

La selezione dell'intervallo di tempo è stata semplificata in una semplice interfaccia con un solo clic.

![Intervallo di tempo](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Le prestazioni complessive sono state notevolmente migliorate. È disponibile l'accesso con un clic a funzionalità comuni, ad esempio **Ricerca** e **Analitica**. Ciascun riquadro KPI con aggiornamento dinamico predefinito fornisce informazioni dettagliate sulle funzionalità Application Insights corrispondenti. Per altre informazioni sulle richieste non riuscite, selezionare **Errori** sotto l'intestazione **Analisi**:

![Errori](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Dashboard dell'applicazione

Il dashboard delle applicazioni sfrutta la tecnologia del dashboard esistente in Azure per fornire una visualizzazione a pannello singolo completamente personalizzabile dello stato e delle prestazioni dell'applicazione.

Per accedere al dashboard predefinito selezionare _Dashboard dell'applicazione_ nell'angolo superiore sinistro.

![Vista dashboard](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Se è la prima volta che si accede al dashboard, verrà avviata una visualizzazione predefinita:

![Vista dashboard](./media/overview-dashboard/0001-dashboard.png)

Se si desidera, è possibile mantenere la visualizzazione predefinita. Oppure è anche possibile aggiungerla ed eliminare dal dashboard per soddisfare al meglio le esigenze del team.

> [!NOTE]
> Tutti gli utenti con accesso alla risorsa di Application Insights condividono la stessa esperienza di dashboard dell'applicazione. Le modifiche apportate da un utente modificheranno la visualizzazione per tutti gli utenti.

Per tornare all'esperienza panoramica, selezionare:

![Pulsante Panoramica](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si seleziona **Configura le impostazioni del riquadro** e si imposta un intervallo di tempo personalizzato superiore a 31 giorni, il dashboard non visualizzerà oltre 31 giorni di dati, anche con la conservazione dei dati predefinita di 90 giorni. Attualmente non esiste alcuna soluzione alternativa per questo comportamento.

## <a name="next-steps"></a>Passaggi successivi

- [Grafici a imbuto](../../azure-monitor/app/usage-funnels.md)
- [Conservazione](../../azure-monitor/app/usage-retention.md)
- [Flussi degli utenti](../../azure-monitor/app/usage-flows.md)
