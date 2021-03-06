---
title: Eseguire runbook nel ruolo di lavoro ibrido per runbook di Automazione di Azure
description: Questo articolo fornisce informazioni sull'esecuzione di runbook su computer presenti nel data center locale o in un provider di servizi cloud con il ruolo di lavoro ibrido per runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 9b9196cde45686e42d1baf7faedf94bdb73acccc
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367059"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Esecuzione di runbook in un ruolo di lavoro ibrido per runbook

Manuali operativi destinati a un ruolo di lavoro ibrido per Runbook in genere gestiscono le risorse sul computer locale o sulle risorse nell'ambiente locale in cui viene distribuito il ruolo di lavoro. I runbook usati in Automazione di Azure gestiscono in genere le risorse nel cloud di Azure. Anche se vengono usati in modo diverso, i manuali operativi eseguiti in automazione di Azure e manuali operativi eseguiti in un ruolo di lavoro ibrido per Runbook sono identici nella struttura.

Quando si crea un Runbook per l'esecuzione in un ruolo di lavoro ibrido per Runbook, è necessario modificare e testare il Runbook nel computer che ospita il ruolo di lavoro. Il computer host dispone di tutti i moduli di PowerShell e di accesso alla rete necessari per gestire e accedere alle risorse locali. Una volta testato il Runbook nel computer di lavoro ibrido per Runbook, è possibile caricarlo nell'ambiente di automazione di Azure, dove può essere eseguito nel ruolo di lavoro. 

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](automation-update-azure-modules.md).

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Autorizzazioni Runbook per un ruolo di lavoro ibrido per Runbook

Quando accedono a risorse non di Azure, manuali operativi in esecuzione in un ruolo di lavoro ibrido per Runbook non possono usare il meccanismo di autenticazione usato in genere dall'autenticazione manuali operativi per le risorse di Azure. Un Runbook fornisce l'autenticazione per le risorse locali o configura l'autenticazione usando [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). È anche possibile specificare un account RunAs per fornire un contesto utente per tutti manuali operativi.

### <a name="runbook-authentication"></a>Autenticazione dei runbook

Per impostazione predefinita, manuali operativi vengono eseguiti nel computer locale. Per Windows, vengono eseguiti nel contesto dell'account di sistema locale. Per Linux, vengono eseguiti nel contesto dell'account utente speciale **nxautomation**. In entrambi gli scenari, manuali operativi deve fornire la propria autenticazione alle risorse a cui accedono.

È [possibile usare le credenziali e](automation-credentials.md) gli asset di [certificato](automation-certificates.md) nella runbook con i cmdlet che consentono di specificare le credenziali in modo che Runbook possano eseguire l'autenticazione a diverse risorse. L'esempio seguente illustra una parte di un runbook che riavvia un computer. Recupera le credenziali da un asset delle credenziali e il nome del computer da un asset di variabile, quindi usa questi valori con il cmdlet `Restart-Computer`.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

È anche possibile usare un'attività [InlineScript](automation-powershell-workflow.md#inlinescript) . `InlineScript` consente di eseguire blocchi di codice in un altro computer con le credenziali specificate dal [parametro comune PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>account RunAs

Anziché fare in modo che i Runbook forniscano l'autenticazione per le risorse locali, è possibile specificare un account RunAs per un gruppo di lavoro ibrido per Runbook. A tale scopo, è necessario definire un [Asset credenziali](automation-credentials.md) con accesso alle risorse locali. Queste risorse includono gli archivi certificati e tutti i manuali operativi eseguiti con queste credenziali in un ruolo di lavoro ibrido per Runbook nel gruppo.

Il nome utente per le credenziali deve essere in uno dei formati seguenti:

* dominio\nomeutente
* username@domain
* nome utente (per gli account locali nel computer locale)

Usare la procedura seguente per specificare un account RunAs per un gruppo di ruolo di lavoro ibrido per Runbook.

