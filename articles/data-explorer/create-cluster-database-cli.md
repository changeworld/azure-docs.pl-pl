---
title: 'Szybki start: Tworzenie klastra i bazy danych usługi Azure Data Explorer przy użyciu usługi interfejsu wiersza polecenia'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć klaster i bazę danych usługi Azure Data Explorer przy użyciu interfejsu wiersza polecenia
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: 9e0ae547df34594674dc03702310a1537717a4ed
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881120"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-cli"></a>Tworzenie klastra i bazy danych usługi Azure Data Explorer przy użyciu usługi interfejsu wiersza polecenia

W tym przewodniku Szybki start opisano sposób tworzenia klastra i bazy danych usługi Azure Data Explorer przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego przewodnika Szybki start jest potrzebna subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby sprawdzić wersję. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="configure-the-cli-parameters"></a>Konfigurowanie parametrów interfejsu wiersza polecenia

Poniższe kroki nie są wymagane, jeśli uruchamiasz polecenia w usłudze Cloud Shell. Jeśli korzystasz z interfejsu wiersza polecenia lokalnie, wykonaj następujące kroki, aby zalogować się do platformy Azure i ustawić bieżącą subskrypcję:

1. Uruchom następujące polecenia, aby zalogować się na platformie Azure:

    ```azurecli-interactive
    az login
    ```

2. Ustaw subskrypcję, w której chcesz utworzyć klaster. Zastąp wartość `MyAzureSub` nazwą subskrypcji platformy Azure, której chcesz użyć:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Tworzenie klastra usługi Azure Data Explorer

1. Utwórz klaster przy użyciu następującego polecenia:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | name | *azureclitest* | Wybrana nazwa klastra.|
   | sku | *D13_v2* | Jednostka SKU, która będzie używana na potrzeby klastra. |
   | resource-group | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |

    Istnieją dodatkowe parametry opcjonalne, których można używać, takie jak pojemność klastra.

2. Uruchom następujące polecenie, aby sprawdzić, czy klaster został utworzony pomyślnie:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Jeśli wynik zawiera element „provisioningState” o wartości „Powodzenie”, klaster został utworzony pomyślnie.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Tworzenie bazy danych w klastrze usługi Azure Data Explorer

1. Utwórz bazę danych przy użyciu następującego polecenia:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | cluster-name | *azureclitest* | Nazwa klastra, w którym należy utworzyć bazę danych.|
   | name | *clidatabase* | Wybrana nazwa bazy danych.|
   | resource-group | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |
   | soft-delete-period | *3650:00:00:00* | Okres przechowywania danych w celu udostępniania ich dla zapytania. |
   | hot-cache-period | *3650:00:00:00* | Czas przechowywania danych w pamięci podręcznej. |

2. Uruchom następujące polecenie, aby wyświetlić utworzoną bazę danych:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Masz teraz klaster i bazę danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

* Jeśli zamierzasz skorzystać z naszych pozostałych przewodników Szybki start i samouczków, zachowaj utworzone zasoby.
* Aby wyczyścić zasoby, usuń klaster. Usunięcie klastra powoduje również usunięcie znajdujących się w nim baz danych. Użyj poniższego polecenia, aby usunąć klaster:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki start: pozyskiwanie danych przy użyciu biblioteki języka Python w usłudze Azure Data Explorer](python-ingest-data.md)
