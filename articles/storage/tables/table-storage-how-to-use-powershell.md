---
title: Wykonywanie operacji magazynu tabel platformy Azure za pomocą programu PowerShell | Dokumenty firmy Microsoft
description: Dowiedz się, jak uruchamiać typowe zadania, takie jak tworzenie, wykonywanie zapytań, usuwanie danych z konta magazynu tabel platformy Azure przy użyciu programu PowerShell.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.openlocfilehash: 746044aa835df52e61c234c8b5ca61164fffbbc5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545949"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Wykonywanie operacji magazynu tabel platformy Azure za pomocą programu Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Usługa Azure Table storage to magazyn danych NoSQL, którego można używać do przechowywania i wykonywania zapytań o ogromne zestawy ustrukturyzowanych, nierelacyjnych danych. Głównymi składnikami usługi są tabele, jednostki i właściwości. Tabela jest kolekcją jednostek. Jednostka jest zestawem właściwości. Każda jednostka może mieć maksymalnie 252 właściwości, które są parami nazwa-wartość. W tym artykule przyjęto założenie, że znasz już pojęcia usługi Azure Table Storage Service. Aby uzyskać szczegółowe informacje, zobacz [Opis modelu danych usługi tabel](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) i wprowadzenie do magazynu tabel platformy Azure przy użyciu platformy [.NET](../../cosmos-db/table-storage-how-to-use-dotnet.md).

W tym artykule opisano typowe operacje magazynu tabel platformy Azure. Omawiane kwestie: 

> [!div class="checklist"]
> * Tworzenie tabeli
> * Pobieranie tabeli
> * Dodawanie encji tabeli
> * Zapytanie o tabelę
> * Usuwanie encji tabeli
> * Usuwanie tabeli

W tym artykule przedstawiono sposób tworzenia nowego konta usługi Azure Storage w nowej grupie zasobów, dzięki czemu można je łatwo usunąć po zakończeniu. Jeśli wolisz używać istniejącego konta magazynu, możesz to zrobić.

Przykłady wymagają modułów `Az.Storage (1.1.0 or greater)` Az PowerShell i `Az.Resources (1.2.0 or greater)`. W oknie programu PowerShell uruchom, `Get-Module -ListAvailable Az*` aby znaleźć wersję. Jeśli nic nie jest wyświetlane lub trzeba uaktualnić, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Korzystanie z tej funkcji platformy Azure z `Az` programu PowerShell wymaga, że masz zainstalowany moduł. Bieżąca wersja `AzTable` nie jest zgodna ze starszym modułem AzureRM.
> W razie potrzeby postępuj zgodnie z [najnowszymi instrukcjami instalacji modułu Az.](/powershell/azure/install-az-ps)

Po zainstalowaniu lub zaktualizowaniu programu Azure PowerShell należy zainstalować moduł **AzTable**, który ma polecenia do zarządzania jednostkami. Aby zainstalować ten moduł, uruchom program PowerShell jako administrator i użyj polecenia **Install-Module.**

> [!IMPORTANT]
> Ze względu na zgodność nazwy modułu nadal publikujemy `AzureRmStorageTables` ten sam moduł pod starą nazwą w galerii programu PowerShell. Ten dokument będzie odwoływać się tylko do nowej nazwy.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Add-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Pobieranie listy lokalizacji

Jeśli nie wiesz, której lokalizacji użyć, możesz wyświetlić listę dostępnych lokalizacji. Po wyświetleniu listy znajdź lokalizację, której chcesz użyć. W tych przykładach użyto **pliku eastus**. Przechowuj tę wartość w **lokalizacji** zmiennej do wykorzystania w przyszłości.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Przechowuj nazwę grupy zasobów w zmiennej do wykorzystania w przyszłości. W tym przykładzie grupa zasobów o nazwie *pshtablesrg* jest tworzony w regionie *eastus.*

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Tworzenie konta magazynu

Utwórz standardowe konto magazynu ogólnego przeznaczenia z lokalnie nadmiarowym magazynem (LRS) przy użyciu [funkcji New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Pamiętaj, aby określić unikatową nazwę konta magazynu. Następnie pobierz kontekst, który reprezentuje konto magazynu. Działając na koncie magazynu, można odwoływać się do kontekstu zamiast wielokrotnie podaj poświadczeń.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Tworzenie nowej tabeli

Aby utworzyć tabelę, użyj polecenia cmdlet [New-AzStorageTable.](/powershell/module/az.storage/New-AzStorageTable) W tym przykładzie tabela jest wywoływana `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Pobieranie listy tabel na koncie magazynu

Pobierz listę tabel na koncie magazynu przy użyciu [tabeli Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Pobieranie odwołania do określonej tabeli

Aby wykonać operacje w tabeli, należy odwołać się do określonej tabeli. Uzyskaj odwołanie za pomocą [get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Odwołanie do właściwości CloudTable określonej tabeli

> [!IMPORTANT]
> Użycie cloudtable jest obowiązkowe podczas pracy z **modułem AzTable** PowerShell. Wywołanie **polecenia Get-AzStorageTable,** aby uzyskać odwołanie do tego obiektu. To polecenie tworzy również tabelę, jeśli jeszcze nie istnieje.

Aby wykonać operacje w tabeli przy użyciu **tabeli AzTable,** potrzebne jest odwołanie do właściwości CloudTable określonej tabeli.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Usuwanie tabeli

Aby usunąć tabelę, użyj opcji [Usuń—AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). To polecenie cmdlet usuwa tabelę, łącznie ze wszystkimi jej danymi.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli na początku tego sposobu działania utworzono nową grupę zasobów i konto magazynu, można usunąć wszystkie zasoby utworzone w tym ćwiczeniu, usuwając grupę zasobów. To polecenie usuwa wszystkie zasoby zawarte w grupie, a także samą grupę zasobów.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano informacje o typowych operacjach magazynu tabel platformy Azure z programem PowerShell, w tym o tym, jak to zrobić: 

> [!div class="checklist"]
> * Tworzenie tabeli
> * Pobieranie tabeli
> * Dodawanie encji tabeli
> * Zapytanie o tabelę
> * Usuwanie encji tabeli
> * Usuwanie tabeli

Aby uzyskać więcej informacji, zobacz następujące artykuły

* [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/az.storage#storage)

* [Praca z tabelami platformy Azure z programu PowerShell — moduł AzureRmStorageTable/AzTable PS w wersji 2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
