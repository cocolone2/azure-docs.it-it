---
title: 'PowerShell: aggiungere un utente esterno a un Lab in Azure DevTest Labs'
description: Questo articolo fornisce uno script di Azure PowerShell che aggiunge un utente esterno a un Lab in Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: e9efe5476da051d905feff3d6803cb60ea0c4beb
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760488"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Usare PowerShell per aggiunge un utente esterno a un lab in Azure DevTest Labs

Questo script di esempio di PowerShell aggiunge un utente esterno a un lab in Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Prerequisiti
* **Un lab**. Per usare lo script è necessario disporre di un lab esistente. 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Prova di nuovo l'oggetto utente da Azure Active Directory. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Assegna il ruolo specificato all'entità specificata nell'ambito specificato. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per Azure Lab Services sono disponibili in [Azure Lab Services PowerShell samples](../samples-powershell.md) (Esempi di PowerShell per Azure Lab Services).
