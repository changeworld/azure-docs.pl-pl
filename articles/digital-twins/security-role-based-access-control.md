---
title: Opis kontroli dostępu opartej na rolach w usłudze Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Poznaj uwierzytelnianie w programie Digital bliźniaczych reprezentacji z kontrolą dostępu opartą na rolach.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: lyhughes
ms.openlocfilehash: 33e09ad52722665e6162b18159012d69ec1463bd
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849278"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Kontrola dostępu oparta na rolach w usłudze Azure Digital bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji umożliwia precyzyjne sterowanie dostępem do określonych danych, zasobów i akcji w grafie przestrzennym. Odbywa się to za pośrednictwem szczegółowego zarządzania rolami i uprawnieniami o nazwie kontroli dostępu opartej na rolach (RBAC). RBAC składa się z _ról_ i przypisań _ról_. Role identyfikują poziom uprawnień. Przypisania ról kojarzą rolę z użytkownikiem lub urządzeniem.

Przy użyciu RBAC można udzielić uprawnienia do:

- Użytkownik.
- Urządzenie.
- Nazwa główna usługi.
- Funkcja zdefiniowana przez użytkownika.
- Wszyscy użytkownicy, którzy należą do domeny.
- Dzierżawa.

Stopień dostępu może być również dostosowany.

RBAC jest unikatowy w tym, że uprawnienia są dziedziczone jako wykres przestrzenny.

## <a name="what-can-i-do-with-rbac"></a>Co można zrobić za pomocą kontroli dostępu opartej na rolach?

Deweloper może używać RBAC, aby:

- Przyznaj użytkownikowi możliwość zarządzania urządzeniami w całym budynku lub tylko dla określonego pokoju lub podłogi.
- Przyznaj administratorowi globalny dostęp do wszystkich węzłów wykresu przestrzennego dla całego wykresu lub tylko dla sekcji grafu.
- Przyznaj specjalistom pomocy technicznej dostęp do odczytu do grafu, z wyjątkiem kluczy dostępu.
- Przyznaj wszystkim członkom domeny dostęp do odczytu do wszystkich obiektów grafu.

## <a name="rbac-best-practices"></a>Najlepsze rozwiązania RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Role

### <a name="role-definitions"></a>Definicje ról

Definicja roli jest kolekcją uprawnień i innych atrybutów, które stanowią rolę. Definicja roli zawiera listę dozwolonych operacji, takich jak *Tworzenie*, *odczytywanie*, *Aktualizowanie*i *usuwanie* dowolnego obiektu z tą rolą. Określa również, do których typów obiektów odnoszą się uprawnienia.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Aby pobrać pełne definicje dla poprzednich ról, wykonaj zapytanie do interfejsu API System/role.
> Dowiedz się więcej, odczytując [Tworzenie przypisań ról i zarządzanie nimi](./security-create-manage-role-assignments.md#all).

### <a name="object-identifier-types"></a>Typy identyfikatorów obiektów

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Dowiedz się, jak przyznać uprawnienia do nazwy głównej usługi, odczytując [Tworzenie przypisań ról i zarządzanie nimi](./security-create-manage-role-assignments.md#grant).

W poniższych artykułach dokumentacji referencyjnej opisano:

- Jak [wykonać zapytanie lub identyfikator obiektu dla użytkownika](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Jak [uzyskać identyfikator obiektu dla jednostki usługi](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Jak [pobrać identyfikator obiektu dla dzierżawy usługi Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Przypisania ról

Przypisanie roli bliźniaczych reprezentacji Digital Azure kojarzy obiekt, taki jak użytkownik lub dzierżawa usługi Azure AD, z rolą i spacją. Uprawnienia są przyznawane wszystkim obiektom należącym do tego miejsca. Przestrzeń zawiera cały wykres przestrzenny poniżej.

Na przykład użytkownik otrzymuje przypisanie roli z rolą `DeviceInstaller` dla głównego węzła wykresu przestrzennego, który reprezentuje Kompilowanie. Użytkownik może następnie odczytywać i aktualizować urządzenia dla tego węzła oraz wszystkie inne miejsca podrzędne w budynku.

Aby udzielić uprawnień do adresata, Utwórz przypisanie roli. Aby odwołać uprawnienia, usuń przypisanie roli.

>[!IMPORTANT]
> Dowiedz się więcej o przypisaniach ról, odczytując informacje o [tworzeniu przypisań ról i zarządzaniu nimi](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat tworzenia przypisań ról bliźniaczych reprezentacji cyfrowych platformy Azure i zarządzania nimi, zobacz [Tworzenie przypisań ról i zarządzanie nimi](./security-create-manage-role-assignments.md).
