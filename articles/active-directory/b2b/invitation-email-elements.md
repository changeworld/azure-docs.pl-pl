---
title: Elementy współpracy B2B zaproszenie — Azure Active Directory | Dokumentacja firmy Microsoft
description: Azure Active Directory B2B współpracy zaproszenia szablon wiadomości e-mail
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: e8285779154914bd09513c057d8e5ae0b6388831
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260133"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementy współpracy B2B zaproszenie — Azure Active Directory

Wiadomości e-mail z zaproszeniem jest składnikiem krytycznym, aby przenieść do partnerów na statku jako użytkownicy współpracy B2B w usłudze Azure AD. Można używać ich, aby zwiększyć zaufanie adresata. Umożliwia dodanie legalności i doświadczenia z zaznaczenie tak społecznościowych dowód do wiadomości e-mail, aby upewnić się, że odbiorca **wprowadzenie** przycisk o zaakceptowanie zaproszenia. Zaufanie jest klucza oznacza, że zmniejszyć tarcia udostępniania. A także ma być e-mail wygląda świetnie!

![Azure wiadomość e-mail z zaproszeniem AD B2b](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Wyjaśniający wiadomości e-mail
Oto kilka elementów wiadomości e-mail, aby wiedzieć, jak najlepiej używać ich funkcji.

### <a name="subject"></a>Temat
Temat wiadomości e-mail jest zgodny ze wzorcem następujące: Zapraszamy &lt;tenantname&gt; organizacji

### <a name="from-address"></a>Adres nadawcy
Używamy wzorzec przypominającej LinkedIn dla adres nadawcy.  Powinien być Wyczyść, który jest zapraszającej i adres e-mail od firmy oraz wyjaśnić, że wiadomość e-mail pochodzi od firmy Microsoft. Format: &lt;nazwę wyświetlaną zapraszającej&gt; z &lt;tenantname&gt; (za pośrednictwem firmy Microsoft) <invites@microsoft.com>

### <a name="reply-to"></a>Odpowiedz
Odpowiedz na wiadomość e-mail ma ustawioną zapraszającej poczty e-mail, jeśli jest dostępna, tak, aby podczas odpowiadania na wiadomości e-mail wysyła wiadomość e-mail z powrotem do zapraszającej.

### <a name="branding"></a>Znakowania.
Wiadomości e-mail zaproszenia użyciu dzierżawy firmy znakowania, które można zdefiniować dla dzierżawy. Jeśli chcesz skorzystać z zalet tej możliwości [tutaj](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) znajdują się szczegółowe informacje na temat konfigurowania go. Baner logo jest wyświetlany w wiadomości e-mail. Postępuj zgodnie z rozmiarem obrazu i instrukcje jakości [tutaj](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) dla uzyskania najlepszych wyników. Ponadto nazwa firmy również zostaną wyświetlone w wywołaniu akcji.

### <a name="call-to-action"></a>Wywołanie akcji
Wywołanie akcji składa się z dwóch części: wyjaśniający, dlaczego odbiorca odebrał wiadomość i co odbiorca jest pytany, rozwiązanie.
- W sekcji "Dlaczego" może zostać zlikwidowane przy użyciu następującego wzorca: zaproszonych do uzyskania dostępu do aplikacji w &lt;tenantname&gt; organizacji

- I "co użytkownik jest pytany, zrobić" sekcja jest określane przez obecności **wprowadzenie** przycisku. Po dodaniu odbiorca bez konieczności zaproszeń do skorzystania z tego przycisku nie pojawiają się.

### <a name="inviters-information"></a>Informacje w zapraszającej
Nazwa wyświetlana zapraszającej jest dołączany do wiadomości e-mail. I dodatkowo, jeśli ustawiono obraz profilu konta usługi Azure AD, zaproszenia wiadomości e-mail zawierają również obraz. Zarówno mają na celu zwiększenie zaufania z odbiorców w wiadomości e-mail.

Jeśli jeszcze nie skonfigurowano obraz profilu, jest wyświetlana ikona inicjały zapraszającej zamiast obrazu:

  ![Wyświetlanie inicjały zapraszającej](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Treść
Treść zawiera komunikat, który zapraszającej Redaguj lub jest przekazywana zaproszenia interfejsu API. Jest obszaru tekstu, więc nie przetwarza tagów HTML ze względów bezpieczeństwa.

### <a name="footer-section"></a>Stopki
Stopki zawiera marki firmy Microsoft i umożliwia tym adresata, jeśli wiadomość e-mail została wysłana z niemonitorowanego alias. Szczególnych przypadkach:

- Zapraszającej nie ma adresu e-mail w zaproszenia dzierżawy

  ![Obraz zapraszającej nie ma adresu e-mail w dzierżawy zaproszenia](media/invitation-email-elements/inviter-no-email.png)


- Odbiorca nie wymaga zrealizować zaproszenia

  ![Kiedy adresat nie trzeba zrealizować zaproszenia](media/invitation-email-elements/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

- [Co to jest współpraca B2B usługi Azure AD](what-is-b2b.md)
- [Jak Administratorzy usługi Azure Active Directory dodać użytkowników współpracy B2B?](add-users-administrator.md)
- [Jak pracownicy przetwarzający informacje dodać użytkowników współpracy B2B?](add-users-information-worker.md)
- [Realizacji zaproszenia współpracy B2B](redemption-experience.md)
- [Dodawanie użytkowników współpracy B2B bez zaproszenia](add-user-without-invite.md)
