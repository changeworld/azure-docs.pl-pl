---
title: Samoobsługowe resetowanie hasła licencji — usługa Azure Active Directory
description: Wymagania licencjonowania samoobsługowego resetowania haseł usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 598f3bd8500a59cd41cc4126915e6cccbd4fb2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848565"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Wymagania dotyczące licencjonowania samoobsługowego resetowania hasła usługi Azure AD

Usługa Azure Active Directory (Azure AD) jest dostępna w kilku wersjach: Bezpłatna, Premium P1 i Premium P2. Istnieje kilka różnych funkcji, które tworzą samoobsługowe resetowanie hasła, w tym zmiany, resetowania, odblokowywania i zapisywania zwrotnego, które są dostępne w różnych wersjach usługi Azure AD. W tym artykule próbuje wyjaśnić różnice. Więcej szczegółów na temat funkcji zawartych w każdej wersji usługi Azure AD można znaleźć na [stronie cennika usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porównywanie wersji i funkcji

Samoobsługowe resetowanie hasła usługi Azure AD jest licencjonowane na użytkownika, aby utrzymać zgodność organizacje są zobowiązane do przypisania odpowiedniej licencji do swoich użytkowników.

* Samoobsługowa zmiana hasła dla użytkowników chmury
   * Jestem **użytkownikiem tylko** w chmurze i znam swoje hasło.
      * Chciałbym **zmienić** hasło na coś nowego.
   * Ta funkcja jest uwzględniona we wszystkich wersjach usługi Azure AD.

* Samoobsługowe resetowanie hasła dla użytkowników chmury
   * Jestem **użytkownikiem tylko** w chmurze i zapomniałem hasła.
      * Chciałbym **zresetować** hasło do czegoś, co wiem.
   * Ta funkcja jest uwzględniona w usłudze Azure AD Premium P1 lub P2, usłudze Microsoft 365 Business lub usłudze Office 365.

* Samoobsługowe resetowanie/zmienianie/odblokowywanie hasła **za pomocą lokalnego stornowania**
   * Jestem **użytkownikiem hybrydowym,** moje lokalne konto użytkownika usługi Active Directory jest synchronizowane z moim kontem usługi Azure AD przy użyciu usługi Azure AD Connect. Chciałbym zmienić hasło, zapomnieć hasła lub został zablokowany.
      * Chciałbym zmienić hasło lub zresetować go do czegoś, co znam, lub odblokować konto **i** zsynchronizować tę zmianę z powrotem do lokalnej usługi Active Directory.
   * Ta funkcja jest uwzględniona w usłudze Azure AD Premium P1 lub P2 lub Microsoft 365 Business.

> [!WARNING]
> Autonomiczne plany licencjonowania usługi Office 365 *nie obsługują samoobsługowego resetowania/zmieniania/odblokowywania hasła lokalnego za pomocą lokalnego stornowania"* i wymagają planu obejmującego usługę Azure AD Premium P1, Premium P2 lub Microsoft 365 Business, aby ta funkcja działała.
>

Dodatkowe informacje dotyczące licencjonowania, w tym koszty, można znaleźć na następujących stronach:

* [Witryna cenowa usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Funkcje i możliwości usługi Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Opis usługi firmy microsoft 365](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Włączanie licencjonowania grupowego lub opartego na użytkownikach

Usługa Azure AD obsługuje teraz licencjonowanie oparte na grupach. Administratorzy mogą zbiorczo przypisywać licencje do grupy użytkowników, zamiast przypisywać je po kolei. Aby uzyskać więcej informacji, zobacz [Przypisywanie, weryfikowanie i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Aby licencja mogła być przypisana do użytkownika, administrator musi określić właściwość **Lokalizacja użycia** użytkownika. Przypisywanie licencji można wykonać w sekcji**Ustawienia** **profilu** >  **użytkownika** > w witrynie Azure portal. *Podczas korzystania z przypisania licencji grupy, każdy użytkownik bez lokalizacji użycia określony dziedziczy lokalizację katalogu.*

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
* [Myślę, że coś jest zepsute. Jak rozwiązać problem z łatem SSPR?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)
