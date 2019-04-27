---
title: Włącz usługę MFA dla wszystkich administratorów systemu Azure
description: Wskazówki dotyczące włączania Administrator globalny
ms.service: security
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: d0479b834f814616e44a1888ab8b958990610820
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611715"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Wymuszanie uwierzytelniania wieloskładnikowego (MFA) dla administratorów subskrypcji

Po utworzeniu administratorów, w tym z kontem administratora globalnego, istotne jest użycie bardzo silnego uwierzytelniania metody.

Można wykonywać codzienne zadania administracyjne przez przypisywanie ról administratorów określonych — np. administrator programu Exchange lub administrator haseł — na konta użytkowników pracowników działu IT, zgodnie z potrzebami.
Ponadto, umożliwiając [usługi Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) dla administratorów, dodaje drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. Usługa Azure MFA pomaga również IT zmniejszyć prawdopodobieństwo, że poświadczenie ze złamanymi zabezpieczeniami będą mieć dostęp do danych organizacji.

Na przykład: Wymuś uwierzytelnianie wieloskładnikowe Azure dla użytkowników i skonfigurować go do korzystania z połączenia telefonicznego lub wiadomości SMS jako weryfikacji. Poświadczenia użytkownika w przypadku naruszenia zabezpieczeń, osoba atakująca nie będzie można uzyskać dostęp do dowolnego zasobu, ponieważ nie będzie mógł dostęp do telefonu użytkownika. Organizacje, które nie należy dodawać dodatkowych warstw ochrony tożsamości są bardziej podatne na ataku kradzieży poświadczeń, co może prowadzić do naruszenia zabezpieczeń danych.

Jeden alternatywą dla organizacji, które chcesz zachować cały proces uwierzytelniania kontroli lokalnej jest użycie [serwer Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), nazywany również "Lokalna Usługa MFA". Za pomocą tej metody, nadal można wymusić uwierzytelnianie wieloskładnikowe, przy jednoczesnym zachowaniu lokalna Usługa MFA server.

Aby sprawdzić, kto w organizacji ma uprawnienia administracyjne, które można sprawdzić za pomocą następującego polecenia programu PowerShell Microsoft Azure AD w wersji 2:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Włączanie usługi MFA

Przegląd sposobu [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) działa przed kontynuowaniem.

Dopóki użytkownicy będą mieli licencje obejmujące usługę Azure Multi-Factor Authentication, do jej włączenia nie będzie wymagane wykonanie żadnych czynności. Możliwe jest rozpoczęcie wymagania weryfikacji dwuetapowej dla indywidualnych użytkowników. Licencje, które umożliwiają włączenie usługi Azure MFA, to:

- Azure Multi-Factor Authentication
- Usługa Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Włączanie weryfikacji dwuetapowej dla użytkowników

Użyj jednej z procedur wymienionych w [jak wymagać weryfikacji dwuetapowej](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) dla użytkownika lub grupy rozpocząć korzystanie z usługi Azure MFA. Weryfikację dwuetapową możesz wymusić dla wszystkich logowań lub utworzyć zasady dostępu warunkowego, które wymuszają weryfikację dwuetapową tylko wtedy, gdy jest to pożądane.

