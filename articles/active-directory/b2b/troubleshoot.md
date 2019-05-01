---
title: Rozwiązywanie problemów z współpracy B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Środki zaradcze dla typowych problemów przy użyciu funkcji współpracy B2B usługi Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: mimart
author: v-miegge
manager: daveba
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2a0eaf75debf694421ac9e5f2f7eb13891a20cf
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917348"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Rozwiązywanie problemów z współpracy B2B usługi Azure Active Directory

Poniżej przedstawiono niektóre środki zaradcze dla typowych problemów przy użyciu funkcji współpracy B2B usługi Azure Active Directory (Azure AD).

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Czy mogę dodano użytkownika zewnętrznego, ale nie ma ich w książce adresowej globalne lub selektora osób

W przypadkach, w którym użytkownicy zewnętrzni nie zostaną wypełnione na liście obiekt może potrwać kilka minut, aby replikować.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Użytkownik-Gość B2B nie są wyświetlane w Selektor osób programu SharePoint Online/OneDrive

Możliwość wyszukiwania dla istniejących użytkowników-gości w Selektor osób programu SharePoint Online (SPO) został WYŁĄCZONY, aby dopasować starsze zachowanie domyślnie.

Aby włączyć tę funkcję, należy za pomocą ustawienia "ShowPeoplePickerSuggestionsForGuestUsers" na poziomie kolekcji dzierżawy i witryny. Można ustawić funkcję przy użyciu Set-SPOTenant i SPOSite zestaw poleceń cmdlet, które pozwalają członkom wyszukiwanie wszystkich istniejących użytkowników-gości w katalogu. Zmiany w zakresie dzierżawy nie wpływają na już aprowizowanych witryn SPO.

## <a name="invitations-have-been-disabled-for-directory"></a>Zaproszeń zostały wyłączone dla katalogu

Jeśli zostanie wyświetlone powiadomienie, że nie masz uprawnień do zapraszania użytkowników, należy sprawdzić, czy Twoje konto użytkownika jest autoryzowany do zapraszania użytkowników zewnętrznych w obszarze Ustawienia użytkownika:

![Zrzut ekranu przedstawiający ustawienia użytkowników zewnętrznych](media/troubleshoot/external-user-settings.png)

Jeśli zostały ostatnio zmodyfikowane tych ustawień lub przypisana rola osoba zapraszająca gości do użytkownika, może występować opóźnienie 15 – 60 minut aby zmiany zaczęły obowiązywać.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Użytkownik, który mogę zaproszenie jest komunikat o błędzie podczas realizacji

Typowe błędy:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Zaproszonej administrator nie zezwolił EmailVerified użytkowników z tworzona w ramach ich dzierżawy

Podczas zapraszania użytkowników, których organizacja używa usługi Azure Active Directory, ale których nie istnieje konta określonego użytkownika (na przykład, użytkownik nie istnieje w domenie contoso.com w usłudze Azure AD). Administrator domeny contoso.com może mieć zasady w miejscu, uniemożliwiając tworzonych przez użytkowników. Użytkownik musi skontaktować się z ich administratora, aby określić, czy użytkownicy zewnętrzni mogą. Może być konieczne użytkownika zewnętrznego administratora umożliwiające użytkownikom zweryfikować poczty E-mail w ich domenie (zobacz ten [artykułu](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) na co pozwala użytkownikom zweryfikować wiadomości E-mail).

