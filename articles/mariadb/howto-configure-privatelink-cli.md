---
title: Collegamento privato-interfaccia della riga di comando di Azure-database di Azure per MariaDB
description: Informazioni su come configurare il collegamento privato per il database di Azure per MariaDB dall'interfaccia della riga di comando di Azure
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: c28c5494c1cff2c198a94ea6b92003ae74ee2c8e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371801"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>Creare e gestire un collegamento privato per database di Azure per MariaDB tramite l'interfaccia della riga di comando

Un endpoint privato è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse Collegamento privato. In questo articolo si apprenderà come usare l'interfaccia della riga di comando di Azure per creare una VM in una rete virtuale di Azure e un database di Azure per il server MariaDB con un endpoint privato di Azure.

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui il database di Azure per MariaDB supporta i piani tariffari per utilizzo generico e con ottimizzazione per la memoria.

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questa guida, si richiedono:

- Un [database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si decide di installare e usare l'interfaccia della riga di comando di Azure in locale, per questa guida introduttiva è necessario usare l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per poter creare le risorse, è prima necessario creare un gruppo di risorse in cui ospitare la rete virtuale. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). Questo esempio crea un gruppo di risorse denominato *myResourceGroup* nella località *westeurope* :

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Creazione di una rete virtuale
Creare una rete virtuale con il comando [az network vnet create](/cli/azure/network/vnet). Questo esempio crea una rete virtuale predefinita denominata *myVirtualNetwork* con una subnet denominata *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Disabilitare i criteri per gli endpoint privati della subnet 
Azure distribuisce le risorse in una subnet all'interno di una rete virtuale, di conseguenza è necessario creare o aggiornare la subnet per disabilitare i criteri di rete per gli endpoint privati. Aggiornare una configurazione di subnet denominata *mySubnet* con [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Creazione della VM 
Creare una macchina virtuale con il comando az vm create. Quando richiesto, specificare la password da usare come credenziali di accesso per la macchina virtuale. Questo esempio crea una macchina virtuale denominata *myVM*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Prendere nota dell'indirizzo IP pubblico della macchina virtuale. Questo indirizzo verrà usato per connettersi alla VM da Internet nel passaggio successivo.

## <a name="create-an-azure-database-for-mariadb-server"></a>Creare un database di Azure per un server MariaDB 
Creare un database di Azure per MariaDB con il comando AZ mariadb server create. Tenere presente che il nome del server MariaDB deve essere univoco in Azure, quindi sostituire il valore del segnaposto tra parentesi quadre con il proprio valore univoco: 

```azurecli-interactive
# Create a logical server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Si noti che l'ID del server MariaDB è simile a ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMariaDB/servers/servername.``` l'ID del server MariaDB verrà usato nel passaggio successivo. 

## <a name="create-the-private-endpoint"></a>Creare l'endpoint privato 
Creare un endpoint privato per il server MariaDB nella rete virtuale: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MariaDB Server ID>" \  
    --group-ids mariadbServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privato 
Creare una zona DNS privato per il dominio del server MariDB e creare un collegamento di associazione con la rete virtuale. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mariadb.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mariadb.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Il nome di dominio completo nell'impostazione DNS del cliente non viene risolto nell'indirizzo IP privato configurato. Sarà necessario configurare una zona DNS per il nome di dominio completo configurato come illustrato di [seguito](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connettersi alla macchina virtuale *myVm* da Internet come indicato di seguito:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file *downloaded.rdp*.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Scegliere **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Accedere al server MariaDB privatamente dalla macchina virtuale

1. Nel desktop remoto di  *myVM* aprire PowerShell.

2. Immettere  `nslookup mydemoserver.privatelink.mariadb.database.azure.com`. 

    Verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Testare la connessione del collegamento privato per il server MariaDB usando un client disponibile. Nell'esempio seguente ho usato [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) per eseguire l'operazione.

4. In **nuova connessione**immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome della connessione| Selezionare il nome della connessione scelta.|
    | HostName | Seleziona *mydemoserver.privatelink.MariaDB.database.Azure.com* |
    | Username | Immettere username come *username@servername* fornito durante la creazione del server MariaDB. |
    | Password | Immettere una password specificata durante la creazione del server MariaDB. |
    ||

5. Selezionare **Test connessione** o **OK**.

6. Facoltativamente Esplorare i database dal menu a sinistra e creare o eseguire query sulle informazioni dal database MariaDB

8. Chiudere la connessione Desktop remoto a myVm.

## <a name="clean-up-resources"></a>Pulire le risorse 
Quando non sono più necessari, è possibile usare il comando az group delete per rimuovere il gruppo di risorse e tutte le risorse in esso contenute: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sull' [endpoint privato di Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
