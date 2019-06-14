---
title: Sprawdź użycie zasobów platformy Azure względem limity | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprawdzić wykorzystanie zasobów platformy Azure względem limity subskrypcji platformy Azure.
services: networking
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: jdial
ms.openlocfilehash: 54050c4c20b6ebb35f198775448f51ee8cdc533b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60425417"
---
# <a name="check-resource-usage-against-limits"></a>Sprawdź użycie zasobów limitów

W tym artykule dowiesz się, jak wyświetlić liczbę każdego typu zasobu sieciowego wdrożoną w Twojej subskrypcji i co Twoje [limity subskrypcji](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) są. Możliwość wyświetlenia wykorzystania zasobów względem limity jest przydatne do śledzenia bieżącego użycia i planowanie do użytku w przyszłości. Możesz użyć [witryny Azure Portal](#azure-portal), [PowerShell](#powershell), lub [wiersza polecenia platformy Azure](#azure-cli) śledzenie użycia.

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do platformy Azure [portal](https://portal.azure.com).
2. Górnego, lewego rogu witryny Azure portal, wybierz **wszystkich usług**.
3. Wprowadź *subskrypcje* w **filtru** pole. Gdy **subskrypcje** pojawi się w wynikach wyszukiwania, wybierz ją.
4. Wybierz nazwę subskrypcji, którą chcesz wyświetlić informacje o sposobie użycia.
5. W obszarze **ustawienia**, wybierz opcję **użycia i przydziałów**.
6. Możesz wybrać następujące opcje:
   - **Typy zasobów**: Wybierz wszystkie typy zasobów lub wybierz konkretne typy zasobów, które chcesz wyświetlić.
   - **Dostawców**: Możesz wybrać wszystkich dostawców zasobów lub wybierz **obliczenia**, **sieci**, lub **magazynu**.
   - **Lokalizacje**: Możesz wybrać wszystkich lokalizacji platformy Azure lub wybrać określone lokalizacje.
   - Możesz wybrać, aby wyświetlić wszystkie zasoby lub zasoby, których jest wdrażany co najmniej jeden.

     Przykład pokazany na poniższej ilustracji przedstawiono wszystkich zasobów sieciowych z co najmniej jeden zasób wdrażane w regionie wschodnie stany USA:

       ![Wyświetlanie danych użycia](./media/check-usage-against-limits/view-usage.png)

     Kolumny można sortować, wybierając nagłówek kolumny. Limity, wyświetlane są limity dla Twojej subskrypcji. Jeśli potrzebujesz do zwiększenia domyślnego limitu, wybierz pozycję **żądanie zwiększenia**, następnie wypełnij i prześlij żądanie pomocy technicznej. Wszystkie zasoby mają maksymalny limit na platformie Azure na liście [limity](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). Jeśli bieżący limit jest już maksymalną liczbę, nie można zwiększyć limit.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz uruchamiać polecenia, które należy wykonać w [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Po uruchomieniu programu PowerShell z komputera, należy modułu Azure PowerShell w wersji 1.0.0 lub nowszym. Uruchom `Get-Module -ListAvailable Az` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli korzystasz z programu PowerShell lokalnie, trzeba będzie również uruchomić `Login-AzAccount` zalogować się do platformy Azure.

Wyświetl użycie ograniczeń za pomocą [Get AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage). Poniższy przykład pobiera użycia zasobów, w którym co najmniej jeden zasób jest wdrażany w lokalizacji wschodnie stany USA:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Otrzymasz dane wyjściowe sformatowane taka sama jak następujące przykładowe dane wyjściowe:

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

Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten artykuł wymaga interfejsu wiersza polecenia Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli korzystasz z wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` zalogować się do platformy Azure.

Wyświetl użycie ograniczeń za pomocą [sieci polecenia az list-usages](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). Poniższy przykład pobiera użycia zasobów w lokalizacji wschodnie stany USA:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Otrzymasz dane wyjściowe sformatowane taka sama jak następujące przykładowe dane wyjściowe:

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
