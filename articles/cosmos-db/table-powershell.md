---
title: Wykonywanie operacji interfejsu API Azure rozwiązania Cosmos DB tabeli przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Jak wykonać operacje interfejsu API Azure rozwiązania Cosmos DB tabeli przy użyciu programu PowerShell
services: storage
author: SnehaGunda
manager: kfile
editor: tysonn
ms.service: storage
ms.component: cosmosdb-table
ms.devlang: na
ms.topic: how-to
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: cce1cfc4cf883fcecab0e339177bc70c18e7af0b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798648"
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>Wykonywanie operacji interfejsu API Azure rozwiązania Cosmos DB tabeli przy użyciu programu Azure PowerShell 

>[!NOTE]
>Interfejsu API Azure rozwiązania Cosmos DB tabeli zawiera funkcje premium magazynu tabel gotowe dystrybucji globalnych, odczyty małe opóźnienia i zapisy, automatycznego indeksowania dodatkowej i dedykowanych przepływności. W większości przypadków poleceń programu PowerShell w pracach tego artykułu dla magazynu zarówno interfejsu API Azure rozwiązania Cosmos DB tabeli i tabel Azure, ale w tym artykule jest specyficzne dla interfejsu API Azure rozwiązania Cosmos bazy danych tabeli. Jeśli korzystasz z magazynem tabel Azure, zobacz [operacje magazynu tabel Azure wykonywać przy użyciu programu Azure PowerShell](../storage/tables/table-storage-how-to-use-powershell.md).
>

Tabela bazy danych Azure rozwiązania Cosmos interfejs API umożliwia przechowywanie i zapytań dotyczących dużych zestawów strukturalnych danych nierelacyjnych. Główne składniki usługi są tabele, jednostki i właściwości. Tabela jest kolekcji jednostek. Jednostka jest zbiór właściwości. Każdy obiekt może mieć maksymalnie 252 właściwości, które są wszystkie pary nazwa wartość. W tym artykule przyjęto założenie, że znasz już pojęcia interfejsu API Azure rozwiązania Cosmos bazy danych tabeli. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do interfejsu API Azure rozwiązania Cosmos DB tabeli](table-introduction.md) i [tworzenia aplikacji platformy .NET przy użyciu interfejsu API tabeli](create-table-dotnet.md).

W tym artykule opisano typowe operacje interfejsu API tabeli. Omawiane kwestie: 

> [!div class="checklist"]
> * Tworzenie tabeli
> * Pobierz tabelę
> * Dodaj jednostki tabeli
> * Zapytania dotyczącego tabeli
> * Usuwanie jednostek tabeli

## <a name="prerequisites"></a>Wymagania wstępne

Przykłady wymagają programu Azure PowerShell w wersji modułu 4.4.0 lub nowszym. W oknie programu PowerShell, uruchom `Get-Module -ListAvailable AzureRM` można znaleźć wersji. Jeśli nie będą wyświetlane żadne lub konieczne uaktualniania, zobacz [modułu instalacji programu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Po zainstalowaniu programu Azure PowerShell lub zaktualizowaniu, należy zainstalować moduł **AzureRmStorageTable**, który zawiera polecenia do zarządzania jednostek. Aby zainstalować ten moduł, uruchom program PowerShell jako administrator i użyj **instalacji modułu** polecenia.

```powershell
Install-Module AzureRmStorageTable
```

Następnie zainstaluj zestawy Azure DB rozwiązania Cosmos lokalnie, aby używać tych poleceń cmdlet programu PowerShell. Aby uzyskać instrukcje, jak to zrobić, zobacz [modułu PowerShell tabel magazynu Menedżera zasobów Azure dla rozwiązania Cosmos tabel bazy danych](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/).

Wypróbowanie następujących czynnościach, musisz mieć konto bazy danych Azure DB rozwiązania Cosmos. Jeśli nie masz jeszcze jeden tworzenia rozwiązania Cosmos Azure nowej bazy danych konta przy użyciu [portalu Azure](https://portal.azure.com). Aby uzyskać pomoc przy tworzeniu nowego konta bazy danych, zobacz [bazy danych Azure rozwiązania Cosmos: Tworzenie konta bazy danych](create-table-dotnet.md#create-a-database-account).

Pobierz grupy nazwy i zasobów konta bazy danych z portalu; należy te wartości, które mają zostać umieszczone w skrypt, aby uzyskać dostępu do tabel. 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>Utwórz tabelę lub odwołać się do tabeli

Aby utworzyć tabelę lub odwołać się do tabeli, należy użyć **Get-AzureStorageTableTable**. Można wywołać tego polecenia cmdlet, wprowadzając nazwę tabeli, która nie istnieje, tworzy nową tabelę o takiej nazwie i zwraca odwołanie do nowej tabeli. Jeśli tabela istnieje, zwraca odwołanie do istniejącej tabeli.

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

Nie można wyświetlić listę tabel w ramach konta bazy danych rozwiązania Cosmos Azure przy użyciu programu PowerShell, ale możesz zalogować się do portalu i znajduje się w tabeli. Teraz zobaczmy, jak zarządzać jednostek w tabeli.

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Usuwanie tabeli 

PowerShell nie obsługuje usuwania tabel z bazy danych Azure rozwiązania Cosmos. Aby usunąć tabeli, przejdź do [portalu Azure](https://azure.portal.com), Znajdź konto bazy danych Azure rozwiązania Cosmos używasz, następnie znajdź i Usuń tabelę. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli utworzono nową grupę zasobów, a następnie utworzyć nowe konto bazy danych Azure rozwiązania Cosmos w tej grupie, możesz usunąć wszystkie zasoby zostały utworzone w tym ćwiczeniu przez usunięcie grupy zasobów. To polecenie usuwa wszystkie zasoby zawarte w grupie, a także samej grupy zasobów.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono o typowych operacji interfejsu API tabeli przy użyciu programu PowerShell, w tym jak: 

> [!div class="checklist"]
> * Tworzenie tabeli
> * Pobierz tabelę
> * Dodaj jednostki tabeli
> * Zapytania dotyczącego tabeli
> * Usuwanie jednostek tabeli

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Praca z tabelami magazynu platformy Azure z programu PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
