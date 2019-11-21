---
title: 'Quickstart: Your first Azure CLI query'
description: In this quickstart, you follow the steps to enable the Resource Graph extension for Azure CLI and run your first query.
ms.date: 10/18/2019
ms.topic: quickstart
ms.openlocfilehash: a3ef32b2c2dcaa7a043bbb3e1e859ee295f02f2f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74216611"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Quickstart: Run your first Resource Graph query using Azure CLI

Pierwszym krokiem do korzystania z usługi Azure Resource Graph jest zainstalowanie rozszerzenia dla [wiersza polecenia platformy Azure](/cli/azure/). Ten przewodnik Szybki start przeprowadzi Cię przez proces dodawania rozszerzenia do instalacji interfejsu wiersza polecenia platformy Azure. Rozszerzenia można używać z interfejsem wiersza polecenia platformy Azure zainstalowanym lokalnie lub za pośrednictwem [usługi Azure Cloud Shell](https://shell.azure.com).

Po zakończeniu tego procesu będziesz mieć rozszerzenie dodane do instalacji interfejsu wiersza polecenia platformy Azure wybranym i uruchomisz swoje pierwsze zapytanie usługi Resource Graph.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="add-the-resource-graph-extension"></a>Dodawanie rozszerzenia usługi Resource Graph

Aby włączyć wykonywanie zapytań usługi Azure Resource Graph przy użyciu interfejsu wiersza polecenia platformy Azure, konieczne jest dodanie rozszerzenia. To rozszerzenie działa wszędzie tam, gdzie interfejs wiersza polecenia platformy Azure może być używany, w tym w [funkcji bash w systemie Windows 10](/windows/wsl/install-win10), usłudze [Cloud Shell](https://shell.azure.com) (autonomicznej i wewnątrz portalu), [obrazie platformy Docker interfejsu wiersza polecenia platformy Azure](https://hub.docker.com/r/microsoft/azure-cli/), lub zainstalowany lokalnie.

1. Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.0.45**). Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. W wybranym środowisku interfejsu wiersza polecenia platformy Azure zaimportuj rozszerzenie za pomocą następującego polecenia:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Validate that the extension has been installed and is the expected version (at least **1.0.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Teraz, gdy rozszerzenie interfejsu wiersza polecenia platformy Azure zostało dodane do Twojego wybranego środowiska, nadszedł czas na wypróbowanie prostego zapytania usługi Resource Graph. Zapytanie zwróci pierwsze pięć zasobów platformy Azure przy użyciu właściwości **Name** i **Resource Type** każdego zasobu.

1. Uruchom swoje pierwsze zapytanie usługi Azure Resource Graph przy użyciu rozszerzenia `graph` i polecenia `query`:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Ponieważ to przykładowe zapytanie nie udostępnia modyfikatora sortowania takiego jak `order by`, wielokrotne uruchomienie tego zapytania będzie prawdopodobnie zwracało inny zestaw zasobów dla każdego żądania.

1. Zaktualizuj zapytanie, dodając modyfikator `order by` do właściwości **Name**:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Spowoduje to najpierw ograniczenie wyników zapytania, a następnie ich uporządkowanie.

1. Zaktualizuj zapytanie, aby najpierw wykonywało polecenie `order by` w celu sortowania według właściwości **Name**, a następnie polecenie `limit` w celu ograniczenia do pięciu pierwszych wyników:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

Gdy końcowe zapytanie zostanie uruchomione wielokrotnie, zakładając, że nic się nie zmieniło w Twoim środowisku, zwrócone wyniki będą spójne i zgodne z oczekiwaniami — uporządkowane według właściwości **Name**, ale nadal ograniczone do pięciu pierwszych wyników.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz usunąć rozszerzenie usługi Resource Graph ze środowiska interfejsu wiersza polecenia platformy Azure, możesz to zrobić za pomocą następującego polecenia:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Następne kroki

- Get more information about the [query language](./concepts/query-language.md).
- Learn more about how to [explore resources](./concepts/explore-resources.md).
- Run your first query by using the [Azure portal](first-query-portal.md).
- Run your first query with [Azure PowerShell](first-query-powershell.md).
- See samples of [Starter queries](./samples/starter.md).
- See samples of [Advanced queries](./samples/advanced.md).
- Provide feedback on [UserVoice](https://feedback.azure.com/forums/915958-azure-governance).