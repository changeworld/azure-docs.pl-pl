---
title: 'Szybki start: Tworzenie klastra Eksplorator danych platformy Azure i bazy danych przy użyciu programu PowerShell'
description: Dowiedz się, jak utworzyć klaster Eksplorator danych platformy Azure i bazy danych przy użyciu programu PowerShell
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: 84b0cbfd7e8ec6709b79328220aac7c9ae904bdb
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047164"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Tworzenie klastra Eksplorator danych platformy Azure i bazy danych przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Interfejs wiersza polecenia](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie pozyskaj (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. W tym przewodniku Szybki Start utworzysz klaster i bazę danych przy użyciu programu Powershell. Można uruchomić polecenia cmdlet programu PowerShell i skryptów na Windows, Linux, lub w [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) z [Az.Kusto](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto) do tworzenia i konfigurowania klastrów Eksploratora danych platformy Azure i bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby sprawdzić wersję. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Konfigurowanie parametrów

Poniższe kroki nie są wymagane, jeśli uruchamiasz polecenia w usłudze Azure Cloud Shell. Jeśli korzystasz z interfejsu wiersza polecenia lokalnie, wykonaj kroki 1 i 2, zaloguj się do platformy Azure i ustawienie bieżącej subskrypcji:

1. Uruchom następujące polecenia, aby zalogować się na platformie Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Ustaw subskrypcję, którego ma zostać utworzony klaster:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Podczas uruchamiania wiersza polecenia platformy Azure, lokalnie lub w usłudze Azure Cloud Shell, musisz zainstalować moduł Az.Kusto na twoim urządzeniu:
    
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

1. Uruchom następujące polecenie, aby sprawdzić, czy klaster został utworzony pomyślnie:

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

1. Uruchom następujące polecenie, aby wyświetlić utworzoną bazę danych:

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

* [Dodatkowe polecenia Az.Kusto](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto)
* [Szybki start: Pozyskiwanie danych przy użyciu usługi Azure Data Explorer zestaw .NET Standard SDK (wersja zapoznawcza)](net-standard-ingest-data.md)
