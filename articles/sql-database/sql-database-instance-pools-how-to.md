---
title: Przewodnik po pulach wystąpień (wersja zapoznawcza)
description: W tym artykule opisano sposób tworzenia pul wystąpień usługi Azure SQL Database i zarządzania nimi (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299366"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Przewodnik po pulach wystąpień usługi Azure SQL Database (wersja zapoznawcza)

Ten artykuł zawiera szczegółowe informacje na temat tworzenia [pul wystąpień](sql-database-instance-pools.md)i zarządzania nimi.

## <a name="instance-pool-operations"></a>Operacje puli wystąpień

W poniższej tabeli przedstawiono dostępne operacje związane z pulami wystąpień i ich dostępności w witrynie Azure portal i programie PowerShell.

|Polecenie|Portal Azure|PowerShell|
|:---|:---|:---|
|Tworzenie puli wystąpień|Nie|Tak|
|Aktualizuj pulę wystąpień (ograniczona liczba właściwości)|Nie |Tak |
|Sprawdzanie użycia i właściwości puli wystąpień|Nie|Tak |
|Usuwanie puli wystąpień|Nie|Tak|
|Tworzenie wystąpienia zarządzanego wewnątrz puli wystąpień|Nie|Tak|
|Aktualizowanie użycia zasobów wystąpienia zarządzanego|Tak |Tak|
|Sprawdzanie użycia i właściwości wystąpienia zarządzanego|Tak|Tak|
|Usuwanie wystąpienia zarządzanego z puli|Tak|Tak|
|Tworzenie bazy danych w wystąpieniu zarządzanym umieszczonym w puli|Tak|Tak|
|Usuwanie bazy danych z wystąpienia zarządzanego|Tak|Tak|

Dostępne [polecenia programu PowerShell](https://docs.microsoft.com/powershell/module/az.sql/)

|Polecenie cmdlet |Opis |
|:---|:---|
|[Nowy-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Tworzy pulę wystąpień usługi Azure SQL Database. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Zwraca informacje o puli wystąpień sql platformy Azure. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Ustawia właściwości puli wystąpień usługi Azure SQL Database. |
|[Usuń-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Usuwa pulę wystąpień usługi Azure SQL Database. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Zwraca informacje o użyciu puli wystąpień sql platformy Azure. |


Aby korzystać z programu PowerShell, [zainstaluj najnowszą wersję programu PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)i postępuj zgodnie z instrukcjami, aby [zainstalować moduł programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

W przypadku operacji związanych z wystąpieniami zarówno wewnątrz pul, jak i pojedynczych wystąpień, należy użyć standardowych [poleceń wystąpienia zarządzanego,](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)ale właściwość *nazwa puli wystąpień* musi być wypełniona podczas korzystania z tych poleceń dla wystąpienia w puli.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Jak wdrożyć wystąpienia zarządzane w pulach

Proces wdrażania wystąpienia w puli składa się z następujących dwóch kroków:

1. Jednorazowe wdrożenie puli wystąpień. Jest to długotrwała operacja, w której czas trwania jest taki sam jak wdrażanie [pojedynczego wystąpienia utworzonego w pustej podsieci](sql-database-managed-instance.md#managed-instance-management-operations).

2. Powtarzające się wdrożenie wystąpienia w puli wystąpień. Parametr puli wystąpień musi być jawnie określony jako część tej operacji. Jest to stosunkowo szybka operacja, która zwykle trwa do 5 minut.

W publicznej wersji zapoznawczej oba kroki są obsługiwane tylko przy użyciu szablonów programu PowerShell i Menedżera zasobów. Środowisko portalu Azure nie jest obecnie dostępne.

Po wdrożeniu wystąpienia zarządzanego w puli *można* użyć witryny Azure Portal, aby zmienić jego właściwości na stronie warstwy cenowej.


## <a name="create-an-instance-pool"></a>Tworzenie puli wystąpień

Aby utworzyć pulę wystąpień:

1. [Tworzenie sieci wirtualnej za pomocą podsieci](#create-a-virtual-network-with-a-subnet).
2. [Utwórz pulę wystąpień](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Tworzenie sieci wirtualnej za pomocą podsieci 

Aby umieścić wiele pul wystąpień w tej samej sieci wirtualnej, zobacz następujące artykuły:

- [Określ rozmiar podsieci sieci wirtualnej dla wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Utwórz nową sieć wirtualną i podsieć przy użyciu [szablonu portalu Azure](sql-database-managed-instance-create-vnet-subnet.md) lub postępuj zgodnie z instrukcjami [dotyczącymi przygotowania istniejącej sieci wirtualnej](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Tworzenie puli wystąpień 

Po wykonaniu poprzednich kroków można przystąpić do utworzenia puli wystąpień.

Następujące ograniczenia mają zastosowanie do puli wystąpień:

- Tylko ogólnego przeznaczenia i Gen5 są dostępne w publicznej wersji zapoznawczej.
- Nazwa puli może zawierać tylko małe litery, liczby i łącznik i nie może zaczynać się od łącznika.
- Jeśli chcesz użyć AHB (Korzyści hybrydowe platformy Azure), jest stosowany na poziomie puli wystąpień. Typ licencji można ustawić podczas tworzenia puli lub zaktualizować go w dowolnym momencie po utworzeniu.

> [!IMPORTANT]
> Wdrażanie puli wystąpień jest długotrwałą operacją, która trwa około 4,5 godziny.

Aby uzyskać parametry sieci:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Aby utworzyć pulę wystąpień:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Ponieważ wdrażanie puli wystąpień jest długotrwałą operacją, należy poczekać, aż zakończy się przed uruchomieniem któregokolwiek z poniższych kroków w tym artykule.

## <a name="create-a-managed-instance-inside-the-pool"></a>Tworzenie wystąpienia zarządzanego wewnątrz puli 

Po pomyślnym wdrożeniu puli wystąpień nadszedł czas, aby utworzyć wystąpienie wewnątrz niego.

Aby utworzyć wystąpienie zarządzane, wykonaj następujące polecenie:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Wdrażanie wystąpienia wewnątrz puli zajmuje kilka minut. Po utworzeniu pierwszego wystąpienia można utworzyć dodatkowe wystąpienia:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Tworzenie bazy danych wewnątrz wystąpienia 

Aby utworzyć bazy danych i zarządzać nimi w wystąpieniu zarządzanym znajdującym się wewnątrz puli, należy użyć poleceń pojedynczego wystąpienia.

Aby utworzyć bazę danych wewnątrz wystąpienia zarządzanego:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Pobierz użycie puli wystąpień 
 
Aby uzyskać listę wystąpień wewnątrz puli:

```powershell
$instancePool | Get-AzSqlInstance
```


Aby uzyskać użycie zasobów puli:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Aby uzyskać szczegółowy przegląd użycia puli i wystąpień wewnątrz niego:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Aby wyświetlić listę baz danych w wystąpieniu:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Istnieje limit 100 baz danych na pulę (nie na wystąpienie).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Skalowanie wystąpienia zarządzanego wewnątrz puli 


Po zapełnieniu wystąpienia zarządzanego za pomocą baz danych może zostać trafione limity wystąpień dotyczące magazynu lub wydajności. W takim przypadku jeśli użycie puli nie zostało przekroczone, można skalować wystąpienie.
Skalowanie wystąpienia zarządzanego wewnątrz puli jest operacją, która trwa kilka minut. Warunkiem wstępnym skalowania są dostępne umowy wirtualne i magazyn na poziomie puli wystąpień.

Aby zaktualizować liczbę korpów wirtualnych i rozmiar magazynu:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Aby zaktualizować tylko rozmiar magazynu:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Łączenie się z wystąpieniem zarządzanym wewnątrz puli

Aby połączyć się z wystąpieniem zarządzanym w puli, wymagane są następujące dwa kroki:

1. [Włącz publiczny punkt końcowy dla wystąpienia](#enable-the-public-endpoint-for-the-instance).
2. [Dodaj regułę przychodzącą do sieciowej grupy zabezpieczeń](#add-an-inbound-rule-to-the-network-security-group).

Po zakończeniu obu kroków można połączyć się z wystąpieniem przy użyciu publicznego adresu końcowego, portu i poświadczeń podanych podczas tworzenia wystąpienia. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Włączanie publicznego punktu końcowego dla wystąpienia

Włączenie publicznego punktu końcowego dla wystąpienia można wykonać za pomocą witryny Azure portal lub za pomocą następującego polecenia programu PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Ten parametr można ustawić podczas tworzenia wystąpienia, jak również.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Dodawanie reguły przychodzącej do sieciowej grupy zabezpieczeń 

Ten krok można wykonać za pomocą witryny Azure portal lub za pomocą poleceń programu PowerShell i można to zrobić w dowolnym momencie po przygotowaniu podsieci dla wystąpienia zarządzanego.

Aby uzyskać szczegółowe informacje, zobacz [Zezwalanie na publiczny ruch punkt końcowy w sieciowej grupie zabezpieczeń](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Przenoszenie istniejącego pojedynczego wystąpienia wewnątrz puli wystąpień 
 
Przenoszenie wystąpień do i z puli jest jednym z ograniczeń publicznej wersji zapoznawczej. Obejście, którego można użyć, opiera się na przywracaniu baz danych w czasie z wystąpienia poza pulą do wystąpienia, które znajduje się już w puli. 

Oba wystąpienia muszą znajdować się w tej samej subskrypcji i regionie. Przywracanie między regionami i między subskrypcjami nie jest obecnie obsługiwane.

Ten proces ma okres przestojów.

Aby przenieść istniejące bazy danych:

1. Wstrzymaj obciążenia w przypadku wystąpienia zarządzanego, z którego przeprowadzasz migrację.
2. Generowanie skryptów do tworzenia baz danych systemu i wykonywania ich w wystąpieniu, które znajduje się wewnątrz puli wystąpień.
3. Wykonaj przywracanie punktu w czasie każdej bazy danych z pojedynczego wystąpienia do wystąpienia w puli.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Wskaż aplikację do nowego wystąpienia i wznowić jego obciążeń.

Jeśli istnieje wiele baz danych, powtórz proces dla każdej bazy danych.


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać funkcje i listę porównawczą, zobacz [wspólne funkcje SQL](sql-database-features.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [konfigurację sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
- Aby uzyskać przewodnik Szybki start, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby zapoznać się z samouczkiem przy użyciu usługi migracji bazy danych Azure (DMS) do migracji, zobacz [migracja wystąpienia zarządzanego przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpienia zarządzanego za pomocą wbudowanej analizy rozwiązywania problemów, zobacz [Monitorowanie usługi Azure SQL Database przy użyciu usługi Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Aby uzyskać informacje o cenach, zobacz [cennik zarządzanych wystąpień bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
