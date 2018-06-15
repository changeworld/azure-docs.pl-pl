---
title: Włączyć usługę MFA dla wszystkich administratorów platformy Azure
description: Wskazówki dotyczące włączania Administrator globalny
ms.service: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: a7a08c54fe0c59e1e100e1c46e7a640da0692077
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181018"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Wymusić uwierzytelnianie wieloskładnikowe (MFA) dla administratorów subskrypcji

Podczas tworzenia Administratorzy w Twojej organizacji, łącznie z konta administratora globalnego jest istotne, że używasz metody bardzo silnego uwierzytelniania.

Można wykonywać codzienne zadania administracyjne poprzez przypisywanie ról administratora określonych — takie jak administrator programu Exchange lub administrator haseł — na konta użytkowników pracowników IT zgodnie z potrzebami.
Ponadto włączenie [Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) administratorami dodaje drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. Usługa Azure MFA ułatwia również IT zmniejszyć prawdopodobieństwo, że przejęciem poświadczeń dostępu do danych organizacji.

Na przykład: Wymuszanie usługi Azure MFA dla użytkowników i skonfigurować go do korzystania z połączenia telefonicznego lub wiadomości tekstowej jako weryfikacji. Naruszenie poświadczenia użytkownika, atakujący będzie mógł dostęp do dowolnych zasobów, ponieważ on nie ma dostępu do telefonu użytkownika. Organizacje, które nie należy dodawać dodatkowych warstw ochrony tożsamości są bardziej narażony na atak kradzieży poświadczeń, co może prowadzić do naruszenia danych.

Jeden alternatywą dla organizacji, które mają być zachowane cały proces uwierzytelniania sterowania lokalnej jest użycie [serwera usługi Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), nazywane również "MFA lokalnymi". Za pomocą tej metody, nadal będzie mógł wymusić uwierzytelnianie wieloskładnikowe, przy zachowaniu MFA serwera lokalnego.

Aby sprawdzić, kto w organizacji ma uprawnienia administracyjne, które można sprawdzić za pomocą następującego polecenia programu PowerShell V2 programu Microsoft Azure AD:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Włączenie uwierzytelniania MFA

Przegląd sposobu [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) działa przed kontynuowaniem.

Dopóki użytkownicy będą mieli licencje obejmujące usługę Azure Multi-Factor Authentication, do jej włączenia nie będzie wymagane wykonanie żadnych czynności. Możliwe jest rozpoczęcie wymagania weryfikacji dwuetapowej dla indywidualnych użytkowników. Licencje, które umożliwiają włączenie usługi Azure MFA, to:

- Azure Multi-Factor Authentication
- Usługa Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Włączanie weryfikacji dwuetapowej dla użytkowników

Jedną z procedur wymienionych w [sposobu wymagać weryfikacji dwuetapowej](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) dla użytkownika lub grupy rozpocząć korzystanie z usługi Azure MFA. Weryfikację dwuetapową możesz wymusić dla wszystkich logowań lub utworzyć zasady dostępu warunkowego, które wymuszają weryfikację dwuetapową tylko wtedy, gdy jest to pożądane.

