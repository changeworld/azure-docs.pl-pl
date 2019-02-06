---
title: Tworzenia klastra usługi Azure Eksploratora danych i bazę danych przy użyciu interfejsu wiersza polecenia
description: W tym artykule opisano sposób tworzenia klastrów Eksploratora danych platformy Azure i bazy danych przy użyciu wiersza polecenia platformy Azure
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: howto
ms.date: 1/31/2019
ms.openlocfilehash: 8c035524adebcb131872c700280201aaac07c52b
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747955"
---
# <a name="create-an-azure-data-explorer-cluster-and-a-database-using-cli"></a>Tworzenia klastra usługi Azure Eksploratora danych i bazę danych przy użyciu interfejsu wiersza polecenia

W tym artykule opisano sposób tworzenia klastrów Eksploratora danych platformy Azure i bazy danych przy użyciu wiersza polecenia platformy Azure.

## <a name="whats-the-difference-between-the-management-plane-and-data-plane-apis"></a>Jaka jest różnica między płaszczyzny zarządzania i płaszczyzny danych interfejsów API

Istnieją dwie różne biblioteki interfejsu API zarządzania i dane na płaszczyźnie interfejsów API.
Interfejsy API Management są używane do zarządzania zasobami, dla wystąpienia tworzenia klastra, Utwórz bazę danych, usuwanie połączenia danych, zmień liczbę wystąpień, liczby itp. Płaszczyzna danych, interfejsy API są używane do interakcji z danymi, uruchamiać zapytania, pozyskiwania danych itp.

## <a name="configure-the-cli-parameters"></a>Konfigurowanie parametrów interfejs wiersza polecenia

Logowanie się na swoim koncie

```Bash
az login
```

Ustaw subskrypcję, w którym chcesz klastra ma zostać utworzony.

```Bash
az account set --subscription "your_subscription"
```

## <a name="create-the-azure-data-explorer-cluster"></a>Tworzenie klastra Eksplorator danych platformy Azure

Tworzenie klastra przy użyciu następującego polecenia.

```Bash
az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
```

Podaj następujące wartości

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | name | *azureclitest* | Odpowiednią nazwę klastra.|
    | jednostka SKU | *D13_v2* | Jednostka SKU, która będzie używana dla klastra. |
    | resource-group | *testrg* | Nazwa grupy zasobów, której będzie można utworzyć klastra. |
    | | |

Jeśli chcesz, istnieją więcej opcjonalnych parametrów, których można użyć, takie jak pojemność klastra itp.

Aby sprawdzić, czy klaster został pomyślnie utworzony, można uruchomić

```Bash
az kusto cluster show --name azureclitest --resource-group testrg
```

Jeśli wynik zawiera "provisioningState" wartość "Powodzenie", oznacza to, że klaster został pomyślnie utworzony.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Tworzenie bazy danych w klastrze Azure Eksplorator danych

Tworzenie bazy danych przy użyciu następującego polecenia.

```Bash
az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
```

Podaj następujące wartości

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa klastra | *azureclitest* | Nazwa klastra gdzie powinna zostać utworzona.|
    | name | *clidatabase* | Żądaną nazwę bazy danych.|
    | resource-group | *testrg* | Nazwa grupy zasobów, której będzie można utworzyć klastra. |
    | soft-delete-period | *3650:00:00:00* | Ilość czasu, który dane powinny być przechowywane, dzięki czemu są one dostępne dla zapytania. |
    | gorąco pamięci podręcznej — okresu | *3650:00:00:00* | Ilość czasu, który dane powinny być przechowywane w pamięci podręcznej. |
    | | |

Widać, bazy danych, która jest tworzona po uruchomieniu

```Bash
az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
```

To wszystko masz teraz klastra i bazy danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz skorzystać z naszych pozostałych przewodników Szybki start i samouczków, zachowaj utworzone zasoby.

Po usunięciu klastra, powoduje również usunięcie wszystkich baz danych w nim. Użyj poniższego polecenia, aby usunąć klaster.

```Bash
az kusto cluster delete --name azureclitest --resource-group testrg
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Szybki start: Pozyskiwanie danych z Centrum zdarzeń do Eksploratora danych usługi Azure](ingest-data-event-hub.md)
