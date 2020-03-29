---
title: Kontrola dostępu oparta na rolach w usłudze Azure Cosmos DB
description: Dowiedz się, jak usługa Azure Cosmos DB zapewnia ochronę bazy danych za pomocą integracji usługi Active Directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445101"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Kontrola dostępu oparta na rolach w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB zapewnia wbudowaną kontrolę dostępu opartą na rolach (RBAC) dla typowych scenariuszy zarządzania w usłudze Azure Cosmos DB. Osoba, która ma profil w usłudze Azure Active Directory, może przypisać te role RBAC do użytkowników, grup, podmiotów zabezpieczeń usługi lub tożsamości zarządzanych w celu udzielenia lub odmowy dostępu do zasobów i operacji w zasobach usługi Azure Cosmos DB. Przypisania ról są ograniczone tylko do dostępu do płaszczyzny kontroli, która obejmuje dostęp do kont usługi Azure Cosmos, baz danych, kontenerów i ofert (przepływność).

## <a name="built-in-roles"></a>Wbudowane role

Poniżej przedstawiono wbudowane role obsługiwane przez usługę Azure Cosmos DB:

|**Wbudowana rola**  |**Opis**  |
|---------|---------|
|[Współautor konta DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Może zarządzać kontami usługi Azure Cosmos DB.|
|[Czytnik kont usługi Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Może odczytywać dane konta usługi Azure Cosmos DB.|
|[Operator kopii zapasowej cosmosu](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Można przesłać żądanie przywracania dla bazy danych usługi Azure Cosmos lub kontenera.|
|[Operator bazy danych Cosmos](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Można aprowizować konta usługi Azure Cosmos, bazy danych i kontenery, ale nie można uzyskać dostępu do kluczy, które są wymagane do uzyskania dostępu do danych.|

> [!IMPORTANT]
> Obsługa rbac w usłudze Azure Cosmos DB dotyczy tylko operacji płaszczyzny sterowania. Operacje na płaszczyźnie danych są zabezpieczone przy użyciu kluczy głównych lub tokenów zasobów. Aby dowiedzieć się więcej, zobacz [Bezpieczny dostęp do danych w usłudze Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Zarządzanie tożsamościami i dostępem (IAM)

Okienko **Kontrola dostępu (IAM)** w witrynie Azure Portal służy do konfigurowania kontroli dostępu opartej na rolach w zasobach usługi Azure Cosmos. Role są stosowane do użytkowników, grup, podmiotów usługi i tożsamości zarządzanych w usłudze Active Directory. Dla osób i grup można używać wbudowanych ról lub ról niestandardowych. Poniższy zrzut ekranu przedstawia integrację usługi Active Directory (RBAC) przy użyciu kontroli dostępu (IAM) w witrynie Azure portal:

![Kontrola dostępu (IAM) w witrynie Azure portal — demonstrowanie zabezpieczeń bazy danych](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Role niestandardowe

Oprócz wbudowanych ról użytkownicy mogą również tworzyć [role niestandardowe](../role-based-access-control/custom-roles.md) na platformie Azure i stosować te role do podmiotów usługi we wszystkich subskrypcjach w dzierżawie usługi Active Directory. Role niestandardowe umożliwiają użytkownikom tworzenie definicji ról RBAC za pomocą niestandardowego zestawu operacji dostawcy zasobów. Aby dowiedzieć się, które operacje są dostępne do tworzenia ról niestandardowych dla usługi Azure Cosmos DB zobacz, [operacje dostawcy zasobów usługi Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Zapobieganie zmianom w usłudze Cosmos SDK

Dostawca zasobów usługi Cosmos można zablokować, aby zapobiec wszelkim zmianom zasobów, w tym konta usługi Cosmos, baz danych, kontenerów i przepływności od dowolnego klienta łączącego się za pośrednictwem kluczy konta (tj. aplikacji łączących się za pośrednictwem usługi Cosmos SDK). Po ustawieniu zmiany w dowolnym zasobie muszą pochodzić od użytkownika z właściwą rolą i poświadczeniami RBAC. Ta funkcja jest `disableKeyBasedMetadataWriteAccess` ustawiona z wartością właściwości w dostawcy zasobów usługi Cosmos. Poniżej znajduje się przykład szablonu usługi Azure Resource Manager z tym ustawieniem właściwości.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

- [Co to jest kontrola dostępu oparta na rolach (RBAC) dla zasobów platformy Azure](../role-based-access-control/overview.md)
- [Niestandardowe role dla zasobów platformy Azure](../role-based-access-control/custom-roles.md)
- [Operacje dostawcy zasobów usługi Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
