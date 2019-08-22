---
title: Ustawianie ról RBAC dla dostępu administracyjnego platformy Azure w portalu — Azure Search
description: Kontrola administracyjna oparta na rolach (RBAC) w Azure Portal do kontrolowania i delegowania zadań administracyjnych do zarządzania Azure Search.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 924d2529e3477c299d4a90c076fe9e6c8faf11f3
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647410"
---
# <a name="set-rbac-roles-for-administrative-access"></a>Ustawianie ról RBAC na potrzeby dostępu administracyjnego

Platforma Azure udostępnia [globalny model autoryzacji oparty na rolach](../role-based-access-control/role-assignments-portal.md) dla wszystkich usług zarządzanych za pośrednictwem portalu lub Menedżer zasobów interfejsów API. Role Owner, współautor i Reader określają poziom *administrowania usługą* dla Active Directory użytkowników, grup i podmiotów zabezpieczeń przypisanych do każdej roli. 

> [!Note]
> Nie ma kontroli dostępu opartej na rolach w celu zabezpieczania części indeksu lub podzbioru dokumentów. W przypadku dostępu opartego na tożsamościach za pomocą wyników wyszukiwania można utworzyć filtry zabezpieczeń, aby przyciąć wyniki według tożsamości, usuwając dokumenty, dla których obiekt żądający nie powinien mieć dostępu. Aby uzyskać więcej informacji, zobacz [filtry zabezpieczeń](search-security-trimming-for-azure-search.md) i [zabezpieczanie przy Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Zadania zarządzania według roli

W przypadku Azure Search role są skojarzone z poziomem uprawnień, które obsługują następujące zadania zarządzania:

| Role | Zadanie |
| --- | --- |
| Właściciel |Utwórz lub Usuń usługę lub dowolny obiekt z usługi, w tym klucze API-Keys, indeksy, indeksatory, źródła danych indeksatora i harmonogramy indeksatora.<p>Wyświetlanie stanu usługi, w tym liczby i rozmiaru magazynu.<p>Dodaj lub Usuń członkostwo w roli (tylko właściciel może zarządzać członkostwem w roli).<p>Administratorzy subskrypcji i właściciele usług mają automatyczne członkostwo w roli właściciele. |
| Współautor |Taki sam poziom dostępu jak właściciel, minus Zarządzanie rolą RBAC. Na przykład współautor może tworzyć lub usuwać obiekty lub wyświetlać i generować ponownie [klucze API](search-security-api-keys.md), ale nie mogą modyfikować członkostw ról. |
| [Wbudowana rola współautora Search Service](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Odpowiednik roli współautor. |
| Czytelnik |Wyświetl podstawowe informacje dotyczące usługi i metryki. Członkowie tej roli nie mogą wyświetlać informacji dotyczących indeksu, indeksatora, źródła danych ani klucza.  |

Role nie przyznają praw dostępu do punktu końcowego usługi. Operacje usługi wyszukiwania, takie jak zarządzanie indeksami, populacja indeksu i zapytania dotyczące danych wyszukiwania, są kontrolowane przez klucze API-Keys, a nie role. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami interfejsu API](search-security-api-keys.md).

## <a name="see-also"></a>Zobacz także

+ [Zarządzanie za pomocą programu PowerShell](search-manage-powershell.md) 
+ [Wydajność i optymalizacja w Azure Search](search-performance-optimization.md)
+ [Rozpocznij pracę z Access Controlami opartymi na rolach w Azure Portal](../role-based-access-control/overview.md).