---
title: Ochrona haseł usługi Azure AD w środowisku lokalnym — często zadawane pytania
description: Ochrona haseł usługi Azure AD w środowisku lokalnym — często zadawane pytania
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 62bda3a1c9cb9d53578c2d471b9e63d1f0873234
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663302"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Wersja zapoznawcza: Azure AD ochrony hasłem lokalnych — często zadawane pytania

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Pytania ogólne

**Pyt.: Podczas ochrony haseł usługi Azure AD będzie korzystał z ogólna dostępność (GA)?**

GA planowane jest wprowadzenie Q1 CY2019 (końca marca 2019). Dziękujemy wszystkim, który dostarczył opinie na temat funkcji Data — Jesteśmy wdzięczni za jej!

**Pyt.: Czy w środowisku lokalnym ochrony haseł usługi Azure AD w chmurach bez publicznego obsługiwane?**

Nie — ochrony haseł usługi Azure AD w środowisku lokalnym jest obsługiwana tylko w chmurze publicznej.

**Pyt.: Jak zastosować korzyści z ochrony haseł usługi Azure AD do podzbioru użytkowników w środowisku lokalnym?**

Nieobsługiwane. Po wdrożeniu i włączony, ochrony haseł usługi Azure AD nie rozróżniania — wszyscy użytkownicy otrzymują korzyści równy zabezpieczeń.

**Pyt.: Instalowanie ochrony haseł usługi Azure AD równolegle z innymi produktami na podstawie filtru haseł jest obsługiwane?**

Tak. Obsługa wielu biblioteki DLL filtru haseł zarejestrowanych jest funkcją Windows podstawowych i nie odnoszą się do ochrony haseł usługi Azure AD. Wszystkie biblioteki DLL filtru haseł zarejestrowanych musi wyrazić zgodę, zanim zostanie zaakceptowane hasła.

**Pyt.: Dlaczego DFSR jest wymagany do replikacji folderu sysvol**

Usługa replikacji plików (technologia poprzedzająca DFSR) ma wiele znanych problemów i jest całkowicie obsługiwana w nowszych wersjach systemu Windows Server Active Directory. Testowanie zera ochrony haseł usługi Azure AD zostanie wykonane w przypadku skonfigurowana usługi FRS domen.

Aby uzyskać więcej informacji zobacz następujące artykuły:

[W przypadku replikacji folderu sysvol Migrowanie do DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Punkt końcowy jest Nigh w przypadku usługi FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**Pyt.: Dlaczego jest wymagane ponowne uruchomienie Zainstaluj lub Uaktualnij oprogramowanie agenta kontrolera domeny?**

To wymaganie jest spowodowany przez podstawowe zachowanie Windows.

**Pyt.: Czy istnieje sposób można skonfigurować agenta kontrolera domeny, aby użyć konkretnego serwera proxy?**

Nie.

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz pytanie ochrony haseł usługi Azure AD w środowisku lokalnym, który nie ma tutaj odpowiedzi, Prześlij elementu opinii poniżej. Dziękujemy!

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
