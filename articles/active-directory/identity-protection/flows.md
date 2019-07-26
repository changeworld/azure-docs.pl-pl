---
title: Środowisko logowania za pomocą Azure AD Identity Protection | Microsoft Docs
description: Zawiera przegląd środowiska użytkownika w przypadku, gdy Ochrona tożsamości została ograniczona lub skorygowana przez użytkownika lub gdy zasady wymagają uwierzytelniania wieloskładnikowego.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e513027eed44ec7649f41f8786882aed8511bc6
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335494"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Środowisko logowania za pomocą Azure AD Identity Protection

Za pomocą Azure Active Directory Identity Protection można:

* Wymagaj, aby użytkownicy rejestrowali się w celu korzystania z uwierzytelniania wieloskładnikowego
* Obsługa ryzykownych logowań i narażonych użytkowników

Odpowiedź systemu na te problemy ma wpływ na środowisko logowania użytkownika, ponieważ nie będzie już możliwe logowanie się bezpośrednio przez podanie nazwy użytkownika i hasła. Dodatkowe kroki są wymagane, aby użytkownik mógł bezpiecznie zalogować się do firmy.

Ten artykuł zawiera omówienie środowiska logowania użytkownika w przypadku wszystkich przypadków, które mogą wystąpić.

**Multi-Factor Authentication**

* Rejestracja w usłudze uwierzytelniania wieloskładnikowego

**Logowanie na ryzyko**

* Ryzykowne odzyskiwanie do logowania
* Ryzykowne logowanie zostało zablokowane
* Rejestracja w usłudze uwierzytelniania wieloskładnikowego podczas ryzykownego logowania

**Użytkownik narażony na ryzyko**

* Odzyskiwanie naruszonego konta
* Naruszone konto zostało zablokowane

## <a name="multi-factor-authentication-registration"></a>Rejestracja w usłudze uwierzytelniania wieloskładnikowego
Najlepszym środowiskiem użytkownika dla obu systemów, złamany przepływ odzyskiwania konta i przepływ ryzykownego logowania jest możliwość samozyskania sprawności. Jeśli użytkownicy są zarejestrowani do uwierzytelniania wieloskładnikowego, mają już przypisany numer telefonu do konta, za pomocą którego można zdać wyzwania w zakresie zabezpieczeń. W celu odzyskania naruszenia zabezpieczeń kont nie jest konieczne zaangażowanie pomocy technicznej ani administratora. Dlatego zdecydowanie zaleca się, aby użytkownicy byli zarejestrowani do uwierzytelniania wieloskładnikowego. 

Administratorzy mogą ustawić zasady, które wymagają od użytkowników skonfigurowania ich kont w celu przeprowadzenia dodatkowej weryfikacji zabezpieczeń. Te zasady umożliwiają użytkownikom pomijanie rejestracji w usłudze uwierzytelniania wieloskładnikowego przez maksymalnie 14 dni. Nie można skonfigurować 14-dniowego okresu prolongaty.

**Rejestracja w usłudze wieloskładnikowe uwierzytelnianie obejmuje trzy kroki:**

1. W pierwszym kroku użytkownik otrzymuje powiadomienie o wymaganiu ustawienia konta do uwierzytelniania wieloskładnikowego. 
   
    ![Korygowanie](./media/flows/140.png "Korygowanie")
2. Aby skonfigurować uwierzytelnianie wieloskładnikowe, należy poinformować system o tym, jak chcesz się z Tobą skontaktować.
   
    ![Korygowanie](./media/flows/141.png "Korygowanie")
3. System przesyła wyzwanie do Ciebie i musisz odpowiedzieć.
   
    ![Korygowanie](./media/flows/142.png "Korygowanie")

## <a name="risky-sign-in-recovery"></a>Ryzykowne odzyskiwanie do logowania
Gdy administrator skonfigurował zasady dotyczące ryzyka związanego z logowaniem, użytkownicy, których to dotyczy, są powiadamiani, gdy spróbują się zalogować. 

