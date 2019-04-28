---
title: Ustaw role RBAC na potrzeby dostępu administracyjnego platformy Azure w portalu — usługa Azure Search
description: Oparta na rolach kontroli administracyjnej (RBAC) w witrynie Azure portal, związanych z kontrolowaniem i delegowanie zadań administracyjnych dotyczących zarządzania usługi Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 34def35eba1e5c1645e6e1f9a505704d153ac716
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282390"
---
# <a name="set-rbac-roles-for-administrative-access"></a>Ustaw role RBAC na potrzeby dostępu administracyjnego

Platforma Azure udostępnia [modelu autoryzacji opartej na rolach globalnego](../role-based-access-control/role-assignments-portal.md) dla wszystkich usług zarządzanych za pośrednictwem portalu lub usługi Resource Manager interfejsów API. Role właściciela, współautora i czytelnika określają poziom *usługi administracyjnej* dla użytkowników, grup i podmioty zabezpieczeń przypisane do poszczególnych ról usługi Active Directory. 

> [!Note]
> Nie ma żadnych kontroli dostępu opartej na rolach, do zabezpieczania części indeksu lub podzbiór dokumentów. W przypadku dostępu na podstawie tożsamości za pośrednictwem wyników wyszukiwania można utworzyć filtry zabezpieczeń, aby przyciąć wyniki według tożsamości, usuwanie dokumentów, dla których obiekt żądający nie powinny mieć dostępu. Aby uzyskać więcej informacji, zobacz [filtrów zabezpieczeń](search-security-trimming-for-azure-search.md) i [bezpiecznego z usługą Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Zadania zarządzania według roli

Usługi Azure Search role są powiązane z poziomami uprawnień, które obsługują następujące zadania zarządzania:

| Rola | Zadanie |
| --- | --- |
| Właściciel |Tworzenie lub usuwanie usługi lub dowolny obiekt na usługi, w tym klucze api Key, indeksy, indeksatory, indeksator źródeł danych i harmonogramy indeksatora.<p>Wyświetl stan usługi, w tym liczby i rozmiaru magazynu.<p>Dodawanie lub usuwanie członkostwa w roli (tylko właściciel może zarządzać członkostwem w roli).<p>Administratorzy subskrypcji i właściciele usługi mają automatyczne członkostwa w roli właścicieli. |
| Współautor |Ten sam poziom dostępu jako właściciela, minus RBAC zarządzania rolami. Na przykład współautora można utworzyć lub usunąć obiekty, wyświetlić lub ponownie wygenerować [klucze api Key](search-security-api-keys.md), ale nie można modyfikować członkostwa w roli. |
| [Wbudowana rola Współautor usługi wyszukiwania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Wartość równoważna do roli współautor. |
| Czytelnik |Podstawowe informacje o usłudze widoku i metryki. Członkowie tej roli nie można wyświetlić indeks, indeksator, źródła danych lub informacje o kluczu.  |

Role nie prawa dostępu do punktu końcowego usługi. Wyszukiwania usługi operacje, takie jak Zarządzanie indeksami, wypełniania indeksami pełnotekstowymi i zapytań wyszukiwania danych, są kontrolowane za pomocą kluczy interfejsu api, nie ról. Aby uzyskać więcej informacji, zobacz [zarządzanie kluczami interfejsu api](search-security-api-keys.md).

## <a name="see-also"></a>Zobacz także

+ [Zarządzanie za pomocą programu PowerShell](search-manage-powershell.md) 
+ [Wydajność i optymalizacja w usłudze Azure Search](search-performance-optimization.md)
+ [Rozpoczynanie pracy z usługą opartej na rolach kontrola dostępu w witrynie Azure portal](../role-based-access-control/overview.md).