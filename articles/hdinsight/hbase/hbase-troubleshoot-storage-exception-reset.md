---
title: Eccezione di archiviazione dopo la reimpostazione della connessione in Azure HDInsight
description: Eccezione di archiviazione dopo la reimpostazione della connessione in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887224"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Scenario: eccezione di archiviazione dopo la reimpostazione della connessione in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Non è possibile creare la nuova tabella Apache HBase.

## <a name="cause"></a>Causa

Durante un processo di troncamento della tabella si è verificato un problema di connessione di archiviazione. La voce della tabella è stata eliminata nella tabella dei metadati HBase. Sono stati eliminati tutti i file BLOB tranne uno.

Sebbene non sia presente alcun BLOB di cartelle chiamato `/hbase/data/default/ThatTable` seduto nello spazio di archiviazione. Il driver WASB ha rilevato l'esistenza del file BLOB precedente e non consente di creare alcun BLOB denominato `/hbase/data/default/ThatTable` perché presume che le cartelle padre esistano, pertanto la creazione della tabella avrà esito negativo.

## <a name="resolution"></a>Risoluzione

1. Dall'interfaccia utente di Apache Ambari, riavviare il HMaster attivo. In questo modo uno dei due HMaster di standby diventa quello attivo e il nuovo HMaster attivo ricarica le informazioni della tabella dei metadati. In questo modo, la tabella `already-deleted` non viene visualizzata nell'interfaccia utente di HMaster.

1. È possibile trovare il file BLOB orfano dagli strumenti dell'interfaccia utente, ad esempio Cloud Explorer o eseguire un comando come `hdfs dfs -ls /xxxxxx/yyyyy`. Eseguire `hdfs dfs -rmr /xxxxx/yyyy` per eliminare il BLOB. Ad esempio: `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

A questo punto è possibile creare una nuova tabella con lo stesso nome in HBase.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
