---
title: Historia wersji wersji agenta usługi Azure AD hasło ochrony lokalnych
description: W wersji dokumentów i zachowania historii zmian
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: c52c84a1311c30c19356bb8a1287b203faf476fc
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50743261"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>(Wersja zapoznawcza): Historia wersji agenta ochrony haseł usługi Azure AD

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="12100"></a>1.2.10.0

Data wydania: 8/17/2018 r.

Poprawki:

* AzureADPasswordProtectionProxy Zarejestruj się i zarejestruj AzureADPasswordProtectionForest obsługują teraz uwierzytelnianie wieloskładnikowe
* Zarejestruj AzureADPasswordProtectionProxy wymaga WS2012 kontrolera domeny lub nowszym w domenie, aby uniknąć błędów szyfrowania.
* Usługa agenta kontrolera domeny jest bardziej niezawodna dotyczących żądania nowych zasad haseł z platformy Azure przy uruchamianiu.
* Usługa agenta kontrolera domeny będzie żądać nowych zasad haseł z platformy Azure co godzinę, jeśli to konieczne, ale teraz będzie wpływać na losowo wybranej godzinie rozpoczęcia.
* Usługa agenta kontrolera domeny nie jest już spowoduje opóźnienie nieokreślony w nowy anons kontrolera domeny podczas instalacji na serwerze przed jego promocję jako replika.
* Usługa agenta kontrolera domeny będzie obsługiwać ustawienie konfiguracji "Włącz ochrona za pomocą hasła w systemie Windows Server Active Directory"
* Oba instalatory agent i serwer proxy kontrolera domeny będzie teraz obsługiwać uaktualnienia w miejscu, w przypadku uaktualniania do kolejnych wersji.

> [!WARNING]
> Uaktualnienie w miejscu z wersji 1.1.10.3 nie jest obsługiwane i spowodują błąd instalacji. Do uaktualnienia do wersji 1.2.10 lub nowszej, należy najpierw całkowicie odinstalować oprogramowanie usługi agenta i serwer proxy kontrolera domeny, a następnie zainstalowanie nowej wersji od podstaw. Ponowna rejestracja ochrony hasłem usługi Azure AD, usługa serwera Proxy jest wymagany.  Ponownie zarejestrować lasu nie jest wymagany.

> [!NOTE]
> Uaktualnienie oprogramowania agenta kontrolera domeny będzie wymagać ponownego uruchomienia systemu.

* Kontroler domeny usługi agenta i serwer proxy jest teraz obsługiwana na serwerze skonfigurowanym do użycia tylko algorytmów zgodnych ze standardem FIPS.
* Ulepszone rejestrowanie
* Mniejszy przyrost wydajności i niezawodności poprawek

## <a name="11103"></a>1.1.10.3

Data wydania: 6/15/2018 r.

Początkowa publicznej wersji zapoznawczej

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
