---
title: Włącz usługę MFA dla wszystkich administratorów platformy Azure
description: Wskazówki dotyczące włączania administratora globalnego
ms.service: security
ms.subservice: security-fundamentals
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: e30ce71a66dd5cb6c810111d359660d875ae97a8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927917"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Wymuszanie uwierzytelniania wieloskładnikowego (MFA) dla administratorów subskrypcji

Podczas tworzenia administratorów, w tym konta administratora globalnego, należy używać bardzo silnych metod uwierzytelniania.

Możesz wykonywać codzienne zadania administracyjne, przypisując określone role administratora, takie jak administrator programu Exchange lub administrator haseł — do kont użytkowników działu IT, zgodnie z potrzebami.
Ponadto włączenie [uwierzytelniania wieloskładnikowego systemu Azure (MFA)](../../active-directory/authentication/multi-factor-authentication.md) dla administratorów powoduje dodanie drugiej warstwy zabezpieczeń do logowania i transakcji użytkownika. Usługa Azure MFA pomaga również ograniczyć prawdopodobieństwo, że naruszone poświadczenie będzie miało dostęp do danych organizacji.

Na przykład: Wymusisz korzystanie z usługi Azure MFA dla użytkowników i skonfigurujesz ją w taki sposób, aby korzystała z połączenia telefonicznego lub wiadomości tekstowej jako weryfikacji. W przypadku naruszenia zabezpieczeń poświadczeń użytkownika atakujący nie będzie mógł uzyskać dostępu do żadnego zasobu, ponieważ nie będzie miał dostępu do telefonu użytkownika. Organizacje, które nie dodawaj dodatkowych warstw ochrony tożsamości, są bardziej podatne na ataki kradzieży poświadczeń, co może prowadzić do złamania danych.

Alternatywą dla organizacji, które chcą zachować całą kontrolę uwierzytelniania lokalnie, jest korzystanie z [serwera usługi Azure](../../active-directory/authentication/howto-mfaserver-deploy.md)MFA, nazywanego również "uwierzytelnianiem lokalnym". Korzystając z tej metody, nadal będzie można wymusić uwierzytelnianie wieloskładnikowe, zachowując jednocześnie lokalny serwer usługi MFA.

Aby sprawdzić, kto w organizacji ma uprawnienia administracyjne, można sprawdzić za pomocą następującego polecenia Microsoft Azure AD v2 PowerShell:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Włączanie usługi MFA

Przejrzyj sposób działania usługi [MFA](../../active-directory/authentication/howto-mfa-mfasettings.md) przed kontynuowaniem.

Dopóki użytkownicy będą mieli licencje obejmujące usługę Azure Multi-Factor Authentication, do jej włączenia nie będzie wymagane wykonanie żadnych czynności. Możliwe jest rozpoczęcie wymagania weryfikacji dwuetapowej dla indywidualnych użytkowników. Licencje, które umożliwiają włączenie usługi Azure MFA, to:

- Azure Multi-Factor Authentication
- Usługa Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Włączanie weryfikacji dwuetapowej dla użytkowników

Aby rozpocząć korzystanie z usługi Azure MFA, należy skorzystać z jednej z procedur wymienionych w temacie [jak wymagać weryfikacji](../../active-directory/authentication/howto-mfa-userstates.md) dwuetapowej dla użytkownika lub grupy. Można wybrać opcję wymuszania weryfikacji dwuetapowej dla wszystkich logowań lub można utworzyć zasady dostępu warunkowego, aby wymagać weryfikacji dwuetapowej tylko wtedy, gdy jest to ważne.

