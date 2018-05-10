---
title: Licencja samoobsługowego resetowania hasła — Azure Active Directory
description: Azure AD samoobsługowego resetowania hasła wymagania licencyjne
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 78d4d721f2821a8365185c0bad6d795c67a75292
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Wymagania dotyczące usługi Azure AD samoobsługi hasła licencjonowania resetowania

Aby usługi Azure Active Directory (Azure AD) resetowania hasła, aby funkcja możesz *musi mieć co najmniej jedną licencję przypisane w Twojej organizacji*. Firma Microsoft nie wymuszają licencjonowania na środowisku resetowania hasła użytkownika. Wymagana jest właściwa licencja, jeśli użytkownik odnosi korzyści z dowolnej funkcji objętej licencją w sposób bezpośredni lub pośredni.

* **Tylko w chmurze użytkowników**: usługi Office 365 żadnego płatnej SKU lub Azure AD podstawowa
* **Chmura** lub **lokalnych użytkowników**: Azure AD Premium P1 lub P2 pakietu Enterprise Mobility + Security (EMS) i Microsoft 365

## <a name="licenses-required-for-password-writeback"></a>Licencjami wymaganymi do zapisywania zwrotnego haseł

Aby użyć funkcji zapisywania zwrotnego haseł, musi mieć jedną z następujących licencji przypisanych na dzierżawy:

* Usługa Azure AD — warstwa Premium P1
* Usługa Azure AD — warstwa Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5
* Microsoft 365 F1

> [!WARNING]
> Plany licencjonowania usługi Office 365 autonomiczny *nie obsługują funkcji zapisywania zwrotnego haseł* i mieć jeden z poprzednim planów dla tej funkcji do pracy.
>

Dodatkowe informacje licencyjne, wraz z kosztami, można znaleźć na następujących stronach:

* [Usługa Azure Active Directory cennik lokacji](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory, funkcje i możliwości](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Włącz grupy lub użytkownika na podstawie licencjonowania

Teraz usługi Azure AD obsługuje oparte na grupach licencji. Administratorzy mogą przypisywać licencje zbiorcze do grupy użytkowników, a nie przypisywanie pojedynczo. Aby uzyskać więcej informacji, zobacz [przypisać, sprawdź i rozwiąż problemy z licencjami](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses).

Pewnych usług firmy Microsoft nie są dostępne we wszystkich lokalizacjach. Zanim można przypisać licencję do użytkownika, administrator musi określić **lokalizacji użytkowania** właściwości użytkownika. Można wykonać przypisania licencji w obszarze **użytkownika** > **profilu** > **ustawienia** sekcji w portalu Azure. *Gdy używasz przypisanie do grupy licencji użytkowników bez użycia lokalizacji określonej dziedziczą lokalizację katalogu.*

## <a name="next-steps"></a>Kolejne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../active-directory-passwords-reset-register.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)
