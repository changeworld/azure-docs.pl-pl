---
title: Opis kontroli dostępu opartej na rolach — usługi Azure Digital Twins | Dokumenty firmy Microsoft
description: Dowiedz się więcej o kontroli dostępu opartej na rolach i zarządzaniu uprawnieniami w usłudze Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: feda4b3a7f21b581fb4f08aec013f87c0fabb7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044940"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Kontrola dostępu oparta na rolach w programie Azure Digital Twins

Usługa Azure Digital Twins umożliwia precyzyjną kontrolę dostępu nad określonymi danymi, zasobami i akcjami na wykresie przestrzennym. Czyni to za pomocą szczegółowej roli i zarządzania uprawnieniami o nazwie [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC). RBAC składa się z _ról_ i _przypisań ról_. Role identyfikują poziom uprawnień. Przypisania ról kojarzą rolę z użytkownikiem lub urządzeniem.

Za pomocą funkcji RBAC można udzielić uprawnień:

- Użytkownik.
- Urządzenie.
- Podmiot usługi.
- Funkcja zdefiniowana przez użytkownika.
- Wszyscy użytkownicy należący do domeny.
- Dzierżawca.

Stopień dostępu można również dostosować.

RBAC jest unikatowy, ponieważ uprawnienia są dziedziczone w dół wykresu przestrzennego.

## <a name="what-can-i-do-with-rbac"></a>Co można zrobić za pomocą kontroli dostępu opartej na rolach?

Deweloper może użyć RBAC do:

- Zapewnij użytkownikowi możliwość zarządzania urządzeniami dla całego budynku lub tylko dla określonego pomieszczenia lub podłogi.
- Udziel administratorowi globalnego dostępu do wszystkich węzłów wykresu przestrzennego dla całego wykresu lub tylko dla sekcji wykresu.
- Udziel pomocy technicznej specjalisty dostępu do odczytu wykresu, z wyjątkiem kluczy dostępu.
- Udziel każdemu członkowi domeny dostępu do odczytu do wszystkich obiektów wykresu.

## <a name="rbac-best-practices"></a>Najlepsze praktyki RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Role

### <a name="role-definitions"></a>Definicje ról

Definicja roli jest zbiorem uprawnień i innych atrybutów, które stanowią rolę. Definicja roli zawiera listę dozwolonych operacji, które obejmują *CREATE*, *READ*, *UPDATE*i *DELETE,* który może wykonać dowolny obiekt z tą rolą. Określa również, do których typów obiektów mają zastosowanie uprawnienia.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Aby pobrać pełne definicje dla poprzednich ról, kwerendy interfejsu API systemu/ról.
> Dowiedz się więcej, [czytając: Tworzenie przypisań ról i zarządzanie nimi](./security-create-manage-role-assignments.md#retrieve-all-roles).

### <a name="object-identifier-types"></a>Typy identyfikatorów obiektów

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Dowiedz się, jak udzielić uprawnień podmiotowi usługi, czytając pozycję [Tworzenie przypisań ról i zarządzanie nimi.](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal)

W następujących dokumentach referencyjnych opisano:

- Jak [zapytać lub identyfikator obiektu dla użytkownika](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Jak [uzyskać identyfikator obiektu dla jednostki usługi](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Jak [pobrać identyfikator obiektu dla dzierżawy usługi Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Przypisania ról

Przypisanie roli usługi Azure Digital Twins kojarzy obiekt, taki jak użytkownik lub dzierżawa usługi Azure AD, z rolą i miejscem. Uprawnienia są przyznawane wszystkim obiektom, które należą do tego miejsca. Przestrzeń zawiera cały wykres przestrzenny pod nim.

Na przykład użytkownik otrzymuje przypisanie roli `DeviceInstaller` z rolą węzła głównego wykresu przestrzennego, który reprezentuje budynek. Użytkownik może następnie odczytywać i aktualizować urządzenia dla tego węzła i wszystkich innych przestrzeni podrzędnych w budynku.

Aby udzielić uprawnień adresatowi, utwórz przypisanie roli. Aby odwołać uprawnienia, usuń przypisanie roli.

>[!IMPORTANT]
> Dowiedz się więcej o przydziałach ról, czytając opis [Tworzenie przypisań ról i zarządzanie nimi](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o tworzeniu przypisań ról usługi Azure Digital Twins i zarządzaniu nimi, przeczytaj artykuł [Tworzenie przypisań ról i zarządzanie nimi](./security-create-manage-role-assignments.md).

- Dowiedz się więcej o [RBAC for Azure](https://docs.microsoft.com/azure/role-based-access-control/).
