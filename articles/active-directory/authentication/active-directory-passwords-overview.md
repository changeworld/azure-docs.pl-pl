---
title: Usługa Azure AD samoobsługowego resetowania haseł — omówienie | Dokumentacja firmy Microsoft
description: Co robią resetowania haseł mogą usługi Azure AD dla Twojej organizacji?
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: da63278ad3ae0ec34aa484794e9c910554da1de2
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158764"
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Usługa Azure AD Samoobsługowe resetowanie haseł dla specjalistów IT

Za pomocą usługi Azure Active Directory (Azure AD) samoobsługowego resetowania haseł (SSPR) użytkownicy mogą resetować swoje hasła na ich własnych tam, gdzie istnieje potrzeba. W tym samym czasie Administratorzy mogą kontrolować sposób pobiera resetowania hasła użytkownika. Użytkownicy nie muszą już do wywołania pomocy technicznej, po prostu do zresetowania swojego hasła. Usługa Azure AD SSPR, obejmuje:

* **Zmiana hasła samoobsługi**: użytkownik zna hasło, ale chce ją zmienić na coś nowego.
* **Samoobsługowe resetowanie haseł**: użytkownik nie można się zalogować i chce, aby zresetować swoje hasło za pomocą co najmniej jeden z następujących metod uwierzytelniania zweryfikowanych:
   * Wyślij wiadomość SMS na zweryfikowanych telefon komórkowy.
   * Połączenie telefoniczne należy dokonać zweryfikowanych telefonu komórkowego lub pakietu office.
   * Wyślij wiadomość e-mail na konto zweryfikowanych pomocniczego adresu e-mail.
   * Odpowiedzi na pytania zabezpieczające.
* **Odblokowywanie kont samoobsługi**: użytkownik nie będzie mógł zalogować się przy użyciu hasła i zostało zablokowane. Użytkownik chce, aby odblokować konto bez interwencji administratora przy użyciu metody ich uwierzytelniania.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

## <a name="why-choose-azure-ad-sspr"></a>Dlaczego warto wybrać usługę Azure AD SSPR

Usługa Azure AD SSPR ułatwi Ci:

* **Obniżenie kosztów** jako pomocy technicznej asystowanej resetowania haseł zazwyczaj konta 20% wywołania pomocy technicznej IT w organizacji. 
* **Ulepszenie środowiska użytkownika końcowego** i **ograniczyć liczbę pomocy technicznej** , zapewniając użytkownikom możliwości, aby rozwiązać problemy z ich własnych haseł. Nie ma potrzeby wywołania dział pomocy technicznej lub Otwórz żądanie obsługi.
* **Dysk mobilności** jak użytkownicy mogą resetować swoje hasła niezależnie od miejsca.
* **Obsługa kontroli** zasad zabezpieczeń. Administratorzy mogą w dalszym ciągu wymuszać zasady, które mają już dziś.

Gdy wszystko będzie gotowe, możesz zacząć korzystać z usługi Azure AD SSPR za pomocą naszych [szybki start wskazówki](quickstart-sspr.md). Użytkownicy mogą szybko oferowanie opcję resetowania ich własnych haseł.

## <a name="azure-ad-sspr-availability"></a>Dostępność usługi Azure AD SSPR

Samoobsługowe Resetowanie HASEŁ usługi Azure AD jest dostępna w ramach trzech warstw w zależności od Twojej subskrypcji:

* **Azure AD bezpłatna**: Administratorzy tylko do chmury mogą resetować swoje hasła.
* **Usługa Azure AD — warstwa podstawowa** lub dowolnego **płatnej subskrypcji usługi Office 365**: tylko w chmurze, użytkownicy mogą resetować swoje hasła.
* **Usługa Azure AD Premium**: dowolnego użytkownika lub administratora, w tym tylko w chmurze, federacyjnego, przekazywanego uwierzytelniania lub użytkowników synchronizację skrótów haseł mogą resetować swoje hasła. Hasłami lokalnymi wymagają funkcji zapisywania zwrotnego haseł, aby włączyć.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD cen, umowy SLA, aktualizacji i plan

Więcej szczegółów na temat licencjonowania, cennik i plany na przyszłość można znaleźć w następujących witrynach:

* [Szczegółach cennika usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Cennik usługi Office 365](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Umowy dotyczące poziomu usług platformy Azure](https://azure.microsoft.com/support/legal/sla/)
* [Umowa dotycząca poziomu usług dla Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Aktualizacje platformy Azure](https://azure.microsoft.com/updates/)
* [Harmonogram działania dla platformy Azure](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Kolejne kroki

* Czy chcesz zacząć korzystać z funkcji samoobsługowego resetowania HASEŁ? [Konfigurowanie haseł usługi Azure AD resetowania](quickstart-sspr.md).
* Zaplanować pomyślne wdrożenie funkcji samoobsługowego resetowania HASEŁ dla użytkowników, korzystając z informacji przedstawionych w naszym [Przewodnik wdrożenia](howto-sspr-deployment.md).
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md).
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md).
