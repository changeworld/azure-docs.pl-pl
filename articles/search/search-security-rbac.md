---
title: Ustaw role RBAC dla dostępu administracyjnego do usługi Azure Search w portalu | Dokumentacja firmy Microsoft
description: Oparta na rolach kontrola administracyjna w portalu Azure.
services: search
documentationcenter: ''
author: HeidiSteen
manager: cgronlun
editor: ''
tags: azure-portal
ms.assetid: ''
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: d14bf9b154c450d863d4d365c215e9712694a767
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="set-rbac-roles-for-administrative-access"></a>Ustaw role RBAC dla dostępu administracyjnego

Platforma Azure udostępnia [modelu autoryzacji opartej na rolach globalne](../role-based-access-control/role-assignments-portal.md) dla wszystkich usług zarządzanych za pomocą portalu lub Menedżera zasobów interfejsów API. Role właściciela, współautora i czytnika określają poziom *usługi administracyjnej* dla użytkowników, grup i podmiotów zabezpieczeń przypisanych do każdej roli usługi Active Directory. 

> [!Note]
> Nie ma żadnych kontroli dostępu opartej na rolach, do zabezpieczania części indeksu lub podzbiór dokumentów. Oparte na tożsamości dostępu za pośrednictwem wyniki wyszukiwania można utworzyć filtrów zabezpieczeń, aby przyciąć wyników przez tożsamość, usuwanie dokumentów, dla których obiekt żądający nie powinny mieć dostępu. Aby uzyskać więcej informacji, zobacz [filtrów zabezpieczeń](search-security-trimming-for-azure-search.md) i [bezpiecznego z usługą Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Zadania zarządzania przez rolę

Dla usługi Azure Search role są powiązane z poziomami uprawnień, które obsługują następujące zadania zarządzania:

| Rola | Zadanie |
| --- | --- |
| Właściciel |Utwórz lub Usuń usługę lub dowolny obiekt na usługi, w tym klucze interfejsu api, indeksów, indeksatorów, indeksatora źródeł danych i harmonogramy indeksatora.<p>Wyświetl stan usługi, w tym liczby i rozmiaru magazynu.<p>Dodawanie lub usuwanie członkostwo roli (tylko właściciel może zarządzać członkostwem w roli).<p>Administratorzy subskrypcji i właścicieli usług mają automatyczne członkostwo w roli właścicieli. |
| Współautor |Sam poziom dostępu właściciel minus RBAC zarządzania rolami. Na przykład współautora możliwości tworzenia lub usuwania obiektów, lub wyświetlić i ponownie wygenerować [klucze interfejsu api](search-security-api-keys.md), ale nie można zmodyfikować członkostwa w roli. |
| [Wbudowana Rola współautora usługi wyszukiwania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Równoważne roli współautora. |
| Czytelnik |Widok usługi essentials i metryki. Członkowie tej roli nie można wyświetlić indeks, indeksator, źródła danych lub informacje o kluczu.  |

Role nie prawa dostępu do punktu końcowego usługi. Wyszukiwania na nich operacji usługi, takie jak Zarządzanie indeksami, wypełniania indeksu i zapytań wyszukiwania danych, są kontrolowane przez klucze interfejsu api, nie ról. Aby uzyskać więcej informacji, zobacz [zarządzanie kluczami interfejsu api](search-security-api-keys.md).

## <a name="see-also"></a>Zobacz także

+ [Zarządzanie za pomocą programu PowerShell](search-manage-powershell.md) 
+ [Wydajność i optymalizacji w usłudze Azure Search](search-performance-optimization.md)
+ [Rozpoczynanie pracy z opartej na rolach kontroli dostępu w portalu Azure](../role-based-access-control/overview.md).