---
title: 'Szybki start: Tworzenie klastra Eksplorator danych platformy Azure i bazy danych przy użyciu programu PowerShell'
description: Dowiedz się, jak utworzyć klaster Eksplorator danych platformy Azure i bazy danych przy użyciu programu PowerShell
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 650bdc5cdf99645bc2be6c8e85737dacd10a6b27
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287522"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Tworzenie klastra Eksplorator danych platformy Azure i bazy danych przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Interfejs wiersza polecenia](create-cluster-database-cli.md)
> * [Program PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Ten przewodnik Szybki Start opisuje sposób tworzenia klastra Azure Eksploratora danych i bazy danych przy użyciu programu PowerShell.

Można uruchomić polecenia cmdlet programu PowerShell i skryptów na Windows, Linux, lub w [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) do tworzenia i konfigurowania [Eksploratora danych usługi Azure](https://docs.microsoft.com/azure/kusto/ ).

The [**Az.Kusto**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto ). Za pomocą programu Azure PowerShell i **Az.Kusto**, można wykonywać następujące zadania:

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego przewodnika Szybki start jest potrzebna subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="configure-parameters"></a>Konfigurowanie parametrów

Poniższe kroki nie są wymagane, jeśli uruchamiasz polecenia w usłudze Azure Cloud Shell. Jeśli korzystasz z interfejsu wiersza polecenia lokalnie, wykonaj następujące kroki, aby zalogować się do platformy Azure i ustawić bieżącą subskrypcję:

1. Uruchom następujące polecenia, aby zalogować się na platformie Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Ustaw subskrypcję, w której chcesz utworzyć klaster.

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
3. Instalowanie modułu Az.Kusto na twoim urządzeniu:
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Tworzenie klastra usługi Azure Data Explorer

1. Utwórz klaster przy użyciu następującego polecenia:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | Name (Nazwa) | *mykustocluster* | Wybrana nazwa klastra.|
   | SKU | *D13_v2* | Jednostka SKU, która będzie używana na potrzeby klastra. |
   | ResourceGroupName | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |

    Istnieją dodatkowe parametry opcjonalne, których można używać, takie jak pojemność klastra.

2. Uruchom następujące polecenie, aby sprawdzić, czy klaster został utworzony pomyślnie:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster --ResourceGroupName testrg
    ```

Jeśli wynik zawiera element `provisioningState` o wartości `Succeeded`, klaster został utworzony pomyślnie.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Tworzenie bazy danych w klastrze usługi Azure Data Explorer

1. Utwórz bazę danych przy użyciu następującego polecenia:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | ClusterName | *mykustocluster* | Nazwa klastra, w którym zostanie utworzona baza danych.|
   | Name (Nazwa) | *mykustodatabase* | Nazwa bazy danych.|
   | ResourceGroupName | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |
   | SoftDeletePeriod | *3650:00:00:00* | Okres przechowywania danych na potrzeby zapytań. |
   | HotCachePeriod | *3650:00:00:00* | Okres przechowywania danych w pamięci podręcznej. |

2. Uruchom następujące polecenie, aby wyświetlić utworzoną bazę danych:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster --ResourceGroupName testrg -Name mykustodatabase
    ```

Masz teraz klaster i bazę danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

* Jeśli zamierzasz skorzystać z naszych pozostałych przewodników Szybki start i samouczków, zachowaj utworzone zasoby.
* Aby wyczyścić zasoby, usuń klaster. Usunięcie klastra powoduje również usunięcie znajdujących się w nim baz danych. Użyj następującego polecenia, aby usunąć klaster:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Kolejne kroki

Można znaleźć więcej poleceń Az.Kusto [ **tutaj**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto )

> [!div class="nextstepaction"]
> [Szybki start: Pozyskiwanie danych przy użyciu usługi Azure Data Explorer zestaw .NET Standard SDK (wersja zapoznawcza)](net-standard-ingest-data.md)
