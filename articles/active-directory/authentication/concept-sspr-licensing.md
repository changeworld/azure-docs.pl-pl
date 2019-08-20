---
title: Samoobsługowe resetowanie haseł licencji — Azure Active Directory
description: Wymagania licencyjne dotyczące samoobsługowego resetowania haseł usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d7a23ed0a84ebd671c69744dfec145763ef6897
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622715"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Wymagania licencyjne dotyczące funkcji samoobsługowego resetowania haseł w usłudze Azure AD

Azure Active Directory (Azure AD) znajduje się w kilku wersjach: Bezpłatna, Premium P1 i Premium P2. Istnieje kilka różnych funkcji, które składają się na Samoobsługowe resetowanie hasła, w tym zmiany, resetowanie, odblokowywanie i zapisywanie zwrotne, które są dostępne w różnych wersjach usługi Azure AD. Ten artykuł próbuje wyjaśnić różnice. Więcej informacji o funkcjach dostępnych w poszczególnych wersjach usługi Azure AD można znaleźć na [stronie cennika Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porównanie wersji i funkcji

Funkcja samoobsługowego resetowania hasła w usłudze Azure AD jest licencjonowana dla poszczególnych użytkowników, aby zachować odpowiednie licencje dla swoich użytkowników.

* Samoobsługowa zmiana haseł użytkowników w chmurze
   * Jestem **użytkownikiem tylko w chmurze** i znają moje hasło.
      * Chcę **zmienić** moje hasło na nowe.
   * Ta funkcja jest uwzględniona we wszystkich wersjach usługi Azure AD.

* Samoobsługowe resetowanie haseł użytkowników w chmurze
   * Jestem **użytkownikiem tylko w chmurze** i zapomniano mojego hasła.
      * Chcę **zresetować** moje hasło do mnie.
   * Ta funkcja jest uwzględniona w Azure AD — wersja Premium P1 lub P2, Microsoft 365 Business lub Office 365.

* Samoobsługowe resetowanie/zmiana/odblokowanie hasła **przy użyciu lokalnego zapisywania zwrotnego**
   * Jestem **użytkownikiem hybrydowym** mojego lokalnego Active Directory konto użytkownika jest synchronizowane z kontem usługi Azure AD przy użyciu Azure AD Connect. Chcę zmienić moje hasło, zapomniano moje hasło lub zostało zablokowane.
      * Chcę zmienić moje hasło lub zresetować je do czegoś znanego lub Odblokuj moje konto, **a** następnie zmień synchronizację z powrotem do Active Directory lokalnego.
   * Ta funkcja jest uwzględniona w Azure AD — wersja Premium P1 lub P2 lub Microsoft 365 Business.

> [!WARNING]
> Autonomiczne plany licencjonowania pakietu Office 365 *nie obsługują funkcji samoobsługowego resetowania hasła/zmiany/odblokowywania przy użyciu lokalnego zapisywania zwrotnego* i wymagają planu, który obejmuje Azure AD — wersja Premium P1, Premium P2 lub Microsoft 365 Business, aby ta funkcja działała.
>

Dodatkowe informacje o licencjonowaniu, w tym koszty, można znaleźć na następujących stronach:

* [Azure Active Directory witryna cenowa](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory funkcje i możliwości](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Opis usługi Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Włączanie licencjonowania grupowego lub użytkownika

Usługa Azure AD obsługuje teraz Licencjonowanie oparte na grupach. Administratorzy mogą przypisywać licencje zbiorczo do grupy użytkowników, a nie przypisywać ich pojedynczo. Aby uzyskać więcej informacji, zobacz [Przypisywanie, weryfikowanie i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do użytkownika, administrator musi określić właściwość **Lokalizacja użycia** dla użytkownika. Przypisanie licencji można wykonać w sekcji**ustawień** **profilu** >  **użytkownika** > w Azure Portal. *W przypadku korzystania z przypisania licencji grupy Wszyscy użytkownicy bez określonej lokalizacji użycia dziedziczą lokalizację katalogu.*

## <a name="next-steps"></a>Następne kroki

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
