---
title: Udostępnianie i zgoda usługi LinkedIn — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Wyjaśniono, jak integracja z serwisem LinkedIn udostępnia dane za pośrednictwem aplikacji firmy Microsoft w usłudze Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a21c986ccfe96bae5d341e0ba2ee6622d998d076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025392"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>LinkedIn połączenia konta udostępnianie danych i zgody

Można włączyć użytkowników w organizacji usługi Active Directory (Azure AD) do zgody na połączenie ich konta służbowego firmy Microsoft z ich konta LinkedIn. Gdy użytkownik połączy swoje konta, informacje i najważniejsze informacje z LinkedIn są dostępne w niektórych aplikacjach i usługach firmy Microsoft. Użytkownicy mogą również oczekiwać, że ich doświadczenie w sieci na LinkedIn zostanie ulepszone i wzbogacone o informacje od firmy Microsoft.

Aby wyświetlić informacje o linkedin w aplikacjach i usługach firmy Microsoft, użytkownicy muszą wyrazić zgodę na łączenie własnych kont Microsoft i LinkedIn. Użytkownicy są monitowani o połączenie swoich kont przy pierwszym kliknięciu, aby wyświetlić informacje o linkedin innej osoby na karcie profilu w programie Outlook, usłudze OneDrive lub usłudze SharePoint Online. Połączenia kont LinkedIn nie są w pełni włączone dla użytkowników, dopóki nie zgodzą się na środowisko i nie połączą swoich kont.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Korzyści z udostępniania informacji linkedin

Dostęp do informacji linkedin w aplikacjach i usługach firmy Microsoft ułatwia użytkownikom łączenie się, angażowanie się i budowanie profesjonalnych relacji ze współpracownikami, klientami i partnerami w organizacji i poza nią. Nowi użytkownicy mogą szybciej się dotrzeć, kontaktując się ze współpracownikami, dowiadując się więcej o nich i łatwo uzyskując dostęp do większej ilości informacji. Oto przykład sposobu, w jaki informacje linkedin pojawiają się na karcie profilu w aplikacjach firmy Microsoft:

