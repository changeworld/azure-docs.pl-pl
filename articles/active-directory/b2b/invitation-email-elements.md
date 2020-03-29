---
title: Elementy wiadomości e-mail z zaproszeniem B2B — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Szablon wiadomości e-mail z zaproszeniem do współpracy usługi Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f93586d46aa01116990f8f02f344c6952d3c1b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65768368"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementy wiadomości e-mail z zaproszeniem do współpracy B2B — usługa Azure Active Directory

Wiadomości e-mail z zaproszeniem są kluczowym składnikiem, który ma na celu wprowadzenie partnerów jako użytkowników współpracy B2B w usłudze Azure AD. Można ich użyć, aby zwiększyć zaufanie adresata. możesz dodać legitymację i dowód społeczny do wiadomości e-mail, aby upewnić się, że odbiorca czuje się komfortowo z wybraniem przycisku **Rozpocznij,** aby zaakceptować zaproszenie. To zaufanie jest kluczowym środkiem do zmniejszenia tarcia współdzielenia. A także chcesz, aby e-mail wyglądał świetnie!

![Zrzut ekranu przedstawiający wiadomość e-mail z zaproszeniem B2B](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Objaśnienie wiadomości e-mail
Przyjrzyjmy się kilku elementom wiadomości e-mail, aby wiedzieć, jak najlepiej korzystać z ich możliwości.

### <a name="subject"></a>Podmiot
Temat wiadomości e-mail jest zgodny z następującym &lt;wzorcem: Zostaniesz zaproszony do organizacji tenantname&gt;

### <a name="from-address"></a>Z adresu
Używamy wzorca podobnego do LinkedIn dla adresu Od.  Powinieneś być jasny, kto jest zaproszonym i z jakiej firmy, a także wyjaśnić, że wiadomość e-mail pochodzi z adresu e-mail firmy Microsoft. Format to: Zaproszenia <invites@microsoft.com> &lt;firmy Microsoft lub&gt; &lt;wyświetlana&gt; nazwa osoby <invites@microsoft.com>zapraszania z tenantname (za pośrednictwem firmy Microsoft).

### <a name="reply-to"></a>Odpowiedz na
Wiadomość e-mail z odpowiedzią jest ustawiona na adres e-mail osoby zapraszającej, gdy jest dostępna, dzięki czemu odpowiadanie na wiadomość e-mail odsyła wiadomość e-mail z powrotem do osoby zapraszającej.

### <a name="branding"></a>Znakowanie
Wiadomości e-mail z zaproszeniem od dzierżawy używają marki firmy, która może być skonfigurowana dla dzierżawy. Jeśli chcesz skorzystać z tej możliwości, [oto](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) szczegółowe informacje na temat konfigurowania go. Logo baneru pojawi się w wiadomości e-mail. Postępuj zgodnie z instrukcjami dotyczącymi rozmiaru i jakości [obrazu,](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) aby uzyskać najlepsze wyniki. Ponadto nazwa firmy pojawia się również w wezwaniu do działania.

### <a name="call-to-action"></a>Wezwanie do działania
Wezwanie do działania składa się z dwóch części: wyjaśnienia, dlaczego odbiorca otrzymał wiadomość i o co prosi się odbiorcę.
- Sekcję "dlaczego" można rozwiązać za pomocą następującego wzorca: Zostałeś &lt;zaproszony&gt; do dostępu do aplikacji w organizacji tenantname

- A sekcja "o to, o co cię proszono" jest wskazywana przez obecność przycisku **Rozpocznij.** Gdy odbiorca został dodany bez konieczności zapraszania, ten przycisk nie jest pokazywany.

### <a name="inviters-information"></a>Informacje o zaproszeniu
Nazwa wyświetlana osoby zapraszawej jest podłączona do wiadomości e-mail. Ponadto jeśli masz skonfigurowany obraz profilu dla konta usługi Azure AD, zapraszająca wiadomość e-mail również będzie zawierać ten obraz. Oba mają na celu zwiększenie zaufania odbiorcy do wiadomości e-mail.

Jeśli zdjęcie profilowe nie jest jeszcze skonfigurowane, zamiast obrazu zostanie wyświetlona ikona z inicjałami zaproszonego:

  ![Zrzut ekranu przedstawiający zaproszenie z wyświetlonymi inicjałami osoby zapraszania](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Treść
Treść zawiera komunikat, który osoba zapraszająca komponuje podczas [zapraszania użytkownika-gościa do katalogu, grupy lub aplikacji](add-users-administrator.md) lub [przy użyciu interfejsu API zaproszenia](customize-invitation-api.md). Jest to obszar tekstowy, więc nie przetwarza tagów HTML ze względów bezpieczeństwa.

  ![Zrzut ekranu przedstawiający treść wiadomości e-mail z zaproszeniem](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>Przekrój stopki
Stopka zawiera markę firmy Microsoft i informuje odbiorcę, czy wiadomość e-mail została wysłana z niemonitorowanego aliasu. 

Przypadki szczególne:

- Osoba zapraszająca nie ma adresu e-mail w zaproszeniu do najmu

  ![Zrzut ekranu, gdy osoba zapraszająca nie ma wiadomości e-mail w zaproszeniu do najmu](media/invitation-email-elements/inviter-no-email.png)


- Odbiorca nie musi realizować zaproszenia

  ![Zrzut ekranu, gdy odbiorca nie musi realizować zaproszenia](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>Jak określa się język
Język przedstawiony użytkownikowi-gościowi w wiadomości e-mail z zaproszeniem zależy od następujących ustawień. Te ustawienia są wyświetlane w kolejności pierwszeństwa. Jeśli ustawienie nie jest skonfigurowane, następne ustawienie na liście określa język. 
- **MessageLanguage** właściwość [zaproszonegouserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) obiektu, jeśli create invitation API jest używany
-   Właściwość **preferredLanguage** określona w [obiekcie użytkownika](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) gościa
-   **Język powiadomień** ustawiony we właściwościach dzierżawy domowej użytkownika-gościa (tylko dla dzierżaw usługi Azure AD)
-   **Język powiadomień** ustawiony we właściwościach dzierżawy zasobów

Jeśli żadne z tych ustawień nie jest skonfigurowane, domyślnie język jest domyślny na angielski (US).

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły dotyczące współpracy usługi Azure AD B2B:

- [Co to jest współpraca usługi Azure AD B2B](what-is-b2b.md)
- [Jak administratorzy usługi Azure Active Directory dodawać użytkowników współpracy B2B?](add-users-administrator.md)
- [W jaki sposób pracownicy informacji dodają użytkowników współpracy B2B?](add-users-information-worker.md)
- [Wykup zaproszenia do współpracy B2B](redemption-experience.md)
- [Dodawanie użytkowników współpracy B2B bez zaproszenia](add-user-without-invite.md)
