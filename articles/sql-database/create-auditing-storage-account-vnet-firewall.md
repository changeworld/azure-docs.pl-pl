---
title: Inspekcja konta magazynu za siecią wirtualną i zaporą
description: Konfigurowanie inspekcji do zapisywania zdarzeń bazy danych na koncie magazynu za siecią wirtualną i zaporą
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 6345d210e26747f921595039a2a3c8e11be11fda
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387635"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Napisz inspekcję do konta magazynu za siecią wirtualną i zaporą

Inspekcja usługi [Azure SQL Database](sql-database-technical-overview.md) i usługi Azure [Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) obsługuje zapisywanie zdarzeń bazy danych na [koncie usługi Azure Storage](../storage/common/storage-account-overview.md) za siecią wirtualną i zaporą. 

W tym artykule wyjaśniono dwa sposoby konfigurowania konta usługi Azure SQL Server i usługi Azure storage dla tej opcji. Pierwszy używa witryny Azure portal, drugi używa REST.

### <a name="background"></a>Tło

[Sieć wirtualna platformy Azure (VNet)](../virtual-network/virtual-networks-overview.md) jest podstawowym elementem konstrukcyjnym sieci prywatnej na platformie Azure. Sieć wirtualna umożliwia wiele typów zasobów platformy Azure, takich jak maszyny wirtualne platformy Azure (VM), aby bezpiecznie komunikować się ze sobą, Internet i sieci lokalne. Sieć wirtualna jest podobna do tradycyjnej sieci we własnym centrum danych, ale przynosi ze sobą dodatkowe korzyści z infrastruktury platformy Azure, takie jak skalowanie, dostępność i izolacja.

Aby dowiedzieć się więcej na temat koncepcji sieci wirtualnej, najlepszych rozwiązań i wielu innych, zobacz [Co to jest usługa Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Aby dowiedzieć się więcej o tworzeniu sieci wirtualnej, zobacz [Szybki start: Tworzenie sieci wirtualnej przy użyciu portalu Azure](../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby inspekcja zapisywała na koncie magazynu za siecią wirtualną lub zaporą, wymagane są następujące wymagania wstępne:

> [!div class="checklist"]
> * Ogólne przeznaczenie konta magazynu w wersji 2. Jeśli masz ogólne przeznaczenie konta magazynu w wersji 1 lub obiektu blob, [uaktualnij do konta magazynu ogólnego przeznaczenia w wersji 2](../storage/common/storage-account-upgrade.md). Aby uzyskać więcej informacji, zobacz [Typy kont magazynu](../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * Konto magazynu musi znajdować się w tej samej subskrypcji i w tej samej lokalizacji co serwer bazy danych SQL azure. 
> * Konto usługi Azure `Allow trusted Microsoft services to access this storage account`Storage wymaga . Ustaw to na **zaporach**kont magazynu i sieciach wirtualnych .
> * Musisz mieć `Microsoft.Authorization/roleAssignments/write` uprawnienia do wybranego konta magazynu. Aby uzyskać więcej informacji, zobacz [wbudowane role platformy Azure](../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Konfigurowanie w witrynie Azure Portal

Połącz się z [witryną Azure portal](https://portal.azure.com) za pomocą subskrypcji. Przejdź do grupy zasobów i serwera bazy danych SQL platformy Azure.

1. Kliknij **inspekcję** pod nagłówkiem Zabezpieczenia. Wybierz **pozycję Włączone**.

2. Wybierz **opcję Magazyn**. Wybierz konto magazynu, na którym będą zapisywane dzienniki. Konto magazynu musi spełniać wymagania wymienione w [wymaganiach wstępnych](#prerequisites).

3. Szczegóły **otwórz magazyn** 

  > [!NOTE]
  > Jeśli wybrane konto magazynu znajduje się za siecią wirtualną, zostanie wyświetlony następujący komunikat:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Jeśli ten komunikat nie jest widoczny, konto magazynu nie znajduje się za siecią wirtualną.

4. Wybierz liczbę dni dla okresu przechowywania. Następnie kliknij przycisk **OK**. Dzienniki starsze niż okres przechowywania są usuwane.

5. Wybierz **pozycję Zapisz** w ustawieniach inspekcji.

Pomyślnie skonfigurowano inspekcję do zapisywania na koncie magazynu za siecią wirtualną lub zaporą. 

## <a name="configure-with-rest-commands"></a>Konfigurowanie za pomocą poleceń REST

Jako alternatywę dla korzystania z witryny Azure portal, można użyć poleceń REST do konfigurowania inspekcji do zapisywania zdarzeń bazy danych na koncie magazynu za sieci wirtualnej i zapory. 

Przykładowe skrypty w tej sekcji wymagają aktualizacji skryptu przed ich uruchomieniem. Zastąp następujące wartości w skryptach:

|Wartość przykładowa|Przykładowy opis|
|:-----|:-----|
|`<subscriptionId>`| Identyfikator subskrypcji platformy Azure|
|`<resource group>`| Grupa zasobów|
|`<sql database server>`| Nazwa serwera bazy danych SQL platformy Azure|
|`<administrator login>`| Konto administratora bazy danych SQL |
|`<complex password>`| Złożone hasło do konta administratora|

Aby skonfigurować usługę SQL Audit do zapisywania zdarzeń na koncie magazynu za siecią wirtualną lub zaporą sieciową:

1. Zarejestruj swój serwer bazy danych SQL azure w usłudze Azure Active Directory (Azure AD). Użyj interfejsu API programu PowerShell lub REST.

   **Powershell**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**INTERFEJS SPOCZYNKOWY**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate):

   Przykładowe żądanie

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Treść żądania

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do swojego konta magazynu. Znajdź **kontrolę dostępu (IAM)** i kliknij pozycję **Dodaj przypisanie roli**. Przypisz rolę RBAC **współautora danych obiektów blob magazynu** do serwera SQL platformy Azure, w której znajduje się baza danych SQL platformy Azure zarejestrowana w usłudze Azure Active Directory (Azure AD) zgodnie z poprzednim krokiem.

   > [!NOTE]
   > Tylko członkowie z uprawnieniem Owner mogą wykonać ten krok. W przypadku różnych wbudowanych ról dla zasobów platformy Azure można zapoznać się z [wbudowanymi rolami platformy Azure.](../role-based-access-control/built-in-roles.md)

3. Konfigurowanie [zasad inspekcji obiektów blob serwera SQL platformy Azure](/rest/api/sql/server%20auditing%20settings/createorupdate)bez określania *klucza dostępu do konta magazynu:*

   Przykładowe żądanie

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   Treść żądania

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="next-steps"></a>Następne kroki

- [Użyj programu PowerShell, aby utworzyć punkt końcowy usługi sieci wirtualnej, a następnie regułę sieci wirtualnej dla usługi Azure SQL Database.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Reguły sieci wirtualnej: operacje z interfejsami API REST](/rest/api/sql/virtualnetworkrules)
- [Używanie punktów końcowych usługi sieci wirtualnej i reguł dla serwerów baz danych](sql-database-vnet-service-endpoint-rule-overview.md)
