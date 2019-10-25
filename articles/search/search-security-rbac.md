---
title: Ustawianie ról RBAC dla dostępu administracyjnego platformy Azure w portalu
titleSuffix: Azure Cognitive Search
description: Kontrola administracyjna oparta na rolach (RBAC) w Azure Portal do kontrolowania i delegowania zadań administracyjnych dotyczących zarządzania Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 321aabb26d5929f7587dd61e7d4059701f7ad526
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794321"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>Ustawianie ról RBAC na potrzeby dostępu administracyjnego do usługi Azure Wyszukiwanie poznawcze

Platforma Azure udostępnia [globalny model autoryzacji oparty na rolach](../role-based-access-control/role-assignments-portal.md) dla wszystkich usług zarządzanych za pośrednictwem portalu lub Menedżer zasobów interfejsów API. Role Owner, współautor i Reader określają poziom *administrowania usługą* dla Active Directory użytkowników, grup i podmiotów zabezpieczeń przypisanych do każdej roli. 

> [!Note]
> Nie ma kontroli dostępu opartej na rolach w celu zabezpieczania części indeksu lub podzbioru dokumentów. W przypadku dostępu opartego na tożsamościach za pomocą wyników wyszukiwania można utworzyć filtry zabezpieczeń, aby przyciąć wyniki według tożsamości, usuwając dokumenty, dla których obiekt żądający nie powinien mieć dostępu. Aby uzyskać więcej informacji, zobacz [filtry zabezpieczeń](search-security-trimming-for-azure-search.md) i [zabezpieczanie przy Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Zadania zarządzania według roli

W przypadku usługi Azure Wyszukiwanie poznawcze role są skojarzone z poziomem uprawnień, które obsługują następujące zadania zarządzania:

| Rola | Zadanie |
| --- | --- |
| Właściciel |Utwórz lub Usuń usługę lub dowolny obiekt z usługi, w tym klucze API-Keys, indeksy, indeksatory, źródła danych indeksatora i harmonogramy indeksatora.<p>Wyświetlanie stanu usługi, w tym liczby i rozmiaru magazynu.<p>Dodaj lub Usuń członkostwo w roli (tylko właściciel może zarządzać członkostwem w roli).<p>Administratorzy subskrypcji i właściciele usług mają automatyczne członkostwo w roli właściciele. |
| Współautor |Taki sam poziom dostępu jak właściciel, minus Zarządzanie rolą RBAC. Na przykład współautor może tworzyć lub usuwać obiekty lub wyświetlać i generować ponownie [klucze API](search-security-api-keys.md), ale nie mogą modyfikować członkostw ról. |
| [Wbudowana rola współautora Search Service](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Odpowiednik roli współautor. |
| Czytelnik |Wyświetl podstawowe informacje dotyczące usługi i metryki. Członkowie tej roli nie mogą wyświetlać informacji dotyczących indeksu, indeksatora, źródła danych ani klucza.  |

Role nie przyznają praw dostępu do punktu końcowego usługi. Operacje usługi wyszukiwania, takie jak zarządzanie indeksami, populacja indeksu i zapytania dotyczące danych wyszukiwania, są kontrolowane przez klucze API-Keys, a nie role. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami interfejsu API](search-security-api-keys.md).

## <a name="see-also"></a>Zobacz także

+ [Zarządzanie za pomocą programu PowerShell](search-manage-powershell.md) 
+ [Wydajność i optymalizacja na platformie Azure Wyszukiwanie poznawcze](search-performance-optimization.md)
+ [Rozpocznij pracę z Access Controlami opartymi na rolach w Azure Portal](../role-based-access-control/overview.md).