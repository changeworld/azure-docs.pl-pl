---
title: Licencja usługi Azure Active Directory haseł
description: Usługa Azure AD samoobsługowego resetowania haseł wymagania licencyjne
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 6da0bddc3f6c90d0ecd3a554988f510e1063caac
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043043"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Resetuj wymaganiach dotyczących licencjonowania dla samoobsługowego hasła usługi Azure AD

Azure Active Directory (Azure AD) jest oferowana w czterech wersjach: Bezpłatna, podstawowa, Premium P1 i Premium P2. Istnieje kilka różnych funkcji, składających się samoobsługowego resetowania haseł, zmiany, w tym resetowania, odblokuj i zapisywania zwrotnego, które są dostępne w różnych wersjach programu Azure AD. W tym artykule próbuje różnice zostały wyjaśnione w. Więcej szczegółów dotyczących funkcji dostępnych w poszczególnych wydaniach usługi Azure AD można znaleźć na [cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porównanie wersji i funkcji

Samoobsługowe haseł usługi Azure AD resetowania jest licencjonowany na użytkownika, aby zachować zgodność z organizacji są wymagane do przypisywania odpowiedniej licencji do użytkowników.

* Samoobsługowa zmiana haseł użytkowników w chmurze
   * Jestem **użytkowników tylko w chmurze** i swoje hasło.
      * Chcę **zmienić** hasła do czegoś nowego.
   * Ta funkcja znajduje się we wszystkich wersjach programu Azure AD.

* Samoobsługowe resetowanie haseł użytkowników w chmurze
   * Jestem **użytkowników tylko w chmurze** i zapomniane hasła.
      * Chcę **resetowania** hasła na coś, co wiem.
   * Ta funkcja jest dostępna w wersjach usługi Azure AD podstawowa, Premium P1 bądź Premium P2.

* Samoobsługowe hasło Resetowanie/zmiana/odblokowywanie **przy użyciu funkcji zapisywania zwrotnego w środowisku lokalnym**
   * Jestem **użytkownika hybrydowego** Moje konto użytkownika usługi Active Directory w środowisku lokalnym jest zsynchronizowany z moim kontem usługi Azure AD za pomocą usługi Azure AD Connect. Chcę zmienić hasło, mieć zapomniane hasła lub zostało zablokowane.
      * Chcę zmienić hasło lub zresetować je coś o nim znać lub odblokowywanie mojego konta **i** ma czy zmiana zsynchronizowane z powrotem do lokalnej usługi Active Directory.
   * Ta funkcja znajduje się w programie Azure AD Premium P1, albo w wersjach Premium P2.

> [!WARNING]
> Licencjonowanie plany usługi Office 365 autonomiczny *"Samoobsługi hasło Resetowanie/zmiana/odblokowywanie przy użyciu funkcji zapisywania zwrotnego w środowisku lokalnym" nie jest obsługiwany* i wymaga planu, która obejmuje usługi Azure AD Premium P1 albo w wersjach Premium P2 dla tej funkcji do Praca.
>

Dodatkowe informacje o licencjonowaniu, wraz z kosztami, można znaleźć na następujących stronach:

* [Usługa Azure Active Directory, cennik lokacji](https://azure.microsoft.com/pricing/details/active-directory/)
* [Usługa Azure Active Directory funkcje i możliwości](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Włącz grupy lub Licencjonowanie oparte na użytkownika

Teraz usługi Azure AD obsługuje Licencjonowanie na podstawie grupy. Administratorzy mogą przypisywać licencje zbiorcze do grupy użytkowników, a nie przypisując im pojedynczo. Aby uzyskać więcej informacji, zobacz [przypisania, sprawdź i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do użytkownika, administrator musi określić **lokalizacji użytkowania** właściwości użytkownika. Przypisanie licencji może odbywać się w obszarze **użytkownika** > **profilu** > **ustawienia** sekcji w witrynie Azure portal. *Gdy używasz przypisanie licencji do grupy, wszyscy użytkownicy bez określonej lokalizacji użytkowania dziedziczą lokalizację katalogu.*

## <a name="next-steps"></a>Kolejne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)
