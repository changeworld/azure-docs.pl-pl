---
title: W środowisku lokalnym ochrony haseł usługi Azure AD — często zadawane pytania
description: W środowisku lokalnym ochrony haseł usługi Azure AD — często zadawane pytania
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: f690625fda07bdbff671567f0292cc8e1cabeda1
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078699"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Wersja zapoznawcza: Azure AD hasło ochrony lokalnych — często zadawane pytania

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Pytania ogólne

**Pyt.: Gdy ochrona za pomocą hasła usługi Azure AD będzie korzystał z ogólna dostępność (GA)?**

Nie ogłosiliśmy jeszcze datę wersji ogólnie dostępnej.

**Pyt.: Działa lokalnie obsługiwana w chmurach bez publicznego ochrona za pomocą hasła usługi Azure AD?**

Nie — ochrona za pomocą hasła usługi Azure AD w środowisku lokalnym jest obsługiwana tylko w chmurze publicznej.

**Pyt.: Jak zastosować korzyści ochrony haseł usługi Azure AD do podzbioru użytkowników w środowisku lokalnym?**

Nieobsługiwane. Po wdrożeniu i włączone, ochrona za pomocą hasła usługi Azure AD nie rozróżniania — wszyscy użytkownicy otrzymują korzyści równy zabezpieczeń.

**Pyt.: Instalacja ochrona za pomocą hasła usługi Azure AD równolegle z innymi produktami na podstawie filtru haseł jest obsługiwana?**

Tak. Obsługa wielu biblioteki DLL filtru haseł zarejestrowanych jest funkcją Windows podstawowych i nie odnoszą się do ochrony hasłem w usłudze Azure AD. Wszystkie biblioteki DLL filtru haseł zarejestrowanych musi wyrazić zgodę, zanim zostanie zaakceptowane hasła.

**Pyt.: Dlaczego DFSR jest wymagany do replikacji folderu sysvol**

Usługa replikacji plików (technologia poprzedzająca DFSR) ma wiele znanych problemów i jest całkowicie obsługiwana w nowszych wersjach systemu Windows Server Active Directory. Zero testowania ochrona za pomocą hasła usługi Azure AD zostanie wykonane w przypadku skonfigurowana usługi FRS domen.

Aby uzyskać więcej informacji zobacz następujące artykuły:

[W przypadku replikacji folderu sysvol Migrowanie do DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Punkt końcowy jest Nigh w przypadku usługi FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**Pyt.: Dlaczego jest wymagane ponowne uruchomienie Zainstaluj lub Uaktualnij oprogramowanie agenta kontrolera domeny?**

To wymaganie jest spowodowany przez podstawowe zachowanie Windows.

**Pyt.: Czy istnieje sposób można skonfigurować agenta kontrolera domeny, aby użyć konkretnego serwera proxy?**

Nie.

## <a name="next-steps"></a>Kolejne kroki

Jeśli masz lokalne usługi Azure AD ochrony pytanie dotyczące hasła, nie ma tutaj odpowiedzi, należy przesłać elementu opinii poniżej. Dziękujemy!

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
