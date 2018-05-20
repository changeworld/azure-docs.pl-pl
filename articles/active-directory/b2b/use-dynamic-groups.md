---
title: Grupami dynamicznymi i współpracy usługi Azure Active Directory B2B | Dokumentacja firmy Microsoft
description: Pokazuje, jak dynamiczna grup usługi Azure AD za pomocą usługi Azure Active Directory B2B współpracy
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 12/14/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 18057b71415bea5d5f029db6fe311f76c1a549a1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupami dynamicznymi i współpracy usługi Azure Active Directory B2B

## <a name="what-are-dynamic-groups"></a>Co to są grupami dynamicznymi?
Dynamicznej konfiguracji przynależności do grupy zabezpieczeń usługi Azure Active Directory (Azure AD) jest dostępna w [portalu Azure](https://portal.azure.com). Administratorzy mogą ustawić reguły do wypełniania grupy, które są tworzone w usłudze Azure AD na podstawie atrybutów użytkownika (na przykład userType, działu lub kraju). Automatycznie dodawane do elementów członkowskich lub usunięte z grupy zabezpieczeń na podstawie ich atrybutów. Te grupy mogą umożliwiać dostęp do aplikacji lub zasobów w chmurze (witryny programu SharePoint, dokumenty) i przypisywanie licencji do elementów członkowskich. Przeczytaj więcej na temat grup dynamicznych w [grup w usłudze Azure Active Directory w wersji dedykowanej](../active-directory-accessmanagement-dedicated-groups.md).

Odpowiednie [licencjonowania usługi Azure AD Premium P1 lub P2](https://azure.microsoft.com/pricing/details/active-directory/) jest wymagany do tworzenia i używania grup dynamicznych. Dowiedz się więcej informacji, zobacz artykuł [tworzenia reguł opartych na atrybutach dynamiczne członkostwo w grupie w usłudze Azure Active Directory](../active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Co to są wbudowane grupy dynamiczne?
**Wszyscy użytkownicy** Dynamiczna grupa umożliwia administratorom dzierżawy utworzenie grupy zawierającej wszystkich użytkowników w dzierżawie za pomocą jednego kliknięcia. Domyślnie **wszyscy użytkownicy** grupy należą wszyscy użytkownicy w katalogu, w tym elementów członkowskich i gości.
W ramach nowego portalu administracyjnego usługi Azure Active Directory, możesz włączyć **wszyscy użytkownicy** w widoku ustawienia grupy.

![Pokazuje włączyć ustawioną wartość Yes grupy Wszyscy użytkownicy](media/use-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Ograniczenie funkcjonalności wszystkich Dynamiczna grupa użytkowników
Domyślnie **wszyscy użytkownicy** grupa zawiera również użytkowników (Gość) współpracy B2B. Można dodatkowo zabezpieczyć Twoje **wszyscy użytkownicy** grupy przy użyciu reguły, aby usunąć gości. Na poniższej ilustracji pokazano **wszyscy użytkownicy** grupy zmodyfikowane w celu wykluczenia gości.

![Pokazuje reguły, gdzie typ użytkownika nie jest równa gościa](media/use-dynamic-groups/exclude-guest-users.png)

Użytkownik może być również bardzo przydatne do tworzenia nowej grupy dynamicznej, zawierający tylko gości, dzięki czemu można zastosować zasad (np. zasady dostępu warunkowego AD Azure) do nich.
Jakie takiej grupy może wyglądać tak:

![Pokazuje reguły, gdzie typ użytkownika jest równe gościa](media/use-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Kolejne kroki

- [Właściwości użytkownika współpracy B2B](user-properties.md)
- [Dodawanie do roli użytkownika współpracy B2B](add-guest-to-role.md)
- [Dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md)

