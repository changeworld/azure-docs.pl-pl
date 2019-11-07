---
title: Uruchamianie pierwszego zapytania przy użyciu programu PowerShell
description: W tym artykule przedstawiono kroki umożliwiające włączenie modułu usługi Resource Graph dla programu Azure PowerShell i uruchomienie pierwszego zapytania.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 9e41ca2e7c6d789b3d18fa98c4845118fa6538ef
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622549"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Azure PowerShell

Pierwszym krokiem do korzystania z usługi Azure Resource Graph jest zainstalowanie modułu dla programu Azure PowerShell. Ten przewodnik Szybki start przeprowadzi Cię przez proces dodawania modułu do instalacji programu Azure PowerShell.

Po zakończeniu tego procesu będziesz mieć moduł dodany do wybranej instalacji programu Azure PowerShell i uruchomisz swoje pierwsze zapytanie usługi Resource Graph.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="add-the-resource-graph-module"></a>Dodaj moduł usługi Resource Graph

Aby włączyć program Azure PowerShell do wykonywania zapytań do usługi Azure Resource Graph, musisz dodać moduł. Tego modułu można używać z zainstalowanym lokalnie programem PowerShell, z [Azure Cloud Shell](https://shell.azure.com)lub z [obrazem platformy Docker programu PowerShell](https://hub.docker.com/_/microsoft-powershell).

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

1. Sprawdź, czy moduł został zaimportowany, a to Najnowsza wersja (0.7.5):

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
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Ponieważ to przykładowe zapytanie nie udostępnia modyfikatora sortowania takiego jak `order by`, wielokrotne uruchomienie tego zapytania będzie prawdopodobnie zwracało inny zestaw zasobów dla każdego żądania.

1. Zaktualizuj zapytanie, dodając modyfikator `order by` do właściwości **Name**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Spowoduje to najpierw ograniczenie wyników zapytania, a następnie ich uporządkowanie.

1. Zaktualizuj zapytanie, aby najpierw wykonywało polecenie `order by` w celu sortowania według właściwości **Name**, a następnie polecenie `limit` w celu ograniczenia do pięciu pierwszych wyników:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Gdy końcowe zapytanie zostanie uruchomione wielokrotnie, zakładając, że nic się nie zmieniło w Twoim środowisku, zwrócone wyniki będą spójne i zgodne z oczekiwaniami — uporządkowane według właściwości **Name**, ale nadal ograniczone do pięciu pierwszych wyników.

> [!NOTE]
> Jeśli zapytanie nie zwraca wyników z subskrypcji, do której już masz dostęp, należy zauważyć, że `Search-AzGraph` polecenie cmdlet domyślnie ma subskrypcje w domyślnym kontekście. Aby wyświetlić listę identyfikatorów subskrypcji, które są częścią kontekstu domyślnego, uruchom tę `(Get-AzContext).Account.ExtendedProperties.Subscriptions` Jeśli chcesz przeszukać wszystkie subskrypcje, do których masz dostęp, możesz ustawić PSDefaultParameterValues dla `Search-AzGraph` polecenia cmdlet, uruchamiając `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz usunąć moduł usługi Resource Graph ze środowiska programu Azure PowerShell, możesz to zrobić za pomocą następującego polecenia:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Nie powoduje ono usunięcia wcześniej pobranego pliku modułu. Powoduje tylko usunięcie go z uruchomionej sesji programu PowerShell.

## <a name="next-steps"></a>Następne kroki

- Uzyskaj więcej informacji na temat [języka zapytań](./concepts/query-language.md).
- Dowiedz się więcej o sposobach [eksplorowania zasobów](./concepts/explore-resources.md).
- Uruchom pierwsze zapytanie przy użyciu [Azure Portal](first-query-portal.md).
- Uruchom pierwsze zapytanie przy użyciu [interfejsu wiersza polecenia platformy Azure](first-query-azurecli.md).
- Zobacz przykłady [początkowych zapytań](./samples/starter.md).
- Zobacz przykłady [zaawansowanych zapytań](./samples/advanced.md).
- Prześlij opinię na temat usługi [UserVoice](https://feedback.azure.com/forums/915958-azure-governance).