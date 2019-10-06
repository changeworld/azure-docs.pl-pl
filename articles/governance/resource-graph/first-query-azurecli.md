---
title: Uruchamianie pierwszego zapytania przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule przedstawiono procedurę włączania rozszerzenia grafu zasobów dla interfejsu wiersza polecenia platformy Azure i uruchamiania pierwszego zapytania.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/26/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 34325fe8e8fdf7f53eb730818dc57800b65076e2
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976714"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Pierwszym krokiem korzystania z grafu zasobów platformy Azure jest sprawdzenie, czy rozszerzenie [interfejsu wiersza polecenia platformy Azure](/cli/azure/) jest zainstalowane. Ten przewodnik Szybki Start przeprowadzi Cię przez proces dodawania rozszerzenia do instalacji interfejsu wiersza polecenia platformy Azure. Możesz użyć rozszerzenia z interfejsem wiersza polecenia platformy Azure zainstalowanym lokalnie lub za pomocą [Azure Cloud Shell](https://shell.azure.com).

Po zakończeniu tego procesu dodaliśmy rozszerzenie do wybranej instalacji interfejsu wiersza polecenia platformy Azure i uruchomimy pierwsze zapytanie dotyczące wykresu zasobów.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="add-the-resource-graph-extension"></a>Dodawanie rozszerzenia grafu zasobów

Aby włączyć interfejs wiersza polecenia platformy Azure do tworzenia zapytań dotyczących grafu zasobów platformy Azure, należy dodać rozszerzenie. To rozszerzenie działa wszędzie tam, gdzie można korzystać z interfejsu wiersza polecenia platformy Azure, [w tym bash w systemie Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (autonomiczny i wewnątrz portalu), [obraz platformy Docker CLI](https://hub.docker.com/r/microsoft/azure-cli/)lub zainstalowany lokalnie.

1. Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.0.45**). Jeśli nie jest jeszcze zainstalowana, wykonaj [te instrukcje](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. W wybranym środowisku interfejsu wiersza polecenia platformy Azure zaimportuj go za pomocą następującego polecenia:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Sprawdź, czy rozszerzenie zostało zainstalowane i czy jest to oczekiwana wersja (co najmniej **1.0.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Uruchom pierwsze zapytanie dotyczące wykresu zasobów

Po dodaniu rozszerzenia interfejsu wiersza polecenia platformy Azure do wybranego środowiska należy wypróbować prostą kwerendę grafu zasobów. Zapytanie zwróci pierwsze pięć zasobów platformy Azure z **nazwą** i **typem zasobu** każdego zasobu.

1. Uruchom pierwsze zapytanie dotyczące wykresu zasobów platformy Azure przy użyciu rozszerzenia `graph` i `query` polecenia:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Ponieważ ten przykład zapytania nie zawiera modyfikatora sortowania, takiego jak `order by`, uruchomienie tego zapytania wiele razy może spowodować, że zostanie wyznaczony inny zestaw zasobów dla każdego żądania.

1. Zaktualizuj zapytanie do `order by` Właściwość **name** :

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Podobnie jak w przypadku pierwszego zapytania, uruchomienie tego zapytania wiele razy może spowodować, że zostanie utworzony inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie `order by` jest dostępna po `limit`. Spowoduje to najpierw ograniczenie wyników zapytania, a następnie ich kolejność.

1. Zaktualizuj zapytanie do pierwszej `order by` Właściwość **name** , a następnie `limit` do pięciu pierwszych wyników:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'project name, type | order by name asc | limit 5'
   ```

Gdy ostateczne zapytanie jest uruchamiane kilka razy, przy założeniu, że nic w środowisku nie zmienia się, zwracane wyniki będą spójne i oczekiwane przez właściwość **name** , ale nadal są ograniczone do pięciu pierwszych wyników.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć rozszerzenie grafu zasobów ze środowiska interfejsu wiersza polecenia platformy Azure, możesz to zrobić za pomocą następującego polecenia:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Następne kroki

- Uzyskaj więcej informacji na temat [języka zapytań](./concepts/query-language.md)
- Dowiedz się więcej na temat [eksplorowania zasobów](./concepts/explore-resources.md)
- Uruchom pierwsze zapytanie przy użyciu [Azure PowerShell](first-query-powershell.md)
- Zobacz przykłady [początkowych zapytań](./samples/starter.md)
- Zobacz przykłady [zapytań zaawansowanych](./samples/advanced.md)
- Prześlij opinię na temat usługi [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)