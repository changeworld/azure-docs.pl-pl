---
title: Ustawianie ról RBAC dla dostępu administracyjnego platformy Azure
titleSuffix: Azure Cognitive Search
description: Kontrola administracyjna oparta na rolach (RBAC) w witrynie Azure portal w celu kontrolowania i delegowanie zadań administracyjnych do zarządzania usługą Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112558"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>Ustawianie ról RBAC dla dostępu administracyjnego do usługi Azure Cognitive Search

Platforma Azure udostępnia [globalny model autoryzacji oparty na rolach](../role-based-access-control/role-assignments-portal.md) dla wszystkich usług zarządzanych za pośrednictwem interfejsów API portalu lub Menedżera zasobów. Role Właściciel, Współautor i Czytelnik określają poziom *administrowania usługami* dla użytkowników, grup i podmiotów zabezpieczeń usługi Active Directory przypisanych do każdej roli. 

> [!Note]
> Nie ma kontroli dostępu opartych na rolach do zabezpieczania części indeksu lub podzbioru dokumentów. W przypadku dostępu opartego na tożsamości nad wynikami wyszukiwania można utworzyć filtry zabezpieczeń, aby przyciąć wyniki według tożsamości, usuwając dokumenty, do których żądający nie powinien mieć dostępu. Aby uzyskać więcej informacji, zobacz [Filtry zabezpieczeń](search-security-trimming-for-azure-search.md) i [Bezpieczne za pomocą usługi Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Zadania zarządzania według roli

W przypadku usługi Azure Cognitive Search role są skojarzone z poziomami uprawnień, które obsługują następujące zadania zarządzania:

| Rola | Zadanie |
| --- | --- |
| Właściciel |Tworzenie lub usuwanie usługi lub dowolnego obiektu w usłudze, w tym api-keys, indeksy, indeksatory, źródła danych indeksatora i harmonogramy indeksatora.<p>Wyświetl stan usługi, w tym liczby i rozmiar magazynu.<p>Dodawanie lub usuwanie członkostwa roli (tylko właściciel może zarządzać członkostwem roli).<p>Administratorzy subskrypcji i właściciele usług mają automatyczne członkostwo w roli Właściciele. |
| Współautor |Ten sam poziom dostępu jak właściciel, minus zarządzanie rolami RBAC. Na przykład współautor może tworzyć lub usuwać obiekty lub wyświetlać i regenerować [klucze interfejsu API](search-security-api-keys.md), ale nie może modyfikować członkostw roli. |
| [Funkcja wbudowana współautora usługi wyszukiwania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Odpowiednik roli współautora. |
| Czytelnik |Wyświetl podstawowe informacje i metryki usługi. Członkowie tej roli nie mogą wyświetlać indeksu, indeksatora, źródła danych ani kluczowych informacji.  |

Role nie przyznają praw dostępu do punktu końcowego usługi. Operacje usługi wyszukiwania, takie jak zarządzanie indeksami, zasiew indeksu i zapytania dotyczące danych wyszukiwania, są kontrolowane za pomocą kluczy interfejsu API, a nie ról. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami interfejsu api](search-security-api-keys.md).

## <a name="see-also"></a>Zobacz też

+ [Zarządzanie za pomocą programu PowerShell](search-manage-powershell.md) 
+ [Wydajność i optymalizacja w usłudze Azure Cognitive Search](search-performance-optimization.md)
+ [Wprowadzenie do kontroli dostępu opartej na rolach w witrynie Azure portal](../role-based-access-control/overview.md).