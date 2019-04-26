---
title: Konta LinkedIn, udostępnianie danych połączeń i zgody — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Wyjaśniono sposób integracji z usługą LinkedIn udostępnia dane za pośrednictwem aplikacji firmy Microsoft w usłudze Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e6e24f4e726e25ae65ecceaeb161f8e16d61721
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60469652"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>Udostępnianie danych połączenia konta usługi LinkedIn i zgody

Możesz umożliwić użytkownikom w organizacji usługi Active Directory (Azure AD), aby wyrazić zgodę na połączyć swoją pracę Microsoft konta służbowego przy użyciu swojego konta usługi LinkedIn. Po użytkownik łączy swoje konta, informacje i najważniejsze informacje z usługi LinkedIn są dostępne w niektórych aplikacjach firmy Microsoft i usługach. Użytkownicy mogą również oczekiwać środowiska sieciowego w serwisie LinkedIn udoskonalany i wzbogacone o informacje od firmy Microsoft.

Aby wyświetlić informacje LinkedIn z usługami i aplikacjami firmy Microsoft, użytkownicy muszą wyrazić zgodę, do łączenia z własnymi kontami Microsoft i LinkedIn. Użytkownicy są monitowani o łączenie ich kont, gdy są one kliknij, aby zobaczyć informacje LinkedIn innego użytkownika na karcie profil w programie Outlook, OneDrive lub SharePoint Online po raz pierwszy. Połączenia konta usługi LinkedIn nie są w pełni włączone dla użytkowników, dopóki nie mogą wyrazić zgodę, do środowiska oraz łączenie ich kont.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Zalety udostępniania informacji usługi LinkedIn

Dostęp do informacji usługi LinkedIn w ramach usług i aplikacji firmy Microsoft ułatwia użytkownikom łączenie, zaangażowania i utworzyć relacje profesjonalnych współpracownikom, klientom i partnerom wewnątrz organizacji i poza nią. Nowi użytkownicy mogą szybciej się wszystkiego, łączenie ze współpracownikami, więcej informacji na temat ich i łatwe uzyskiwanie dostępu do informacji. Poniżej przedstawiono przykładowy sposób LinkedIn informacje są wyświetlane na karcie profil w aplikacjach firmy Microsoft:

