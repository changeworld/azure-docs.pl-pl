---
title: Projekt tożsamości hybrydowej — wymagania dotyczące uwierzytelniania wieloskładnikowego Azure | Dokumenty firmy Microsoft
description: Dzięki kontroli dostępu warunkowego usługa Azure Active Directory sprawdza określone warunki, które można wybrać podczas uwierzytelniania użytkownika i przed zezwoleniem na dostęp do aplikacji. Po spełnieniu tych warunków użytkownik jest uwierzytelniony i ma dostęp do aplikacji.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4743195fc79d43571ec79a13b8518edc7e81379b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109292"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Określanie wymagań dotyczących uwierzytelniania wieloskładnikowego dla rozwiązania tożsamości hybrydowej
W tym świecie mobilności, gdy użytkownicy uzyskują dostęp do danych i aplikacji w chmurze i z dowolnego urządzenia, zabezpieczenie tych informacji stało się najważniejsze.  Każdego dnia pojawia się nowy nagłówek o naruszeniu bezpieczeństwa.  Chociaż nie ma żadnej gwarancji przed takimi naruszeniami, uwierzytelnianie wieloskładnikowe zapewnia dodatkową warstwę zabezpieczeń, aby zapobiec tym naruszeniom.
Zacznij od oceny wymagań organizacji dotyczących uwierzytelniania wieloskładnikowego. To jest to, co organizacja stara się zabezpieczyć.  Ta ocena jest ważne, aby zdefiniować wymagania techniczne dotyczące konfigurowania i włączania użytkowników organizacji do uwierzytelniania wieloskładnikowego.

Pamiętaj, aby odpowiedzieć na następujące pytania:

* Czy Twoja firma próbuje zabezpieczyć aplikacje firmy Microsoft? 
* Jak te aplikacje są publikowane?
* Czy Twoja firma zapewnia zdalny dostęp, aby umożliwić pracownikom dostęp do aplikacji lokalnych?

Jeśli tak, jakiego typu dostęp zdalny? Należy również ocenić, gdzie będą znajdować się użytkownicy uzyskujący dostęp do tych aplikacji. Ta ocena jest kolejnym ważnym krokiem do zdefiniowania odpowiedniej strategii uwierzytelniania wieloskładnikowego. Pamiętaj, aby odpowiedzieć na następujące pytania:

* Gdzie mają się znajdować użytkownicy?
* Czy mogą znajdować się w dowolnym miejscu?
* Czy Twoja firma chce ustanowić ograniczenia w zależności od lokalizacji użytkownika?

Po zapoznaniu się z tymi wymaganiami, ważne jest również, aby ocenić wymagania użytkownika dotyczące uwierzytelniania wieloskładnikowego. Ta ocena jest ważna, ponieważ zdefiniuje wymagania dotyczące wprowadzania uwierzytelniania wieloskładnikowego. Pamiętaj, aby odpowiedzieć na następujące pytania:

* Czy użytkownicy są zaznajomieni z uwierzytelnianiem wieloskładnikowym?
* Czy niektóre zastosowania będą wymagane do zapewnienia dodatkowego uwierzytelniania?  
  * Jeśli tak, cały czas, gdy pochodzą z sieci zewnętrznych lub dostęp do określonych aplikacji, lub w innych warunkach?
* Czy użytkownicy będą wymagać przeszkolenia w zakresie konfigurowania i implementowania uwierzytelniania wieloskładnikowego?
* Jakie są kluczowe scenariusze, które firma chce włączyć uwierzytelnianie wieloskładnikowe dla swoich użytkowników?

Po udzieleniu odpowiedzi na poprzednie pytania, będzie można zrozumieć, czy istnieją uwierzytelnianie wieloskładnikowe już zaimplementowane lokalnie. Ta ocena jest ważne, aby zdefiniować wymagania techniczne dotyczące konfigurowania i włączania użytkowników organizacji do uwierzytelniania wieloskładnikowego. Pamiętaj, aby odpowiedzieć na następujące pytania:

* Czy Twoja firma musi chronić uprzywilejowane konta za pomocą usługi MFA?
* Czy twoja firma musi włączyć usługę MFA dla niektórych aplikacji ze względu na zgodność?
* Czy twoja firma musi włączyć usługę MFA dla wszystkich uprawnionych użytkowników tej aplikacji lub tylko administratorów?
* Czy potrzebne jest zawsze włączone uwierzytelnianie usługi MFA lub tylko wtedy, gdy użytkownicy są zalogowani poza siecią firmową?

## <a name="next-steps"></a>Następne kroki
[Definiowanie strategii wdrażania tożsamości hybrydowej](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień projektowych](plan-hybrid-identity-design-considerations-overview.md)

