---
title: Introduzione a Log Analytics in Monitoraggio di Azure | Microsoft Docs
description: Questo articolo contiene un'esercitazione per l'uso di Log Analytics nel portale di Azure per la scrittura di query.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 07/19/2019
ms.openlocfilehash: 1cf1695db50e6aee2a5dae24ed5231fdda7c12de
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360851"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Introduzione a Log Analytics in Monitoraggio di Azure

> [!NOTE]
> È possibile eseguire questo esercizio nel proprio ambiente se si raccolgono dati da almeno una macchina virtuale. In caso contrario, usare l'[ambiente Demo](https://portal.loganalytics.io/demo), che include numerosi dati di esempio.

In questa esercitazione si apprenderà come usare Log Analytics nel portale di Azure per scrivere query relative ai log di Monitoraggio di Azure. Si apprenderà come:

- Usare Log Analytics per scrivere una query semplice
- Comprendere lo schema dei dati
- Filtrare, ordinare e raggruppare i risultati
- Applicare un intervallo di tempo
- Creare grafici
- Salvare e caricare query
- Esportare e condividere query

Per un'esercitazione sulla scrittura di query di log, vedere [Introduzione alle query di log in Monitoraggio di Azure](get-started-queries.md).<br>
Per altri dettagli sulle query di log in Monitoraggio di Azure, vedere [Panoramica delle query di log in Monitoraggio di Azure](log-query-overview.md).

## <a name="meet-log-analytics"></a>Informazioni preliminari su Log Analytics
Log Analytics è uno strumento Web usato per scrivere ed eseguire query relative ai log di Monitoraggio di Azure. Per aprirlo, selezionare **Log** nel menu di Monitoraggio di Azure. Viene visualizzata una nuova query vuota.

