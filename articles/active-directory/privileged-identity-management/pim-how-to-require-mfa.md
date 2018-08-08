---
title: Instrukcje ustawiania wymogu uwierzytelniania wieloskładnikowego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wymagać uwierzytelniania wieloskładnikowego (MFA) dla uprzywilejowanymi tożsamościami przy użyciu rozszerzenia usługi Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8e1c0fa212b31c05fcc4559f9f8d42b627f0da0e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622851"
---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Jak zażądać usługi MFA w usłudze Azure AD Privileged Identity Management
Firma Microsoft zaleca Wymagaj uwierzytelniania wieloskładnikowego (MFA) dla wszystkich administratorów. Zmniejsza to ryzyko ataku z powodu hasła ze złamanymi zabezpieczeniami.

Możesz wymagać, użytkownicy wykonać żądanie uwierzytelniania Wieloskładnikowego podczas logowania. Wpis w blogu [uwierzytelnianie wieloskładnikowe dla usługi Office 365 i uwierzytelnianie wieloskładnikowe dla usługi Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) porównuje, co obejmuje subskrypcji pakietu Office i platformy Azure, dzięki funkcjom znajdujących się w ramach oferty Microsoft Azure Multi-Factor Authentication.

Może również wymagać, że użytkownicy ukończyć żądanie usługi MFA podczas aktywacji roli w usłudze Azure AD PIM. Dzięki temu, jeśli użytkownik nie została ukończona żądania uwierzytelniania MFA, gdy są one podpisane, ich zostanie wyświetlony monit to zrobić przez usługę PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Wymaganie uwierzytelniania Wieloskładnikowego w usłudze Azure AD Privileged Identity Management
W przypadku zarządzania tożsamościami w usłudze PIM jako administrator ról uprzywilejowanych może zostać wyświetlony alerty, które zalecamy MFA dla kont uprzywilejowanych. Kliknij alert zabezpieczeń na pulpicie nawigacyjnym usługi PIM, a zostanie otwarty nowy blok z listą kont administratora, które należy wymagać uwierzytelniania Wieloskładnikowego.  Uwierzytelniania MFA można wymagać, zaznaczając wiele ról, a następnie klikając polecenie **naprawić** przycisku, lub kliknij wielokropek obok poszczególnych ról a następnie kliknij przycisk **naprawić** przycisku.

> [!IMPORTANT]
> Kliknij prawym przyciskiem myszy teraz usługi Azure MFA działa tylko w przypadku pracy lub konta służbowe, a nie konta Microsoft (zazwyczaj konta osobistego, która jest używana do logowania do usług firmy Microsoft, takich jak Skype, Xbox, Outlook.com itd.). W związku z tym każda osoba korzystająca z konta Microsoft nie może być administrator kwalifikujących się ponieważ nie mogą używać usługi MFA do aktywacji ich ról. Jeśli Ci użytkownicy muszą kontynuować zarządzanie obciążeń przy użyciu konta Microsoft, podnoszenie ich poziomu do stałych administratorów teraz.
> 
> 

Ponadto wymaganie uwierzytelniania Wieloskładnikowego dla konkretnej roli można zmienić, klikając je w sekcji role pulpitu nawigacyjnego usługi PIM. Następnie kliknij **ustawienia** w bloku rolę, a następnie wybierając pozycję **Włącz** w ramach usługi Multi-Factor authentication.

## <a name="how-azure-ad-pim-validates-mfa"></a>Jak usługa Azure AD PIM weryfikuje usługi MFA
Dostępne są dwie opcje umożliwiające walidację MFA, gdy użytkownik aktywuje roli.

Jest to najprostsza opcja korzystaniem z usługi Azure MFA dla użytkowników, którzy aktywowania roli uprzywilejowanej. Aby to zrobić, najpierw sprawdź, czy Ci użytkownicy są licencjonowane w razie potrzeby i zarejestrowany dla usługi Azure MFA. Więcej informacji na temat sposobu wykonania tej czynności znajduje się w [wprowadzenie do usługi Azure Multi-Factor Authentication w chmurze](../authentication/howto-mfa-getstarted.md). Jest to zalecane, ale nie jest wymagane, należy skonfigurować wymuszanie uwierzytelniania Wieloskładnikowego dla tych użytkowników podczas logowania w usłudze Azure AD. Jest to spowodowane kontroli uwierzytelniania Wieloskładnikowego, które zostaną wprowadzone przez usługi Azure AD PIM, sam.

Alternatywnie Jeśli użytkownicy są uwierzytelniani w środowisku lokalnym może mieć dostawcy tożsamości do uwierzytelniania Wieloskładnikowego. Na przykład, jeśli skonfigurowano usługi federacyjnej AD wymagające uwierzytelniania opartego na karty inteligentnej przed uzyskaniem dostępu do usługi Azure AD [zabezpieczanie zasobów w chmurze za pomocą usługi Azure Multi-Factor Authentication i usług AD FS](../authentication/howto-mfa-adfs.md) zawiera instrukcje do konfigurowania usług AD FS do wysyłania oświadczeń do usługi Azure AD. Gdy użytkownik próbuje aktywować rolę, usługi Azure AD PIM będzie akceptować czy MFA została już zweryfikowana użytkownika po odebraniu odpowiednie oświadczenia.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