![Użytkowników zweryfikowanych błędzie informujący, że dzierżawa nie zezwala na adres e-mail](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Użytkownik zewnętrzny nie istnieje już w domeny federacyjnej

Jeśli używasz uwierzytelniania federacyjnego i użytkownik nie istnieje w usłudze Azure Active Directory, nie można zaprosić użytkownika.

Aby rozwiązać ten problem, administrator użytkownika zewnętrznego należy zsynchronizować konta do usługi Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Jak oznacza\#", który nie jest zazwyczaj prawidłowy znak synchronizacji z usługą Azure AD?

"\#" to zastrzeżony znak w UPN dla użytkowników zewnętrznych lub współpracy B2B usługi Azure AD, ponieważ konto zaproszonego user@contoso.com staje się user_contoso.com#EXT#@fabrikam.onmicrosoft.com. W związku z tym \# w pochodzące z lokalnej nazwy UPN nie mogą logować się do witryny Azure portal. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Otrzymuję komunikat o błędzie podczas dodawania użytkowników zewnętrznych do grupy zsynchronizowane

Użytkownicy zewnętrzni można dodawać tylko w grupach "Zabezpieczenia" lub "przypisane", a nie grupy, które są zarządzanego w środowisku lokalnym.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Moje użytkowników zewnętrznych nie otrzymał wiadomość e-mail, aby zrealizować

Osoby zaproszonej skontaktować się z ich usługodawcy internetowego lub spamu filtr, aby upewnić się, że następujący adres może: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>I Zwróć uwagę, że niestandardowy komunikat nie uzyska dołączone do wiadomości z zaproszeniem w czasie

Aby zapewnić zgodność z przepisami dotyczącymi prywatności, nasze interfejsy API zawierają niestandardowych komunikatów w wiadomości e-mail z zaproszeniem po:

- Osoba zapraszająca nie ma adresu e-mail w zapraszający dzierżawca
- Gdy z jednostki usługi App Service wysyła zaproszenia

Ten scenariusz jest dla Ciebie ważne, można pominąć wiadomości e-mail z zaproszeniem naszego interfejsu API i wyślij go za pośrednictwem mechanizmu e-mail wybranego. Zapoznaj się z Twojej organizacji doradcą prawnym, aby upewnić się, że dowolnych wiadomości e-mail, że wysyłane w ten sposób również są zgodne z przepisami dotyczącymi prywatności.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Komunikat o błędzie "AADSTS65005" podczas próby logowania do zasobu platformy Azure

Użytkownik mający konto gościa nie można zalogować się i otrzymuje następujący komunikat o błędzie:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

Użytkownik ma konto użytkownika usługi Azure i utworzyć niezbędnej dzierżawy, która została porzucona lub niezarządzane. Ponadto istnieją nie globalne lub firmy Administratorzy w dzierżawie.

Aby rozwiązać ten problem, możesz przejąć dzierżawy porzucone. Zapoznaj się [przejąć niezarządzanego katalogu jako administrator usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover). Należy również uzyskać dostęp dla danego sufiksu domeny DNS połączone z Internetem w celu zapewnienia bezpośredniego dowody, że Ty masz kontrolę przestrzeni nazw. Po dzierżawy są zwracane do zarządzanego stanu, należy omówić z klientem, czy opuszczania użytkowników i zweryfikowaną nazwą domeny jest najlepszym rozwiązaniem dla organizacji.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Użytkownik-Gość w dzierżawie just in time lub "wirusowego" nie jest w stanie do zresetowania swojego hasła

Jeśli dzierżawa tożsamości jest just-in-time (JIT) lub utworzyć niezbędnej dzierżawy (co oznacza, że jest oddzielnym niezarządzanej dzierżawy platformy Azure), tylko użytkownik-Gość mogą zresetować swoje hasło. Czasami organizacji będzie [przejęcie zarządzania wirusowe dzierżaw](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) które są tworzone, gdy pracownicy korzystają ich służbowe adresy e-mail, aby się zarejestrować dla usług. Po organizacji ma utworzyć niezbędnej dzierżawy, tylko przez administratorów w organizacji można zresetować hasła użytkownika lub włączyć funkcji samoobsługowego resetowania HASEŁ. W razie potrzeby jako organizacji zapraszającej można usunąć konta gościa z katalogu i ponownie wysłać zaproszenia.

## <a name="next-steps"></a>Kolejne kroki

[Uzyskaj pomoc techniczną dla współpracy B2B](get-support.md)
