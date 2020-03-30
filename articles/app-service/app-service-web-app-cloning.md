---
title: Klonowanie aplikacji za pomocą programu PowerShell
description: Dowiedz się, jak sklonować aplikację usługi App Service do nowej aplikacji przy użyciu programu PowerShell. Różne scenariusze klonowania są objęte, w tym integracji usługi Traffic Manager.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18
ms.openlocfilehash: e7ad45ea4cb1049ed7eeb454162e23e81ed35019
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255203"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Klonowanie aplikacji usługi Azure App Service przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wraz z wydaniem programu Microsoft Azure PowerShell w wersji 1.1.0 dodano nową opcję, która `New-AzWebApp` umożliwia klonowanie istniejącej aplikacji usługi App Service do nowo utworzonej aplikacji w innym regionie lub w tym samym regionie. Ta opcja umożliwia klientom szybkie i łatwe wdrażanie wielu aplikacji w różnych regionach.

Klonowanie aplikacji jest obsługiwane dla planów usługi aplikacji Standard, Premium, Premium V2 i Isolated. Nowa funkcja korzysta z tych samych ograniczeń co funkcja tworzenia kopii zapasowej usługi App Service, zobacz [Tworzenie kopii zapasowej aplikacji w usłudze Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Klonowanie istniejącej aplikacji
Scenariusz: istniejąca aplikacja w regionie Południowo-środkowe stany USA i chcesz sklonować zawartość do nowej aplikacji w regionie północno-środkowe stany USA. Można to osiągnąć za pomocą wersji usługi Azure Resource Manager polecenia cmdlet programu `-SourceWebApp` PowerShell, aby utworzyć nową aplikację z opcją.

Znając nazwę grupy zasobów zawierającą aplikację źródłową, można użyć następującego polecenia programu PowerShell, `source-webapp`aby uzyskać informacje o aplikacji źródłowej (w tym przypadku o nazwie):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Aby utworzyć nowy plan usługi aplikacji, można użyć `New-AzAppServicePlan` polecenia, jak w poniższym przykładzie

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Za `New-AzWebApp` pomocą polecenia można utworzyć nową aplikację w regionie północno-środkowe stany USA i powiązać ją z istniejącym planem usługi app service. Ponadto można użyć tej samej grupy zasobów co aplikacja źródłowa lub zdefiniować nową grupę zasobów, jak pokazano w następującym poleceniu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Aby sklonować istniejącą aplikację, w tym wszystkie skojarzone miejsca wdrożenia, należy użyć tego parametru. `IncludeSourceWebAppSlots`  Należy zauważyć, że `IncludeSourceWebAppSlots` parametr jest obsługiwany tylko do klonowania całej aplikacji, w tym wszystkich jej gniazd. Następujące polecenie programu PowerShell pokazuje użycie tego `New-AzWebApp` parametru z poleceniem:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Aby sklonować istniejącą aplikację w tym samym regionie, należy utworzyć nową grupę zasobów i nowy plan usługi aplikacji w tym samym regionie, a następnie użyć następującego polecenia programu PowerShell do sklonowania aplikacji:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klonowanie istniejącej aplikacji w środowisku usługi aplikacji
Scenariusz: istniejąca aplikacja w regionie południowo-środkowa usa i chcesz sklonować zawartość do nowej aplikacji do istniejącego środowiska usługi app service (ASE).

Znając nazwę grupy zasobów zawierającą aplikację źródłową, można użyć następującego polecenia programu PowerShell, `source-webapp`aby uzyskać informacje o aplikacji źródłowej (w tym przypadku o nazwie):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Znając nazwę ASE i nazwę grupy zasobów, do której należy ASE, można utworzyć nową aplikację w istniejącym ASE, jak pokazano w następującym poleceniu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

Parametr `Location` jest wymagany ze względu na starszą przyczynę, ale jest ignorowany podczas tworzenia aplikacji w ase. 

## <a name="cloning-an-existing-app-slot"></a>Klonowanie istniejącego gniazda aplikacji
Scenariusz: Chcesz sklonować istniejące gniazdo wdrażania aplikacji do nowej aplikacji lub nowego gniazda. Nowa aplikacja może znajdować się w tym samym regionie co oryginalny slot aplikacji lub w innym regionie.

Znając nazwę grupy zasobów zawierającą aplikację źródłową, można użyć następującego polecenia programu PowerShell, `source-appslot`aby uzyskać `source-app`informacje o źródle gniazda aplikacji (w tym przypadku o nazwie) powiązane z:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Następujące polecenie pokazuje tworzenie klonu aplikacji źródłowej do nowej aplikacji:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurowanie menedżera ruchu podczas klonowania aplikacji
Tworzenie aplikacji wieloregionowych i konfigurowanie usługi Azure Traffic Manager do kierowania ruchu do wszystkich tych aplikacji jest ważnym scenariuszem, aby zapewnić, że aplikacje klientów są wysoce dostępne. Podczas klonowania istniejącej aplikacji można połączyć obie aplikacje z nowym profilem menedżera ruchu lub istniejącym. Obsługiwane jest tylko wersja usługi Azure Resource Manager usługi Traffic Manager.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Tworzenie nowego profilu usługi Traffic Manager podczas klonowania aplikacji
Scenariusz: chcesz sklonować aplikację do innego regionu, podczas konfigurowania profilu menedżera ruchu usługi Azure Resource Manager, który zawiera obie aplikacje. Następujące polecenie pokazuje tworzenie klona aplikacji źródłowej do nowej aplikacji podczas konfigurowania nowego profilu usługi Traffic Manager:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Dodawanie nowej sklonowanej aplikacji do istniejącego profilu usługi Traffic Manager
Scenariusz: masz już profil menedżera ruchu usługi Azure Resource Manager i chcesz dodać obie aplikacje jako punkty końcowe. Aby to zrobić, należy najpierw zebrać istniejący identyfikator profilu menedżera ruchu. Potrzebny jest identyfikator subskrypcji, nazwa grupy zasobów i istniejąca nazwa profilu menedżera ruchu.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Po uzyskaniu identyfikatora menedżera ruchu następujące polecenie pokazuje tworzenie klona aplikacji źródłowej do nowej aplikacji podczas dodawania ich do istniejącego profilu usługi Traffic Manager:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Bieżące ograniczenia
Oto znane ograniczenia klonowania aplikacji:

* Ustawienia automatycznego skalowania nie są klonowane
* Ustawienia harmonogramu tworzenia kopii zapasowych nie są klonowane
* Ustawienia sieci wirtualnej nie są klonowane
* Usługi App Insights nie są automatycznie łączane w aplikacji docelowej
* Łatwe ustawienia emocie nie są klonowane
* Rozszerzenie Kudu nie są klonowane
* Reguły TiP nie są klonowane
* Zawartość bazy danych nie jest klonowana
* Wychodzące adresy IP zmieniają się, jeśli klonowanie do innej jednostki skalowania
* Niedostępne dla aplikacji na Linuksa

### <a name="references"></a>Dokumentacja
* [Klonowanie usługi aplikacji](app-service-web-app-cloning.md)
* [Łącze zapasowe aplikacji w usłudze Azure App Service](manage-backup.md)
* [Pomoc techniczna usługi Azure Resource Manager dla usługi Azure Traffic Manager Preview](../traffic-manager/traffic-manager-powershell-arm.md)
* [Wprowadzenie do usługi App Service Environment](environment/intro.md)
* [Używanie programu Azure PowerShell z usługą Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

