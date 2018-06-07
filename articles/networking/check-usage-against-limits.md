---
title: Sprawdź użycie zasobów Azure limitów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i Sprawdź użycie zasobów platformy Azure przed limity subskrypcji platformy Azure.
services: networking
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: networking
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: jdial
ms.openlocfilehash: 0c51f48576c665fbe67f2f18198d6422fe872895
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34811750"
---
# <a name="check-resource-usage-against-limits"></a>Sprawdź użycie zasobów ograniczeń

W tym artykule opisano sposób orientację w liczbie każdy typ zasobu sieciowego wdrożonej w Twojej subskrypcji i co Twoje [limity subskrypcji](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) są. Możliwość wyświetlania użycia zasobów limitów jest przydatne do śledzenia bieżącego użycia i planowanie do użytku w przyszłości. Można użyć [Azure Portal](#azure-portal), [PowerShell](#powershell), lub [interfejsu wiersza polecenia Azure](#azure-cli) celu śledzenie użycia.

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do platformy Azure [portal](https://portal.azure.com).
2. W górnego, lewego rogu portalu Azure, wybierz **wszystkie usługi**.
3. Wprowadź *subskrypcje* w **filtru** pole. Gdy **subskrypcje** pojawia się w wynikach wyszukiwania, wybierz go.
4. Wybierz nazwę subskrypcji, który chcesz wyświetlić informacje o sposobie użycia.
5. W obszarze **ustawienia**, wybierz pozycję **użycia i przydział**.
6. Możesz wybrać następujące opcje:
    - **Typy zasobów**: Wybierz wszystkich typów zasobów lub wybierz określonych typów zasobów, które chcesz wyświetlić.
    - **Dostawców**: Wybierz wszystkich dostawców zasobów lub wybierz **obliczeniowe**, **sieci**, lub **magazynu**.
    - **Lokalizacje**: Wybierz wszystkich lokalizacji platformy Azure, lub wybierz określone lokalizacje.
    - Można wybrać, aby wyświetlić wszystkie zasoby lub tylko do zasobów, której wdrażana jest co najmniej jeden.

    Przykład pokazany na poniższej ilustracji przedstawiono wszystkie zasoby sieci z co najmniej jeden zasób wdrożone w wschodnie stany USA:

        ![View usage data](./media/check-usage-against-limits/view-usage.png)

    Kolumny można sortować, wybierając nagłówek kolumny. Limity wyświetlane są limity dla Twojej subskrypcji. Jeśli potrzebujesz do zwiększenia domyślnego limitu, wybierz **żądanie zwiększenia**, ukończenia i przesłać żądanie pomocy technicznej. Wszystkie zasoby mają maksymalny limit na liście Azure [limity](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). Jeśli Twój bieżący limit jest już maksymalną liczbę, nie można zwiększyć limit.

## <a name="powershell"></a>PowerShell

Możesz uruchamiać polecenia, które należy wykonać w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Powłoka chmury Azure jest bezpłatne powłoki interakcyjne. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Po uruchomieniu programu PowerShell z komputera, należy *AzureRM* modułu programu PowerShell, wersji 6.0.1 lub nowszej. Uruchom `Get-Module -ListAvailable AzureRM` na komputerze, aby znaleźć zainstalowanej wersji. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli korzystasz z programu PowerShell lokalnie, należy uruchomić `Login-AzureRmAccount` logować się do platformy Azure.

Wyświetl użycie limitów z [Get-AzureRmNetworkUsage](/powershell/module/azurerm.network/powershell/module/azurerm.network/get-azurermnetworkusage). Poniższy przykład pobiera użycia zasobów, w której wdrażana jest co najmniej jeden zasób w lokalizacji wschodnie stany USA:

```azurepowershell-interactive
Get-AzureRmNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Zostanie wyświetlony komunikat sformatowany taka sama jak w poniższym przykładzie danych wyjściowych:

```powershell
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Jeśli za pomocą poleceń Azure interfejsu wiersza polecenia (CLI), aby wykonać zadania w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/bash), lub za pomocą interfejsu wiersza polecenia z tego komputera. W tym artykule wymaga wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` logować się do platformy Azure.

Wyświetl użycie limitów z [sieci az listy użycia](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). Poniższy przykład pobiera zużycie zasobów w lokalizacji wschodnie stany USA:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Zostanie wyświetlony komunikat sformatowany taka sama jak w poniższym przykładzie danych wyjściowych:

```azurecli
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```
