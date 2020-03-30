---
title: 'Role niestandardowe: migracje wystąpienia zarządzanych przez program SQL server online do sql'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak używać ról niestandardowych dla sql server do migracji online wystąpienia zarządzanego usługi Azure SQL Database.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: e9a1024ca3ab68841474ab051c029042df4915b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254940"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>Role niestandardowe dla migracji online wystąpienia zarządzanego programu SQL Server do bazy danych SQL

Usługa migracji bazy danych platformy Azure używa identyfikatora aplikacji do interakcji z usługami platformy Azure. Identyfikator aplikacji wymaga roli współautora na poziomie subskrypcji (na co wiele działów zabezpieczeń firmy nie zezwala) lub utworzenia ról niestandardowych, które przyznają określone uprawnienia, których wymaga usługa migracji bazy danych platformy Azure. Ponieważ w usłudze Azure Active Directory istnieje limit 2000 ról niestandardowych, można połączyć wszystkie uprawnienia wymagane specjalnie przez identyfikator APLIKACJI w jedną lub dwie role niestandardowe, a następnie przyznać identyfikatorowi APLIKACJI rolę niestandardową dla określonych obiektów lub grup zasobów (w porównaniu z w przypadku określonych obiektów lub grup zasobów). poziom subskrypcji). Jeśli liczba ról niestandardowych nie jest problemem, można podzielić role niestandardowe według typu zasobu, aby utworzyć trzy role niestandardowe w sumie, jak opisano poniżej.

Sekcja AssignableScopes ciągu json definicji roli umożliwia kontrolowanie, gdzie uprawnienia są wyświetlane w interfejsie użytkownika **dodaj przypisanie roli** w portalu. Prawdopodobnie należy zdefiniować rolę w grupie zasobów lub nawet na poziomie zasobów, aby uniknąć zaśmiecania interfejsu użytkownika dodatkowymi rolami. Należy zauważyć, że nie wykonuje to rzeczywistego przypisania roli.

## <a name="minimum-number-of-roles"></a>Minimalna liczba ról

Obecnie zaleca się utworzenie co najmniej dwóch ról niestandardowych dla identyfikatora aplikacji, jednej na poziomie zasobu, a drugiej na poziomie subskrypcji.

> [!NOTE]
> Ostatnie wymaganie roli niestandardowej może ostatecznie zostać usunięte, ponieważ nowy kod wystąpienia zarządzanego bazy danych SQL jest wdrażany na platformie Azure.

**Rola niestandardowa dla identyfikatora aplikacji**. Ta rola jest wymagana dla migracji usługi migracji usługi Azure Database na poziomie *zasobu* lub *grupy zasobów* (aby uzyskać więcej informacji na temat identyfikatora aplikacji, zobacz artykuł [Użyj portalu, aby utworzyć aplikację i jednostkę usługi Azure AD, która może uzyskiwać dostęp do zasobów).](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Rola niestandardowa dla identyfikatora APLIKACJI — subskrypcja**. Ta rola jest wymagana dla migracji usługi migracji usługi Azure Database na poziomie *subskrypcji.*

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

Powyższy json musi być przechowywany w trzech plikach tekstowych i można użyć polecenia cmdlet AzureRM, AZ PowerShell lub interfejsu wiersza polecenia platformy Azure do tworzenia ról przy użyciu **funkcji New-AzureRmRoleDefinition (AzureRM)** lub **New-AzRoleDefinition (AZ)**.

Aby uzyskać więcej informacji, zobacz artykuł [Niestandardowe role zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Po utworzeniu tych ról niestandardowych należy dodać przypisania ról do użytkowników i identyfikatory aplikacji do odpowiednich zasobów lub grup zasobów:

* Rola "Rola DMS — identyfikator aplikacji" musi być przyznana identyfikatorowi APLIKACJI, który będzie używany do migracji, a także na poziomie zasobów konta magazynu, usługi migracji bazy danych Azure i poziomów zasobów zarządzanych wystąpień bazy danych SQL.
* Rola "Rola DMS — identyfikator aplikacji — sub" musi być przyznana identyfikatorowi APLIKACJI na poziomie subskrypcji (udzielanie w grupie zasobów lub zasobów zakończy się niepowodzeniem). To wymaganie jest tymczasowe, dopóki nie zostanie wdrożona aktualizacja kodu.

## <a name="expanded-number-of-roles"></a>Rozszerzona liczba ról

Jeśli liczba ról niestandardowych w usłudze Azure Active Directory nie jest problemem, zaleca się utworzenie łącznie trzech ról. Nadal będziesz potrzebować roli "Rola DMS — identyfikator aplikacji — sub", ale powyższa rola "Rola DMS — identyfikator aplikacji" jest podzielona według typu zasobu na dwie różne role.

**Rola niestandardowa wystąpienia zarządzanego identyfikatora aplikacji dla bazy danych SQL**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Rola niestandardowa identyfikatora aplikacji dla magazynu**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Przypisanie roli

Aby przypisać rolę do identyfikatora użytkowników/aplikacji, otwórz witrynę Azure Portal, wykonaj następujące czynności:

1. Przejdź do grupy zasobów lub zasobu (z wyjątkiem roli, która musi zostać przyznana w ramach subskrypcji), przejdź do **sekcji Kontrola dostępu,** a następnie przewiń, aby znaleźć utworzone właśnie niestandardowe role.

2. Wybierz odpowiednią rolę, wybierz identyfikator aplikacji, a następnie zapisz zmiany.

  Identyfikatory aplikacji są teraz wyświetlane na karcie **Przypisania ról.**

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się ze wskazówkami dotyczącymi migracji dla swojego scenariusza w [Przewodniku migracji bazy danych firmy](https://datamigration.microsoft.com/)Microsoft .
