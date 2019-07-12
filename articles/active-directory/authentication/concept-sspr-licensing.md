---
title: Licencja samoobsługowego resetowania haseł — Azure Active Directory
description: Usługa Azure AD samoobsługowego resetowania haseł wymagania licencyjne
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56b74e6a9f1d83798b557c48eb78242d70e85dfc
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612632"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Resetuj wymaganiach dotyczących licencjonowania dla samoobsługowego hasła usługi Azure AD

Usługa Azure Active Directory (Azure AD) jest dostępna w czterech wersjach — Bezpłatna, Podstawowa, Premium P1 i Premium P2. Istnieje kilka różnych funkcji, składających się samoobsługowego resetowania haseł, zmiany, w tym resetowania, odblokuj i zapisywania zwrotnego, które są dostępne w różnych wersjach programu Azure AD. W tym artykule próbuje różnice zostały wyjaśnione w. Więcej szczegółów dotyczących funkcji dostępnych w poszczególnych wydaniach usługi Azure AD można znaleźć na [cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porównanie wersji i funkcji

Samoobsługowe haseł usługi Azure AD resetowania jest licencjonowany na użytkownika, aby zachować zgodność z organizacji są wymagane do przypisywania odpowiedniej licencji do użytkowników.

* Samoobsługowa zmiana haseł użytkowników w chmurze
   * Jestem **użytkowników tylko w chmurze** i swoje hasło.
      * Chcę **zmienić** hasła do czegoś nowego.
   * Ta funkcja znajduje się we wszystkich wersjach programu Azure AD.

* Samoobsługowe resetowanie haseł użytkowników w chmurze
   * Jestem **użytkowników tylko w chmurze** i zapomniane hasła.
      * Chcę **resetowania** hasła na coś, co wiem.
   * Ta funkcja znajduje się w usłudze Azure AD w wersji podstawowa, Premium P1 lub P2 lub Microsoft 365 Business.

* Samoobsługowe hasło Resetowanie/zmiana/odblokowywanie **przy użyciu funkcji zapisywania zwrotnego w środowisku lokalnym**
   * Jestem **użytkownika hybrydowego** Moje konto użytkownika usługi Active Directory w środowisku lokalnym jest zsynchronizowany z moim kontem usługi Azure AD za pomocą usługi Azure AD Connect. Chcę zmienić hasło, mieć zapomniane hasła lub zostało zablokowane.
      * Chcę zmienić hasło lub zresetować je coś o nim znać lub odblokowywanie mojego konta **i** ma czy zmiana zsynchronizowane z powrotem do lokalnej usługi Active Directory.
   * Ta funkcja znajduje się w usłudze Azure AD Premium P1 lub P2 lub Microsoft 365 Business.

> [!WARNING]
> Licencjonowanie plany usługi Office 365 autonomiczny *nie obsługują "Samoobsługi hasło Resetowanie/zmiana/odblokowywanie przy użyciu funkcji zapisywania zwrotnego w środowisku lokalnym"* i wymagają plan, który zawiera program Azure AD Premium P1, P2 — wersja Premium lub Microsoft 365 Business, w tym funkcje do pracy.
>

Dodatkowe informacje o licencjonowaniu, wraz z kosztami, można znaleźć na następujących stronach:

* [Usługa Azure Active Directory, cennik lokacji](https://azure.microsoft.com/pricing/details/active-directory/)
* [Usługa Azure Active Directory funkcje i możliwości](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Opis usługi Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

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
