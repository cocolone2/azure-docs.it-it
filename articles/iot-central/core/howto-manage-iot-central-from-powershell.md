---
title: Gestire IoT Central da Azure PowerShell | Microsoft Docs
description: Questo articolo descrive come creare e gestire le applicazioni IoT Central da Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1598451ce184db5a25cac28870b70a446aef123c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198821"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Gestire IoT Central da Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Invece di creare e gestire applicazioni IoT Central nel sito Web di [Azure IOT Central Application Manager](https://aka.ms/iotcentral) , è possibile usare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per gestire le applicazioni.

## <a name="prerequisites"></a>Prerequisites

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si preferisce eseguire Azure PowerShell nel computer locale, vedere [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) (Installare il modulo Azure PowerShell). Quando si esegue Azure PowerShell in locale, usare il cmdlet **Connect-AzAccount** per accedere ad Azure prima di eseguire i cmdlet illustrati in questo articolo.

> [!TIP]
> Se è necessario eseguire i comandi di PowerShell in una sottoscrizione di Azure diversa, vedere [modificare la sottoscrizione attiva](/powershell/azure/manage-subscriptions-azureps?view=azps-3.4.0#change-the-active-subscription).

## <a name="install-the-iot-central-module"></a>Installare il modulo IoT Central

Per verificare che il [modulo IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) sia installato nell'ambiente PowerShell, eseguire il comando seguente:

```powershell
Get-InstalledModule -name Az.I*
```

Se l'elenco dei moduli installati non include **Az.IotCentral**, eseguire il comando seguente:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Creare un'applicazione

Usare il cmdlet [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) per creare un'applicazione IoT Central nella sottoscrizione di Azure. Ad esempio:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Lo script crea prima un gruppo di risorse nell'area Stati Uniti orientali per l'applicazione. La tabella seguente descrive i parametri usati con il comando **New-AzIotCentralApp**:

|Parametro         |Descrizione |
|------------------|------------|
|ResourceGroupName |Gruppo di risorse che contiene l'applicazione. Questo gruppo di risorse deve già esistere nella sottoscrizione. |
|Location |Per impostazione predefinita, questo cmdlet usa la località definita per il gruppo di risorse. Attualmente, è possibile creare un'applicazione IoT Central in **Australia**, **Asia Pacifico**, **Europa**o **Stati Uniti** geografie.  |
|Nome              |Nome dell'applicazione nel portale di Azure. |
|Sottodominio         |Sottodominio nell'URL dell'applicazione. In questo esempio l'URL dell'applicazione è https://mysubdomain.azureiotcentral.com. |
|Sku               |Attualmente, è possibile usare **ST1** o **ST2**. Vedere [Prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modello          | Modello di applicazione da usare. Per altre informazioni, vedere la tabella seguente. |
|DisplayName       |Nome dell'applicazione visualizzato nell'interfaccia utente. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>Visualizzare le applicazioni IoT Central

Usare il cmdlet [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) per elencare le applicazioni IoT Central e visualizzare i metadati.

## <a name="modify-an-application"></a>Modificare un'applicazione

Usare il cmdlet [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) per aggiornare i metadati di un'applicazione IoT Central. Ad esempio, per modificare il nome visualizzato dell'applicazione creata:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Rimuovere un'applicazione

Usare il cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) per eliminare un'applicazione IoT Central. Ad esempio:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni Azure IoT Central in Azure PowerShell, il passaggio successivo consigliato è:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)