![Home page](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>Requisiti del firewall
Per usare Log Analytics, il browser richiede l'accesso agli indirizzi seguenti. Se il browser accede al portale di Azure attraverso un firewall, è necessario abilitare l'accesso a questi indirizzi.

| Uri | IP | Porte |
|:---|:---|:---|
| portal.loganalytics.io | Dinamico | 80,443 |
| api.loganalytics.io | Dinamico | 80,443 |
| docs.loganalytics.io | Dinamico | 80,443 |

## <a name="basic-queries"></a>Query di base
È possibile usare le query per cercare termini, identificare le tendenze, analizzare i modelli e ottenere molte altre informazioni dettagliate basate sui dati. Iniziare con una query di base:

```Kusto
Event | search "error"
```

Questa query cerca nella tabella _Event_ i record che contengono il termine _error_ in qualsiasi proprietà.

Le query possono iniziare con un nome di tabella o un comando [search](/azure/kusto/query/searchoperator). L'esempio precedente inizia con il nome della tabella _Event_, in modo da recuperare tutti i record da questa tabella. Il carattere di barra verticale, o pipe (|), separa i comandi in modo che l'output del primo comando venga usato come input di quello successivo. È possibile aggiungere qualsiasi numero di comandi a una singola query.

Un altro modo per scrivere la stessa query è il seguente:

```Kusto
search in (Event) "error"
```

In questo esempio l'ambito di **search** è la tabella _Event_ e viene eseguita una ricerca del termine _error_ in tutti i record della tabella.

## <a name="running-a-query"></a>Esecuzione di una query
Per eseguire una query, fare clic sul pulsante **Esegui** oppure premere **MAIUSC+INVIO**. Prendere in considerazione i dettagli seguenti che determinano il codice che verrà eseguito e i dati restituiti:

- Interruzioni di riga: una singola interruzione rende più semplice la lettura della query. Più interruzioni di riga suddividono la query in query separate.
- Cursore: posizionare il cursore in un punto qualsiasi all'interno della query per eseguirla. La query corrente viene considerata come codice fino a quando non viene trovata una riga vuota.
- Intervallo di tempo: per impostazione predefinita, viene usato un intervallo di tempo corrispondente alle _ultime 24 ore_. Per usare un intervallo diverso, usare il controllo di selezione di data e ora o aggiungere alla query un filtro di intervallo di tempo esplicito.


## <a name="understand-the-schema"></a>Informazioni sullo schema
Lo schema è una raccolta di tabelle raggruppate visivamente in una categoria logica. Diverse categorie provengono dalle soluzioni di monitoraggio. La categoria _LogManagement_ contiene i dati comuni, ad esempio eventi di Windows e Syslog, dati sulle prestazioni ed eventi heartbeat degli agenti.

![SCHEMA](media/get-started-portal/schema.png)

In ogni tabella i dati sono organizzati in colonne con tipi di dati diversi, come indicato dalle icone accanto al nome di colonna. La tabella _Event_ nello screenshot contiene ad esempio colonne come _Computer_, costituita da testo, _EventCategory_, costituita da un numero, e _TimeGenerated_, di tipo data/ora.

## <a name="filter-the-results"></a>Filtrare i risultati
Per iniziare, recuperare tutti gli elementi della tabella _Event_.

```Kusto
Event
```

Log Analytics definisce automaticamente l'ambito dei risultati in base a questi parametri:

- Intervallo di tempo: per impostazione predefinita, le query sono limitate alle ultime 24 ore.
- Numero di risultati: i risultati sono limitati a un massimo di 10.000 record.

Questa query è molto generica e restituisce troppi risultati per essere utile. È possibile filtrare i risultati tramite gli elementi della tabella o aggiungendo in modo esplicito un filtro alla query. Il filtro dei risultati tramite gli elementi della tabella si applica al set di risultati esistente, mentre un filtro per la query restituisce un nuovo set di risultati filtrato e può quindi generare risultati più accurati.

### <a name="add-a-filter-to-the-query"></a>Aggiungere un filtro alla query
A sinistra di ogni record è presente una freccia. Fare clic su tale freccia per visualizzare i dettagli per un record specifico.

Passare il mouse sopra un nome di colonna per visualizzare le icone "+" e "-". Per aggiungere un filtro che restituisce solo i record con lo stesso valore, fare clic sul segno "+". Fare clic su "-" per escludere i record con questo valore e quindi fare clic su **Esegui** per eseguire di nuovo la query.

![Aggiungere un filtro alla query](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrare gli elementi della tabella
Verranno ora presi n considerazione gli eventi con gravità corrispondete a _Error_. Questo valore è specificato in una colonna denominata _EventLevelName_. Scorrere verso destra per visualizzare questa colonna.

Fare clic sull'icona di filtro accanto al titolo della colonna e nella finestra popup selezionare _Inizia con_ e il testo _error_:

![Filtro](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Ordinare e raggruppare i risultati
I risultati sono ora limitati in modo da includere solo gli eventi di errore di SQL Server creati nelle ultime 24 ore. Tuttavia, i risultati non sono ordinati in alcun modo. Per ordinare i risultati in base a una colonna specifica, ad esempio _timestamp_, fare clic sul titolo della colonna. Con il primo clic viene applicato l'ordinamento crescente, con il secondo clic quello decrescente.

![Ordinamento in base alla colonna](media/get-started-portal/sort-column.png)

Un altro modo per organizzare i risultati è in gruppi. Per raggruppare i risultati in base una colonna specifica, è sufficiente trascinare l'intestazione di colonna sopra le altre colonne. Per creare sottogruppi, trascinare anche altre colonne sulla barra superiore.

![Gruppi](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Selezionare le colonne da visualizzare
La tabella dei risultati include spesso molte colonne. Alcune colonne restituite possono non venire visualizzate per impostazione predefinita oppure si potrebbe voler rimuovere alcune colonne visualizzate. Per selezionare le colonne da visualizzare, fare clic sul pulsante Colonne:

![Selezione colonne](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Selezionare un intervallo di tempo
Per impostazione predefinita, Log Analytics applica un intervallo di tempo corrispondente alle _ultime 24 ore_. Per usare un intervallo diverso, selezionare un valore diverso tramite il controllo di selezione di data e ora e fare clic su **Esegui**. Oltre ai valori predefiniti, è possibile usare l'opzione _Intervallo di tempo personalizzato_ per selezionare un intervallo assoluto per la query.

![Controllo di selezione di data e ora](media/get-started-portal/time-picker.png)

Quando si seleziona un intervallo di tempo personalizzato, i valori selezionati sono in formato UTC, che può essere diverso rispetto al fuso orario locale.

Se la query contiene in modo esplicito un filtro per _TimeGenerated_, nel titolo del controllo di selezione di data e ora verrà indicato _Set in query_ (Impostato nella query). La selezione manuale verrà disabilitata per evitare conflitti.


## <a name="charts"></a>Grafici
Oltre a restituire i risultati in una tabella, è possibile presentare i risultati della query in formato visivo. Usare la query seguente come esempio:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Per impostazione predefinita, i risultati vengono visualizzati in una tabella. Fare clic su _Grafico_ per visualizzare i risultati in una visualizzazione grafica:

![Grafico a barre](media/get-started-portal/bar-chart.png)

I risultati vengono visualizzati in un grafico a barre in pila. Fare clic su _Istogramma in pila_ e selezionare _Torta_ per mostrare un'altra visualizzazione dei risultati:

![Grafico a torta](media/get-started-portal/pie-chart.png)

Diverse proprietà della visualizzazione, ad esempio gli assi x e y, o le preferenze di raggruppamento e divisione, possono essere modificate manualmente dalla barra di controllo.

È anche possibile impostare la visualizzazione preferita nella query stessa, usando l'operatore render.

### <a name="smart-diagnostics"></a>Diagnostica intelligente
In un grafico del tempo, se si verifica un picco o una variazione improvvisa dei dati, sulla linea viene evidenziato un punto. Ciò indica che la funzionalità di _diagnostica intelligente_ ha identificato una combinazione di proprietà che permettono di escludere tramite filtro le variazioni improvvise. Fare clic sul punto per ottenere maggiori dettagli sul filtro e per visualizzare la versione filtrata. Ciò può essere utile per identificare la causa della variazione:

![Diagnostica intelligente](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Aggiungi al dashboard
Per aggiungere un diagramma o una tabella a uno dei dashboard di Azure condivisi, fare clic sull'icona a forma di puntina. Si noti che questa icona è stata spostata nella parte superiore della finestra di Log Analytics, diversamente dallo screenshot riportato di seguito.

![Aggiungi al dashboard](media/get-started-portal/pin-dashboard.png)

Quando si aggiunge un grafico a un dashboard, vengono applicate al grafico determinate semplificazioni:

- Colonne e righe di tabella: per aggiungere una tabella al dashboard, la tabella deve avere un massimo di quattro colonne. Verranno visualizzate solo le prime sette righe.
- Limite di tempo: le query vengono automaticamente limitate agli ultimi 14 giorni.
- Limite del numero di contenitori: se si visualizza un grafico con numerosi contenitori discreti, i contenitori meno popolati vengono automaticamente raggruppati in un unico contenitore _altri_.

## <a name="save-queries"></a>Salvataggio delle query
Una volta creata una query utile, è possibile salvarla o condividerla con altri utenti. L'icona **Salva** si trova sulla barra superiore.

È possibile salvare l'intera pagina di query o una singola query come funzione. Le funzioni sono query a cui altre query possono fare riferimento. Per salvare una query come funzione, è necessario specificare un alias di funzione, ovvero il nome usato per chiamare la query quando altre query vi fanno riferimento.

![Salvare la funzione](media/get-started-portal/save-function.png)

>[!NOTE]
>Quando si salva o si modifica la query salvata, nel campo `a–z, A–Z, 0-9, -, _, ., <space>, (, ), |`Nome**sono supportati i caratteri seguenti:** .

Le query di Log Analytics vengono sempre salvate in un'area di lavoro selezionata e condivise con altri utenti dell'area di lavoro.

## <a name="load-queries"></a>Caricare le query
L'icona Esplora query si trova nell'area in alto a destra. Permette di elencare tutte le query salvate in base alla categoria. Consente inoltre di contrassegnare query specifiche come preferite per trovarle rapidamente in futuro. Fare doppio clic su una query salvata per aggiungerla alla finestra corrente.

![Esplora query](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Esportare e condividere come collegamento
Log Analytics supporta diversi metodi di esportazione:

- Excel: salvare i risultati come file CSV.
- Power BI: esportare i risultati in Power BI. Per informazioni dettagliate, vedere [Importare i dati dei log di Monitoraggio di Azure in Power BI](../../azure-monitor/platform/powerbi.md).
- Condividere un collegamento: la query stessa può essere condivisa come collegamento che altri utenti che hanno accesso alla stessa area di lavoro possono quindi inviare ed eseguire.

## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni sulla [scrittura di query relative ai log di Monitoraggio di Azure](get-started-queries.md).