**Przepływ ryzykownej rejestracji ma dwie czynności:** 

1. Użytkownik jest informowany o wykryciu nietypowej rejestracji, na przykład w przypadku logowania się z nowej lokalizacji, urządzenia lub aplikacji. 
   
    ![Korygowanie](./media/flows/120.png "Korygowanie")
2. Użytkownik musi potwierdzić swoją tożsamość, rozwiązując wyzwanie dotyczące zabezpieczeń. Jeśli użytkownik jest zarejestrowany na potrzeby uwierzytelniania wieloskładnikowego, muszą one obsłużyć kod zabezpieczający, aby obsłużyć numer telefonu. Ponieważ jest to tylko ryzykowne logowanie i nie jest to naruszone konto, użytkownik nie będzie musiał zmieniać hasła w tym przepływie. 
   
    ![Korygowanie](./media/flows/121.png "Korygowanie")

## <a name="risky-sign-in-blocked"></a>Ryzykowne logowanie zostało zablokowane
Administratorzy mogą również ustawić zasady dotyczące ryzyka związanego z logowaniem, aby blokować użytkownikom logowanie się w zależności od poziomu ryzyka. Aby uzyskać odblokowywanie, użytkownicy końcowi muszą skontaktować się z administratorem lub pracownikiem działu pomocy technicznej lub mogą próbować zalogować się ze znajomej lokalizacji lub urządzenia. Samoobsługowe odzyskiwanie przez rozwiązanie uwierzytelniania wieloskładnikowego nie jest opcją w tym przypadku.

![Korygowanie](./media/flows/200.png "Korygowanie")

## <a name="compromised-account-recovery"></a>Odzyskiwanie naruszonego konta
W przypadku skonfigurowania zasad zabezpieczeń dotyczących ryzyka związanego z użytkownikiem, którzy spełniają poziom ryzyka użytkownika określony w zasadach (w związku z czym zakłada się naruszenie zabezpieczeń), przed zalogowaniem się użytkownik musi przejść przez ten proces. 

**Przepływ odzyskiwania przez użytkownika narusza trzy kroki:**

1. Użytkownik jest informowany, że zabezpieczenia konta są zagrożone ze względu na podejrzane działanie lub nieujawnione poświadczenia.
   
    ![Korygowanie](./media/flows/101.png "Korygowanie")
2. Użytkownik musi potwierdzić swoją tożsamość, rozwiązując wyzwanie dotyczące zabezpieczeń. Jeśli użytkownik jest zarejestrowany na potrzeby uwierzytelniania wieloskładnikowego, może to spowodować naruszenie zabezpieczeń. Będą musieli zaokrąglić kod zabezpieczający do numeru telefonu. 
   
   ![Korygowanie](./media/flows/110.png "Korygowanie")
3. Na koniec użytkownik musi zmienić hasło, ponieważ ktoś inny mógł uzyskać dostęp do swojego konta. 
   Zrzuty ekranu tego środowiska są poniżej.
   
   ![Korygowanie](./media/flows/111.png "Korygowanie")

## <a name="compromised-account-blocked"></a>Naruszone konto zostało zablokowane
Aby uzyskać użytkownika, który został zablokowany przez zasady zabezpieczeń dotyczące ryzyka użytkownika, należy skontaktować się z administratorem lub pomocą techniczną. Samoobsługowe odzyskiwanie przez rozwiązanie uwierzytelniania wieloskładnikowego nie jest opcją w tym przypadku.

![Korygowanie](./media/flows/104.png "Korygowanie")

## <a name="reset-password"></a>Resetowanie hasła
Jeśli użytkownicy z naruszonymi zabezpieczeniami nie zostaną zarejestrowani, administrator może wygenerować tymczasowe hasło dla nich. Użytkownicy będą musieli zmienić swoje hasło podczas kolejnego logowania.

![Korygowanie](./media/flows/160.png "Korygowanie")

## <a name="see-also"></a>Zobacz także

* [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md) 
