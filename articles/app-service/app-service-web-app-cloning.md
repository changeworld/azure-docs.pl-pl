---
title: Klonowanie aplikacji za pomocą programu PowerShell — usługa Azure App Service
description: Dowiedz się, jak sklonować aplikację usługi App Service w nowej aplikacji przy użyciu programu PowerShell.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 9d4b664c9b1fc0deb10794a5f0b29c2b600d19e2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53712667"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Usługa Azure App Service aplikacja klonowania, przy użyciu programu PowerShell
Wraz z wydaniem programu Microsoft Azure PowerShell w wersji 1.1.0 dodano nową opcję do `New-AzureRMWebApp` umożliwiający klonowanie istniejącej aplikacji usługi App Service do aplikacji nowo utworzonej w innym regionie lub w tym samym regionie. Ta opcja umożliwia klientom wdrażanie liczby aplikacji w różnych regionach, szybkie i łatwe.

Klonowanie aplikacji jest obecnie obsługiwana tylko w przypadku planów usługi app service — wersja premium warstwy. Nowa funkcja używa te same ograniczenia co funkcji Kopia zapasowa usługi aplikacji, zobacz [wykonywanie kopii zapasowych aplikacji w usłudze Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Klonowanie istniejącej aplikacji
Scenariusz: Istniejącej aplikacji w regionie południowo-środkowe stany USA, a chcesz sklonować zawartość w nowej aplikacji w regionie północno-środkowe stany USA. Można to osiągnąć przy użyciu polecenia cmdlet programu PowerShell w wersji usługi Azure Resource Manager w celu utworzenia nowej aplikacji z `-SourceWebApp` opcji.

Wiedząc, nazwę grupy zasobów zawierającej aplikację dla źródła, można użyć następującego polecenia programu PowerShell można pobrać informacji o aplikacji źródłowej (w tym przypadku o nazwie `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Aby utworzyć nowy Plan usługi App Service, można użyć `New-AzureRmAppServicePlan` polecenia jak w poniższym przykładzie

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Za pomocą `New-AzureRmWebApp` polecenia Utwórz nową aplikację w regionie północno-środkowe stany USA i powiązanie ich do istniejącej warstwy premium, Plan usługi App Service. Ponadto możesz użyć tej samej grupy zasobów jako źródłowej aplikacji lub zdefiniować nową grupę zasobów, jak pokazano w poniższym poleceniu:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Aby sklonować istniejącą aplikację w tym wszystkie skojarzone wdrożenie gniazda, należy użyć `IncludeSourceWebAppSlots` parametru. Następujące polecenie programu PowerShell pokazano użycie tego parametru z `New-AzureRmWebApp` polecenia:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Klonowanie istniejącej aplikacji w tym samym regionie, musisz utworzyć nową grupę zasobów i nowej usługi app service plan w tym samym regionie, a następnie użyj następującego polecenia programu PowerShell, aby sklonować aplikację:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klonowanie istniejącej aplikacji do środowiska usługi App Service
Scenariusz: Istniejącej aplikacji w regionie południowo-środkowe stany USA, a chcesz sklonować zawartość w nowej aplikacji do istniejącego App Service Environment (ASE).

Wiedząc, nazwę grupy zasobów zawierającej aplikację dla źródła, można użyć następującego polecenia programu PowerShell można pobrać informacji o aplikacji źródłowej (w tym przypadku o nazwie `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Wiedząc, nazwa środowiska ASE i nazwę grupy zasobów, której należy środowiska ASE, można utworzyć nowej aplikacji w istniejących ASE, jak pokazano w poniższym poleceniu:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

`Location` Parametr jest wymagany z powodu starszej wersji, ale jest on ignorowany podczas tworzenia aplikacji w środowisku ASE. 

## <a name="cloning-an-existing-app-slot"></a>Klonowanie istniejącego miejsca aplikacji
Scenariusz: Chcesz sklonować istniejące miejsce wdrożenia aplikacji w nowej aplikacji lub nowe miejsce. Nowa aplikacja może być w tym samym regionie, co oryginalny miejsca aplikacji lub w innym regionie.

Wiedząc, nazwę grupy zasobów zawierającej aplikację dla źródła, można użyć następującego polecenia programu PowerShell można pobrać informacji o miejscu aplikacji źródłowym (w tym przypadku o nazwie `source-appslot`) związany z `source-app`:

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Następujące polecenie pokazuje, utworzenie klona źródłowej aplikacji w nowej aplikacji:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurowanie usługi Traffic Manager podczas klonowania aplikacji
Tworzenie aplikacji w wielu regionach i konfigurowanie usługi Azure Traffic Manager kierować ruchem do tych aplikacji, jest to ważne, aby zapewnić wysoką dostępność aplikacji klientów. Podczas klonowania istniejącej aplikacji, istnieje możliwość połączyć obie aplikacje do istniejącego lub nowego profilu usługi traffic manager. Tylko usługi Azure Resource Manager w wersji usługi Traffic Manager jest obsługiwana.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Tworzenie nowego profilu usługi Traffic Manager podczas klonowania aplikacji
Scenariusz: Chcesz sklonować aplikację do innego regionu podczas konfigurowania usługi Azure Resource Manager profilu usługi traffic manager zawierający obie aplikacje. Następujące polecenie pokazuje, utworzenie klona źródłowej aplikacji w nowej aplikacji podczas konfigurowania profilu usługi Traffic Manager:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Dodano nowe sklonowany aplikacji do istniejącego profilu usługi Traffic Manager
Scenariusz: Już masz profilu usługi traffic manager usługi Azure Resource Manager i chcesz dodać obie aplikacje jako punktów końcowych. Aby to zrobić, należy najpierw złożyć istniejącego ruchu profilu Menedżera identyfikatora. Potrzebujesz Identyfikatora subskrypcji, nazwę grupy zasobów i istniejącej nazwy profilu Menedżera ruchu.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Po identyfikatorze Menedżer ruchu, następujące polecenie pokazuje, utworzenie klona źródłowej aplikacji w nowej aplikacji podczas dodawania ich do istniejącego profilu usługi Traffic Manager:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Bieżące ograniczenia
Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a nowe możliwości są dodawane wraz z upływem czasu. Poniżej przedstawiono znane ograniczenia dotyczące bieżącą wersję klonowanie aplikacji:

* Ustawienia skalowania automatycznego nie są klonowane.
* Ustawienia harmonogramu tworzenia kopii zapasowej nie są klonowane.
* Ustawienia sieci Wirtualnej nie są klonowane.
* App Insights są nie automatycznie skonfigurowane w aplikacji docelowej
* Łatwe ustawień uwierzytelniania nie są klonowane.
* Aparat kudu rozszerzenia nie są klonowane.
* Porada reguły nie są klonowane.
* Zawartość bazy danych nie są klonowane.
* Wychodzące adresy IP zmienia się, gdy klonowania na jednostkę skalowania innego

### <a name="references"></a>Dokumentacja
* [Klonowanie w usłudze App Service](app-service-web-app-cloning.md)
* [Tworzenie kopii zapasowych aplikacji w usłudze Azure App Service](manage-backup.md)
* [Pomoc techniczna platformy Azure Resource Manager dla usługi Azure Traffic Manager w wersji zapoznawczej](../traffic-manager/traffic-manager-powershell-arm.md)
* [Wprowadzenie do usługi App Service Environment](environment/intro.md)
* [Używanie programu Azure PowerShell z usługą Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)