![Włączanie integracji usługi LinkedIn w Twojej organizacji](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Włącz i poinformować o integracji z usługą LinkedIn

Musi być administratora Azure Active Directory do zarządzania ustawienie dla Twojej organizacji. Można go włączyć dla wszystkich użytkowników lub dla określonych użytkowników.

1. Aby włączyć lub wyłączyć integrację, wykonaj kroki opisane w [wyrazić zgodę na integracji z usługą LinkedIn dla organizacji usługi Azure AD](linkedin-integration.md).
2. Podczas integracji z usługą LinkedIn anonsowania w Twojej organizacji, wskaż użytkowników często zadawane pytania dotyczące [LinkedIn informacji w usługach i aplikacjach firmy Microsoft](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). Artykuł zawiera informacje o tym, gdzie LinkedIn informacji pojawia się, jak połączyć konta i nie tylko.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Użytkownik wyrazić zgodę na dostęp do danych w Microsoft i LinkedIn

Danych, który jest dostępny z LinkedIn nie są trwale przechowywane w usługach firmy Microsoft. Dane używane przez firmę Microsoft nie są przechowywane trwale serwisowi LinkedIn, z wyjątkiem kontaktów. Contacts firmy Microsoft są przechowywane w serwisie LinkedIn dopóki użytkownicy ich usunięcia, zgodnie z opisem w [usuwanie importowanych kontakty z usługi LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Gdy użytkownicy łączą się z ich konta, informacje i szczegółowe informacje z usługi LinkedIn są dostępne w niektórych aplikacjach firmy Microsoft, takich jak karty profilu. Użytkownicy mogą również oczekiwać środowiska sieciowego w serwisie LinkedIn udoskonalany i wzbogacone o informacje od firmy Microsoft.
Gdy użytkownicy w Twojej organizacji połączyć swoją pracę LinkedIn i firmy Microsoft lub kont służbowych, są dostępne dwie opcje:

* Nadaj uprawnienia dla danych, które były dostępne z obu kont. Oznacza to, przyznać uprawnienia dla ich firmy Microsoft lub konta dostępu do danych ze swojego konta usługi LinkedIn i dla służbowego [swoje konto LinkedIn, aby uzyskać dostęp do danych z pracą firmy Microsoft lub konta służbowego](https://www.linkedin.com/help/linkedin/answer/84077).
* Nadawanie uprawnień dla danych w usługi LinkedIn dostęp przez ich firmę Microsoft do pracy i konta służbowego.

Użytkownicy mogą odłączyć konta i usunąć uprawnienia dostępu do danych w dowolnym momencie i [użytkownicy mogą kontrolować sposób wyświetlania własnego profilu usługi LinkedIn](https://www.linkedin.com/help/linkedin/answer/83), łącznie z tego, czy można wyświetlić swój profil w aplikacjach firmy Microsoft.

### <a name="linkedin-account-data"></a>Dane konta LinkedIn

Gdy łączysz konta Microsoft i LinkedIn, zezwalasz usłudze LinkedIn na zawiera następujące dane do firmy Microsoft:

* Profil danych — obejmują tożsamość w usłudze LinkedIn, informacje kontaktowe i informacje, możesz udostępnić innym osobom w swojej [profilu usługi LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Interesujące dane — zawiera zainteresowania w serwisie LinkedIn, takie jak osób i tematów, wykonaj grup kursów i zawartości możesz, takich jak i udostępnianie.
* Dane subskrypcji — obejmują subskrypcje usługi LinkedIn aplikacji i usług oraz powiązane dane. 
* Zawiera połączenia danych — Twoje [sieć usługi LinkedIn](https://www.linkedin.com/help/linkedin/answer/110) tym profile i dane kontaktowe połączeń stopień 1 dnia.

Danych, który jest dostępny z LinkedIn nie są trwale przechowywane w usługach firmy Microsoft. Aby uzyskać więcej informacji dotyczących użycia do firmy Microsoft dane osobowe, zobacz [zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Służbowego Microsoft dane konta

Gdy łączysz konta Microsoft i LinkedIn, zezwolisz firmy Microsoft, aby zapewnić następujące dane w serwisie LinkedIn:

* Profil danych — obejmują informacje, takie jak swoje imię, ostatni nazwy, zdjęcie profilowe, adres e-mail, Menedżera i osoby, którym zarządzasz.
* Data w kalendarzu — obejmuje spotkania w Twoich kalendarzach, ich czasy, lokalizacji i informacje kontaktowe uczestników. Informacje o spotkaniu, takich jak planu, zawartości lub tytułu spotkania nie znajduje się w danych kalendarza.
* Interesujące danych — obejmują zainteresowania skojarzonych z Twoim kontem, oparte na korzystanie z usług firmy Microsoft, takich jak Cortana i Bing dla firm.
* Dane subskrypcji — obejmuje udostępnioną przez Twoją organizację do aplikacji firmy Microsoft oraz usług, takich jak Office 365.
* Kontaktuje się dane — zawiera listy kontaktów w programie Outlook, Skype i innych usługach kont Microsoft, w tym informacje kontaktowe dla osób, które często komunikują się lub współpracuje. Kontakty będą okresowo importowanych przechowywane i używane przez usługi LinkedIn, na przykład zasugerować połączeń pomaga organizować kontakty i Pokaż aktualizacje dotyczące kontaktów.

Dane używane przez firmę Microsoft nie są przechowywane trwale serwisowi LinkedIn, z wyjątkiem kontaktów. Contacts firmy Microsoft są przechowywane w serwisie LinkedIn, dopóki użytkownicy je usunąć. Dowiedz się więcej o [usuwanie importowanych kontakty z usługi LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Aby uzyskać więcej informacji przy użyciu usługi LinkedIn firmy danych osobowych, zobacz [zasady ochrony prywatności usługi LinkedIn](https://www.linkedin.com/legal/privacy-policy). Usługi, transferu danych i magazynu usługi LinkedIn mogą przepływ danych pochodzących z Unii Europejskiej do Stanów Zjednoczonych i z powrotem i prywatność użytkowników jest chroniona zgodnie z opisem w [transfery danych w Unii Europejskiej](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Kolejne kroki

* [Usługi LinkedIn w aplikacjach firmy Microsoft przy użyciu konta służbowego](https://www.linkedin.com/help/linkedin/answer/84077)
