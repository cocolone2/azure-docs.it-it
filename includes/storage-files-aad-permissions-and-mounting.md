---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 23550c83e76631e44d5036e0a038f01b61a79f1b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208241"
---
## <a name="assign-access-permissions-to-an-identity"></a>Assegnare le autorizzazioni di accesso a un'identità

Per accedere alle risorse File di Azure con l'autenticazione basata su identità, un'identità (un utente, un gruppo o un'entità servizio) deve disporre delle autorizzazioni necessarie a livello di condivisione. Questo processo è simile alla specifica delle autorizzazioni di condivisione di Windows, in cui è possibile specificare il tipo di accesso di un determinato utente a una condivisione file. Le istruzioni dettagliate fornite in questa sezione spiegano come assegnare a un'identità le autorizzazioni di lettura, scrittura o eliminazione per una condivisione file.

Sono stati introdotti tre ruoli predefiniti di Azure per la concessione di autorizzazioni a livello di condivisione agli utenti:

- **Storage file data SMB Share Reader** consente l'accesso in lettura alle condivisioni file di archiviazione di Azure tramite SMB.
- Il **collaboratore condivisione SMB per i dati dei file di archiviazione** consente l'accesso in lettura, scrittura ed eliminazione nelle condivisioni file di archiviazione di Azure tramite SMB.
- **Storage file data SMB Share collaboratore con privilegi elevati** consente di leggere, scrivere, eliminare e modificare le autorizzazioni NTFS nelle condivisioni file di archiviazione di Azure tramite SMB.

> [!IMPORTANT]
> Il controllo amministrativo completo di una condivisione file, inclusa la possibilità di assumere la proprietà di un file, richiede l'uso della chiave dell'account di archiviazione. Il controllo amministrativo non è supportato con le credenziali di Azure AD.

È possibile usare la portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per assegnare i ruoli predefiniti all'identità Azure AD di un utente per concedere autorizzazioni a livello di condivisione.

> [!NOTE]
> Ricordarsi di sincronizzare le credenziali di Active Directory con Azure AD se si prevede di usare l'Active Directory per l'autenticazione. La sincronizzazione dell'hash delle password da AD a Azure AD è facoltativa. L'autorizzazione a livello di condivisione verrà concessa al Azure AD identità sincronizzata da Active Directory.

#### <a name="azure-portal"></a>Portale di Azure
Per assegnare un ruolo controllo degli accessi in base al ruolo a un'identità Azure AD, usando il [portale di Azure](https://portal.azure.com), attenersi alla procedura seguente:

1. Nella portale di Azure passare alla condivisione file o [creare una condivisione file](../articles/storage/files/storage-how-to-create-file-share.md).
2. Selezionare **Controllo di accesso (IAM)** .
3. Selezionare **Aggiungi un'assegnazione di ruolo**
4. Nel pannello **Aggiungi assegnazione ruolo** selezionare il ruolo predefinito appropriato (Storage file data SMB Share Reader, storage file data SMB condivisione Contributor) dall'elenco dei **ruoli** . Lasciare **assegna accesso a** con l'impostazione predefinita: **Azure ad utente, gruppo o entità servizio**. Selezionare la destinazione Azure AD identità in base al nome o all'indirizzo di posta elettronica.
5. Selezionare **Salva** per completare l'operazione di assegnazione di ruolo.

#### <a name="powershell"></a>PowerShell

L'esempio di PowerShell seguente illustra come assegnare un ruolo RBAC a un'identità Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione dei ruoli RBAC con PowerShell, vedere [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Prima di eseguire lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, comprese le parentesi, con valori personalizzati.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Il comando seguente dell'interfaccia della riga di comando 2,0 illustra come assegnare un ruolo RBAC a un'identità Azure AD, in base al nome di accesso. Per ulteriori informazioni sull'assegnazione di ruoli RBAC con l'interfaccia della riga di comando di Azure, vedere [Manage Access by using RBAC and Azure CLI](../articles/role-based-access-control/role-assignments-cli.md). 

Prima di eseguire lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, comprese le parentesi, con valori personalizzati.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Configurare le autorizzazioni NTFS tramite SMB 
Dopo aver assegnato le autorizzazioni a livello di condivisione con RBAC, è necessario assegnare le autorizzazioni NTFS appropriate a livello di radice, directory o file. Si pensi a autorizzazioni a livello di condivisione come Gatekeeper di alto livello che determina se un utente può accedere alla condivisione. Mentre le autorizzazioni NTFS agiscono a un livello più granulare per determinare le operazioni che l'utente può eseguire a livello di directory o di file.

File di Azure supporta il set completo di autorizzazioni NTFS di base e avanzate. È possibile visualizzare e configurare le autorizzazioni NTFS per directory e file in una condivisione file di Azure montando la condivisione e quindi usando Esplora file di Windows o eseguendo il comando Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) o [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

Per configurare NTFS con autorizzazioni superuser, è necessario montare la condivisione usando la chiave dell'account di archiviazione della macchina virtuale aggiunta al dominio. Seguire le istruzioni nella sezione successiva per montare una condivisione file di Azure dal prompt dei comandi e configurare di conseguenza le autorizzazioni NTFS.

I set di autorizzazioni seguenti sono supportati per la directory radice di una condivisione file:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurare le autorizzazioni NTFS con icacls
Usare il comando seguente di Windows per concedere autorizzazioni complete a tutti i file e le sottodirectory nella condivisione file, inclusa la directory radice. Ricordarsi di sostituire i valori segnaposto nell'esempio con valori personalizzati.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Per ulteriori informazioni su come utilizzare icacls per impostare le autorizzazioni NTFS e sui diversi tipi di autorizzazioni supportate, vedere [la Guida di riferimento alla riga di comando per icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montare una condivisione file dal prompt dei comandi

Usare il comando di Windows **net use** per montare la condivisione file di Azure. Ricordarsi di sostituire i valori segnaposto nell'esempio seguente con valori personalizzati. Per altre informazioni sul montaggio di condivisioni file, vedere [usare una condivisione file di Azure con Windows](../articles/storage/files/storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Configurare le autorizzazioni NTFS con Esplora file di Windows
Utilizzare Esplora file di Windows per concedere l'autorizzazione completa a tutte le directory e i file nella condivisione file, inclusa la directory radice.

1. Aprire Esplora file di Windows e fare clic con il pulsante destro del mouse su file/directory e selezionare **Proprietà** .
2. Fare clic sulla scheda **sicurezza** .
3. Fare clic su **modifica..** . pulsante per modificare le autorizzazioni
4. È possibile modificare l'autorizzazione degli utenti esistenti oppure fare clic su **Aggiungi...** per concedere le autorizzazioni ai nuovi utenti
5. Nella finestra di messaggio di richiesta per l'aggiunta di nuovi utenti, immettere il nome utente di destinazione a cui si vuole concedere l'autorizzazione nella casella **immettere i nomi degli oggetti da selezionare** , quindi fare clic su **Controlla nomi** per trovare il nome UPN completo dell'utente di destinazione.
7.  Fare clic su **OK**
8.  Nella scheda sicurezza selezionare tutte le autorizzazioni che si desidera concedere al nuovo utente
9.  Fare clic su **Applica**

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montare una condivisione file da una macchina virtuale aggiunta a dominio

Il processo seguente verifica che la condivisione file e le autorizzazioni di accesso siano state configurate correttamente e che sia possibile accedere a una condivisione file di Azure da una macchina virtuale aggiunta a un dominio:

Accedere alla macchina virtuale usando l'identità Azure AD a cui sono state concesse le autorizzazioni, come illustrato nella figura seguente. Se è stata abilitata l'autenticazione di AD per File di Azure, usare le credenziali di Active Directory. Per l'autenticazione Azure AD DS, accedere con Azure AD Credential.

![Screenshot che mostra la schermata di accesso ad Azure AD per l'autenticazione utente](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Usare il comando seguente per montare la condivisione file di Azure. Ricordarsi di sostituire i valori segnaposto con valori personalizzati. Poiché è stata eseguita l'autenticazione, non è necessario specificare la chiave dell'account di archiviazione, le credenziali di Active Directory o le credenziali Azure AD. L'esperienza Single Sign-on è supportata per l'autenticazione con AD o Azure AD DS.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
