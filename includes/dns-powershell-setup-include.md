---
title: dołączanie pliku programu PowerShell dla usługi Azure DNS
description: dołączanie pliku programu PowerShell dla usługi Azure DNS
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 682b3d3fee4ff1cdbed6e05a51fd8828b6eb155f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66117271"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Konfigurowanie programu Azure PowerShell dla usługi Azure DNS

### <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

Przed rozpoczęciem konfiguracji sprawdź, czy dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Niezbędne jest zainstalowanie najnowszej wersji poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azureps-cmdlets-docs).

Ponadto aby korzystać z usługi Private Zones (publiczna wersja zapoznawcza), należy upewnić się, że zainstalowano poniższe moduły programu PowerShell w określonych wersjach. 
* AzureRM.Dns — [wersja 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) lub nowsza
* AzureRM.Network — [wersja 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) lub nowsza

```powershell 
Find-Module -Name AzureRM.Dns 
``` 
 
```powershell 
Find-Module -Name AzureRM.Network 
``` 
 
Dane wyjściowe powyższych poleceń wymaga pokazać, że program azurerm.DNS jest w wersji 4.1.0 lub nowszej, a program AzureRM.Network jest w wersji 5.4.0 lub nowszej.  

Jeśli w systemie są wcześniejsze wersje, możesz zainstalować najnowszą wersję programu Azure PowerShell lub pobrać i zainstalować powyższe moduły z galerii programu PowerShell, korzystając z linków obok wersji modułu. Następnie możesz zainstalować je przy użyciu poniższych poleceń. Oba moduły są wymagane i są w pełni zgodne z poprzednimi wersjami. 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Zaloguj się do swojego konta platformy Azure

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Aby uzyskać więcej informacji, zobacz [Zaloguj](/powershell/azure/install-az-ps?view=azps-1.4.0#sign-in).

```powershell
Connect-AzureRmAccount
```

### <a name="select-the-subscription"></a>Wybierz subskrypcję
 
Sprawdź subskrypcje dostępne na koncie.

```powershell
Get-AzureRmSubscription
```

Wybierz subskrypcję platformy Azure do użycia.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Usługa Azure Resource Manager wymaga, aby wszystkie grupy zasobów określały lokalizację. Ta lokalizacja będzie używana jako domyślna lokalizacja dla zasobów w danej grupie zasobów. Ponieważ jednak wszystkie zasoby DNS są globalne, a nie regionalne, wybór lokalizacji grupy zasobów nie ma wpływu na usługę Azure DNS.

Ten krok można pominąć, jeśli używasz istniejącej grupy zasobów.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Rejestrowanie dostawcy zasobów

Usługa Azure DNS jest zarządzana przez dostawcę zasobów Microsoft.Network. Aby można było korzystać z usługi Azure DNS, Twoja subskrypcja platformy Azure musi być zarejestrowana do używania tego dostawcy zasobów. Jest to jednorazowa operacja dla każdej subskrypcji.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```
