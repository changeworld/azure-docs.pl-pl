---
title: Azure AD samoobsługowego resetowania hasła omówienie | Dokumentacja firmy Microsoft
description: Do czego może usługi Azure AD hasła samodzielnego resetowania dla Twojej organizacji?
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7d1103e0ab21d2eac336a0e460156d47012b2a86
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Azure AD samodzielnego resetowania haseł dla specjalistów IT

Z usługi Azure Active Directory (Azure AD) samoobsługowego resetowania hasła (SSPR) użytkownicy mogą zresetować swoje hasła na ich własnych when i gdzie muszą. W tym samym czasie Administratorzy mogą kontrolować sposób pobiera resetowania hasła użytkownika. Użytkownicy nie muszą już wywołać pomocy technicznej, po prostu do zresetowania swojego hasła. Azure AD SSPR obejmuje:

* **Zmiana hasła samoobsługi**: użytkownik zna hasło, ale chce zmienić ją na inną.
* **Samoobsługowego resetowania hasła**: użytkownik nie może się zalogować i chce do zresetowania swojego hasła przy użyciu co najmniej jeden z następujących metod uwierzytelniania zweryfikowanych:
   * Wyślij wiadomość SMS do zweryfikowanych telefonu komórkowego.
   * Rozpoczynanie rozmowy telefonicznej na zweryfikowanych telefon komórkowy lub pakietu office.
   * Wysłanie wiadomości e-mail do konta zweryfikowanych dodatkowej poczty e-mail.
   * Odpowiedzi na pytania zabezpieczające.
* **Odblokowywanie kont samoobsługi**: użytkownik nie może zalogować się przy użyciu hasła i zostało zablokowane. Użytkownik chce, aby odblokować konto bez interwencji administratora przy użyciu ich metod uwierzytelniania.

## <a name="why-choose-azure-ad-sspr"></a>Dlaczego usługi Azure AD SSPR

Azure AD SSPR ułatwia:

* **Obniżenie kosztów** jako pomocy technicznej asystowaną resetowania haseł zwykle konta 20% wywołań obsługi organizacji IT. 
* **Poprawę środowiska użytkownika końcowego** i **ograniczyć liczbę pomocy technicznej** , zapewniając użytkownikom uprawnienia do rozwiązania problemów hasła. Nie istnieje potrzeba połączeń telefonicznych pomocy technicznej lub Otwórz żądanie pomocy technicznej.
* **Dysk mobilności** jak użytkownicy mogą resetować hasła w dowolnym miejscu.
* **Obsługa kontroli** zasad zabezpieczeń. Administratorzy mogą w dalszym ciągu egzekwowanie zasad, które już dziś.

Jeśli wszystko jest gotowe, możesz rozpocząć pracę z usługi Azure AD SSPR za pomocą naszych [szybki start wskazówki](quickstart-sspr.md). Można szybko zapewnić użytkownikom możliwość resetowania haseł.

## <a name="azure-ad-sspr-availability"></a>Dostępność usługi Azure AD SSPR

Azure AD SSPR są dostępne w trzech warstwach w zależności od Twojej subskrypcji:

* **Azure AD wolne**: tylko w chmurze, Administratorzy mogą resetować swoje hasła.
* **Azure AD podstawowa** lub **płatnej subskrypcji usługi Office 365**: tylko w chmurze, użytkownicy mogą resetować swoje hasła.
* **Azure AD Premium**: dowolnego użytkownika lub administratora, w tym tylko w chmurze, federacyjnych, przekazywanego uwierzytelniania, czy użytkownicy synchronizacji skrótów haseł mogą resetować swoje hasła. Hasłami lokalnymi wymagają funkcji zapisywania zwrotnego haseł jest włączony.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD cennik, umowy SLA, aktualizacji i plan

Więcej informacji dotyczących licencjonowania, ceny i przyszłych planów można znaleźć w następujących witrynach:

* [Azure AD szczegóły cennika](https://azure.microsoft.com/pricing/details/active-directory/)
* [Cennik usługi Office 365](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Umowy dotyczące poziomu usług platformy Azure](https://azure.microsoft.com/support/legal/sla/)
* [Umowa dotycząca poziomu usług Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Aktualizacje platformy Azure](https://azure.microsoft.com/updates/)
* [Harmonogram działania dla platformy Azure](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Kolejne kroki

* Czy chcesz teraz rozpocząć pracę z SSPR? [Konfigurowanie usługi Azure AD samoobsługi hasła zresetować](quickstart-sspr.md).
* Planowanie pomyślne wdrożenie SSPR do użytkowników przy użyciu wskazówek w naszym [przewodnik wdrażania](howto-sspr-deployment.md).
* [Resetowanie lub zmienianie hasła](../active-directory-passwords-update-your-own-password.md).
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../active-directory-passwords-reset-register.md).
