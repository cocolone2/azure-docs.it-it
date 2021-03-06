---
title: Panoramica sulla risoluzione dei problemi di desktop virtuale Windows-Azure
description: Panoramica per la risoluzione dei problemi durante la configurazione di un ambiente tenant di desktop virtuali Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127395"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Panoramica della risoluzione dei problemi, feedback e supporto

Questo articolo fornisce una panoramica dei problemi che possono verificarsi durante la configurazione di un ambiente tenant di desktop virtuali Windows e fornisce modi per risolvere i problemi.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="escalation-tracks"></a>Tracce di escalation

Usare la tabella seguente per identificare e risolvere i problemi che possono verificarsi durante la configurazione di un ambiente tenant usando Desktop remoto client. Una volta configurata la configurazione del tenant, è possibile usare il nuovo [servizio di diagnostica](diagnostics-role-service.md) per identificare i problemi per gli scenari comuni.

>[!NOTE]
> È disponibile un forum della community tecnica che è possibile visitare per discutere i problemi con il team del prodotto e i membri della community attiva. Per iniziare una discussione, visitare il [desktop virtuale di Windows Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) .

| **Problema**                                                            | **Soluzione suggerita**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Creazione di un tenant desktop virtuale Windows                                                    | Se si verifica un'interruzione di Azure, [aprire una richiesta di supporto di Azure](https://azure.microsoft.com/support/create-ticket/). in caso contrario, [aprire una richiesta di supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/), selezionare **desktop virtuale Windows** per il servizio, selezionare **distribuzione** per il tipo di problema, quindi selezionare problemi durante la **creazione di un tenant desktop virtuale Windows** per il sottotipo di problema.|
| Accesso ai modelli di Marketplace in portale di Azure       | Se si verifica un'interruzione di Azure, [aprire una richiesta di supporto di Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> I modelli di desktop virtuali Windows di Azure Marketplace sono disponibili gratuitamente.|
| Accesso ai modelli di Azure Resource Manager da GitHub                                  | Vedere la sezione [creazione di VM host sessione desktop virtuale di Windows](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) della creazione di [tenant e pool host](troubleshoot-set-up-issues.md). Se il problema non è ancora risolto, contattare il [team di supporto di GitHub](https://github.com/contact). <br> <br> Se l'errore si verifica dopo aver eseguito l'accesso al modello in GitHub, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/).|
| Pool host sessione-impostazioni della rete virtuale di Azure (VNET) e Express Route               | [Aprire una richiesta di supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/), quindi selezionare il servizio appropriato (nella categoria rete). |
| Creazione della macchina virtuale (VM) del pool host della sessione quando non vengono usati Azure Resource Manager modelli forniti con il desktop virtuale Windows | [Aprire una richiesta di supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/), quindi selezionare la **macchina virtuale che esegue Windows** per il servizio. <br> <br> Per problemi relativi ai modelli di Azure Resource Manager forniti con desktop virtuale di Windows, vedere la sezione relativa alla creazione di un tenant desktop virtuale Windows per la [creazione di tenant e pool host](troubleshoot-set-up-issues.md). |
| Gestione dell'ambiente host sessione desktop virtuale Windows dal portale di Azure    | [Aprire una richiesta di supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Per problemi di gestione quando si usa PowerShell per desktop virtuale Servizi Desktop remoto/Windows, vedere [PowerShell per desktop virtuale Windows](troubleshoot-powershell.md) o [aprire una richiesta di supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/), selezionare **desktop virtuale Windows** per il servizio, selezionare **configurazione e gestione** per il tipo di problema, quindi selezionare **problemi configurazione tenant con PowerShell** per il sottotipo di problema. |
| Gestione della configurazione del desktop virtuale di Windows associata ai pool di host e ai gruppi di applicazioni (gruppi di app)      | Vedere [desktop virtuale di Windows PowerShell](troubleshoot-powershell.md)o [aprire una richiesta di supporto di Azure](https://azure.microsoft.com/support/create-ticket/), selezionare **desktop virtuale Windows** per il servizio, quindi selezionare il tipo di problema appropriato.|
| Distribuzione e gestione di contenitori di profili FSLogix | Vedere la [Guida alla risoluzione dei problemi per i prodotti FSLogix](/fslogix/fslogix-trouble-shooting-ht/) e se il problema persiste, [aprire una richiesta di supporto di Azure](https://azure.microsoft.com/support/create-ticket/), selezionare **desktop virtuale Windows** per il servizio, selezionare **FSLogix** per il tipo di problema, quindi selezionare il sottotipo di problema appropriato. |
| Malfunzionamento dei client desktop remoto all'avvio                                                 | Vedere [risolvere i problemi relativi al client di desktop remoto](troubleshoot-client.md) e, se il problema persiste, [aprire una richiesta di supporto di Azure](https://azure.microsoft.com/support/create-ticket/), selezionare **desktop virtuale Windows** per il servizio e quindi selezionare **Desktop remoto client** per il tipo di problema.  <br> <br> Se si tratta di un problema di rete, gli utenti devono contattare l'amministratore di rete. |
| Connessione senza feed                                                                 | Risolvere i problemi relativi all'utilizzo delle connessioni [utente, ma non viene visualizzata alcuna sezione (nessun feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) delle [connessioni al servizio desktop virtuale di Windows](troubleshoot-service-connection.md). <br> <br> Se gli utenti sono stati assegnati a un gruppo di app, [aprire una richiesta di supporto di Azure](https://azure.microsoft.com/support/create-ticket/), selezionare **desktop virtuale Windows** per il servizio, quindi selezionare **Desktop remoto client** per il tipo di problema. |
| Problemi di individuazione dei feed dovuti alla rete                                            | Gli utenti devono contattare l'amministratore di rete. |
| Connessione dei client                                                                    | Vedere [connessioni al servizio desktop virtuale Windows](troubleshoot-service-connection.md) . se il problema persiste, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md). |
| Velocità di risposta delle applicazioni remote o del desktop                                      | Se i problemi sono collegati a un'applicazione o a un prodotto specifico, contattare il team responsabile del prodotto. |
| Messaggi di licenza o errori                                                          | Se i problemi sono collegati a un'applicazione o a un prodotto specifico, contattare il team responsabile del prodotto. |
| Problemi relativi all'uso di strumenti desktop virtuali di Windows su GitHub (modelli di Azure Resource Manager, strumento di diagnostica, strumento di gestione) | Per la segnalazione di problemi, vedere [modelli di Azure Resource Manager per Servizi Desktop remoto](https://github.com/Azure/RDS-Templates/blob/master/README.md) . |

## <a name="next-steps"></a>Passaggi successivi

- Per risolvere i problemi durante la creazione di un tenant e di un pool host in un ambiente desktop virtuale Windows, vedere [creazione di tenant e pool host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in desktop virtuale di Windows, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows, vedere [connessioni al servizio desktop virtuale di Windows](troubleshoot-service-connection.md).
- Per risolvere i problemi relativi ai client di Desktop remoto, vedere [risoluzione dei problemi del client di desktop remoto](troubleshoot-client.md)
- Per risolvere i problemi relativi all'uso di PowerShell con desktop virtuale di Windows, vedere [PowerShell per desktop virtuale di Windows](troubleshoot-powershell.md).
- Per ulteriori informazioni sul servizio, vedere [ambiente desktop virtuale di Windows](environment-setup.md).
- Per un'esercitazione per la risoluzione dei problemi, vedere [esercitazione: risolvere i problemi relativi alle distribuzioni di modelli gestione risorse](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Per informazioni sulle azioni per determinare gli errori durante la distribuzione, vedere [visualizzare le operazioni di distribuzione](../azure-resource-manager/templates/deployment-history.md).
