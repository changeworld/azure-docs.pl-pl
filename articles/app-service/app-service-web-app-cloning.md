---
title: Klonowanie aplikacji sieci Web przy użyciu programu PowerShell
description: Dowiedz się, jak klonowanie aplikacji sieci Web do nowej aplikacji sieci Web przy użyciu programu PowerShell.
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
ms.openlocfilehash: 30817a1a6a8079e7a896305ab0b59e48fad4d644
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2018
ms.locfileid: "27867476"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Usługa aplikacji Azure aplikacji klonowania przy użyciu programu PowerShell
Wraz z wydaniem programu Microsoft Azure PowerShell w wersji 1.1.0 dodano nową opcję do `New-AzureRMWebApp` , która umożliwia sklonować istniejącą aplikację sieci Web do aplikacji nowo utworzonej w innym regionie lub w tym samym regionie. Ta opcja umożliwia klienci mogą wdrożyć wiele aplikacji w różnych regionach, szybkie i łatwe.

Klonowanie aplikacji jest obecnie obsługiwany tylko w przypadku planów usługi aplikacji warstwy premium. Nowa funkcja używa tego samego ograniczenia jako funkcja kopii zapasowej aplikacji sieci Web, zobacz [kopii zapasowej aplikacji sieci web w usłudze Azure App Service](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Klonowanie istniejącej aplikacji
Scenariusz: Istniejącą aplikację sieci web w regionie południowo-środkowe Stany i chcesz sklonować zawartość do nowej aplikacji sieci web w regionie północno-środkowe Stany. Można osiągnąć za pomocą polecenia cmdlet programu PowerShell za pośrednictwem usługi Azure Resource Manager do tworzenia nowej aplikacji sieci web z `-SourceWebApp` opcji.

Znajomość Nazwa grupy zasobów, która zawiera źródłowej aplikacji sieci web, służy następujące polecenie programu PowerShell można uzyskać informacji o źródłowej aplikacji sieci web (w tym przypadku o nazwie `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Aby utworzyć nowy Plan usługi App Service, można użyć `New-AzureRmAppServicePlan` jak w poniższym przykładzie polecenia

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Przy użyciu `New-AzureRmWebApp` polecenia Utwórz nową aplikację sieci web w regionie północno-środkowe Stany i powiązać ją do istniejącej warstwy premium planu usługi App Service. Ponadto można użyć tej samej grupy zasobów jako źródłowej aplikacji sieci web lub zdefiniuj nową grupę zasobów, jak pokazano w poniższym poleceniu:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Klonowanie istniejącej aplikacji sieci web, w tym wszystkich miejsc wdrożenia skojarzony, należy użyć `IncludeSourceWebAppSlots` parametru. Pokazuje następującego polecenia programu PowerShell, użyj tego parametru z `New-AzureRmWebApp` polecenia:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Można sklonować istniejącą aplikację sieci web w tym samym regionie, należy utworzyć nową grupę zasobów i nowej usługi aplikacji planowanie w tym samym regionie, a następnie użyj następującego polecenia programu PowerShell na potrzeby klonowania aplikacji sieci web

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klonowanie istniejącej aplikacji do środowiska usługi aplikacji
Scenariusz: Istniejącą aplikację sieci web w regionie południowo-środkowe Stany i chcesz sklonować zawartość do nowej aplikacji sieci web do istniejącej aplikacji usługi środowiska (ASE).

Znajomość Nazwa grupy zasobów, która zawiera źródłowej aplikacji sieci web, służy następujące polecenie programu PowerShell można uzyskać informacji o źródłowej aplikacji sieci web (w tym przypadku o nazwie `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Znajomość ASE nazwy i nazwy grupy zasobów, której należy ASE, można utworzyć nowej aplikacji sieci web w istniejącej ASE, jak pokazano w poniższym poleceniu:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

`Location` Parametr jest wymagany powodu starszej wersji, ale jest on ignorowany podczas tworzenia aplikacji w elemencie ASE. 

## <a name="cloning-an-existing-app-slot"></a>Klonowanie istniejącego miejsca aplikacji
Scenariusz: Chcesz sklonować istniejącą miejsca aplikacji sieci Web albo w nowej aplikacji sieci Web lub nowe miejsce aplikacji sieci Web. W nowej aplikacji sieci Web może być w tym samym regionie co oryginalna miejsca aplikacji sieci Web lub w innym regionie.

Znajomość Nazwa grupy zasobów, która zawiera źródłowej aplikacji sieci web, służy następujące polecenie programu PowerShell można uzyskać informacji o miejsca aplikacji sieci web źródła (w tym przypadku o nazwie `source-webappslot`) związana z aplikacji sieci Web `source-webapp`:

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot
```

Polecenie przedstawiono tworzenie własnego klonu źródłowej aplikacji sieci web nowej aplikacji sieci web:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurowanie usługi Traffic Manager podczas klonowania aplikacji
Tworzenie aplikacji sieci web w przypadku i konfigurowanie usługi Azure Traffic Manager można kierować ruchem do tych aplikacji sieci web, jest ważne w celu zapewnienia wysokiej dostępności aplikacji klientów. W przypadku klonowania istniejącej aplikacji sieci web, istnieje możliwość nawiązywania połączenia z nowego profilu Menedżera ruchu lub istniejącego obie aplikacje sieci web. Obsługiwana jest tylko wersja Menedżera zasobów Azure Traffic Manager.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Tworzenie nowego profilu usługi Traffic Manager podczas klonowania aplikacji
Scenariusz: Chcesz sklonować aplikacji sieci web do innego regionu, podczas konfigurowania profilu Menedżera ruchu usługi Azure Resource Manager, która obejmuje zarówno aplikacji sieci web. Polecenie przedstawiono tworzenie własnego klonu źródłowej aplikacji sieci web nowej aplikacji sieci web podczas konfigurowania profilu Menedżera ruchu:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Dodawanie nowych sklonować aplikacji sieci Web do istniejącego profilu Menedżera ruchu
Scenariusz: Już profilu Menedżera ruchu usługi Azure Resource Manager i chcesz dodać jako punktów końcowych obie aplikacje sieci web. Aby to zrobić, należy najpierw utworzyć istniejącego ruchu profil Menedżera identyfikatora. Należy identyfikator subskrypcji, nazwę grupy zasobów i istniejącej nazwy profilu Menedżera ruchu.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Po identyfikatorze Menedżera ruchu, polecenie przedstawiono tworzenie własnego klonu źródłowej aplikacji sieci web nowej aplikacji sieci web podczas dodawania ich do istniejącego profilu Menedżera ruchu:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Bieżące ograniczenia
Ta funkcja jest obecnie w wersji zapoznawczej i nowe funkcje zostaną dodane w czasie. Poniżej przedstawiono znane ograniczenia dotyczące bieżącej wersji w klonowania aplikacji:

* Ustawienia skalowania automatycznego nie są klonowane.
* Ustawienia harmonogramu tworzenia kopii zapasowej nie są klonowane.
* Ustawienia sieci Wirtualnej nie są klonowane.
* Wgląd w aplikację nie są automatycznie skonfigurowane na docelowej aplikacji sieci web
* Łatwe ustawienia uwierzytelniania nie są klonowane.
* Program kudu rozszerzenia nie są klonowane.
* Porada reguły nie są klonowane.
* Zawartość bazy danych nie został sklonowany.
* Klonowanie na jednostkę skalowania różnych zmian wychodzących adresów IP

### <a name="references"></a>Dokumentacja
* [Klonowanie aplikacji sieci Web](app-service-web-app-cloning.md)
* [Tworzenie kopii zapasowej aplikacji sieci web w usłudze Azure App Service](web-sites-backup.md)
* [Usługa Azure Resource Manager obsługę Azure Traffic Manager w wersji zapoznawczej](../traffic-manager/traffic-manager-powershell-arm.md)
* [Wprowadzenie do usługi App Service Environment](environment/intro.md)
* [Używanie programu Azure PowerShell z usługą Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)

