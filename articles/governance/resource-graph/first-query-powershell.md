---
title: Uruchom pierwsze zapytanie przy użyciu Azure PowerShell
description: W tym artykule omówiono procedurę włączania modułu Graf zasobów dla Azure PowerShell i uruchamiania pierwszego zapytania.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 95cf19d4a782f9e4c866d31fac0da74aebff5d2d
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976785"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Azure PowerShell

Pierwszym krokiem do korzystania z grafu zasobów platformy Azure jest sprawdzenie, czy moduł dla Azure PowerShell jest zainstalowany. Ten przewodnik Szybki Start przeprowadzi Cię przez proces dodawania modułu do instalacji Azure PowerShell.

Po zakończeniu tego procesu dodaliśmy moduł do wybranej instalacji Azure PowerShell i uruchomimy pierwsze zapytanie dotyczące wykresu zasobów.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="add-the-resource-graph-module"></a>Dodawanie modułu grafu zasobów

Aby włączyć Azure PowerShell zapytania dotyczącego grafu zasobów platformy Azure, należy dodać moduł. Tego modułu można używać z zainstalowanym lokalnie programem PowerShell, z [Azure Cloud Shell](https://shell.azure.com)lub z [obrazem platformy Docker programu PowerShell](https://hub.docker.com/_/microsoft-powershell).

### <a name="base-requirements"></a>Wymagania podstawowe

Moduł grafu zasobów platformy Azure wymaga następującego oprogramowania:

- Azure PowerShell 1.0.0 lub wyższy. Jeśli nie jest jeszcze zainstalowana, wykonaj [te instrukcje](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 lub nowszy. Jeśli nie jest zainstalowana lub zaktualizowana, wykonaj [te instrukcje](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Instalowanie modułu

Moduł grafu zasobów dla programu PowerShell to **AZ. ResourceGraph**.

1. W **administracyjnym** wierszu polecenia programu PowerShell uruchom następujące polecenie:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Sprawdź, czy moduł został zaimportowany, a to Najnowsza wersja (0.7.5):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Uruchom pierwsze zapytanie dotyczące wykresu zasobów

Po dodaniu modułu Azure PowerShell do wybranego środowiska należy wypróbować prostą kwerendę grafu zasobów. Zapytanie zwróci pierwsze pięć zasobów platformy Azure z **nazwą** i **typem zasobu** każdego zasobu.

1. Uruchom pierwsze zapytanie dotyczące wykresu zasobów platformy Azure przy użyciu polecenia cmdlet `Search-AzGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Ponieważ ten przykład zapytania nie zawiera modyfikatora sortowania, takiego jak `order by`, uruchomienie tego zapytania wiele razy może spowodować, że zostanie wyznaczony inny zestaw zasobów dla każdego żądania.

1. Zaktualizuj zapytanie do `order by` Właściwość **name** :

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Podobnie jak w przypadku pierwszego zapytania, uruchomienie tego zapytania wiele razy może spowodować, że zostanie utworzony inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie `order by` jest dostępna po `limit`. Spowoduje to najpierw ograniczenie wyników zapytania, a następnie ich kolejność.

1. Zaktualizuj zapytanie do pierwszej `order by` Właściwość **name** , a następnie `limit` do pięciu pierwszych wyników:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Gdy ostateczne zapytanie jest uruchamiane kilka razy, przy założeniu, że nic w środowisku nie zmienia się, zwracane wyniki będą spójne i oczekiwane przez właściwość **name** , ale nadal są ograniczone do pięciu pierwszych wyników.

> [!NOTE]
> Jeśli zapytanie nie zwraca wyników z subskrypcji, do której już masz dostęp, należy zauważyć, że `Search-AzGraph` polecenie cmdlet domyślnie ma subskrypcje w kontekście domyślnym. Aby wyświetlić listę identyfikatorów subskrypcji, które są częścią domyślnego kontekstu, uruchom tę `(Get-AzContext).Account.ExtendedProperties.Subscriptions` Jeśli chcesz przeszukać wszystkie subskrypcje, do których masz dostęp, możesz ustawić PSDefaultParameterValues dla `Search-AzGraph` polecenia cmdlet, uruchamiając `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć moduł grafu zasobów ze środowiska Azure PowerShell, możesz to zrobić za pomocą następującego polecenia:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Nie spowoduje to usunięcia pobranego wcześniej pliku modułu. Usuwa go tylko z uruchomionej sesji programu PowerShell.

## <a name="next-steps"></a>Następne kroki

- Uzyskaj więcej informacji na temat [języka zapytań](./concepts/query-language.md)
- Dowiedz się więcej na temat [eksplorowania zasobów](./concepts/explore-resources.md)
- Uruchamianie pierwszego zapytania przy użyciu [interfejsu wiersza polecenia platformy Azure](first-query-azurecli.md)
- Zobacz przykłady [początkowych zapytań](./samples/starter.md)
- Zobacz przykłady [zapytań zaawansowanych](./samples/advanced.md)
- Prześlij opinię na temat usługi [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)