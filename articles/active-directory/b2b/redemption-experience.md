---
title: Realizacja zaproszenia we współpracy B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym artykule opisano środowisko realizacji zaproszenia współpracy B2B usługi Azure AD dla użytkowników końcowych, w tym umowy postanowienia dotyczące prywatności.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99fe386ec75e60a16f3095f22dccc3852883f8aa
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304508"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Realizacja zaproszenia współpracy w usłudze Azure Active Directory B2B

Do współpracy z użytkowników z organizacji partnerskich do współpracy B2B usługi Azure Active Directory (Azure AD), możesz zaprosić użytkowników-gości, uzyskiwania dostępu do udostępnionych aplikacji. Po użytkownik-Gość zostanie dodany do katalogu za pomocą interfejsu użytkownika, lub użytkownik otrzymał zaproszenie za pomocą programu PowerShell, użytkowników (gości) musi przejść proces zgody po raz pierwszy, w których użytkownik wyrazi zgodę na [postanowienia dotyczące prywatności](#privacy-policy-agreement). Ten proces odbywa się w jednym z następujących sposobów:

- Osoba zapraszająca gości wysyła bezpośredni link do udostępnionej aplikacji. Osoby zaproszonej kliknie link, aby zalogować się akceptuje postanowienia dotyczące prywatności i bezproblemowo uzyskuje dostęp do zasobu udostępnionego. (Użytkownik-Gość wciąż otrzymuje powitalną wiadomość e-mail z adresem URL realizacji, ale inne niż kilka szczególnych przypadkach jest już potrzebne, użyj wiadomości e-mail z zaproszeniem).  
- Użytkownik-Gość otrzyma wiadomość e-mail z zaproszeniem i klika adres URL realizacji. W ramach logowania po raz pierwszy otrzymuje monit zaakceptuj postanowienia dotyczące prywatności.

## <a name="redemption-through-a-direct-link"></a>Realizacji za pomocą linku bezpośredniego

Osoba zapraszająca gości mogą zapraszać użytkownikiem-gościem, wysyłając [bezpośredni link do aplikacji udostępnionej](../manage-apps/end-user-experiences.md#direct-sign-on-links). Dla użytkownika-gościa środowisko realizacji jest równie proste jak logowanie do aplikacji, która została udostępniona z nimi. One kliknij link do aplikacji, przejrzyj i zaakceptuj postanowienia dotyczące prywatności i następnie bezproblemowo uzyskiwać dostęp do aplikacji. W większości przypadków użytkownicy-goście nie muszą już kliknij adres URL realizacji w wiadomości e-mail z zaproszeniem.

Jeśli użytkownicy-goście, za pomocą interfejsu użytkownika lub wybranej do wysyłania wiadomości e-mail z zaproszeniem jako część środowisko programu PowerShell zaproszenie zaproszonego użytkownika wciąż otrzymuje wiadomość e-mail z zaproszeniem. Ta wiadomość e-mail jest przydatne w przypadku następujących przypadków:

- Użytkownik nie ma konta usługi Azure AD lub konta Microsoft (MSA). W takim przypadku użytkownik musi utworzyć MSA, zanim kliknięciu łącza lub używają adresu URL realizacji w wiadomości e-mail z zaproszeniem. Procesu realizacji automatycznie monituje użytkownika do utworzenia konta Microsoft.
- Czasami obiekt zaproszonego użytkownika nie może być adres e-mail ze względu na konflikt z obiektem kontaktu (na przykład obiekt kontaktów programu Outlook). W takim przypadku użytkownik musi kliknąć adres URL realizacji w wiadomości e-mail z zaproszeniem.
- Użytkownik może zalogować się przy użyciu aliasu adresu e-mail, który został zaproszony. (Alias jest adres e-mail dodatkowych skojarzony z kontem e-mail). W takim przypadku użytkownik musi kliknąć adres URL realizacji w wiadomości e-mail z zaproszeniem.

Jeśli te przypadki specjalne są ważne dla organizacji, firma Microsoft zaleca zaprosić użytkowników przy użyciu metod, które nadal wysyłać wiadomości e-mail z zaproszeniem. Ponadto użytkownik nie są objęte jedną z tych specjalnych przypadków, nadal po kliknięciu adresu URL w wiadomości e-mail z zaproszeniem, aby uzyskać dostęp.

## <a name="redemption-through-the-invitation-email"></a>Realizacji za pośrednictwem wiadomości e-mail z zaproszeniem

Jeśli zaproszenie za pośrednictwem metody, która wysyła wiadomość e-mail z zaproszeniem, użytkownicy mogą również zrealizować zaproszenie za pośrednictwem wiadomości e-mail z zaproszeniem. Zaproszono użytkownika można kliknij adres URL realizacji w wiadomości e-mail, a następnie zaakceptuj postanowienia dotyczące prywatności. Proces jest opisany bardziej szczegółowo w tym miejscu:

1.  Po zaproszeni, osoby zaproszonej otrzymuje zaproszenie za pośrednictwem poczty e-mail, które są wysyłane z **Invitations Microsoft**.
2.  Wybiera osoby zaproszonej **wprowadzenie** w wiadomości e-mail.
3.  Osoby zaproszonej nie ma konta usługi Azure AD lub MSA, są monitowani do utworzenia konta Microsoft.
4.  Osoby zaproszonej jest przekierowywane do **Przegląd uprawnień** ekranu, w którym można przejrzeć zasady zachowania poufności informacji w organizacji zapraszającej i zaakceptuj warunki.

## <a name="privacy-policy-agreement"></a>Umowa zasad ochrony prywatności

Po zalogowaniu się dowolnego użytkownika-gościa dostęp do zasobów w organizacji partnerskiej po raz pierwszy zobaczą **Przegląd uprawnień** ekranu. W tym miejscu mogą przejrzeć zasady zachowania poufności informacji w organizacji zapraszającej. Użytkownik musi zaakceptować wykorzystywania ich informacji zgodnie z zasady zachowania poufności informacji organizacji zapraszającej, aby kontynuować.

![Zrzut ekranu przedstawiający ustawienia użytkownika w panelu dostępu](media/redemption-experience/ConsentScreen.png) 

Aby dowiedzieć się, jak jak Administrator dzierżawy można połączyć z zasady zachowania poufności informacji w organizacji, zobacz [porad: Dodaj informacje o prywatności w organizacji w usłudze Azure Active Directory](https://aka.ms/adprivacystatement).

## <a name="terms-of-use"></a>Warunki użytkowania

Warunki użytkowania można przedstawić użytkownikowi Gość podczas procesu realizacji początkowej przy użyciu warunków usługi Azure AD, użyj funkcji. W usłudze Azure Active Directory uzyskujesz dostęp do tej funkcji w obszarze **Zarządzaj** > **relacje w organizacji** > **warunki użytkowania** lub obszarze **Zabezpieczeń** > **dostępu warunkowego** > **warunki użytkowania**. Aby uzyskać więcej informacji, zobacz [są wyposażone w usłudze Azure AD z warunkami użytkowania](../conditional-access/terms-of-use.md).

![Zrzut ekranu przedstawiający nowe warunki użytkowania](media/redemption-experience/organizational-relationships-terms-of-use.png) 

## <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Dodają użytkowników we współpracy B2B usługi Azure Active Directory w witrynie Azure portal](add-users-administrator.md)
- [Jak pracownicy przetwarzający informacje mogą dodać użytkowników we współpracy B2B do usługi Azure Active Directory?](add-users-information-worker.md)
- [Dodają użytkowników we współpracy B2B usługi Azure Active Directory przy użyciu programu PowerShell](customize-invitation-api.md#powershell)
- [Opuścić organizację jako użytkownika-gościa](leave-the-organization.md)
