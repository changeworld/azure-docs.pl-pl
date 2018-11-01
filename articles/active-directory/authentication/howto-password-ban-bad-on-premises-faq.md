---
title: W środowisku lokalnym ochrony haseł usługi Azure AD — często zadawane pytania
description: W środowisku lokalnym ochrony haseł usługi Azure AD — często zadawane pytania
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 59c89c81f618876de48de66a38e1063eb658fba4
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50743296"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Wersja zapoznawcza: Usługi Azure AD hasło ochrony lokalnych — często zadawane pytania

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Pytania ogólne

**P: kiedy ochrona za pomocą hasła usługi Azure AD będzie korzystał z ogólna dostępność (GA)?**

Nie ogłosiliśmy jeszcze datę wersji ogólnie dostępnej.

**Pytanie: czy w środowisku lokalnym, obsługiwane w chmurach bez publicznego ochrona za pomocą hasła usługi Azure AD?**

Nie — ochrona za pomocą hasła usługi Azure AD w środowisku lokalnym jest obsługiwana tylko w chmurze publicznej.

**P: jak zastosować korzyści ochrony haseł usługi Azure AD do podzbioru użytkowników w środowisku lokalnym?**

Nieobsługiwane. Po wdrożeniu i włączone, ochrona za pomocą hasła usługi Azure AD nie rozróżniania — wszyscy użytkownicy otrzymują korzyści równy zabezpieczeń.

**P: czy obsługiwane instalowanie ochrona za pomocą hasła usługi Azure AD równolegle z innymi produktami na podstawie filtru hasła?**

Tak. Obsługa wielu biblioteki DLL filtru haseł zarejestrowanych jest funkcją Windows podstawowych i nie odnoszą się do ochrony hasłem w usłudze Azure AD. Wszystkie biblioteki DLL filtru haseł zarejestrowanych musi wyrazić zgodę, zanim zostanie zaakceptowane hasła.

**P: Dlaczego DFSR jest wymagany do replikacji folderu sysvol**

Usługa replikacji plików (technologia poprzedzająca DFSR) ma wiele znanych problemów i jest całkowicie obsługiwana w nowszych wersjach systemu Windows Server Active Directory. Zero testowania ochrona za pomocą hasła usługi Azure AD zostanie wykonane w przypadku skonfigurowana usługi FRS domen.

Aby uzyskać więcej informacji zobacz następujące artykuły:

[W przypadku replikacji folderu sysvol Migrowanie do DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Punkt końcowy jest Nigh dla usługi rejestracji urządzeń](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**P: Dlaczego Zainstaluj lub Uaktualnij oprogramowanie agenta kontrolera domeny wymagany jest ponowny rozruch**

To wymaganie jest spowodowany przez podstawowe zachowanie Windows.

**P: czy istnieje jakikolwiek sposób, aby skonfigurować agenta kontrolera domeny, aby użyć konkretnego serwera proxy?**

Nie.

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz lokalne usługi Azure AD ochrony pytanie dotyczące hasła, nie ma tutaj odpowiedzi, należy przesłać elementu opinii poniżej. Dziękujemy!

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
