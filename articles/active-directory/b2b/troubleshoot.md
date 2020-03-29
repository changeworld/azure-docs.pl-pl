---
title: Rozwiązywanie problemów ze współpracą B2B — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Środki zaradcze w przypadku typowych problemów ze współpracą usługi Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 03/19/2020
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f8bafb04d0a5d9c6d25a7ed7e155888d492e9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050780"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Rozwiązywanie problemów ze współpracą usługi Azure Active Directory B2B

Poniżej przedstawiono kilka środków zaradczych dotyczących typowych problemów ze współpracą b2b usługi Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **Począwszy od 31 marca 2021**r. firma Microsoft nie będzie już obsługiwać realizacji zaproszeń, tworząc niezarządzane konta usługi Azure AD i dzierżawy scenariuszy współpracy B2B. W ramach przygotowań zachęcamy klientów do wyrażenia zgody na [jednorazowe uwierzytelnianie kodem dostępu pocztą e-mail.](one-time-passcode.md) Cieszymy się z waszych opinii na temat tej publicznej funkcji podglądu i cieszymy się, że możemy stworzyć jeszcze więcej sposobów współpracy.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Dodałem użytkownika zewnętrznego, ale nie widzę go w mojej globalnej książce adresowej ani w selektorze osób

W przypadkach, gdy użytkownicy zewnętrzni nie są wypełniane na liście, obiekt może potrwać kilka minut, aby replikować.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Użytkownik-gość B2B nie jest wyświetlany w selektorze osób usługi SharePoint Online/OneDrive

Możliwość wyszukiwania istniejących użytkowników-gości w selektorze osób usługi SharePoint Online (SPO) jest domyślnie WYŁĄCZONA, aby dopasować starsze zachowanie.

Tę funkcję można włączyć za pomocą ustawienia "ShowPeoplePickerSuggestionsForGuestUsers" na poziomie dzierżawy i zbioru witryn. Funkcję można ustawić za pomocą poleceń cmdlet Set-SPOTenant i Set-SPOSite, które umożliwiają członkom przeszukiwanie wszystkich istniejących użytkowników-gości w katalogu. Zmiany w zakresie dzierżawy nie mają wpływu na już zainicjowane witryny SPO.

## <a name="invitations-have-been-disabled-for-directory"></a>Zaproszenia zostały wyłączone dla katalogu

Jeśli zostaniesz powiadomiony, że nie masz uprawnień do zapraszania użytkowników, sprawdź, czy twoje konto użytkownika jest autoryzowane do zapraszania użytkowników zewnętrznych w obszarze Ustawienia użytkownika usługi Azure Active Directory > > użytkownicy zewnętrzni > Zarządzanie ustawieniami współpracy zewnętrznej:

![Zrzut ekranu przedstawiający ustawienia Użytkownicy zewnętrzni](media/troubleshoot/external-user-settings.png)

Jeśli ostatnio zmodyfikowano te ustawienia lub przypisano mu rolę Zapraszanie gościa, może wystąpić 15-60 minutowe opóźnienie, zanim zmiany zajmą się ich wprowadzeniem.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Użytkownik, który zaprosiłem, otrzymuje błąd podczas realizacji

Typowe błędy obejmują:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Administrator osoby zaproszonej uniemożliwił użytkownikom e-mailzweryfikowanym tworzenie się w ich dzierżawie

Podczas zapraszania użytkowników, których organizacja korzysta z usługi Azure Active Directory, ale gdy konto określonego użytkownika nie istnieje (na przykład użytkownik nie istnieje w usłudze Azure AD contoso.com). Administrator contoso.com może mieć zasady uniemożliwiające tworzenie użytkowników. Użytkownik musi skontaktować się z administratorem, aby ustalić, czy użytkownicy zewnętrzni są dozwolone. Administrator użytkownika zewnętrznego może być konieczne zezwolenie zweryfikowanym użytkownikom poczty e-mail w ich domenie (zobacz ten [artykuł](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) dotyczący zezwalania zweryfikowanym użytkownikom poczty e-mail).

