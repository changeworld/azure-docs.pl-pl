---
title: Uruchamianie pierwszego zapytania przy użyciu programu Azure PowerShell
description: W tym artykule przedstawiono kroki umożliwiające włączenie modułu usługi Resource Graph dla programu Azure PowerShell i uruchomienie pierwszego zapytania.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 76771a7dfcc323cca6ea52366195c895ee510701
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165594"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Szybki start: Uruchamianie pierwszego zapytania usługi Resource Graph przy użyciu programu Azure PowerShell

Pierwszym krokiem do korzystania z usługi Azure Resource Graph jest zainstalowanie modułu dla programu Azure PowerShell. Ten przewodnik Szybki start przeprowadzi Cię przez proces dodawania modułu do instalacji programu Azure PowerShell.

Po zakończeniu tego procesu będziesz mieć moduł dodany do wybranej instalacji programu Azure PowerShell i uruchomisz swoje pierwsze zapytanie usługi Resource Graph.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="add-the-resource-graph-module"></a>Dodaj moduł usługi Resource Graph

Aby włączyć program Azure PowerShell do wykonywania zapytań do usługi Azure Resource Graph, musisz dodać moduł. Ten moduł może być używany z lokalnie zainstalowanym programem PowerShell, [programem Azure Cloud Shell](https://shell.azure.com) lub [obrazem Docker programu Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Wymagania podstawowe

Moduł usługi Azure Resource Graph wymaga następującego oprogramowania:

- Program Azure PowerShell w wersji 1.0.0 lub nowszej. Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/powershell/azure/install-az-ps).

- Modułu PowerShellGet w wersji 2.0.1 lub nowszej. Jeśli jeszcze nie został on zainstalowany lub zaktualizowany, postępuj zgodnie z [tymi instrukcjami](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Instalowanie modułu

Moduł usługi Resource Graph dla programu PowerShell to **Az.ResourceGraph**.

1. Za pomocą monitu **administracyjnego** programu PowerShell uruchom następujące polecenie:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Zweryfikuj, czy moduł został zaimportowany i jego wersja jest poprawna (0.7.1):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Teraz, gdy moduł programu Azure PowerShell został dodany do Twojego wybranego środowiska, nadszedł czas na wypróbowanie prostego zapytania usługi Resource Graph. Zapytanie zwróci pierwsze pięć zasobów platformy Azure przy użyciu właściwości **Name** i **Resource Type** każdego zasobu.

1. Uruchom swoje pierwsze zapytanie usługi Azure Resource Graph za pomocą polecenia cmdlet `Search-AzGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Ponieważ to przykładowe zapytanie nie udostępnia modyfikatora sortowania takiego jak `order by`, wielokrotne uruchomienie tego zapytania będzie prawdopodobnie zwracało inny zestaw zasobów dla każdego żądania.

1. Zaktualizuj zapytanie, dodając modyfikator `order by` do właściwości **Name**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Spowoduje to najpierw ograniczenie wyników zapytania, a następnie ich uporządkowanie.

1. Zaktualizuj zapytanie, aby najpierw wykonywało polecenie `order by` w celu sortowania według właściwości **Name**, a następnie polecenie `limit` w celu ograniczenia do pięciu pierwszych wyników:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Gdy końcowe zapytanie zostanie uruchomione wielokrotnie, zakładając, że nic się nie zmieniło w Twoim środowisku, zwrócone wyniki będą spójne i zgodne z oczekiwaniami — uporządkowane według właściwości **Name**, ale nadal ograniczone do pięciu pierwszych wyników.

> [!NOTE]
> Jeśli zapytanie nie zwraca wyników z subskrypcji masz już dostęp, następnie należy pamiętać, że `Search-AzGraph` domyślne polecenia cmdlet do subskrypcji w kontekście domyślnym. Aby wyświetlić listę subskrypcji identyfikatorów, które są częścią domyślnego kontekstu Uruchom to `(Get-AzContext).Account.ExtendedProperties.Subscriptions` w razie potrzeby do wyszukania we wszystkich subskrypcjach, o których masz dostęp do jednego zestawu PSDefaultParameterValues dla `Search-AzGraph` , uruchamiając polecenie cmdlet `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID }`
   
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz usunąć moduł usługi Resource Graph ze środowiska programu Azure PowerShell, możesz to zrobić za pomocą następującego polecenia:

```azurepowershell-interactive
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Nie powoduje ono usunięcia wcześniej pobranego pliku modułu. Powoduje tylko usunięcie go z uruchomionej sesji programu PowerShell.

## <a name="next-steps"></a>Kolejne kroki

- Uzyskaj więcej informacji na temat [języka zapytań](./concepts/query-language.md)
- Dowiedz się, jak [eksplorować zasoby](./concepts/explore-resources.md)
- Uruchamianie pierwszego zapytania za pomocą [interfejsu wiersza polecenia platformy Azure](first-query-azurecli.md)
- Zobacz przykłady [zapytań dla początkujących](./samples/starter.md)
- Zobacz przykłady [zapytań zaawansowanych](./samples/advanced.md)
- Podziel się opinią na platformie [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)
