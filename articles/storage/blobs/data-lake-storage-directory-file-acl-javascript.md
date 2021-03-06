---
title: Utilizzare JavaScript per file & ACL in Azure Data Lake Storage Gen2 (anteprima)
description: Usare archiviazione di Azure Data Lake libreria client per JavaScript per gestire directory e elenchi di controllo di accesso (ACL) di file e directory negli account di archiviazione con spazio dei nomi gerarchico (HNS) abilitato.
author: normesta
ms.service: storage
ms.date: 12/18/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 8fd63adc76422b7fd9978e626208aa90593f8604
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154868"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Utilizzare JavaScript per gestire directory, file e ACL in Azure Data Lake Storage Gen2 (anteprima)

Questo articolo illustra come usare JavaScript per creare e gestire directory, file e autorizzazioni negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS). 

> [!IMPORTANT]
> La libreria JavaScript descritta in questo articolo è attualmente disponibile in anteprima pubblica.

[Pacchetto (gestione pacchetti node)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [inviare commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico (HNS) abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne uno.
> * Se si usa questo pacchetto in un'applicazione Node. js, è necessario 8.0.0 node. js o versione successiva.

## <a name="set-up-your-project"></a>Configurare il progetto

Installare Data Lake libreria client per JavaScript aprendo una finestra del terminale e digitando il comando seguente.

```javascript
npm install @azure/storage-file-datalake
```

Importare il pacchetto di `storage-file-datalake` inserendo questa istruzione all'inizio del file di codice. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Connettersi all'account 

Per usare i frammenti di codice in questo articolo, è necessario creare un'istanza di **DataLakeServiceClient** che rappresenta l'account di archiviazione. Il modo più semplice per ottenerne uno consiste nell'usare una chiave dell'account. 

Questo esempio crea un'istanza di **DataLakeServiceClient** usando una chiave dell'account.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> Questo metodo di autorizzazione funziona solo per le applicazioni node. js. Se si prevede di eseguire il codice in un browser, è possibile autorizzare usando Azure Active Directory (AD). Per istruzioni su come eseguire questa operazione, vedere il file Leggimi del file [di archiviazione di Azure Data Lake libreria client per JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) . 

## <a name="create-a-file-system"></a>Creare un file system

Un file system funge da contenitore per i file. È possibile crearne uno ottenendo un'istanza di **FileSystemClient** e quindi chiamando il metodo **FileSystemClient. Create** .

In questo esempio viene creato un file system denominato `my-file-system`. 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory ottenendo un'istanza di **DirectoryClient** e quindi chiamando il metodo **DirectoryClient. Create** .

In questo esempio viene aggiunta una directory denominata `my-directory` a un file system. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory chiamando il metodo **DirectoryClient. Rename** . Passare il percorso della directory desiderata a un parametro. 

Questo esempio rinomina una sottodirectory con il nome `my-directory-renamed`.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

In questo esempio una directory denominata `my-directory-renamed` viene spostata in una sottodirectory di una directory denominata `my-directory-2`. 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory chiamando il metodo **DirectoryClient. Delete** .

In questo esempio viene eliminata una directory denominata `my-directory`.   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Gestire un ACL di directory

Questo esempio ottiene e imposta l'ACL di una directory denominata `my-directory`. Questo esempio fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri utenti l'accesso in lettura.

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che l'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stata assegnata al [ruolo proprietario dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Per ulteriori informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere [controllo di accesso in Azure Data Lake storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Prima di tutto, leggere un file. Questo esempio usa il modulo `fs` di node. js. Quindi, creare un riferimento a un file nella directory di destinazione creando un'istanza **fileclient** e chiamando il metodo **fileclient. Create** . Caricare un file chiamando il metodo **fileclient. Append** . Assicurarsi di completare il caricamento chiamando il metodo **fileclient. Flush** .

Questo esempio consente di caricare un file di testo in una directory denominata `my-directory`.

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>Gestire un ACL di file

Questo esempio ottiene e imposta l'ACL di un file denominato `upload-file.txt`. Questo esempio fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri utenti l'accesso in lettura.

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che l'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stata assegnata al [ruolo proprietario dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Per ulteriori informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere [controllo di accesso in Azure Data Lake storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>Scarica da una directory

Per prima cosa, creare un'istanza di **FileSystemClient** che rappresenti il file che si desidera scaricare. Usare il metodo **FileSystemClient. Read** per leggere il file. Quindi, scrivere il file. Questo esempio usa il modulo `fs` di node. js a tale scopo. 

> [!NOTE]
> Questo metodo di download di un file funziona solo per le applicazioni node. js. Se si prevede di eseguire il codice in un browser, vedere il file Leggimi del file di [archiviazione di Azure Data Lake libreria client per JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) per un esempio di come eseguire questa operazione in un browser. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

In questo esempio vengono stampati i nomi di ogni directory e file che si trova in una directory denominata `my-directory`.

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Vedere anche

* [Pacchetto (gestione pacchetti di nodi)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-java/issues)