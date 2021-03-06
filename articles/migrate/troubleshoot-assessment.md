---
title: Risolvere i problemi di visualizzazione delle dipendenze e della valutazione in Azure Migrate
description: Ottenere assistenza per la risoluzione dei problemi relativi alla valutazione e alla visualizzazione delle dipendenze in Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: fb1e32d9f67febb09eadfb5d31221db33504eb05
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289476"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Risolvere i problemi relativi alla visualizzazione di valutazioni/dipendenze

Questo articolo consente di risolvere i problemi relativi alla valutazione e alla visualizzazione delle dipendenze con [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problemi di conformità della valutazione

Correggere i problemi di conformità della valutazione come indicato di seguito:

**Problema** | **Correzione**
--- | ---
Il tipo di avvio non è supportato | Azure non supporta le VM con un tipo di avvio EFI. È consigliabile convertire il tipo di avvio in BIOS prima di eseguire una migrazione. <br/><br/>È possibile usare Azure Migrate migrazione del server per gestire la migrazione di tali macchine virtuali. Il tipo di avvio della macchina virtuale verrà convertito in BIOS durante la migrazione.
Sistema operativo Windows supportato in modo condizionale | Il sistema operativo ha superato la data di fine del supporto ed è necessario un contratto di supporto personalizzato (CSA) per il [supporto in Azure](https://aka.ms/WSosstatement). Prendere in considerazione l'aggiornamento prima di eseguire la migrazione ad Azure.
Sistema operativo Windows non supportato | Azure supporta solo le [versioni del sistema operativo Windows selezionate](https://aka.ms/WSosstatement). Provare ad aggiornare il computer prima di eseguire la migrazione ad Azure.
Sistema operativo Linux con approvazione condizionale | Azure approva solo le [versioni del sistema operativo Linux selezionate](../virtual-machines/linux/endorsed-distros.md). Provare ad aggiornare il computer prima di eseguire la migrazione ad Azure.
Sistema operativo Linux non approvato | Il computer potrebbe essere avviato in Azure, ma Azure non offre alcun supporto per il sistema operativo. Prendere in considerazione l'aggiornamento a una [versione di Linux approvata](../virtual-machines/linux/endorsed-distros.md) prima di eseguire la migrazione ad Azure.
Sistema operativo sconosciuto | Il sistema operativo della macchina virtuale è stato specificato come "altro" in server vCenter. Questo comportamento blocca Azure Migrate dalla verifica della conformità di Azure della macchina virtuale. Prima di eseguire la migrazione del computer, assicurarsi che il sistema operativo sia [supportato](https://aka.ms/azureoslist) da Azure.
Versione bit non supportata | Le macchine virtuali con sistemi operativi a 32 bit possono essere avviate in Azure, ma è consigliabile eseguire l'aggiornamento a 64 bit prima di eseguire la migrazione ad Azure.
Richiede una sottoscrizione di Microsoft Visual Studio | Il computer esegue un sistema operativo client Windows, che è supportato solo tramite una sottoscrizione di Visual Studio.
Macchina virtuale non trovata per le prestazioni di archiviazione richieste | Le prestazioni di archiviazione (operazioni di input/output al secondo [IOPS] e velocità effettiva) richieste per il computer superano il supporto delle macchine virtuali di Azure. Prima di eseguire la migrazione, ridurre i requisiti di archiviazione per la macchina.
Macchina virtuale non trovata per le prestazioni di rete richieste | Le prestazioni di rete (ingresso/uscita) richieste per la macchina superano le prestazioni supportate dalla macchina virtuale di Azure. Ridurre i requisiti di rete per la macchina.
La macchina virtuale non è stata trovata nel percorso specificato | Usare una località di destinazione diversa prima di eseguire migrazione.
Uno o più dischi non appropriati | Uno o più dischi collegati alla macchina virtuale non soddisfano i requisiti di Azure. Un<br/><br/> Azure Migrate: la valutazione del server non supporta attualmente i dischi Ultra SSD e valuta i dischi in base ai limiti del disco per i dischi gestiti Premium (32 TB).<br/><br/> Per ogni disco collegato alla macchina virtuale, verificare che le dimensioni del disco siano < 64 TB (supportato da dischi Ultra SSD).<br/><br/> In caso contrario, ridurre le dimensioni del disco prima di eseguire la migrazione ad Azure o usare più dischi in Azure e [rimuoverli insieme](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) per ottenere limiti di archiviazione più elevati. Assicurarsi che le prestazioni (IOPS e velocità effettiva) richieste da ogni disco siano supportate dai [dischi delle macchine virtuali gestite](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)da Azure.
Una o più schede di rete non idonee. | Prima di eseguire la migrazione, rimuovere le schede di rete non usate dalla macchina.
Il numero di dischi supera il limite | Rimuovere i dischi non usati dalla macchina prima di eseguire la migrazione.
Le dimensioni del disco hanno superato il limite | Azure Migrate: la valutazione del server non supporta attualmente i dischi Ultra SSD e valuta i dischi in base ai limiti del disco Premium (32 TB).<br/><br/> Tuttavia, Azure supporta dischi con dimensioni fino a 64 TB (supportate da dischi Ultra SSD). Compattare i dischi fino a 64 TB prima della migrazione oppure usare più dischi in Azure e [rimuoverli insieme](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) per ottenere limiti di archiviazione più elevati.
Il disco non è disponibile nella località specificata | Assicurarsi che il disco si trovi nella località di destinazione prima di eseguire la migrazione.
Il disco non è disponibile per la ridondanza specificata | Il disco deve usare il tipo di archiviazione di ridondanza definito nelle impostazioni di valutazione (per impostazione predefinita, LRS).
Non è stato possibile determinare l'idoneità del disco a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
La macchina virtuale con core e memoria richiesti non è stata trovata | Azure non è riuscito a trovare un tipo di macchina virtuale appropriato. Prima di eseguire la migrazione, quindi, ridurre la memoria e il numero di core della macchina locale.
Non è stato possibile determinare l'idoneità della macchina virtuale a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
Non è stato possibile determinare l'idoneità per uno o più dischi a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
Non è stato possibile determinare l'idoneità per una o più schede di rete a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.

## <a name="linux-vms-are-conditionally-ready"></a>Le VM Linux sono "predisposte in modo condizionale"

Server Assessment contrassegna le VM Linux come "predisposte in modo condizionale" a causa di un gap noto nella valutazione del server.

- Il gap impedisce di rilevare la versione secondaria del sistema operativo Linux installato nelle macchine virtuali locali.
- Ad esempio, per RHEL 6,10, attualmente server Assessment rileva solo RHEL 6 come versione del sistema operativo.
-  Poiché Azure approva solo versioni specifiche di Linux, le VM Linux sono attualmente contrassegnate come predisposte in modo condizionale nella valutazione del server.
- È possibile determinare se il sistema operativo Linux in esecuzione nella macchina virtuale locale è approvato in Azure esaminando il supporto per [Linux di Azure](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  Dopo aver verificato la distribuzione approvata, è possibile ignorare questo avviso.

## <a name="azure-skus-bigger-than-on-premises"></a>SKU di Azure di dimensioni maggiori rispetto a locali

Azure Migrate server Assessment potrebbe consigliare gli SKU di VM di Azure con più core e memoria rispetto all'allocazione locale corrente in base al tipo di valutazione:


- La raccomandazione SKU della macchina virtuale dipende dalle proprietà di valutazione.
- Questo problema è influenzato dal tipo di valutazione eseguito in server assessment: *basato sulle prestazioni*o in *locale*.
- Per le valutazioni basate sulle prestazioni, valutazione server considera i dati di utilizzo delle macchine virtuali locali (CPU, memoria, disco e utilizzo della rete) per determinare lo SKU di VM di destinazione appropriato per le macchine virtuali locali. Viene inoltre aggiunto un fattore di comfort quando si determina un utilizzo efficace.
- Per il dimensionamento locale, i dati sulle prestazioni non vengono considerati e lo SKU di destinazione è consigliato in base all'allocazione locale.

Per illustrare il modo in cui questo può influire sulle raccomandazioni, si prenda un esempio:

Abbiamo una macchina virtuale locale con quattro core e 8 GB di memoria, con un utilizzo della CPU del 50% e un utilizzo della memoria del 50% e un fattore di comfort specificato di 1,3.

-  Se la valutazione è **come in locale**, è consigliabile usare uno SKU di VM di Azure con quattro core e 8 GB di memoria.
- Se la valutazione è basata sulle prestazioni, in base all'utilizzo effettivo di CPU e memoria (50% di 4 core * 1,3 = 2,6 core e 50% di 8 GB di memoria * 1,3 = 5,3-GB di memoria), lo SKU di VM più economico di quattro core (numero di core supportato più vicino) e otto GB di memoria (supportata più vicina). è consigliabile usare le dimensioni della memoria.
- [Altre](concepts-assessment-calculation.md#assessments-in-server-assessment) informazioni sul dimensionamento della valutazione.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>SKU dischi di Azure di dimensioni maggiori rispetto a locali

Azure Migrate server Assessment potrebbe consigliare un disco di dimensioni maggiori in base al tipo di valutazione.
- Il dimensionamento del disco nella valutazione del server dipende da due proprietà di valutazione: criteri di ridimensionamento e tipo di archiviazione.
- Se i criteri di ridimensionamento sono **basati sulle prestazioni**e il tipo di archiviazione è impostato su **automatico**, i valori di IOPS e velocità effettiva del disco vengono considerati quando si identifica il tipo di disco di destinazione (HDD standard, SDD standard o Premium). Viene quindi consigliato uno SKU del disco dal tipo di disco e la raccomandazione considera i requisiti di dimensioni del disco locale.
- Se i criteri di ridimensionamento sono **basati sulle prestazioni**e il tipo di archiviazione è **Premium**, è consigliabile usare uno SKU di dischi Premium in Azure in base ai requisiti di IOPS, velocità effettiva e dimensioni del disco locale. La stessa logica viene usata per eseguire il ridimensionamento del disco quando i criteri di ridimensionamento sono **locali** e il tipo di archiviazione è **HDD standard**, **SDD standard**o **Premium**.

Ad esempio, se si dispone di un disco locale con 32 GB di memoria, ma le operazioni di i/o di lettura e scrittura aggregate per il disco sono 800 IOPS, server Assessment consiglia un disco Premium (a causa dei requisiti di IOPS più elevati), quindi consiglia uno SKU del disco in grado di supportare r ecessario IOPS e dimensioni. In questo esempio la corrispondenza più vicina sarebbe P15 (256 GB, 1.100 operazioni di I/O al secondo). Anche se le dimensioni richieste dal disco locale sono 32 GB, server Assessment consiglia un disco più grande a causa del requisito di IOPS elevato del disco locale.

## <a name="utilized-corememory-percentage-missing"></a>Percentuale memoria/Core utilizzata mancante

Server Assessment segnala "PercentageOfCoresUtilizedMissing" o "PercentageOfMemoryUtilizedMissing" quando l'appliance Azure Migrate non è in grado di raccogliere i dati sulle prestazioni per le VM locali pertinenti.

- Questo problema può verificarsi se le macchine virtuali sono spente durante la durata della valutazione. L'appliance non può raccogliere i dati sulle prestazioni per una macchina virtuale quando è spenta.
- Se mancano solo i contatori di memoria e si sta provando a valutare le VM Hyper-V, controllare se la memoria dinamica è abilitata in queste VM. Si verifica un problema noto solo per le macchine virtuali Hyper-V, in cui un'appliance Azure Migrate non è in grado di raccogliere dati di utilizzo della memoria per le macchine virtuali in cui non è abilitata la memoria dinamica.
- Se non è presente alcun contatore delle prestazioni, Azure Migrate server Assessment esegue il fallback alla memoria e ai core allocati e suggerisce le dimensioni della VM corrispondenti.
- Se tutti i contatori delle prestazioni risultano mancanti, verificare che siano soddisfatti i requisiti di accesso alle porte per la valutazione. Altre informazioni sui requisiti di accesso alle porte per [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access), [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) e la valutazione dei server [fisici](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access) .

## <a name="is-the-operating-system-license-included"></a>La licenza del sistema operativo è inclusa?

Azure Migrate server Assessment considera attualmente il costo della licenza del sistema operativo solo per i computer Windows. I costi di licenza per i computer Linux non sono attualmente considerati.

## <a name="how-does-performance-based-sizing-work"></a>Come funziona il dimensionamento basato sulle prestazioni?

Server Assessment raccoglie continuamente i dati delle prestazioni dei computer locali e li usa per consigliare lo SKU della macchina virtuale e del disco in Azure. [Informazioni su come](concepts-assessment-calculation.md#calculate-sizing-performance-based) vengono raccolti i dati basati sulle prestazioni.


## <a name="dependency-visualization-in-azure-government"></a>Visualizzazione delle dipendenze in Azure per enti pubblici

Azure Migrate dipende da Mapping dei servizi per la funzionalità di visualizzazione delle dipendenze. Poiché Mapping dei servizi non è attualmente disponibile in Azure per enti pubblici, questa funzionalità non è disponibile in Azure per enti pubblici.

## <a name="dependencies-dont-show-after-agent-install"></a>Le dipendenze non vengono visualizzate dopo l'installazione dell'agente

Dopo aver installato gli agenti di visualizzazione delle dipendenze nelle VM locali, Azure Migrate in genere impiega 15-30 minuti per visualizzare le dipendenze nel portale. Se sono state attese più di 30 minuti, verificare che il Microsoft Monitoring Agent (MMA) sia in grado di connettersi all'area di lavoro Log Analytics.

Per VM di Windows:
1. Nel pannello di controllo avviare MMA.
2. Nel **Microsoft Monitoring Agent proprietà** > **Azure log Analytics (OMS)** , verificare che lo **stato** dell'area di lavoro sia verde.
3. Se lo stato non è verde, provare a rimuovere l'area di lavoro e aggiungerlo di nuovo a MMA.

    ![Stato MMA](./media/troubleshoot-assessment/mma-properties.png)

Per le macchine virtuali Linux, assicurarsi che i comandi di installazione per MMA e Dependency Agent siano riusciti.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

- **Agente MMS**: esaminare i sistemi operativi [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) supportati.
- **Dependency Agent**: sistemi operativi [Windows e Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) supportati.

## <a name="visualize-dependencies-for--hour"></a>Visualizza le dipendenze per > ora

Sebbene Azure Migrate consenta di tornare a una data specifica nell'ultimo mese, la durata massima per la quale è possibile visualizzare le dipendenze è di un'ora.

Ad esempio, è possibile usare la funzionalità durata tempo nella mappa delle dipendenze per visualizzare le dipendenze di ieri, ma è possibile visualizzarle solo per un periodo di un'ora.

Tuttavia, è possibile usare i log di monitoraggio di Azure per [eseguire query sui dati di dipendenza](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) per un periodo di tempo più lungo.

## <a name="visualized-dependencies-for--10-machines"></a>Dipendenze visualizzate per > 10 computer

In Azure Migrate server assessment è possibile [visualizzare le dipendenze per i gruppi](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) con un massimo di 10 VM. Per i gruppi più grandi, è consigliabile suddividere le macchine virtuali in gruppi più piccoli per visualizzare le dipendenze.

## <a name="machines-show-install-agent"></a>Computer che visualizzano "Installa agente"

Dopo aver eseguito la migrazione dei computer con la visualizzazione delle dipendenze abilitata in Azure, i computer potrebbero visualizzare l'azione "Installa agente" invece di "Visualizza dipendenze" a causa del comportamento seguente:


- Dopo la migrazione ad Azure, i computer locali sono spenti e le VM equivalenti vengono riattivate in Azure. Queste macchine acquisiscono un indirizzo MAC diverso
- I computer possono anche avere un indirizzo IP diverso, a seconda che l'indirizzo IP locale sia stato mantenuto o meno.
- Se gli indirizzi MAC e IP sono diversi da quelli locali, Azure Migrate non associa i computer locali con i dati di dipendenza Mapping dei servizi. In questo caso, verrà visualizzata l'opzione per l'installazione dell'agente anziché per visualizzare le dipendenze.
- Dopo una migrazione di test in Azure, i computer locali rimangono accesi come previsto. Computer equivalenti in Azure acquisiscono un indirizzo MAC diverso e potrebbero acquisire indirizzi IP diversi. A meno che non venga bloccato il traffico dei log di monitoraggio di Azure in uscita da questi computer, Azure Migrate non associa i computer locali con i dati delle dipendenze Mapping dei servizi e quindi visualizzerà l'opzione per installare gli agenti, anziché visualizzare le dipendenze.


## <a name="capture-network-traffic"></a>Acquisisci traffico di rete

Raccogliere i log del traffico di rete come segue:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Premere F12 per avviare Strumenti di sviluppo. Se necessario, deselezionare l'impostazione **Cancella voci per la navigazione** .
3. Selezionare la scheda **rete** e avviare l'acquisizione del traffico di rete:
   - In Chrome selezionare **Preserve log** (Mantieni log). La registrazione viene avviata automaticamente. Un cerchio rosso indica che è in corso l'acquisizione del traffico. Se il cerchio rosso non viene visualizzato, selezionare il cerchio nero per iniziare.
   - In Microsoft Edge e Internet Explorer la registrazione dovrebbe avviarsi automaticamente. In caso contrario, selezionare il pulsante verde Riproduci.
4. Provare a riprodurre l'errore.
5. Dopo aver riscontrato l'errore durante la registrazione, arrestare la registrazione e salvare una copia dell'attività registrata:
   - In Chrome fare clic con il pulsante destro del mouse e scegliere **Salva come har con contenuto**. Questa azione comprime ed Esporta i log come file con estensione har.
   - In Microsoft Edge o Internet Explorer selezionare l'opzione **Esporta il traffico acquisito** . Questa azione comprime ed esporta il log.
6. Selezionare la scheda **console** per verificare la presenza di eventuali avvisi o errori. Per salvare il log della console:
   - In Chrome fare clic con il pulsante destro del mouse in un punto qualsiasi del log della console. Selezionare **Salva con nome**, per esportare e comprimere il log.
   - In Microsoft Edge o Internet Explorer fare clic con il pulsante destro del mouse sugli errori e selezionare **copia tutto**.
7. Chiudere Strumenti di sviluppo.

## <a name="next-steps"></a>Passaggi successivi

[Creare](how-to-create-assessment.md) o [personalizzare](how-to-modify-assessment.md) una valutazione.
