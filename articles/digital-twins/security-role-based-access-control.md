---
title: Zrozumienie kontroli dostępu opartej na rolach Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, uwierzytelnianie w reprezentacji urządzeń cyfrowych przy użyciu kontroli dostępu opartej na rolach.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: lyrana
ms.openlocfilehash: bfc73a71a0ccda5c135e6a740d6f63bd37522a9b
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904274"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Kontrola dostępu oparta na rolach w reprezentacji urządzeń cyfrowych platformy Azure

Twins cyfrowych platformy Azure umożliwia kontrolowanie precyzyjnego dostępu do określonych danych, zasobów i akcji w grafie przestrzennych. Robi to za pośrednictwem szczegółowe zarządzanie rolami i uprawnieniami o nazwie kontroli dostępu opartej na rolach (RBAC). RBAC składa się z _role_ i _przypisań ról_. Role Określ poziom uprawnień. Przypisania ról skojarzyć roli z użytkowników lub urządzeń.

Korzystając z modelu RBAC uprawnienia nadane:

- Użytkownik.
- Urządzenie.
- Jednostki usługi.
- Funkcja zdefiniowana przez użytkownika.
- Wszyscy użytkownicy, którzy należą do domeny.
- Dzierżawca.

Można także dostosować stopień dostępu.

RBAC jest unikatowa, w tym, że uprawnienia są dziedziczone w dół przestrzenne programu graph.

## <a name="what-can-i-do-with-rbac"></a>Co można zrobić za pomocą kontroli dostępu opartej na rolach?

Deweloper może używać funkcji RBAC w usłudze:

- Udzielić użytkownikowi możliwość zarządzania urządzeniami dla całego kompilowania lub tylko dla konkretnego pomieszczenia lub piętra.
- Udziel administrator globalny dostęp do wszystkich węzłów przestrzenne wykresu cały wykres, czy tylko w części wykresu.
- Udzielanie dostępu Odczyt specjalista ds. pomocy technicznej do grafu, z wyjątkiem klucze dostępu.
- Przyznaj każdy członek domeny do odczytu wszystkich obiektów grafu.

## <a name="rbac-best-practices"></a>Najlepsze rozwiązania RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Role

### <a name="role-definitions"></a>Definicje ról

Definicja roli to zbiór uprawnień i inne atrybuty wchodzących w skład roli. Definicja roli Wyświetla dozwolone operacje, które obejmują *Utwórz*, *odczytu*, *aktualizacji*, i *Usuń* , dowolnego obiektu, korzystając z niego Rola może wykonywać. Określa również, do którego dotyczą uprawnień typów obiektu.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Aby pobrać pełną definicje ról poprzedniej, zapytania systemu/role interfejsu API.
> Dowiedz się więcej, czytając [tworzenie i Zarządzanie przypisaniami ról](./security-create-manage-role-assignments.md#all).

### <a name="object-identifier-types"></a>Typy identyfikatorów obiektów

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Dowiedz się, jak udzielić uprawnień do jednostki usługi, czytając [tworzenie i Zarządzanie przypisaniami ról](./security-create-manage-role-assignments.md#grant).

Opisano w następujących artykułach dokumentacji odwołania:

- Jak [zapytania lub identyfikator obiektu użytkownika](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Jak [uzyskać identyfikator obiektu dla jednostki usługi](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Jak [pobrać identyfikator obiektu dla dzierżawy usługi Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Przypisania ról

Przypisanie roli Azure cyfrowego bliźniaczych reprezentacji kojarzy obiekt, na przykład użytkownik lub dzierżawę usługi Azure AD z roli i spację. Uprawnienia są przyznawane wszystkie obiekty, które należą do tego miejsca. Miejsce obejmuje cały wykres przestrzenne znajdujące się poniżej.

Na przykład, użytkownik otrzyma przypisania roli z rolą `DeviceInstaller` dla węzła głównego przestrzenne programu graph, która reprezentuje budynku. Użytkownik, a następnie może odczytywać i zaktualizuj urządzenia dla tego węzła i wszystkich innych podrzędnych miejsca do magazynowania w budynku.

Aby udzielić uprawnień do adresata, Utwórz przypisanie roli. Aby odwołać uprawnienia, usunąć przypisanie roli.

>[!IMPORTANT]
> Więcej informacji na temat przypisania ról, czytając [tworzenie i Zarządzanie przypisaniami ról](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat tworzenia i zarządzania przypisaniami ról Twins cyfrowych platformy Azure, przeczytaj [tworzenie i Zarządzanie przypisaniami ról](./security-create-manage-role-assignments.md).
