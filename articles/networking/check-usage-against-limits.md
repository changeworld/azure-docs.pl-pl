---
title: Sprawdzanie użycia zasobów platformy Azure pod kątem limitów | Dokumenty firmy Microsoft
description: Dowiedz się, jak sprawdzić użycie zasobów platformy Azure pod kątem limitów subskrypcji platformy Azure.
services: networking
documentationcenter: na
author: KumudD
ms.author: kumud
tags: azure-resource-manager
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.openlocfilehash: cffa5677c5531f3887639c049998523d7d07586a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455566"
---
# <a name="check-resource-usage-against-limits"></a>Sprawdzanie użycia zasobów pod kątem limitów

W tym artykule dowiesz się, jak wyświetlić numer każdego typu zasobu sieciowego wdrożonego w ramach subskrypcji i jakie są [limity subskrypcji.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) Możliwość wyświetlania użycia zasobów względem limitów jest przydatna do śledzenia bieżącego użycia i planowania wykorzystania w przyszłości. Do śledzenia użycia można użyć [usługi Azure Portal](#azure-portal), [PowerShell](#powershell)lub [interfejsu wiersza polecenia platformy Azure.](#azure-cli)

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [portalu](https://portal.azure.com)Azure .
2. U góry lewego rogu witryny Azure portal wybierz pozycję **Wszystkie usługi**.
3. Wprowadź *subskrypcje* w polu **Filtr.** Gdy pozycja **Subskrypcje** pojawi się w wynikach wyszukiwania, wybierz ją.
4. Wybierz nazwę subskrypcji, dla której chcesz wyświetlić informacje o użyciu.
5. W obszarze **USTAWIENIA**wybierz **pozycję Użycie + przydział**.
6. Można wybrać jedną z następujących opcji:
   - **Typy zasobów:** Można wybrać wszystkie typy zasobów lub wybrać określone typy zasobów, które mają być wyświetlane.
   - **Dostawcy:** Można wybrać wszystkich dostawców zasobów lub wybrać **opcję Oblicz**, **Sieć**lub **Magazyn**.
   - **Lokalizacje:** możesz wybrać wszystkie lokalizacje platformy Azure lub wybrać określone lokalizacje.
   - Można wybrać, aby wyświetlić wszystkie zasoby lub tylko zasoby, w których co najmniej jeden jest wdrażany.

     W poniższym obrazie przedstawiono wszystkie zasoby sieciowe z co najmniej jednym zasobem wdrożonym we wschodnich stanach zjednoczonych:

       ![Wyświetlanie danych użycia](./media/check-usage-against-limits/view-usage.png)

     Kolumny można sortować, zaznaczając nagłówek kolumny. Wyświetlane limity to limity subskrypcji. Jeśli chcesz zwiększyć domyślny limit, wybierz **opcję Poproś o zwiększenie**, a następnie wypełnij i prześlij żądanie pomocy technicznej. Wszystkie zasoby mają maksymalny limit wymieniony w [limitach](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)platformy Azure . Jeśli bieżący limit jest już na maksymalnej liczbie, nie można zwiększyć limitu.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić polecenia, które należy wykonać w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchamiając program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli program PowerShell jest uruchamiany z komputera, potrzebny jest moduł programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom `Get-Module -ListAvailable Az` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli korzystasz z programu PowerShell lokalnie, `Login-AzAccount` musisz również uruchomić, aby zalogować się na platformie Azure.

Wyświetlanie użycia względem limitów dzięki [get-AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage). W poniższym przykładzie pobiera użycie zasobów, w których co najmniej jeden zasób jest wdrażany w lokalizacji wschodnich stanów USA:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Otrzymujesz dane wyjściowe sformatowane tak samo jak następujące przykładowe dane wyjściowe:

```output
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Jeśli do wykonywania zadań w tym artykule przy użyciu poleceń interfejsu wiersza polecenia platformy Azure należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchomić interfejs wiersza polecenia z komputera. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli korzystasz z interfejsu wiersza polecenia platformy Azure `az login` lokalnie, musisz również uruchomić, aby zalogować się na platformie Azure.

Wyświetlanie użycia względem limitów za pomocą [az network list-usages](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). W poniższym przykładzie pobiera użycie zasobów w lokalizacji wschodnich stanach amerykańskich:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Otrzymujesz dane wyjściowe sformatowane tak samo jak następujące przykładowe dane wyjściowe:

```output
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
