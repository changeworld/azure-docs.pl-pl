---
title: Raporty dotyczące dostępu i użycia usługi Azure MFA | Dokumentacja firmy Microsoft
description: Opisuje sposób użycia funkcji usługi Azure Multi-Factor Authentication — raportów.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: ead1c5a899057bb26154b45c75251e7d9e481147
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160896"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Raporty w usłudze Azure Multi-Factor Authentication

Usługa Azure Multi-Factor Authentication zawiera kilka raportów, które mogą być używane przez Ciebie i Twojej organizacji, które są dostępne za pośrednictwem witryny Azure portal. W poniższej tabeli wymieniono dostępne raporty:

| Raport | Lokalizacja | Opis |
|:--- |:--- |:--- |
| Historia zablokowanego użytkownika | Usługa Azure AD > serwer usługi MFA > Blokowanie/odblokowywanie użytkowników | Przedstawia historię żądań zablokowania lub odblokowania użytkowników. |
| Alerty użycia i nadużyć | Usługa Azure AD > logowania | Zawiera informacje na temat użycia ogólnej, podsumowanie dotyczące użytkowników i szczegóły użytkownika; jak również historię alertów oszustwa przesłanych w zakresie dat., określony. |
| Użycie lokalnych składników | Usługa Azure AD > serwer usługi MFA > raport aktywności | Udostępnia informacje na temat ogólne użycie usługi MFA za pośrednictwem rozszerzenia serwera NPS, usług AD FS, a serwer usługi MFA. |
| Historia pominiętego użytkownika | Usługa Azure AD > serwer usługi MFA > jednorazowe obejście | Zawiera historię żądań ominięcia usługi Multi-Factor Authentication dla użytkownika. |
| Stan serwera | Usługa Azure AD > serwer usługi MFA > Stan serwera | Wyświetla stan serwerów usługi Multi-Factor Authentication skojarzonych z Twoim kontem. |

## <a name="view-reports"></a>Wyświetlanie raportów 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, wybierz **usługi Azure Active Directory** > **serwera MFA**.
3. Wybierz raport, który chcesz wyświetlić.

   <center>![Chmura](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>Raportowanie programu PowerShell

Zidentyfikuj użytkowników, którzy zarejestrowali na potrzeby usługi MFA za pomocą programu PowerShell, który następuje po.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Zidentyfikuj użytkowników, którzy nie zarejestrowano usługi MFA za pomocą programu PowerShell, która jest zgodna.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Kolejne kroki

* [Dla użytkowników](../user-help/multi-factor-authentication-end-user.md)
* [Miejsca wdrożenia](concept-mfa-whichversion.md)
