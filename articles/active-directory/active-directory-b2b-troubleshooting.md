---
title: Rozwiązywanie problemów z współpracy usługi Azure Active Directory B2B | Dokumentacja firmy Microsoft
description: Środki zaradcze dla typowych problemów z współpracy usługi Azure Active Directory B2B
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
ms.date: 05/25/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 82c171c59b7f465afd760d50b36d17912ee7c8e3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Rozwiązywanie problemów z współpracy usługi Azure Active Directory B2B

Poniżej przedstawiono niektóre środki zaradcze dla typowych problemów z współpracy B2B usługi Azure Active Directory (Azure AD).


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Chcę dodano użytkownika zewnętrznego, ale nie ma ich w książce adresowej globalnych lub selektora osób

W przypadkach, gdy użytkownicy zewnętrzni nie zostały wypełnione na liście obiekt może potrwać kilka minut, aby replikować.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Użytkownik-Gość B2B nie pojawia się w selektorze użytkowników usługi SharePoint Online/OneDrive 
 
Domyślnie, aby dopasować starsze zachowanie możliwość wyszukiwania istniejących użytkowników gościa w selektorze użytkowników programu SharePoint Online (SPO) ma wartość OFF.

Można włączyć tę funkcję za pomocą ustawienia "ShowPeoplePickerSuggestionsForGuestUsers" na poziomie kolekcji dzierżawy i witryny. Można ustawić funkcji za pośrednictwem poleceń cmdlet Set SPOTenant i Set-SPOSite, która zezwala na elementy członkowskie wyszukać wszystkich istniejących użytkowników gościa w katalogu. Zmiany w zakresie dzierżawy nie wpływają na lokacje SPO już zainicjowaną.

## <a name="invitations-have-been-disabled-for-directory"></a>Wyłączono zaproszeń do skorzystania z katalogu

Jeśli zostanie wyświetlone powiadomienie, że nie masz uprawnień do zapraszania użytkowników, należy sprawdzić, czy Twoje konto użytkownika jest autoryzowany z zaproszeniem dla użytkowników zewnętrznych, w obszarze Ustawienia użytkownika:

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Jeśli niedawno zmieniono te ustawienia lub rolą Gość zapraszającej przypisana do użytkownika, mogą wystąpić opóźnienia 15 – 60 minut przed wprowadzeniem zmian.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Użytkownik, który zaproszenie I otrzymuje wystąpił błąd podczas realizacji

Typowe błędy:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Zaproszonej administrator nie zezwolił użytkowników EmailVerified utworzenie w swojej dzierżawy

Gdy zapraszanie użytkowników, których organizacja korzysta z usługi Azure Active Directory, ale gdy konta określonego użytkownika nie istnieje (na przykład użytkownik nie istnieje w usłudze Azure AD w domenie contoso.com). Administrator domeny contoso.com może mieć zasady w miejscu uniemożliwia tworzone przez użytkowników. Użytkownik musi skontaktować się z ich administratora, aby określić, czy użytkownicy zewnętrzni są dozwolone. Użytkownika zewnętrznego administratora może być konieczne użytkownicy zweryfikować poczty E-mail w swojej domenie (zobacz [artykułu](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) na zezwolenie użytkownikom zweryfikować poczty E-mail).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Użytkownik zewnętrzny nie istnieje już w domenie federacyjnych

Jeśli używasz uwierzytelniania federacyjnego i użytkownik nie istnieje już w usłudze Azure Active Directory, nie mogą być zapraszani użytkownika.

Aby rozwiązać ten problem, administrator użytkownika zewnętrznego należy zsynchronizować konta użytkownika do usługi Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Jak jest\#", która nie jest zwykle prawidłowym znakiem synchronizacji z usługą Azure AD?

"\#" jest zarezerwowany znak w UPN współpracy B2B usługi Azure AD lub użytkowników zewnętrznych, ponieważ konto zaproszonych user@contoso.com staje się user_contoso.com#EXT#@fabrikam.onmicrosoft.com. W związku z tym \# w UPN pochodzące z lokalnymi nie mogą logować się do portalu Azure. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Przy dodawaniu użytkowników zewnętrznych do grupy synchronizowane komunikat o błędzie

Użytkownicy zewnętrzni mogą być dodawane tylko dla grup "Zabezpieczenia" lub "przypisane", a nie do grup, które są lokalne zarządzanego.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Moje użytkownika zewnętrznego nie odebrał wiadomość e-mail, aby zrealizować

Osoby zaproszonej skontaktować się z ich usługodawcy internetowego lub spamu filtru, aby upewnić się, czy następujący adres może: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>I Zwróć uwagę, że niestandardowy komunikat nie można uzyskać dołączana do komunikatów zaproszenia w czasie

Do wykonania przepisów dotyczących prywatności, naszych interfejsów API, nie dołączaj niestandardowych komunikatów w wiadomości e-mail z zaproszeniem po:

- Zapraszającej nie ma adresu e-mail w dzierżawie zaproszenia
- Jeśli podmiot zabezpieczeń appservice wysyła zaproszenia

Ten scenariusz jest istotne dla Ciebie, można pominąć naszych wiadomość e-mail z zaproszeniem interfejsu API i wysłać go za pośrednictwem poczty e-mail mechanizmu wybranych przez użytkownika. Zapoznaj się w organizacji korzystania z pomocy prawnej się upewnić się, że każdej wiadomości e-mail, wysyłania, że ten sposób również spełnia przepisów dotyczących prywatności.

## <a name="next-steps"></a>Kolejne kroki

Zobacz nasze inne artykuły dotyczące współpracy B2B w usłudze Azure AD:

* [Czym jest współpraca B2B w usłudze Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Jak Administratorzy usługi Azure Active Directory dodać użytkowników współpracy B2B?](active-directory-b2b-admin-add-users.md)
* [Jak pracownicy przetwarzający informacje dodać użytkowników współpracy B2B?](active-directory-b2b-iw-add-users.md)
* [Elementy współpracy B2B zaproszenie](active-directory-b2b-invitation-email.md)
* [Realizacji zaproszenia współpracy B2B](active-directory-b2b-redemption-experience.md)
* [Azure licencjonowania współpracy B2B usługi AD](active-directory-b2b-licensing.md)
* [Azure współpracy B2B usługi Active Directory — często zadawane pytania (FAQ)](active-directory-b2b-faq.md)
* [Dostosowywanie i Azure Active Directory B2B współpracy interfejsu API](active-directory-b2b-api.md)
* [Usługa Multi-Factor Authentication dla użytkowników współpracy B2B](active-directory-b2b-mfa-instructions.md)
* [Dodawanie użytkowników współpracy B2B bez zaproszenia](active-directory-b2b-add-user-without-invite.md)
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
