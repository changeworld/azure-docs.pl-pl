---
title: Licencja samoobsługowego resetowania haseł — Azure Active Directory
description: Usługa Azure AD samoobsługowego resetowania haseł wymagania licencyjne
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 64c12177e5cf6c82018731b493c0da22e1895b7f
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855528"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Resetuj wymaganiach dotyczących licencjonowania dla samoobsługowego hasła usługi Azure AD

Aby funkcji, resetowania hasła usługi Azure Active Directory (Azure AD) możesz *musi mieć co najmniej jedną licencję przypisaną do organizacji* dla tego użytkownika. Wymagana jest właściwa licencja, jeśli użytkownik odnosi korzyści z dowolnej funkcji objętej licencją w sposób bezpośredni lub pośredni.

* **Użytkownicy tylko w chmurze**: usługi Office 365 wszystkich płatnych jednostek SKU lub usługi Azure AD podstawowa
* **Chmura** lub **użytkowników lokalnych**: Azure AD Premium P1 lub P2, pakietu Enterprise Mobility + Security (EMS) lub Microsoft 365

## <a name="licensing-requirements-for-password-writeback"></a>Wymagania licencyjne dla funkcji zapisywania zwrotnego haseł

**Samoobsługowe hasło Resetowanie/zmiana/odblokowywanie przy użyciu funkcji zapisywania zwrotnego w środowisku lokalnym jest funkcją premium usługi Azure AD**. Aby uzyskać więcej informacji na temat licencjonowania, zobacz [usługi Azure Active Directory ceny witryny](https://azure.microsoft.com/pricing/details/active-directory/).

Aby użyć funkcji zapisywania zwrotnego haseł, musi mieć jeden z następujących licencji, które są przypisane w dzierżawie usługi:

* Usługa Azure AD — warstwa Premium P1
* Usługa Azure AD — warstwa Premium P2
* Pakiet Enterprise Mobility + Security E3 lub A3
* Pakiet Enterprise Mobility + Security E5 lub A5
* Rozwiązania Microsoft 365 E3 lub A3
* Rozwiązania Microsoft 365 E5 lub A5
* Rozwiązania Microsoft 365 F1

> [!WARNING]
> Licencjonowanie plany usługi Office 365 autonomiczny *nie obsługują funkcję zapisywania zwrotnego haseł* i wymagają jednego z poprzednich planów dla tej funkcji do pracy.
>

Dodatkowe informacje o licencjonowaniu, wraz z kosztami, można znaleźć na następujących stronach:

* [Usługa Azure Active Directory, cennik lokacji](https://azure.microsoft.com/pricing/details/active-directory/)
* [Usługa Azure Active Directory funkcje i możliwości](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Włącz grupy lub Licencjonowanie oparte na użytkownika

Teraz usługi Azure AD obsługuje Licencjonowanie na podstawie grupy. Administratorzy mogą przypisywać licencje zbiorcze do grupy użytkowników, a nie przypisując im pojedynczo. Aby uzyskać więcej informacji, zobacz [przypisania, sprawdź i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Niektóre usługi firmy Microsoft nie są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do użytkownika, administrator musi określić **lokalizacji użytkowania** właściwości użytkownika. Przypisanie licencji może odbywać się w obszarze **użytkownika** > **profilu** > **ustawienia** sekcji w witrynie Azure portal. *Gdy używasz przypisanie licencji do grupy, wszyscy użytkownicy bez określonej lokalizacji użytkowania dziedziczą lokalizację katalogu.*

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