![Włączanie integracji z serwisem LinkedIn w organizacji](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Włączanie i ogłaszanie integracji z LinkedIn

Aby zarządzać ustawieniami organizacji, musisz być administratorem usługi Azure Active Directory. Można go włączyć dla wszystkich użytkowników lub dla określonego zestawu użytkowników.

1. Aby włączyć lub wyłączyć integrację, wykonaj kroki opisane w [obszarze Zgoda na integrację z usługą LinkedIn dla organizacji usługi Azure AD](linkedin-integration.md).
2. Po ogłoszeniu integracji z serwisem LinkedIn w organizacji należy wskazać użytkownikom często zadawane pytania dotyczące informacji o [linkedin w aplikacjach i usługach firmy Microsoft.](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381) Ten artykuł zawiera informacje o tym, gdzie są wyświetlane informacje o linkedin, jak łączyć konta i nie tylko.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Zgoda użytkownika na dostęp do danych w firmach Microsoft i LinkedIn

Dane dostępne z serwisu LinkedIn nie są przechowywane na stałe w usługach firmy Microsoft. Dane, do których dostęp ma firma Microsoft, nie są przechowywane na stałe w usłudze LinkedIn, z wyjątkiem kontaktów. Kontakty firmy Microsoft są przechowywane na LinkedIn, dopóki użytkownicy ich nie usuną, zgodnie z opisem [w usuwaniu importowanych kontaktów z LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Gdy użytkownicy łączą swoje konta, informacje i szczegółowe informacje z LinkedIn są dostępne w niektórych aplikacjach firmy Microsoft, takich jak karta profilu. Użytkownicy mogą również oczekiwać, że ich doświadczenie w sieci na LinkedIn zostanie ulepszone i wzbogacone o informacje od firmy Microsoft.
Gdy użytkownicy w organizacji łączą swoje konta w serwisie LinkedIn i Microsoft w szkole, mają dwie opcje:

* Uadź uprawnienia dostępu do danych z obu kont. Oznacza to, że udzielają one uprawnień do uzyskiwania dostępu do danych z konta LinkedIn lub konta Microsoft oraz do konta LinkedIn w [celu uzyskania dostępu do danych z konta służbowego firmy Microsoft.](https://www.linkedin.com/help/linkedin/answer/84077)
* Zezwalaj na dostęp tylko do danych linkedin przez ich konto służbowe i szkolne firmy Microsoft.

Użytkownicy mogą rozłączać konta i usuwać uprawnienia dostępu do danych w dowolnym momencie, a [użytkownicy mogą kontrolować sposób wyświetlania własnego profilu LinkedIn,](https://www.linkedin.com/help/linkedin/answer/83)w tym, czy ich profil może być oglądany w aplikacjach firmy Microsoft.

### <a name="linkedin-account-data"></a>Dane konta LinkedIn

Po połączeniu kont Microsoft i LinkedIn użytkownik zezwala serwisowi LinkedIn na dostarczanie firmie Microsoft następujących danych:

* Dane profilu — obejmują tożsamość LinkedIn, informacje kontaktowe i informacje udostępniane innym osobom w [profilu LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Dane zainteresowań — obejmują zainteresowania w serwisie LinkedIn, takie jak osoby i tematy, które obserwujesz, grupy kursów oraz treści, które lubisz i udostępniasz.
* Dane subskrypcji — obejmują subskrypcje aplikacji i usług LinkedIn wraz ze skojarzonymi danymi. 
* Dane połączeń — obejmują [sieć LinkedIn,](https://www.linkedin.com/help/linkedin/answer/110) w tym profile i informacje kontaktowe połączeń pierwszego stopnia.

Dane dostępne z serwisu LinkedIn nie są przechowywane na stałe w usługach firmy Microsoft. Aby uzyskać więcej informacji na temat wykorzystywania danych osobowych przez firmę Microsoft, zobacz [Zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Dane konta służbowego firmy Microsoft

Po połączeniu kont Microsoft i LinkedIn użytkownik zezwala firmie Microsoft na dostarczanie następujących danych do usługi LinkedIn:

* Dane profilu — obejmują informacje, takie jak imię, nazwisko, zdjęcie profilowe, adres e-mail, menedżer i osoby, którymi zarządzasz.
* Dane kalendarza — obejmują spotkania w kalendarzach, ich godziny, lokalizacje i informacje kontaktowe uczestników. Informacje o spotkaniu, takie jak plan, zawartość lub tytuł spotkania, nie są uwzględniane w danych kalendarza.
* Dane o zainteresowaniach — obejmują zainteresowania związane z kontem użytkownika na podstawie korzystania przez użytkownika z usług firmy Microsoft, takich jak Cortana i Bing dla firm.
* Dane subskrypcji — obejmują subskrypcje dostarczane przez organizację do aplikacji i usług firmy Microsoft, takich jak Office 365.
* Dane kontaktów — obejmują listy kontaktów w programie Outlook, Skype i innych usługach konta Microsoft, w tym informacje kontaktowe dla osób, z którymi często się komunikujesz lub z którymi współpracujesz. Kontakty będą okresowo importowane, przechowywane i używane przez serwis LinkedIn, na przykład w celu sugerowania połączeń, organizowania kontaktów i pokazywania aktualizacji kontaktów.

Dane, do których dostęp ma firma Microsoft, nie są przechowywane na stałe w usłudze LinkedIn, z wyjątkiem kontaktów. Kontakty firmy Microsoft są przechowywane na LinkedIn, dopóki użytkownicy ich nie usuną. Dowiedz się więcej o [usuwaniu zaimportowanych kontaktów z serwisu LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Aby uzyskać więcej informacji na temat wykorzystywania danych osobowych przez LinkedIn, zobacz [Politykę prywatności LinkedIn](https://www.linkedin.com/legal/privacy-policy). W przypadku usług LinkedIn, transferu i przechowywania danych dane mogą przepływać z Unii Europejskiej do Stanów Zjednoczonych i z powrotem, a twoja prywatność jest chroniona zgodnie z opisem w [przekazywaniu danych w Unii Europejskiej.](https://www.linkedin.com/help/linkedin/answer/62533)

## <a name="next-steps"></a>Następne kroki

* [LinkedIn w aplikacjach firmy Microsoft z kontem służbowym](https://www.linkedin.com/help/linkedin/answer/84077)
