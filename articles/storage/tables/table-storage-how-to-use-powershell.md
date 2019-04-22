---
title: Wykonywanie operacji magazynu tabel Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Wykonywanie operacji magazynu tabel Azure przy użyciu programu PowerShell.
services: cosmos-db
author: roygara
ms.service: cosmos-db
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: cosmosdb-table
ms.openlocfilehash: 840c2793928816c6346e2039a38678585f8e0bc7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273128"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Wykonywanie operacji magazynu tabel Azure przy użyciu programu Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Usługa Azure Table storage jest magazynem danych NoSQL, która służy do przechowywania i wykonywania zapytań dotyczących dużych zestawów strukturalnych danych nierelacyjnych. Główne składniki usługi są tabel, obiektów i właściwości. Tabela jest kolekcję jednostek. Jednostka to zbiór właściwości. Każda jednostka może mieć maksymalnie 252 właściwości, które są wszystkie pary nazwa wartość. W tym artykule założono, że znasz już pojęcia dotyczące usługi Azure Table Storage. Aby uzyskać szczegółowe informacje, zobacz [opis modelu danych usługi Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) i [Rozpoczynanie pracy z usługą Azure Table storage przy użyciu platformy .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md).

W tym artykule omówiono typowe operacje magazynu tabel Azure. Omawiane kwestie: 

> [!div class="checklist"]
> * Tworzenie tabeli
> * Pobierz tabelę
> * Dodawanie jednostki do tabeli
> * Wysłać zapytanie do tabeli
> * Usuwania jednostek tablic
> * Usuwanie tabeli

W tym artykule pokazano, jak utworzyć nowe konto usługi Azure Storage w nowej grupie zasobów, dzięki czemu można łatwo go usuniesz po zakończeniu. Jeśli zostanie wykorzystany raczej istniejące konto magazynu, możesz zrobić to w zamian.

Przykłady wymagają modułów programu PowerShell Az `Az.Storage (1.1.0 or greater)` i `Az.Resources (1.2.0 or greater)`. W oknie programu PowerShell, uruchom `Get-Module -ListAvailable Az*` Aby znaleźć wersję. Jeśli nic nie jest wyświetlane, lub należy go uaktualnić, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Za pomocą tej funkcji platformy Azure za pomocą programu PowerShell wymaga posiadania `Az` zainstalowany moduł. Bieżąca wersja `AzTable` nie jest zgodny z starsze modułu AzureRM.
> Postępuj zgodnie z [najnowsze zainstalować instrukcje dotyczące instalowania modułu Az](/powershell/azure/install-az-ps) w razie potrzeby.

Po zainstalowaniu lub zaktualizowaniu programu Azure PowerShell, należy zainstalować moduł **AzTable**, która zawiera polecenia do zarządzania jednostkami. Aby zainstalować ten moduł, uruchom program PowerShell jako administrator i użyj **Install-Module** polecenia.

> [!IMPORTANT]
> Dla modułu zgodność nazwy powodów, dla których obecnie publikujemy nadal tego samego modułu w starej nazwy `AzureRmStorageTables` w galerii programu PowerShell. W tym dokumencie będzie odwoływać się do nowej nazwy.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Add-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Pobieranie listy lokalizacji

Jeśli nie wiesz, której lokalizacji użyć, możesz wyświetlić listę dostępnych lokalizacji. Po wyświetleniu listy znajdź lokalizację, której chcesz użyć. Użyj tych przykładach **eastus**. Store tę wartość w zmiennej **lokalizacji** do użytku w przyszłości.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Store nazwę grupy zasobów w zmiennej, do użytku w przyszłości. W tym przykładzie grupa zasobów o nazwie *pshtablesrg* jest tworzony w *eastus* regionu.

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Tworzenie konta magazynu

Utwórz konto magazynu ogólnego przeznaczenia w warstwie standardowa przy użyciu magazynu lokalnie nadmiarowego (LRS) [New AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Pamiętaj określić unikatowej nazwy konta magazynu. Następnie Pobierz kontekst, który reprezentuje konto magazynu. Działa na koncie magazynu, możesz odwoływać się do kontekstu, zamiast wielokrotnie podawać poświadczenia.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Utwórz nową tabelę

Aby utworzyć tabelę, należy użyć [New AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) polecenia cmdlet. W tym przykładzie jest wywoływana tabeli `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Pobieranie listy tabel na koncie magazynu

Pobieranie listy tabel przy użyciu konta magazynu [Get AzStorageTable](/powershell/module/az.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Pobierz odwołanie do określonej tabeli

Do wykonywania operacji na tabeli, konieczne jest odwołanie do określonej tabeli. Pobierz odwołanie za pomocą [Get AzStorageTable](/powershell/module/az.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Właściwość CloudTable odwołanie do określonej tabeli

> [!IMPORTANT]
> Użycie CloudTable jest obowiązkowy podczas pracy z **AzTable** modułu programu PowerShell. Wywołaj **Get AzTableTable** polecenie, aby pobrać odwołanie do tego obiektu. To polecenie tworzy również tabeli, jeśli jeszcze nie istnieje.

Do wykonywania operacji na tabeli przy użyciu **AzTable**, potrzebujesz odwołania do właściwości CloudTable określonej tabeli.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Usuwanie tabeli

Aby usunąć tabelę, użyj [AzStorageTable Usuń](/powershell/module/az.storage/Remove-AzStorageTable). To polecenie cmdlet spowoduje usunięcie tabeli z uwzględnieniem wszystkich swoich danych.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli na początku Niniejszy instruktaż został utworzony nowy zasób grupy i konto magazynu, możesz usunąć wszystkie zasoby utworzone w tym ćwiczeniu przez usunięcie grupy zasobów. To polecenie usuwa wszystkie zasoby zawarte w grupie, a także samej grupy zasobów.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono typowych operacji magazynu tabel Azure przy użyciu programu PowerShell, np.: 

> [!div class="checklist"]
> * Tworzenie tabeli
> * Pobierz tabelę
> * Dodawanie jednostki do tabeli
> * Wysłać zapytanie do tabeli
> * Usuwania jednostek tablic
> * Usuwanie tabeli

Aby uzyskać więcej informacji zobacz następujące artykuły

* [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/az.storage#storage)

* [Praca z tabelami platformy Azure za pomocą programu PowerShell — moduł AzureRmStorageTable/AzTable PS w wersji 2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
