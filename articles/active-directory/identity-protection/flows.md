---
title: Logowania środowisk przy użyciu usługi Azure AD Identity Protection | Dokumentacja firmy Microsoft
description: Zawiera omówienie środowiska użytkownika, gdy Identity Protection zawiera skorygowane lub rozwiązane przez użytkownika lub uwierzytelnianie wieloskładnikowe jest wymagany przez zasady.
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 449f808e98c4e0db2972071e160f5335153a88f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60295298"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Środowisko logowania za pomocą usługi Azure AD Identity Protection
Za pomocą usługi Azure Active Directory Identity Protection możesz wykonywać następujące czynności:

* Wymagaj od użytkowników rejestracji do uwierzytelniania wieloskładnikowego
* ryzykowne logowania i użytkowników ze złamanymi zabezpieczeniami

Odpowiedzi systemu na te problemy, ma wpływ na środowisko logowania dla użytkownika, ponieważ bezpośrednio logowania, podając nazwę użytkownika i hasło nie będzie możliwe już. Dodatkowe kroki są wymagane do bezpiecznego Pobierz użytkownika do firmy.

Ten artykuł zawiera omówienie środowiska logowania dla użytkownika, dla wszystkich przypadków, które mogą wystąpić.

**Multi-Factor Authentication**

* Rejestracja usługi Multi-Factor authentication

**Zaloguj się na ryzyko**

* Ryzykowne logowania odzyskiwania
* Ryzykowne logowanie zablokowane
* Rejestracja usługi Multi-Factor authentication podczas ryzykowne logowania

**Użytkownik na ryzyko**

* Zagrożone konto odzyskiwania
* Zagrożone Konto zablokowane

## <a name="multi-factor-authentication-registration"></a>Rejestracja usługi Multi-Factor authentication
Najlepsze środowisko użytkownika w obu przypadkach przepływu odzyskiwania konta ze złamanymi zabezpieczeniami oraz ryzykownych przepływu logowania, jest, gdy użytkownik samodzielnie można odzyskać. Jeśli użytkownicy są zarejestrowani do uwierzytelniania wieloskładnikowego, mają numer telefonu skojarzony z ich konta, który może służyć do przekazywania wyzwaniach związanych z zabezpieczeniami. Nie pomocy technicznej lub administratorem zaangażowania jest niezbędny do odzyskania przed naruszeniem konta. W związku z tym ma zdecydowanie zaleca się zachęcić użytkowników zarejestrowany do uwierzytelniania wieloskładnikowego. 

Administratorzy mogą ustawić zasady, która wymaga od użytkowników skonfigurować swoje konta w dodatkowej weryfikacji zabezpieczeń. Ta zasada umożliwia użytkownikom pominięcie rejestracji uwierzytelniania wieloskładnikowego przez okres do 14 dni. 14-dniowy okres prolongaty nie jest konfigurowany.

**Rejestracja usługi Multi-Factor authentication ma trzy kroki:**

1. W pierwszym kroku użytkownik otrzymuje powiadomienie o konieczności Ustaw konto dla usługi Multi-Factor authentication. 
   
    ![Korygowanie](./media/flows/140.png "korygowania")
2. Aby skonfigurować uwierzytelnianie wieloskładnikowe, należy pozostawić wiedzieć, jak chcesz otrzymywać.
   
    ![Korygowanie](./media/flows/141.png "korygowania")
3. System przesyła żądanie do możesz i chcesz odpowiedzieć.
   
    ![Korygowanie](./media/flows/142.png "korygowania")

## <a name="risky-sign-in-recovery"></a>Ryzykowne logowania odzyskiwania
Gdy administrator skonfigurował zasady ryzyka logowania, których to dotyczy użytkownicy są powiadamiani, gdy użytkownik próbuje zalogować. 

**Ryzykowne logowania przepływ ma dwa kroki:** 

1. Użytkownik jest informowany o wykryciu złośliwego coś nietypowego ich logowania, takie jak logowania z nowej lokalizacji, urządzenia lub aplikacji. 
   
    ![Korygowanie](./media/flows/120.png "korygowania")
2. Użytkownik musi potwierdzić swoją tożsamość przy rozwiązywaniu test zabezpieczeń. Jeśli użytkownik jest zarejestrowany do uwierzytelniania wieloskładnikowego, które są im potrzebne do zaokrąglenia przełączył kod zabezpieczeń pod numerem telefonu. Ponieważ jest to po prostu ryzykowne logowania i naruszeniu bezpieczeństwa konta, użytkownik nie ma konieczności zmiany hasła, w tym przepływie. 
   
    ![Korygowanie](./media/flows/121.png "korygowania")

## <a name="risky-sign-in-blocked"></a>Ryzykowne logowanie zablokowane
Administratorzy mogą też ustawić zasady ryzyka logowania w celu zablokowania użytkowników podczas logowania się w zależności od poziomu zagrożenia. Aby uzyskać odblokowany, użytkownicy końcowi musisz skontaktować się z administratorem lub pomocą techniczną lub użytkownik podejmie próbę logowania z dobrze znanych lokalizacji lub urządzenia. Samodzielnie odzyskiwanie przez rozwiązania usługi Multi-Factor authentication nie jest opcją w tym przypadku.

![Korygowanie](./media/flows/200.png "korygowania")

## <a name="compromised-account-recovery"></a>Zagrożone konto odzyskiwania
Po skonfigurowaniu zasad zabezpieczeń ryzyka użytkownika użytkowników, którzy spełniają użytkownika o podwyższonym ryzyku poziom określonym w zasadach (i dlatego są uznawane za naruszenia zabezpieczeń) musi przechodzić przez przepływ odzyskiwania naruszenia zabezpieczeń użytkownika, aby mógł się zalogować. 

**Przepływ odzyskiwania naruszenia zabezpieczeń użytkownika ma trzy kroki:**

1. Użytkownik jest informowany, że jego zabezpieczenia konta są zagrożone ze względu na podejrzane działania lub wyciek poświadczeń.
   
    ![Korygowanie](./media/flows/101.png "korygowania")
2. Użytkownik musi potwierdzić swoją tożsamość przy rozwiązywaniu test zabezpieczeń. Jeśli użytkownik jest zarejestrowany do uwierzytelniania Multi-Factor Authentication będą oni mogli samodzielnie odzyskiwać złamaniu. Muszą round spowodowanych kod zabezpieczeń pod numerem telefonu. 
   
   ![Korygowanie](./media/flows/110.png "korygowania")
3. Ponadto użytkownik jest zmuszony do zmiany hasła, ponieważ ktoś inny mógł logować się do swojego konta. 
   To środowisko zrzuty ekranu można znaleźć poniżej.
   
   ![Korygowanie](./media/flows/111.png "korygowania")

## <a name="compromised-account-blocked"></a>Zagrożone Konto zablokowane
Aby uzyskać użytkownika, który został zablokowany przez zasady zabezpieczeń ryzyka użytkownika zostało odblokowane, użytkownik musi skontaktować się z administratorem lub pomocy technicznej. Samodzielnie odzyskiwanie przez rozwiązania usługi Multi-Factor authentication nie jest opcją w tym przypadku.

![Korygowanie](./media/flows/104.png "korygowania")

## <a name="reset-password"></a>Resetowanie hasła
Po zablokowaniu użytkownicy ze złamanymi zabezpieczeniami Logowanie administratora można wygenerować hasło tymczasowe dla nich. Użytkownicy będą mieć zmiana hasła podczas następnego logowania.

![Korygowanie](./media/flows/160.png "korygowania")

## <a name="see-also"></a>Zobacz także
* [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md) 

