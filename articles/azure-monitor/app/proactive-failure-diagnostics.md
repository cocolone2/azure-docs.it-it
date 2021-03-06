---
title: Rilevamento intelligente - Anomalie degli errori in Application Insights | Documentazione Microsoft
description: Avvisa sui cambiamenti insoliti nella frequenza delle richieste non riuscite all'app Web e offre un'analisi diagnostica. Non è necessaria alcuna configurazione.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: e1c07fca3a4eee19e56c313a889e5b86ce2b4c42
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671750"
---
# <a name="smart-detection---failure-anomalies"></a>Rilevamento intelligente - Anomalie degli errori
[Application Insights](../../azure-monitor/app/app-insights-overview.md) avvisa automaticamente l'utente quasi in tempo reale se l'app Web riscontra un aumento anomalo della frequenza delle richieste non riuscite. rilevando un aumento insolito della percentuale di richieste HTTP o di chiamate alle dipendenze segnalate come non riuscite. Per le richieste, le richieste non riuscite hanno in genere un codice di risposta di 400 o superiore. Per facilitare la valutazione e la diagnosi del problema, nei dettagli dell'avviso viene fornita un'analisi delle caratteristiche degli errori e dei dati delle applicazioni correlati. Sono disponibili anche collegamenti al portale di Application Insights per un'ulteriore diagnosi. La funzionalità non necessita di alcuna installazione o configurazione, perché usa algoritmi di Machine Learning per stimare la normale frequenza degli errori.

Questa funzionalità funziona per qualsiasi app Web, ospitata nel cloud o nei propri server, che generano dati di richiesta o di dipendenza dell'applicazione. Se, ad esempio, si dispone di un ruolo di lavoro che chiama [TrackRequest ()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) o [TrackDependency ()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

Dopo aver configurato [Application Insights per il progetto](../../azure-monitor/app/app-insights-overview.md)e se l'app genera una certa quantità minima di dati, il rilevamento intelligente delle anomalie degli errori richiede 24 ore per apprendere il comportamento normale dell'app, prima che venga attivato e possa inviare avvisi.

Ecco un avviso di esempio:

