---
title: Realizacji zaproszenia we współpracy B2B — Azure Active Directory | Dokumentacja firmy Microsoft
description: Opisano środowisko realizacji zaproszenia współpracy B2B usługi Azure AD dla użytkowników końcowych, w tym umowy zasadami zachowania poufności informacji.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0a0c900bbfbb2778d8fabcbb71e339fd3dabcf47
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260174"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Realizacji zaproszenia współpraca w usłudze Azure Active Directory B2B

Aby współpracować z użytkownicy z organizacji partnerskich do współpracy B2B usługi Azure Active Directory (Azure AD), można zaprosić użytkowników gościa uzyskiwać dostęp do aplikacji udostępnionej. Po użytkownika gościa jest dodawane do katalogu za pomocą interfejsu użytkownika, lub użytkownik jest zaproszony za pomocą programu PowerShell, gości musi przechodzić przez proces zgody po raz pierwszy, gdzie zgadzają się [zasadami zachowania poufności informacji](#privacy-policy-agreement). Ten proces odbywa się w jednej z następujących sposobów:

- Zapraszającej gościa wysyła bezpośredniego łącza do aplikacji udostępnionej. Osoby zaproszonej kliknie link do logowania, akceptuje zasadami zachowania poufności informacji i bezproblemowo uzyskuje dostęp do zasobu udostępnionego. (Użytkownika gościa nadal otrzymuje wiadomość e-mail z zaproszeniem z adresem URL realizacji, ale inne niż niektórych szczególnych przypadkach, ma już wymagane, użyj wiadomość e-mail z zaproszeniem).  
- Gość otrzymuje wiadomość e-mail z zaproszeniem i klika pozycję realizacji adres URL. W ramach logowania po raz pierwszy są monitowani o zaakceptować postanowienia dotyczące prywatności.

## <a name="redemption-through-a-direct-link"></a>Realizacji za pośrednictwem bezpośredniego łącza

Zapraszającej gościa można zaprosić użytkownika gościa przy wysyłaniu bezpośredniego łącza do aplikacji udostępnionej. Dla użytkownika gościa środowisko realizacji jest równie proste jak logowanie do aplikacji, który został udostępniony z nimi. One kliknij łącze do aplikacji, przejrzyj i zaakceptuj postanowienia dotyczące prywatności i następnie bezproblemowo uzyskiwać dostęp do aplikacji. W większości przypadków gości nie trzeba kliknij adres URL realizacji w wiadomość e-mail z zaproszeniem.

Zaproszeni użytkownicy gościa za pomocą interfejsu użytkownika, czy postanowiono Wyślij wiadomość e-mail z zaproszeniem jako część środowiska PowerShell zaproszenia, zaproszony użytkownik nadal otrzymuje wiadomość e-mail z zaproszeniem. Ten adres e-mail jest przydatne w przypadku następujących przypadków:

- Użytkownik nie ma konta usługi Azure AD lub konta Microsoft (MSA). W takim przypadku użytkownik musi utworzyć zarządzanych kont usług, przed odbiorca kliknie link, lub mogą w wiadomość e-mail z zaproszeniem przy użyciu adresu URL realizacji. Proces realizacji automatycznie monituje użytkownika o tworzenie zarządzanych kont usług.
- Czasami obiekt zaproszony użytkownik nie ma adresu e-mail z powodu konfliktu z obiektem kontaktów (na przykład obiekt kontaktów programu Outlook). W takim przypadku użytkownik musi kliknąć realizacji adres URL w wiadomości e-mail z zaproszeniem.
- Użytkownik może zalogować się przy użyciu alias adresu e-mail, który został zaproszony. (Alias jest adres e-mail dodatkowych skojarzony z kontem e-mail). W takim przypadku użytkownik musi kliknąć realizacji adres URL w wiadomości e-mail z zaproszeniem.

Jeśli te specjalne przypadki są ważne dla organizacji, firma Microsoft zaleca zaprosić użytkowników przy użyciu metod, które nadal wysyłać wiadomość e-mail z zaproszeniem. Ponadto użytkownik nie są objęte jedną z tych szczególnych przypadkach, nadal po kliknięciu adres URL w wiadomości e-mail z zaproszeniem do uzyskania dostępu.

## <a name="redemption-through-the-invitation-email"></a>Realizacji za pośrednictwem wiadomość e-mail z zaproszeniem

Jeśli zaproszenie za pomocą metody, która wysyła wiadomość e-mail z zaproszeniem, użytkownicy również zrealizować zaproszenie za pośrednictwem wiadomość e-mail z zaproszeniem. Zaproszony użytkownik może kliknij adres URL realizacji w wiadomości e-mail, a następnie zaakceptuj postanowienia dotyczące prywatności. Proces jest opisany bardziej szczegółowo w tym miejscu:

1.  Po zaproszeni, osoby zaproszonej otrzyma zaproszenie za pośrednictwem poczty e-mail, która została wysłana z **Invitations Microsoft**.
2.  Wybiera osoby zaproszonej **wprowadzenie** w wiadomości e-mail.
3.  Jeśli osoby zaproszonej nie ma konta usługi Azure AD lub zarządzanych kont usług, ich monit o utworzenie zarządzanych kont usług.
4.  Osoby zaproszonej jest przekierowywany do **Przejrzyj uprawnienia** ekranu, w którym można przejrzeć zasady zachowania poufności informacji w organizacji zaproszenia i zaakceptuj postanowienia.

## <a name="privacy-policy-agreement"></a>Umowa zasad ochrony prywatności

Po zalogowaniu użytkownika wszelkie gościa dostęp do zasobów w organizacji partnerskiej po raz pierwszy, zobaczy **Przejrzyj uprawnienia** ekranu. W tym miejscu one Przejrzyj zasady zachowania poufności informacji w organizacji zaproszenia. Użytkownik musi zaakceptować wykorzystanie swoich informacji zgodnie z zasady zachowania poufności informacji organizacji zaproszenia, aby kontynuować.

![Zrzut ekranu przedstawiający ustawień użytkownika w panelu dostępu](media/redemption-experience/ConsentScreen.png) 

Aby dowiedzieć się jak Administrator dzierżawy można łączyć zasady zachowania poufności informacji w organizacji, zobacz [porad: Dodaj informacje o prywatności organizacji w usłudze Azure Active Directory](https://aka.ms/adprivacystatement).

## <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w portalu Azure](add-users-administrator.md)
- [Jak pracownicy przetwarzający informacje dodać użytkowników współpracy B2B do usługi Azure Active Directory?](add-users-information-worker.md)
- [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B przy użyciu programu PowerShell](customize-invitation-api.md#powershell)
- [Pozostaw organizacji jako Gość](leave-the-organization.md)