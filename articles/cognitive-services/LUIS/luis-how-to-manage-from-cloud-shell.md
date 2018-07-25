---
title: Wyświetl użycie usługi LUIS z usługi Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać informacje dotyczące użycia w usłudze Azure Cloud Shell dla usługi LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2017
ms.author: diberry
ms.openlocfilehash: f9d3a489328263bd300d60935bfde2f0dfc0245c
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224045"
---
# <a name="manage-luis-service-from-azure-cloud-shell"></a>Zarządzanie usługą usługi LUIS z usługi Azure Cloud Shell
Portal systemu Azure pozwala na używanie poleceń cmdlet programu PowerShell do pracy z zasobami usługi LUIS. 

Te polecenia cmdlet umożliwiają [tworzenie](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) LUIS subskrypcji, uzyskaj informacje o subskrypcji, w tym [użycia](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), i [Usuń](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) subskrypcji. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Konta magazynu w chmurze powłoki i uwierzytelniania
Aby można było używać programu PowerShell w witrynie Azure portal [cloud shell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell), musisz mieć konto usługi Azure storage. Jeśli nie masz [konta magazynu](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share), użytkownik jest monitowany o jego utworzenie. Konto magazynu pozwala zapisać skrypty programu PowerShell w usłudze cloud shell.  

Należy również uwierzytelnianie na platformie Azure w usłudze cloud shell uzyskiwać dostępu do zasobów. 

Gdy nie masz konta magazynu i uwierzytelnieniu, można uruchomić polecenia cmdlet programu PowerShell.

## <a name="open-cloud-shell"></a>Otwieranie usługi Cloud Shell
Gdy używasz usługi Azure portal cloud shell, Ty masz zawsze w najnowszej wersji programu PowerShell. 

Użyj **Launch Cloud Shell** przycisk Otwórz usługę Cloud Shell lub Otwórz przeglądarkę z [ https://shell.azure.com ](https://shell.azure.com). 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>Informacje o użyciu punktu końcowego usługi LUIS

Polecenia cmdlet programu PowerShell w wersji 6.x, `Get-AzureRmCognitiveServicesAccountUsage`, zapewnia informacje o użyciu dla usług Microsoft Cognitive Services w tym usługi LUIS. [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) wymaga grupy zasobów i nazwę zasobu usługi. 

Składnia polecenia jest następująca:

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

W poniższym przykładzie nazwa grupy zasobów jest `luis-westus-rg` i jest nazwa subskrypcji usługi LUIS `luis-westus-1`. Obie te nazwy są wybierane po utworzeniu usługi LUIS. 

Polecenie cmdlet zwraca informacje o użyciu 16 10 000 trafienia punktu końcowego, używany w ciągu 30 dni, z okresu kończącego się w dniu 7 czerwca:

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Zapisz polecenia jako plik programu PowerShell, *.ps1 koncie magazynu platformy Azure skojarzone z usłudze cloud shell, a następnie wykonaj w dowolnym momencie. 

![Uruchom skrypt z usługi storage](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

Po skryptu są zapisywane na dysku w chmurze, możesz uruchomić skrypt programu PowerShell z platformy Azure phone aplikacji usługa cloud shell. 

![Uruchom skrypt z usługi storage w aplikacji na telefon](./media/luis-how-to-manage-from-powershell/phone-app.png)