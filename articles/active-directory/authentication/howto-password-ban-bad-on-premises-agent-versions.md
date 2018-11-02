---
title: Historia wersji wersji agenta usługi Azure AD hasło ochrony lokalnych
description: W wersji dokumentów i zachowania historii zmian
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 89d64a28d2fe43464995e434c9f3807047b29492
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913640"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>(Wersja zapoznawcza): Historia wersji agenta ochrony haseł usługi Azure AD

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD jest funkcją publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="12250"></a>1.2.25.0

Data wydania: 11/01/2018 r.

Poprawki:

* Kontroler domeny agent i serwer proxy usługi powinna już zakończyć się niepowodzeniem z powodu błędów zaufania certyfikatu.
* Kontroler domeny agent i serwer proxy usługi mają dodatkowe poprawki dla maszyn zgodne ze standardem FIPS.
* Usługa serwera proxy będzie teraz działać prawidłowo w protokół TLS 1.2 — tylko środowisku sieciowym.
* Mniejszy przyrost wydajności i niezawodności poprawek
* Ulepszone rejestrowanie

Zmiany:

* Minimalny wymagany poziom systemu operacyjnego dla usługi serwera Proxy jest teraz systemu Windows Server 2012 R2. Minimalny wymagany poziom systemu operacyjnego dla usługi agenta DC pozostaje w systemie Windows Server 2012.
* Algorytm sprawdzania poprawności hasła używa tabeli normalizacji rozwiniętej znaków. Może to spowodować, że hasła są odrzucane, które zostały zaakceptowane w poprzednich wersjach.

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
* Mniejszy przyrost wydajności i niezawodności poprawek
* Ulepszone rejestrowanie

## <a name="11103"></a>1.1.10.3

Data wydania: 6/15/2018 r.

Początkowa publicznej wersji zapoznawczej

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