![Błąd informujący dzierżawcę nie zezwala na e-mail zweryfikowanych użytkowników](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Użytkownik zewnętrzny nie istnieje już w domenie federacyjnej

Jeśli używasz uwierzytelniania federacyjnego, a użytkownik jeszcze nie istnieje w usłudze Azure Active Directory, nie można zaprosić użytkownika.

Aby rozwiązać ten problem, administrator użytkownika zewnętrznego musi zsynchronizować konto użytkownika z usługą Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Jak '\#", który zwykle nie jest prawidłowym znakiem, synchronizuje się z usługą Azure AD?

"\#" jest zastrzeżonym znakiem w sieciACH UPN dla współpracy usługi user@contoso.com Azure AD B2B lub użytkownikami zewnętrznymi, ponieważ zaproszone konto staje się user_contoso.com#EXT#@fabrikam.onmicrosoft.com. W związku \# z tym w sieciach UPN pochodzących z lokalnych nie mogą logować się do witryny Azure portal. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Pojawia się błąd podczas dodawania użytkowników zewnętrznych do zsynchronizowanej grupy

Użytkownicy zewnętrzni mogą być dodawane tylko do grup "przypisane" lub "Zabezpieczenia", a nie do grup, które są opanowane lokalnie.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Mój użytkownik zewnętrzny nie otrzymał wiadomości e-mail do zrealizowania

Osoba zaproszona powinna skontaktować się z usługodawcą lub filtrem spamu, aby upewnić się, że następujący adres jest dozwolony:Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Zauważyłem, że wiadomość niestandardowa nie jest czasami dołączona do wiadomości z zaproszeniem

Aby zapewnić zgodność z przepisami dotyczącymi prywatności, nasze interfejsy API nie zawierają niestandardowych wiadomości w zaproszeniu e-mail, gdy:

- Osoba zapraszająca nie ma adresu e-mail w zapraszającej dzierżawie
- Gdy główny podmiot usługi appservice wysyła zaproszenie

Jeśli ten scenariusz jest dla Ciebie ważny, możesz pominąć naszą wiadomość e-mail z zaproszeniem interfejsu API i wysłać ją za pośrednictwem wybranego mechanizmu poczty e-mail. Skonsultuj się z radcą prawnym organizacji, aby upewnić się, że każda wysłana w ten sposób wiadomość e-mail jest zgodna z przepisami dotyczącymi prywatności.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Podczas próby zalogowania się do zasobu platformy Azure pojawia się błąd "AADSTS65005".

Użytkownik, który ma konto gościa, nie może się zalogować i otrzymuje następujący komunikat o błędzie:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

Użytkownik ma konto użytkownika platformy Azure i jest dzierżawą wirusową, która została porzucona lub niezarządzana. Ponadto w dzierżawie nie ma administratorów globalnych ani firmowych.

Aby rozwiązać ten problem, należy przejąć opuszczony dzierżawy. Zapoznaj się [z artykułem Przejmij niezarządzany katalog jako administrator w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover). Należy również uzyskać dostęp do internetowego systemu DNS dla danego sufiksu domeny, aby dostarczyć bezpośrednich dowodów na to, że użytkownik ma kontrolę nad obszarem nazw. Po powrocie dzierżawy do stanu zarządzanego, należy omówić z klientem, czy pozostawienie użytkowników i zweryfikowana nazwa domeny jest najlepszym rozwiązaniem dla ich organizacji.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Użytkownik-gość z dzierżawą just-in-time lub "wirusowym" nie może zresetować hasła

Jeśli dzierżawa tożsamości jest dzierżawą just-in-time (JIT) lub wirusową (co oznacza, że jest oddzielną, niezarządzaną dzierżawą platformy Azure), tylko użytkownik-gość może zresetować swoje hasło. Czasami organizacja [przejmie zarządzanie wirusowymi najemcami,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) które są tworzone, gdy pracownicy używają swoich służbowych adresów e-mail do rejestracji w usługach. Po przejęcie przez organizację dzierżawy wirusowej tylko administrator w tej organizacji może zresetować hasło użytkownika lub włączyć wiele łatW. W razie potrzeby jako organizacja zapraszająca można usunąć konto użytkownika gościa z katalogu i ponownie wysuń zaproszenie.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>Użytkownik-gość nie może użyć modułu AzureAD PowerShell V1

Od 18 listopada 2019 r. użytkownicy-goście w katalogu (zdefiniowani jako konta użytkowników, w których właściwość **userType** jest równa **guest)** są blokowani przy użyciu modułu AzureAD PowerShell V1. W przyszłości użytkownik musi być użytkownikiem członkowskim (gdzie **userType** jest równy **członkowi)** lub użyć modułu AzureAD PowerShell V2.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>W dzierżawie usługi Azure us government nie mogę zaprosić użytkownika gościa współpracy B2B

W chmurze dla instytucji rządowych platformy Azure uss współpraca B2B jest obecnie obsługiwana tylko między dzierżawami, które są zarówno w chmurze platformy Azure us government, jak i obsługują współpracę B2B. Jeśli zaprosisz użytkownika w dzierżawie, który nie jest częścią chmury platformy Azure dla instytucji rządowych usa lub który nie obsługuje jeszcze współpracy B2B, otrzymasz błąd. Aby uzyskać szczegółowe informacje i ograniczenia, zobacz [Odmiany usługi Azure Active Directory Premium P1 i P2](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

## <a name="next-steps"></a>Następne kroki

[Uzyskaj wsparcie dla współpracy B2B](get-support.md)