[![](./media/proactive-failure-diagnostics/013.png "Sample smart detection alert showing cluster analysis around failure")](./media/proactive-failure-diagnostics/013.png#lightbox)

I dettagli dell'avviso indicano:

* La frequenza degli errori confrontata al comportamento normale dell’applicazione.
* Il numero di utenti interessati, per far comprendere la portata del problema.
* Un modello caratteristico associato agli errori. In questo esempio sono presenti un particolare codice di risposta, il nome della richiesta (operazione) e la versione dell'applicazione. che indica immediatamente in quale punto del codice cercare. In alternativa si può usare un browser o un sistema operativo client specifico.
* Eccezioni, tracce di log ed errori di dipendenza (database o altri componenti esterni) che sembrano associati ai particolari errori.
* Collegamenti diretti a ricerche rilevanti sui dati in Application Insights.

## <a name="benefits-of-smart-detection"></a>Vantaggi del rilevamento intelligente
I normali [avvisi relativi alla metrica](../../azure-monitor/app/alerts.md) indicano che potrebbe essersi verificato un problema. Tuttavia, il rilevamento intelligente avvia automaticamente il lavoro di diagnostica, eseguendo molto l'analisi che altrimenti sarebbe necessario eseguire manualmente. Si ottengono risultati ben strutturati che consentono individuare rapidamente le cause del problema.

## <a name="how-it-works"></a>Funzionamento
Il rilevamento intelligente monitora i dati ricevuti dall'app e, in particolare, le percentuali di errore. Questa regola conta il numero di richieste per cui la proprietà `Successful request` è false e il numero di chiamate alle dipendenze per cui la proprietà `Successful call` è false. Per le richieste, per impostazione predefinita, `Successful request == (resultCode < 400)` a meno che sia stato scritto codice personalizzato per [filtrare](../../azure-monitor/app/api-filtering-sampling.md#filtering) o generare particolari chiamate [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). 

Le prestazioni dell'applicazione hanno un modello di comportamento tipico. Alcune richieste o chiamate alle dipendenze saranno più soggette a errori di altre e la frequenza degli errori generale potrebbe aumentare proporzionalmente al carico. Il rilevamento intelligente si avvale del servizio Machine Learning per trovare queste anomalie.

Poiché i dati vengono inseriti Application Insights dall'app Web, il rilevamento intelligente confronta il comportamento corrente con i modelli rilevati negli ultimi giorni. Se viene osservato un incremento anomalo della frequenza degli errori rispetto alle prestazioni precedenti, viene attivata un’analisi.

Quando l’analisi viene attivata, il servizio esegue un'analisi cluster della richiesta non riuscita per provare a identificare un modello dei valori che caratterizzano gli errori. 

Nell'esempio precedente l'analisi ha rivelato che la maggior parte degli errori riguardano un codice risultato, un nome richiesta, un URL del server host e un’istanza del ruolo specifici. 

Quando il servizio viene instrumentato con queste chiamate, l'analizzatore cerca un'eccezione e un errore di dipendenza associati alle richieste nel cluster che ha identificato, insieme a un esempio di qualsiasi log di traccia associato a tali richieste.

I risultati dell'analisi vengono inviati all'utente come avviso, a meno che la configurazione attiva preveda di non eseguire questa operazione.

Analogamente agli [avvisi impostati manualmente](../../azure-monitor/app/alerts.md), è possibile esaminare lo stato dell'avviso attivato, che può essere risolto se il problema è stato risolto. Configurare le regole di avviso nella pagina avvisi della risorsa Application Insights. A differenza di altri avvisi, non è però necessario impostare o configurare il rilevamento intelligente. Se necessario, è possibile disabilitarlo o modificare gli indirizzi di posta elettronica di destinazione.

### <a name="alert-logic-details"></a>Dettagli della logica degli avvisi

Gli avvisi vengono attivati da un algoritmo di apprendimento automatico proprietario che impedisce di condividere i dettagli di implementazione esatti. D'altra parte, è comprensibile che talvolta si rende necessario conoscere altri dettagli su come funziona la logica sottostante. Per determinare se deve essere attivato un avviso, vengono valutati i fattori principali seguenti: 

* Analisi della percentuale di errore delle richieste o delle dipendenze in una finestra temporale scorrevole di 20 minuti.
* Confronto tra la percentuale di errore degli ultimi 20 minuti rispetto alla percentuale registrata negli ultimi 40 minuti e negli ultimi sette giorni e individuazione delle deviazioni significative che superano di X volte la deviazione standard.
* Uso di un limite adattivo per la percentuale di errore minima, che varia in base al volume di richieste/dipendenze dell'app.
* Esiste una logica che può risolvere automaticamente la condizione di monitoraggio avviso attivata, se il problema non viene più rilevato per 8-24 ore.

## <a name="configure-alerts"></a>Configurare gli avvisi

È possibile disabilitare la regola di avviso per il rilevamento intelligente dal portale o usando Azure Resource Manager ([vedere l'esempio di modello](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)).

Questa regola di avviso viene creata con un [gruppo di azioni](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) denominato "Application Insights rilevamento intelligente" che contiene le azioni di posta elettronica e webhook e può essere esteso per attivare azioni aggiuntive quando viene attivato l'avviso.

> [!NOTE]
> Le notifiche di posta elettronica inviate da questa regola di avviso vengono ora inviate per impostazione predefinita agli utenti associati ai ruoli di lettore di monitoraggio e di collaboratore monitoraggio della sottoscrizione. Altre informazioni su questo argomento sono disponibili [qui](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification).
> Le notifiche inviate da questa regola di avviso seguono lo [schema di avviso comune](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
>

Aprire la pagina degli avvisi. Le regole di avviso per le anomalie degli errori sono incluse insieme a tutti gli avvisi impostati manualmente e si può verificare se si trova attualmente nello stato di avviso.

[![](./media/proactive-failure-diagnostics/021.png "On the Application Insights resource page, click 'Alerts' tile, then 'Manage alert rules'")](./media/proactive-failure-diagnostics/021.png#lightbox)

Fare clic sull'avviso per configurarlo.

[![](./media/proactive-failure-diagnostics/032.png "Rule configuration screen")](./media/proactive-failure-diagnostics/032.png#lightbox)

Si noti che è possibile disabilitare o eliminare una regola di avviso di anomalie degli errori, ma non è possibile crearne un'altra nella stessa risorsa Application Insights.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Esempio del payload del webhook per gli avvisi di anomalie degli errori

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [["2019-10-30T05:20:00Z",
            0],
            ["2019-10-30T05:40:00Z",
            100],
            ["2019-10-30T06:00:00Z",
            0],
            ["2019-10-30T06:20:00Z",
            0],
            ["2019-10-30T06:40:00Z",
            100],
            ["2019-10-30T07:00:00Z",
            0],
            ["2019-10-30T07:20:00Z",
            0],
            ["2019-10-30T07:40:00Z",
            100],
            ["2019-10-30T08:00:00Z",
            0],
            ["2019-10-30T08:20:00Z",
            0],
            ["2019-10-30T08:40:00Z",
            100],
            ["2019-10-30T17:00:00Z",
            0],
            ["2019-10-30T17:20:00Z",
            0],
            ["2019-10-30T09:00:00Z",
            0],
            ["2019-10-30T09:20:00Z",
            0],
            ["2019-10-30T09:40:00Z",
            100],
            ["2019-10-30T10:00:00Z",
            0],
            ["2019-10-30T10:20:00Z",
            0],
            ["2019-10-30T10:40:00Z",
            100],
            ["2019-10-30T11:00:00Z",
            0],
            ["2019-10-30T11:20:00Z",
            0],
            ["2019-10-30T11:40:00Z",
            100],
            ["2019-10-30T12:00:00Z",
            0],
            ["2019-10-30T12:20:00Z",
            0],
            ["2019-10-30T12:40:00Z",
            100],
            ["2019-10-30T13:00:00Z",
            0],
            ["2019-10-30T13:20:00Z",
            0],
            ["2019-10-30T13:40:00Z",
            100],
            ["2019-10-30T14:00:00Z",
            0],
            ["2019-10-30T14:20:00Z",
            0],
            ["2019-10-30T14:40:00Z",
            100],
            ["2019-10-30T15:00:00Z",
            0],
            ["2019-10-30T15:20:00Z",
            0],
            ["2019-10-30T15:40:00Z",
            100],
            ["2019-10-30T16:00:00Z",
            0],
            ["2019-10-30T16:20:00Z",
            0],
            ["2019-10-30T16:40:00Z",
            100],
            ["2019-10-30T17:30:00Z",
            50]],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [{
                "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
            }],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [{
                "rootJsonNode": null,
                "sectionName": null,
                "displayControls": [{
                    "property": "DetectionSummary",
                    "displayName": "What was detected?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "FormattedOccurenceTime",
                    "displayName": "When did this occur?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "DetectedFailureRate",
                    "displayName": "Detected failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "NormalFailureRate",
                    "displayName": "Normal failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "chartType": "Line",
                    "xAxisType": "Date",
                    "yAxisType": "Percentage",
                    "xAxisName": "",
                    "yAxisName": "",
                    "property": "FailureRateChart",
                    "displayName": "Failure rate over last 12 hours",
                    "type": "Chart",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "defaultLoad": true,
                    "displayConfig": [{
                        "rootJsonNode": null,
                        "sectionName": null,
                        "displayControls": [{
                            "showHeader": false,
                            "columns": [{
                                "property": "Name",
                                "displayName": "Name"
                            },
                            {
                                "property": "Value",
                                "displayName": "Value"
                            }],
                            "property": "tables[0].rows[0][0]",
                            "displayName": "All of the failed requests had these characteristics:",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": true
                        }]
                    }],
                    "property": "ArmSystemEventsRequest",
                    "displayName": "",
                    "type": "ARMRequest",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "showHeader": false,
                    "columns": [{
                        "property": "Link",
                        "displayName": "Link"
                    }],
                    "property": "LinksTable",
                    "displayName": "Links",
                    "type": "Table",
                    "isOptional": false,
                    "isPropertySerialized": false
                }]
            }]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Valutazione e diagnosi di un avviso

Un avviso indica che è stato rilevato un incremento anomalo nella frequenza delle richieste non riuscite. È probabile che si sia verificato un problema con l'app o il relativo ambiente.

Per approfondire l'analisi, fare clic su' Visualizza dettagli completi in Application Insights ' i collegamenti in questa pagina consentono di passare direttamente a una [pagina di ricerca](../../azure-monitor/app/diagnostic-search.md) filtrata per le richieste, le eccezioni, le dipendenze o le tracce pertinenti. 

È anche possibile aprire la [portale di Azure](https://portal.azure.com), passare alla risorsa Application Insights per l'app e aprire la pagina errori.

Se si fa clic su' diagnostica errori ', sarà possibile ottenere altri dettagli e risolvere il problema.

[![](./media/proactive-failure-diagnostics/051.png "Diagnostic search")](./media/proactive-failure-diagnostics/051.png#lightbox)

Dalla percentuale delle richieste e dal numero di utenti interessati è possibile decidere il livello di priorità del problema. Nell'esempio precedente, la percentuale di errori del 78,5% viene confrontata con una frequenza normale pari al 2,2%, indica che è in corso un problema. D'altra parte, sono stati interessati solo 46 utenti. Se fosse l'app, sarebbe possibile valutare la gravità del problema.

In molti casi, sarà possibile diagnosticare rapidamente il problema dal nome della richiesta, dall'eccezione, dall'errore di dipendenza e dai dati di traccia forniti.

In questo esempio è stata generata un'eccezione dal database SQL a causa del raggiungimento del limite di richieste.

[![](./media/proactive-failure-diagnostics/052.png "Failed request details")](./media/proactive-failure-diagnostics/052.png#lightbox)

## <a name="review-recent-alerts"></a>Esaminare gli avvisi recenti

Fare clic su **avvisi** nella pagina della risorsa Application Insights per ottenere gli avvisi generati più di recente:

[![](./media/proactive-failure-diagnostics/070.png "Alerts summary")](./media/proactive-failure-diagnostics/070.png#lightbox)

## <a name="whats-the-difference-"></a>Qual è la differenza
Il rilevamento intelligente delle anomalie degli errori integra altre funzionalità simili ma distinte di Application Insights.

* Gli [avvisi metrica](../../azure-monitor/app/alerts.md) vengono impostati dall'utente e possono monitorare un'ampia gamma di metriche, ad esempio utilizzo della CPU, frequenza delle richieste, tempi di caricamento delle pagine e così via. È possibile usarli per avvisare l'utente, ad esempio, se è necessario aggiungere altre risorse. Al contrario, il rilevamento intelligente delle anomalie degli errori copre una piccola gamma di metriche critiche (attualmente solo la percentuale di richieste non riuscite), progettate per inviare notifiche quasi in tempo reale dopo l'aumento della frequenza delle richieste non riuscite dell'app Web rispetto al comportamento normale dell'app Web. Diversamente dagli avvisi delle metriche, il rilevamento intelligente imposta e aggiorna automaticamente le soglie nelle modifiche della risposta nel comportamento. Il rilevamento intelligente avvia anche il lavoro di diagnostica, risparmiando tempo per la risoluzione dei problemi.

* Il [rilevamento intelligente delle anomalie delle prestazioni](proactive-performance-diagnostics.md) usa l'intelligenza artificiale per individuare modelli insoliti nelle metriche, senza richiedere alcuna configurazione. A differenza del rilevamento intelligente delle anomalie degli errori, tuttavia, lo scopo del rilevamento intelligente delle anomalie delle prestazioni è trovare i segmenti dei molteplici modi di utilizzo che potrebbero essere gestiti in modo non corretto, ad esempio da pagine specifiche in un tipo specifico di browser. L'analisi viene eseguita ogni giorno e se viene trovato un risultato, è probabilmente molto meno urgente di un avviso. Al contrario, l'analisi per le anomalie degli errori viene eseguita continuamente sui dati dell'applicazione in ingresso e si riceverà una notifica entro pochi minuti se le percentuali di errore del server sono maggiori del previsto.

## <a name="if-you-receive-a-smart-detection-alert"></a>Se si riceve un avviso di rilevamento intelligente
*Perché ho ricevuto questo avviso?*

* È stato rilevato un incremento anomalo delle richieste non riuscite rispetto al valore normale del periodo precedente. Dopo l'analisi degli errori e dei dati dell'applicazione associati, si ritiene che sia necessario esaminare il problema.

*La notifica indica la sicura presenza di un problema?*

* Gli avvisi vengono emessi in caso di interruzione dell'app o di riduzione delle prestazioni, ma solo l'utente può comprendere completamente la semantica e l'impatto del problema sull'app o gli utenti.

*Si stanno esaminando i dati dell'applicazione?*

* No. Il servizio è completamente automatico. L'utente riceve le notifiche, ma i dati restano [privati](../../azure-monitor/app/data-retention-privacy.md).

*Va eseguita una sottoscrizione a questo avviso?*

* No. Ogni applicazione che invia i dati della richiesta ha la regola di avviso di rilevamento intelligente.

*È possibile annullare la sottoscrizione oppure ottenere le notifiche inviate ai colleghi?*

* Sì. In Regole di avviso fare clic sulla regola per il rilevamento intelligente per configurarla. È possibile disabilitare l'avviso o modificare i destinatari dell'avviso.

*Non trovo più il messaggio di posta elettronica. Dove trovo le notifiche nel portale?*

* In Log attività. In Azure aprire la risorsa di Application Insights per l'app e quindi selezionare Log attività.

*Alcuni avvisi segnalano problemi noti che è inutile ricevere.*

* È possibile usare la funzionalità di eliminazione [delle regole azione avviso](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-action-rules) .

## <a name="next-steps"></a>Passaggi successivi
Questi strumenti di diagnostica consentono di esaminare i dati dell'app:

* [Esplora metriche](../../azure-monitor/app/metrics-explorer.md)
* [Esplora ricerche](../../azure-monitor/app/diagnostic-search.md)
* [Linguaggio avanzato di query di Analisi](../../azure-monitor/log-query/get-started-portal.md)

I rilevamenti intelligenti sono automatici. tuttavia è possibile configurare avvisi aggiuntivi, se necessario.

* [Configurare manualmente gli avvisi relativi alle metriche](../../azure-monitor/app/alerts.md)
* [Test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
