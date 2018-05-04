---
title: Grupami dynamicznymi i współpracy usługi Azure Active Directory B2B | Dokumentacja firmy Microsoft
description: Pokazuje, jak dynamiczna grup usługi Azure AD za pomocą usługi Azure Active Directory B2B współpracy
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 12/14/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: d96fefb859cba5db65382801fb1ac143df12b647
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2018
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupami dynamicznymi i współpracy usługi Azure Active Directory B2B

## <a name="what-are-dynamic-groups"></a>Co to są grupami dynamicznymi?
Dynamicznej konfiguracji przynależności do grupy zabezpieczeń usługi Azure Active Directory (Azure AD) jest dostępna w [portalu Azure](https://portal.azure.com). Administratorzy mogą ustawić reguły do wypełniania grupy, które są tworzone w usłudze Azure AD na podstawie atrybutów użytkownika (na przykład userType, działu lub kraju). Automatycznie dodawane do elementów członkowskich lub usunięte z grupy zabezpieczeń na podstawie ich atrybutów. Te grupy mogą umożliwiać dostęp do aplikacji lub zasobów w chmurze (witryny programu SharePoint, dokumenty) i przypisywanie licencji do elementów członkowskich. Przeczytaj więcej na temat grup dynamicznych w [grup w usłudze Azure Active Directory w wersji dedykowanej](active-directory-accessmanagement-dedicated-groups.md).

Odpowiednie [licencjonowania usługi Azure AD Premium P1 lub P2](https://azure.microsoft.com/pricing/details/active-directory/) jest wymagany do tworzenia i używania grup dynamicznych. Dowiedz się więcej informacji, zobacz artykuł [tworzenia reguł opartych na atrybutach dynamiczne członkostwo w grupie w usłudze Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Co to są wbudowane grupy dynamiczne?
**Wszyscy użytkownicy** Dynamiczna grupa umożliwia administratorom dzierżawy utworzenie grupy zawierającej wszystkich użytkowników w dzierżawie za pomocą jednego kliknięcia. Domyślnie **wszyscy użytkownicy** grupy należą wszyscy użytkownicy w katalogu, w tym elementów członkowskich i gości.
W ramach nowego portalu administracyjnego usługi Azure Active Directory, możesz włączyć **wszyscy użytkownicy** w widoku ustawienia grupy.

![Pokazuje włączyć ustawioną wartość Yes grupy Wszyscy użytkownicy](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Ograniczenie funkcjonalności wszystkich Dynamiczna grupa użytkowników
Domyślnie **wszyscy użytkownicy** grupa zawiera również użytkowników (Gość) współpracy B2B. Można dodatkowo zabezpieczyć Twoje **wszyscy użytkownicy** grupy przy użyciu reguły, aby usunąć gości. Na poniższej ilustracji pokazano **wszyscy użytkownicy** grupy zmodyfikowane w celu wykluczenia gości.

![Pokazuje reguły, gdzie typ użytkownika nie jest równa gościa](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

Użytkownik może być również bardzo przydatne do tworzenia nowej grupy dynamicznej, zawierający tylko gości, dzięki czemu można zastosować zasad (np. zasady dostępu warunkowego AD Azure) do nich.
Jakie takiej grupy może wyglądać tak:

![Pokazuje reguły, gdzie typ użytkownika jest równe gościa](media/active-directory-b2b-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Kolejne kroki

Zobacz nasze inne artykuły dotyczące współpracy B2B w usłudze Azure AD:

* [Czym jest współpraca B2B w usłudze Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Właściwości użytkownika współpracy B2B](active-directory-b2b-user-properties.md)
* [Dodawanie do roli użytkownika współpracy B2B](active-directory-b2b-add-guest-to-role.md)
* [Delegowanie zaproszenia współpracy B2B](active-directory-b2b-delegate-invitations.md)
* [Kod współpracy B2B i przykłady środowiska PowerShell](active-directory-b2b-code-samples.md)
* [Konfigurowanie aplikacji SaaS do współpracy B2B](active-directory-b2b-configure-saas-apps.md)

