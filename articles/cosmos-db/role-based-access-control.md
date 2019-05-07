---
title: Kontrola dostępu oparta na rolach w usłudze Azure Cosmos DB dzięki integracji usługi Azure Active Directory
description: Dowiedz się, jak usługa Azure Cosmos DB zapewnia ochronę bazy danych, dzięki integracji usługi Active directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: e43341e59e807fdc20033d909beadb9d582149b5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078943"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Kontrola dostępu oparta na rolach w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB zapewnia kontroli dostępu wbudowanych opartej na rolach (RBAC) dla typowych scenariuszy zarządzania w usłudze Azure Cosmos DB. Osoba, która ma profil w usłudze Azure Active Directory można przypisać te role RBAC do użytkowników, grup, nazw głównych usług lub zarządzanych tożsamości, aby udzielić lub odmówić dostępu do zasobów i operacji na zasobach usługi Azure Cosmos DB. Przypisania ról są ograniczone do płaszczyznę kontroli dostępu, obejmuje dostęp do konta usługi Azure Cosmos, bazy danych, kontenerów, która oferuje (przepływność).

## <a name="built-in-roles"></a>Wbudowane role

Poniżej przedstawiono wbudowane role obsługiwane przez usługę Azure Cosmos DB:

|**Wbudowana rola**  |**Opis**  |
|---------|---------|
|[Współautor konta usługi DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | Można zarządzać kontami usługi Azure Cosmos DB.  |
|[Czytnik konta usługi cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Może odczytywać dane z konta usługi Azure Cosmos DB.        |
|[Operator kopii zapasowych cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Mogą przesyłać żądania przywracania bazy danych Azure Cosmos lub kontenera.       |
|[Operator usługi cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Można udostępnić konta, bazy danych i kontenerów usługi Azure Cosmos, ale nie może uzyskać dostęp do kluczy, które są wymagane do uzyskania dostępu do danych.         |

> [!IMPORTANT]
> Obsługa funkcji RBAC w usłudze Azure Cosmos DB dotyczy tylko operacje warstwy kontroli. Operacje płaszczyzny danych są chronione przy użyciu kluczy głównych lub tokenów zasobów. Aby dowiedzieć się więcej, zobacz [bezpieczny dostęp do danych w usłudze Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Zarządzanie dostępem i tożsamościami (IAM)

**Kontrola dostępu (IAM)** okienko w witrynie Azure portal służy do konfigurowania kontroli dostępu opartej na rolach w ramach zasobów usługi Azure Cosmos. Role są stosowane do użytkowników, grup, nazw głównych usług i zarządzanych tożsamości w usłudze Active Directory. Można użyć wbudowanych ról lub ról niestandardowych dla użytkowników indywidualnych i grup. Poniższy zrzut ekranu przedstawia integracji usługi Active Directory (RBAC) przy użyciu kontroli dostępu (IAM) w witrynie Azure portal:

![Kontrola dostępu (IAM) w witrynie Azure portal — prezentacja zabezpieczeń bazy danych](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Role niestandardowe

Oprócz wbudowanych ról, użytkownicy mogą również tworzyć [ról niestandardowych](../role-based-access-control/custom-roles.md) na platformie Azure i dotyczą tych ról jednostki usługi dla wszystkich subskrypcji w ramach swojej dzierżawy usługi Active Directory. Role niestandardowe umożliwiają użytkownikom do definicji ról RBAC przy użyciu niestandardowego zestawu zasobów dostawcy operacji tworzenia. Aby dowiedzieć się, jakie operacje są dostępne dla ról niestandardowych dla usługi Azure Cosmos DB, zobacz temat Tworzenie [operacji dostawcy zasobów usługi Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="next-steps"></a>Kolejne kroki

- [Co to jest kontrola dostępu oparta na rolach (RBAC) dla zasobów platformy Azure](../role-based-access-control/overview.md)
- [Niestandardowe role dla zasobów platformy Azure](../role-based-access-control/custom-roles.md)
- [Operacje dostawcy zasobów w usłudze Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
