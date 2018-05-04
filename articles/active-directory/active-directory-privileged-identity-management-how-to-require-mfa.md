---
title: Jak wymusić uwierzytelnianie wieloskładnikowe | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wymusić uwierzytelnianie wieloskładnikowe (MFA) dla uprzywilejowanymi tożsamościami przy rozszerzenia usługi Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 1e3dc4ad-3a6a-4a52-8417-3ca4f84ae05c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 42552b4b8a7b2704a94786518f411f922dbd6a81
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Jak zażądać usługi MFA w usłudze Azure AD Privileged Identity Management
Firma Microsoft zaleca wymusić uwierzytelnianie wieloskładnikowe (MFA) dla wszystkich Administratorzy w Twojej organizacji. Pozwala to ograniczyć ryzyko ataku z powodu którego bezpieczeństwo zostało naruszone hasła.

Możesz wymagać użytkownicy powinni wykonać żądanie uwierzytelniania MFA, po zalogowaniu. Wpis w blogu [MFA dla usługi Office 365 i uwierzytelniania Wieloskładnikowego na platformie Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) porównuje dostępnych w ramach subskrypcji pakietu Office i Azure przy użyciu funkcji zawartych w ofercie Microsoft Azure Multi-Factor Authentication.

Możesz również wymagać, że użytkownicy aktywują rola w programie Azure AD PIM, wykonać żądanie uwierzytelniania MFA. Dzięki temu, jeśli użytkownik nie została ukończona żądanie uwierzytelniania MFA, gdy są one podpisane, będą oni musieli podać Aby to zrobić przez PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Wymagania uwierzytelniania Wieloskładnikowego w Azure AD Privileged Identity Management
W przypadku zarządzania tożsamościami w PIM jako administrator ról uprzywilejowanych, mogą pojawić się alerty, które zalecamy MFA do kont uprzywilejowanych. Kliknij alert zabezpieczeń na pulpicie nawigacyjnym usługi PIM i zostanie otwarty nowy blok zawierający listę kont administratorów, które należy włączyć uwierzytelnianie wieloskładnikowe.  Uwierzytelniania MFA można wymagać, wybierając wiele ról, a następnie klikając polecenie **napraw** przycisku, lub kliknij przycisk wielokropka obok poszczególnych ról i kliknięciu **napraw** przycisku.

> [!IMPORTANT]
> Kliknij prawym przyciskiem myszy obecnie działa tylko usługi Azure MFA z pracą lub konta służbowego, nie kont Microsoft (zazwyczaj konta osobistego używanego do logowania do usług firmy Microsoft, takich jak Skype, Xbox, Outlook.com, itp.). W związku z tym każda osoba, która za pomocą konta Microsoft nie może być administratorem kwalifikujących się nie mogą używać usługi MFA do aktywacji ich ról. Jeśli Ci użytkownicy muszą kontynuować zarządzanie obciążeń przy użyciu konta Microsoft, podnieść poziom ich do administratorów trwałych teraz.
> 
> 

Ponadto wymaganie usługi MFA dla konkretnej roli można zmienić, klikając go w sekcji ról usługi PIM pulpitu nawigacyjnego. Następnie kliknij **ustawienia** w bloku roli, a następnie wybierając **włączyć** w obszarze usługi Multi-Factor authentication.

## <a name="how-azure-ad-pim-validates-mfa"></a>Jak Azure AD PIM weryfikuje MFA
Dostępne są dwie opcje sprawdzania poprawności MFA, gdy użytkownik aktywuje roli.

Jest to najprostsza opcja do usługi Azure MFA dla użytkowników, którzy są Aktywacja ról uprzywilejowanych. W tym celu należy najpierw sprawdź, czy Ci użytkownicy są licencjonowane w razie potrzeby i zarejestrowany dla usługi Azure MFA. Więcej informacji na temat sposobu wykonania tego zadania jest [wprowadzenie do korzystania z usługi Azure Multi-Factor Authentication w chmurze](authentication/howto-mfa-getstarted.md). Jest zalecane, ale nie musi, skonfigurowanie usługi Azure AD, aby wymusić uwierzytelnianie wieloskładnikowe dla tych użytkowników po zalogowaniu. Jest to spowodowane kontroli MFA wykonuje się przez usługi Azure AD PIM samej siebie.

Alternatywnie użytkownicy są uwierzytelniani lokalnie może mieć odpowiedzialny za MFA dostawcy tożsamości. Na przykład, jeśli skonfigurowano usługi federacyjne AD będą musieli uwierzytelnianie za pomocą karty inteligentnej przed uzyskaniem dostępu do usługi Azure AD [zabezpieczanie zasobów w chmurze Azure Multi-Factor Authentication i usług AD FS](authentication/howto-mfa-adfs.md) zawiera instrukcje dotyczące konfigurowania usług AD FS, aby wysyłać oświadczenia do usługi Azure AD. Gdy użytkownik próbuje aktywować rolę, Azure AD PIM będzie akceptować czy MFA została już sprawdzona dla użytkownika po otrzymaniu odpowiednich oświadczeń.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

