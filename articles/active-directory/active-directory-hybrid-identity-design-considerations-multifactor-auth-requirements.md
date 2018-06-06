---
title: Tożsamość hybrydowym — wymagania dotyczące uwierzytelniania wieloskładnikowego Azure | Dokumentacja firmy Microsoft
description: Z kontroli dostępu warunkowego usługi Azure Active Directory sprawdza określonych warunków, można wybrać podczas uwierzytelniania użytkownika i przed zezwoleniem na dostęp do aplikacji. Gdy te warunki są spełnione, użytkownik jest uwierzytelniony i zezwolenie na dostęp do aplikacji.
documentationcenter: ''
services: active-directory
author: femila
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: fe5ac8386394bf659de8d9905827359900f08d93
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801038"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Określić wymagania dotyczące rozwiązania z tożsamością hybrydową uwierzytelniania wieloskładnikowego
W tym świecie mobilności użytkownikom uzyskiwanie dostępu do danych i aplikacji w chmurze i na dowolnym urządzeniu zabezpieczanie informacji stał się podstawowym.  Codziennie jest nowy nagłówek o naruszenie zabezpieczeń.  Mimo że nie ma żadnej gwarancji przed takie naruszenia, uwierzytelnianie wieloskładnikowe, zapewnia dodatkową warstwę zabezpieczeń, aby uniknąć tych naruszeń.
Rozpocznij od Trwa szacowanie wymagań organizacji w usłudze Multi-Factor authentication. Oznacza to co jest organizacji próby zabezpieczenia.  Ocena ważne jest, aby zdefiniować wymagania techniczne dotyczące konfigurowania i umożliwienie użytkownikom organizacji uwierzytelnianie wieloskładnikowe.

> [!NOTE]
> Jeśli nie masz doświadczenia z usługami MFA i jakie operacje, zdecydowanie zaleca się przeczytanie artykułu [co to jest usługa Azure Multi-Factor Authentication?](authentication/multi-factor-authentication.md) przed kontynuować odczytu w tej sekcji.
> 
> 

Upewnij się, że odpowiedzi na następujące czynności:

* Firma chce do zabezpieczania aplikacji firmy Microsoft 
* Jak te aplikacje są publikowane?
* Firma zapewnia dostępu zdalnego, aby umożliwić pracownikom dostęp do lokalnych aplikacji?

Jeśli tak, jakiego typu dostępu zdalnego? Należy również ocenić, gdzie zostaną umieszczone użytkowników, którzy uzyskują dostęp do tych aplikacji. Tej oceny jest innym ważnym krokiem do definiowania strategii odpowiednie uwierzytelnianie wieloskładnikowe. Upewnij się, że należy odpowiedzieć na następujące pytania:

* Gdzie użytkownicy będą znajdować się?
* Mogą one znajdować się w dowolnym?
* Czy firma chce nawiązać ograniczenia zgodnie z lokalizacją użytkownika?

Po zrozumieniu wymagań, należy również ocenić wymagań użytkownika usługi Multi-Factor Authentication. Tej oceny jest ważne, ponieważ będzie definiował wymagania dotyczące wdrażania usługi Multi-Factor authentication. Upewnij się, że należy odpowiedzieć na następujące pytania:

* Znasz użytkowników usługi Multi-Factor authentication?
* Niektóre zastosowania będą wymagane w celu zapewnienia dodatkowego uwierzytelniania?  
  * Jeśli tak, zawsze, gdy pochodzących z sieci zewnętrznych lub podczas uzyskiwania dostępu do określonych aplikacji lub w innych warunkach?
* Użytkownicy wymaga szkolenia dotyczące sposobu instalacji i zaimplementować uwierzytelnianie wieloskładnikowe?
* Jakie są najważniejsze scenariusze, które firma chce włączyć uwierzytelnianie wieloskładnikowe dla użytkowników?

Po odpowiedzi na pytania Wstecz, można określić, czy istnieją już zaimplementowany uwierzytelnianie wieloskładnikowe lokalnymi. Ocena ważne jest, aby zdefiniować wymagania techniczne dotyczące konfigurowania i umożliwienie użytkownikom organizacji uwierzytelnianie wieloskładnikowe. Upewnij się, że należy odpowiedzieć na następujące pytania:

* Czy firma musi ochronę uprzywilejowanych kont za pomocą usługi MFA?
* Czy firma musi włączyć uwierzytelnianie wieloskładnikowe dla niektórych aplikacji dla zachowania zgodności?
* Czy firma musi włączyć uwierzytelnianie wieloskładnikowe dla wszystkich kwalifikujących się użytkowników z tych aplikacji lub tylko administratorzy?
* Czy konieczne jest zawsze włączone uwierzytelnianie MFA lub tylko, gdy użytkownicy są zalogowani poza siecią firmową?

## <a name="next-steps"></a>Kolejne kroki
[Definiowanie strategii wdrażania tożsamości hybrydowej](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Zobacz także
[Omówienie zagadnień dotyczących projektowania](active-directory-hybrid-identity-design-considerations-overview.md)