1. Creare un [asset credenziali](automation-credentials.md) con accesso alle risorse locali.
2. Nel portale di Azure aprire l'account di automazione.
3. Selezionare il riquadro **Gruppi di ruoli di lavoro ibridi** e quindi il gruppo.
4. Selezionare **tutte le impostazioni**, quindi **le impostazioni del gruppo di lavoro ibrido**.
5. Modificare il valore di **RunAs** da **predefinito** a **Custom**.
6. Selezionare le credenziali e fare clic su **Salva**.

### <a name="managed-identities-for-azure-resources"></a>Identità gestite per le risorse di Azure

I ruoli di lavoro ibridi per Runbook in macchine virtuali di Azure possono usare identità gestite per le risorse di Azure per l'autenticazione nelle risorse di Azure. L'uso delle identità gestite per le risorse di Azure anziché degli account RunAs offre vantaggi perché non è necessario:

* Esportare il certificato RunAs e quindi importarlo nel ruolo di lavoro ibrido per Runbook.
* Rinnovare il certificato utilizzato dall'account RunAs.
* Gestire l'oggetto connessione RunAs nel codice Runbook.

Seguire i passaggi successivi per usare un'identità gestita per le risorse di Azure in un ruolo di lavoro ibrido per Runbook.

1. Creare una macchina virtuale di Azure.
2. Configurare le identità gestite per le risorse di Azure nella macchina virtuale. Vedere [configurare le identità gestite per le risorse di Azure in una macchina virtuale usando il portale di Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Gestione risorse. Vedere [usare un'identità gestita assegnata dal sistema VM Windows per accedere Gestione risorse](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Installare il ruolo di lavoro ibrido per Runbook nella macchina virtuale. Vedere [distribuire un ruolo di lavoro ibrido per Runbook Windows](automation-windows-hrw-install.md).
5. Aggiornare Runbook per usare il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) con il parametro `Identity` per l'autenticazione nelle risorse di Azure. Questa configurazione riduce la necessità di usare un account RunAs ed eseguire la gestione degli account associati.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity` funziona per un ruolo di lavoro ibrido per Runbook usando un'identità assegnata dal sistema e una singola identità assegnata dall'utente. Se si usano più identità assegnate dall'utente nel ruolo di lavoro ibrido per Runbook, è necessario che il Runbook specifichi il parametro *AccountID* per `Connect-AzAccount` per selezionare un'identità specifica assegnata dall'utente.

### <a name="runas-script"></a>Account RunAs di Automazione

Come parte del processo di compilazione automatizzato per la distribuzione di risorse in Azure, potrebbe essere necessario l'accesso ai sistemi locali per supportare un'attività o un set di passaggi nella sequenza di distribuzione. Per fornire l'autenticazione in Azure usando l'account RunAs, è necessario installare il certificato dell'account RunAs.

Il Runbook di PowerShell seguente, denominato **Export-RunAsCertificateToHybridWorker**, esporta il certificato RunAs dall'account di automazione di Azure. Il Runbook Scarica e importa il certificato nell'archivio certificati del computer locale in un ruolo di lavoro ibrido per Runbook connesso allo stesso account. Al termine di questo passaggio, Runbook verifica che il ruolo di lavoro sia in grado di eseguire l'autenticazione in Azure usando l'account RunAs.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Per manuali operativi di PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per `Connect-AzAccount`. Quando si eseguono ricerche negli elementi della libreria, se non si visualizzano `Connect-AzAccount`, è possibile usare `Add-AzAccount`oppure è possibile aggiornare i moduli nell'account di automazione.

Per completare la preparazione dell'account RunAs:

1. Salvare il Runbook **Export-RunAsCertificateToHybridWorker** nel computer con estensione **ps1** .
2. Importarlo nell'account di automazione.
3. Modificare il Runbook, modificando il valore della variabile `Password` o la propria password. 
4. Pubblicare il Runbook.
5. Eseguire Runbook, destinando il gruppo di lavoro ibrido per Runbook che esegue e autentica manuali operativi usando l'account RunAs. 
6. Esaminare il flusso del processo per verificare che segnali il tentativo di importare il certificato nell'archivio del computer locale e che segua più righe. Questo comportamento dipende dal numero di account di automazione definiti nella sottoscrizione e dal grado di riuscita dell'autenticazione.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Comportamento dei processi nei ruoli di lavoro ibridi per Runbook

Automazione di Azure gestisce i processi nei ruoli di lavoro ibridi per Runbook in modo diverso rispetto ai processi eseguiti in sandbox di Azure. Una differenza fondamentale è che non esiste alcun limite per la durata dei processi nei Runbook Worker. I manuali operativi eseguiti in sandbox di Azure sono limitati a tre ore a causa di una [condivisione equa](automation-runbook-execution.md#fair-share).

Per un Runbook a esecuzione prolungata, è necessario assicurarsi che sia resiliente al riavvio possibile, ad esempio se il computer che ospita il ruolo di lavoro viene riavviato. Se il computer host del ruolo di lavoro ibrido per Runbook viene riavviato, qualsiasi processo Runbook in esecuzione viene riavviato dall'inizio o dall'ultimo checkpoint per manuali operativi del flusso di lavoro PowerShell. Quando un processo del Runbook viene riavviato più di tre volte, viene sospeso.

Tenere presente che i processi per i ruoli di lavoro ibridi per Runbook vengono eseguiti con l'account di sistema locale in Windows o con l'account **nxautomation** in Linux. Per Linux, è necessario assicurarsi che l'account **nxautomation** disponga dell'accesso al percorso in cui sono archiviati i moduli Runbook. Quando si usa il cmdlet [install-module](/powershell/module/powershellget/install-module) , assicurarsi di specificare ALLUSERS per il parametro `Scope` per assicurarsi che l'account **nxautomation** abbia accesso. Per altre informazioni su PowerShell in Linux, vedere [problemi noti per PowerShell in piattaforme non Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Avvio di un Runbook in un ruolo di lavoro ibrido per Runbook

[L'avvio di un Runbook in automazione di Azure](automation-starting-a-runbook.md) descrive metodi diversi per l'avvio di un Runbook. L'avvio di un Runbook in un ruolo di lavoro ibrido per Runbook usa un'opzione **Run on** che consente di specificare il nome di un gruppo di ruolo di lavoro ibrido per Runbook. Quando viene specificato un gruppo, uno dei thread di lavoro del gruppo recupera ed esegue il Runbook. Se il Runbook non specifica questa opzione, automazione di Azure esegue il Runbook come di consueto.

Quando si avvia un Runbook nel portale di Azure, viene visualizzata l'opzione **Esegui su** per la quale è possibile selezionare **Azure** o ruolo di **lavoro ibrido**. Se si seleziona ruolo di **lavoro ibrido**, è possibile scegliere il gruppo di lavoro ibrido per Runbook da un elenco a discesa.

Usare il parametro `RunOn` con il cmdlet `Start-AzureAutomationRunbook`. L'esempio seguente usa Windows PowerShell per avviare un Runbook denominato **test-Runbook** in un gruppo di ruolo di lavoro ibrido per Runbook denominato MyHybridGroup.

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Il parametro `RunOn` è stato aggiunto al `Start-AzureAutomationRunbook` nella versione 0.9.1 di Microsoft Azure PowerShell. È consigliabile [scaricare la versione più recente](https://azure.microsoft.com/downloads/) se la versione installata è precedente. Installare questa versione solo nella workstation in cui si avvia il Runbook da PowerShell. Non è necessario installarlo nel computer di lavoro ibrido per Runbook, a meno che non si intenda avviare manuali operativi da questo computer.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Utilizzo di manuali operativi con segno in un ruolo di lavoro ibrido per Runbook Windows

È possibile configurare un ruolo di lavoro ibrido per Runbook Windows per eseguire solo manuali operativi con segno.

> [!IMPORTANT]
> Dopo aver configurato un ruolo di lavoro ibrido per Runbook per eseguire solo manuali operativi firmati, non sarà possibile eseguire manuali operativi che non sono stati firmati sul ruolo di lavoro.

### <a name="create-signing-certificate"></a>Creare il certificato di firma

L'esempio seguente crea un certificato autofirmato che può essere usato per la firma dei runbook. Questo codice crea il certificato e lo esporta in modo che il ruolo di lavoro ibrido per Runbook possa essere importato in un secondo momento. L'identificazione personale viene restituita anche per un uso successivo nel riferimento al certificato.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importare il certificato e configurare i ruoli di lavoro per la convalida della firma

Copiare il certificato creato in ogni ruolo di lavoro ibrido per Runbook in un gruppo. Eseguire lo script seguente per importare il certificato e configurare i ruoli di lavoro per l'uso della convalida della firma in manuali operativi.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Firmare il manuali operativi usando il certificato

Con i ruoli di lavoro ibridi per Runbook configurati per usare solo manuali operativi firmati, è necessario firmare manuali operativi che devono essere usati in Hybrid Runbook Worker. Usare il codice PowerShell di esempio seguente per firmare questi manuali operativi.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando un Runbook è stato firmato, è necessario importarlo nell'account di automazione e pubblicarlo con il blocco della firma. Per informazioni su come importare i runbook,vedere [Importazione di un runbook da un file in Automazione di Azure](manage-runbooks.md#import-a-runbook).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Uso di manuali operativi firmato in un ruolo di lavoro ibrido per Runbook di Linux

Per poter usare manuali operativi con segno, un ruolo di lavoro ibrido per Runbook di Linux deve avere il file eseguibile [GPG](https://gnupg.org/index.html) nel computer locale.

> [!IMPORTANT]
> Dopo aver configurato un ruolo di lavoro ibrido per Runbook per eseguire solo manuali operativi firmati, non sarà possibile eseguire manuali operativi che non sono stati firmati sul ruolo di lavoro.

### <a name="create-a-gpg-keyring-and-keypair"></a>Creare un keyring e una coppia di chiavi di GPG

Per creare il portachiavi e la coppia di chiavi GPG, usare l'account **nxautomation** di lavoro ibrido per Runbook.

1. Usare l'applicazione sudo per accedere con l'account **nxautomation** .

    ```bash
    sudo su – nxautomation
    ```

2. Quando si usa **nxautomation**, generare la coppia di coppie di nomi di GPG. GPG guida l'utente nei passaggi necessari. È necessario specificare il nome, l'indirizzo di posta elettronica, l'ora di scadenza e la passphrase. Si attende quindi che la chiave da generare sia sufficiente per l'entropia del computer.

    ```bash
    sudo gpg --generate-key
    ```

3. Poiché la directory GPG è stata generata con sudo, è necessario modificarne il proprietario in **nxautomation** usando il comando seguente.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Rendere disponibile il portachiavi per il ruolo di lavoro ibrido per Runbook

Una volta creato il portachiavi, renderlo disponibile per il ruolo di lavoro ibrido per Runbook. Modificare il file di impostazioni **/var/opt/Microsoft/omsagent/state/automationworker/DIY/Worker.conf** in modo da includere il codice di esempio seguente nella sezione file `[worker-optional]`.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Verificare che la convalida della firma sia attiva

Se la convalida della firma è stata disabilitata nel computer, è necessario attivarla eseguendo il comando sudo seguente. Sostituire `<LogAnalyticsworkspaceId>` con l'ID dell'area di lavoro.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Firmare un runbook

Dopo aver configurato la convalida della firma, usare il comando GPG seguente per firmare un Runbook.

```bash
gpg –-clear-sign <runbook name>
```

Il Runbook firmato viene chiamato `<runbook name>.asc`.

È ora possibile caricare il Runbook firmato in automazione di Azure ed eseguirlo come un normale Runbook.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui metodi per l'avvio di un Runbook, vedere [avvio di un Runbook in automazione di Azure](automation-starting-a-runbook.md).
* Per informazioni su come usare l'editor di testo per lavorare con manuali operativi di PowerShell in automazione di Azure, vedere [modifica di un Runbook in automazione di Azure](automation-edit-textual-runbook.md).
* Se il manuali operativi non viene completato correttamente, vedere la guida alla risoluzione dei problemi relativi agli [errori di esecuzione di Runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
