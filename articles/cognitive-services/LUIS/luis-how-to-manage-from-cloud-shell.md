---
title: Wyświetl użycie LUIS z powłoki chmury Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można uzyskać w powłoce chmury Azure LUIS informacje o użyciu.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2017
ms.author: v-geberr
ms.openlocfilehash: 2de25645e5377efdd53bcc980695804d34db5ee2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348996"
---
# <a name="manage-luis-service-from-azure-cloud-shell"></a>Zarządzanie usługą LUIS z powłoki chmury Azure
Azure portal umożliwia przy użyciu poleceń cmdlet programu PowerShell do pracy z zasobami LUIS. 

Te polecenia cmdlet umożliwiają [utworzyć](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) subskrypcję LUIS pobranie informacji o subskrypcji, w tym [użycia](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), i [Usuń](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) subskrypcji. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Konta magazynu w chmurze powłoki i uwierzytelniania
Aby można było używać programu PowerShell w portalu Azure [chmury powłoki](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell), musisz mieć konto magazynu platformy Azure. Jeśli nie masz [konta magazynu](https://docs.microsoft.com/en-us/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share), pojawi się monit o jego utworzenie. Konto magazynu umożliwia zapisanie skryptów programu PowerShell w powłoce chmury.  

Należy również uwierzytelniać na platformie Azure w powłoce chmury uzyskiwać dostępu do zasobów. 

Po konta magazynu i są uwierzytelniani, można uruchomić poleceń cmdlet programu PowerShell.

## <a name="open-cloud-shell"></a>Otwieranie usługi Cloud Shell
Korzystając z powłoki chmurze portalu Azure, możesz są zawsze w najnowszej wersji programu PowerShell. 

Użyj **uruchamianie powłoki chmury** przycisk, aby otworzyć powłokę chmury, lub Otwórz przeglądarkę z [ https://shell.azure.com ](https://shell.azure.com). 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>Informacje o użyciu punktu końcowego LUIS

Polecenia cmdlet programu PowerShell 6.x `Get-AzureRmCognitiveServicesAccountUsage`, zapewnia informacje o użyciu kognitywnych usług firmy Microsoft w tym LUIS. [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) wymaga grupy zasobów i nazwa zasobu usługi. 

Składnia polecenia jest następująca:

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

W poniższym przykładzie nazwa grupy zasobów jest `luis-westus-rg` i Nazwa subskrypcji usługi LUIS jest `luis-westus-1`. Obie te nazwy zostały wybrane podczas tworzenia usługi LUIS. 

Polecenie cmdlet zwraca informacje o użyciu 16 10 000 trafień punktu końcowego używane w ciągu 30 dni, z okresu kończącego się w dniu 7 czerwca:

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Polecenie Zapisz jako plik programu PowerShell, *.ps1 na koncie magazynu platformy Azure skojarzone z powłoki chmury, a następnie wykonaj w dowolnym momencie. 

![Uruchom skrypt z magazynu](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

Po skrypt jest zapisywany na dysku w chmurze, można uruchomić skryptu programu PowerShell z platformy Azure aplikacji phone chmury powłoki. 

![Uruchom skrypt z magazynu w aplikacji telefonicznej](./media/luis-how-to-manage-from-powershell/phone-app.png)