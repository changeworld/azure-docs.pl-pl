---
title: Elementy e-mail z zaproszeniem współpracy B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Szablon wiadomości e-mail z zaproszeniem współpracy platformy Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: a119e2797c4771551b6087e148d609c68ffd36db
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977531"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementy e-mail z zaproszeniem współpracy B2B — usługi Azure Active Directory

Wiadomości e-mail z zaproszeniem są kluczowym aspektem doprowadzić partnerów na pokładzie użytkowników we współpracy B2B w usłudze Azure AD. Można użyć je, aby zwiększyć zaufanie adresata. Możesz dodawać legalności i sprawdzone rozwiązanie do poczty e-mail, aby upewnić się, że odbiorca czuje doświadczenia z wybraniu **wprowadzenie** przycisk, aby zaakceptować zaproszenie. Jest to zaufanie klucza oznacza, że wysyłaj do udostępniania. I chcesz również wprowadzić adres e-mail wyglądał świetnie!

![Poczty e-mail z zaproszeniem usługi Azure AD B2b](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Wyjaśniające, wiadomości e-mail
Spójrzmy na kilka elementów w wiadomości e-mail, aby było wiadomo, jak najlepiej wykorzystać jego możliwości.

### <a name="subject"></a>Podmiot
Temat wiadomości e-mail jest zgodny ze wzorcem następujące: Zaproszono Cię do &lt;tenantname&gt; organizacji

### <a name="from-address"></a>Adres nadawcy
Firma Microsoft na użytek wzorzec LinkedIn przypominającej adres nadawcy.  Powinien być niejasne, zapraszającej będącego i adres e-mail od firmy oraz wyjaśnienie, że wiadomość e-mail pochodzi od firmy Microsoft. Format jest następujący: &lt;Nazwa wyświetlana osoby zapraszającej&gt; z &lt;tenantname&gt; (za pośrednictwem firmy Microsoft) <invites@microsoft.com>

### <a name="reply-to"></a>Odpowiedz
Odpowiedz do wiadomości e-mail jest równa zapraszającej poczty e-mail, jeśli są dostępne, tak, aby odpowiadanie na wiadomości e-mail wysyła wiadomość e-mail do osoby zapraszającej.

### <a name="branding"></a>Znakowanie
Wiadomości e-mail z zaproszeniem, w wyniku używania dzierżawy firmowe, można zdefiniować dla Twojej dzierżawy. Jeśli chcesz móc korzystać z tej możliwości [tutaj](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) znajdują się szczegółowe informacje o sposobie konfigurowania go. Baner logo jest wyświetlany w wiadomości e-mail. Postępuj zgodnie z rozmiar obrazu i instrukcje dotyczące jakości [tutaj](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) uzyskać jak najlepsze rezultaty. Ponadto nazwa firmy również pojawia się w wywołaniu akcji.

### <a name="call-to-action"></a>Wywołanie akcji
Wezwanie do działania składa się z dwóch części: wyjaśniający, Dlaczego adresat otrzyma wiadomość e-mail, a co adresat jest pytany, rozwiązanie.
- W sekcji "Dlaczego" może zostać zlikwidowane przy użyciu następującego wzorca: Zaproszono Cię do uzyskania dostępu do aplikacji w &lt;tenantname&gt; organizacji

- I "co to jest monicie o czy" sekcji jest oznaczany przez obecność **wprowadzenie** przycisku. Gdy adresat została dodana bez konieczności zaproszenia, ten przycisk nie jest wyświetlane.

### <a name="inviters-information"></a>Informacji przez osoby zapraszającej
Nazwa wyświetlana osoby zapraszającej jest dołączony do wiadomości e-mail. A dodatkowo, jeśli skonfigurowano zdjęcie profilowe dla konta usługi Azure AD, zapraszający wiadomość e-mail będzie zawierać także tego obrazu. Oba są przeznaczone do zwiększenia ufności z odbiorców w wiadomości e-mail.

W przypadku swoje zdjęcie profilowe nie został jeszcze skonfigurowany, jest wyświetlana ikona inicjały zapraszającej zamiast obrazu:

  ![Wyświetlanie inicjały zapraszającej](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Treść
Treść zawiera komunikat zapraszającej Redaguj, kiedy [zaproszeniem użytkownika gościa do katalogu, grupa lub aplikacja](add-users-administrator.md) lub [przy użyciu interfejsu API zaproszenia](customize-invitation-api.md). Jest obszar tekstu, więc nie przetwarza tagów HTML ze względów bezpieczeństwa.

### <a name="footer-section"></a>Sekcji stopki
Stopka zawiera marki firmy Microsoft i umożliwia odbiorcy znać, jeśli wiadomość e-mail została wysłana z niemonitorowanego aliasu. Specjalne przypadki:

- Osoba zapraszająca nie ma adresu e-mail w zapraszający dzierżawy

  ![Obraz osoba zapraszająca nie ma adresu e-mail w zapraszający dzierżawy](media/invitation-email-elements/inviter-no-email.png)


- Odbiorca nie ma konieczności zrealizować zaproszenia

  ![Kiedy adresat nie ma konieczności zrealizować zaproszenia](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>Jak język jest określana.
Język prezentowane użytkownikowi Gość w wiadomości e-mail z zaproszeniem jest określany przez następujące ustawienia. Te ustawienia są wymienione w kolejności priorytetu. Jeśli to ustawienie nie zostanie skonfigurowane, następne ustawienie na liście określa język. 
- **MessageLanguage** właściwość [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) obiektu, jeśli używana jest zaproszenia do tworzenia interfejsu API
-   **PreferredLanguage** właściwości określonej w przez gościa [obiektu użytkownika](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)
-   **Język powiadomienia** ustaw we właściwościach użytkownika gościa dzierżawy głównej (dla tylko dzierżaw usługi Azure AD)
-   **Język powiadomienia** ustaw we właściwościach zasobu dzierżawy

Jeśli żadne z tych ustawień są skonfigurowane, wartości domyślne języka angielskiego (US).

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

- [Czym jest współpraca B2B w usłudze Azure AD](what-is-b2b.md)
- [Jak Administratorzy usługi Azure Active Directory dodać użytkowników we współpracy B2B?](add-users-administrator.md)
- [Jak pracownicy przetwarzający informacje mogą dodać użytkowników we współpracy B2B?](add-users-information-worker.md)
- [Realizacja zaproszenia współpracy B2B](redemption-experience.md)
- [Dodają użytkowników we współpracy B2B bez zaproszenia](add-user-without-invite.md)
