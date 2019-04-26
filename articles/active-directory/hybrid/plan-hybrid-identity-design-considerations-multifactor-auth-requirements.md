---
title: Projektowania tożsamości hybrydowej — wymagania dotyczące uwierzytelniania wieloskładnikowego Azure | Dokumentacja firmy Microsoft
description: Kontrola dostępu warunkowego usługi Azure Active Directory sprawdza, czy określonych warunków, wybranego podczas uwierzytelniania użytkownika, a jeśli tak, to przed zezwoleniem na dostęp do aplikacji. Po spełnieniu tych warunków, użytkownik jest uwierzytelniony i zezwolenie na dostęp do aplikacji.
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
ms.openlocfilehash: 3dabb381c16aa107e41c1d556e61e020b8c6a6c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60455741"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Określić wymagania dotyczące uwierzytelniania wieloskładnikowego dla rozwiązania tożsamości hybrydowej
W tym świecie mobilności użytkownikom uzyskiwanie dostępu do danych i aplikacji w chmurze, jak i z dowolnego urządzenia i zabezpieczanie te informacje mają ogromne znaczenie.  Codziennie jest nowy nagłówek o naruszenia zabezpieczeń.  Mimo że nie ma żadnej gwarancji, przed naruszeniami takie, uwierzytelnianie wieloskładnikowe zapewnia dodatkową warstwę zabezpieczeń, aby uniknąć tych naruszeń.
Rozpocznij od oceny wymagania organizacji dotyczące uwierzytelniania wieloskładnikowego. Oznacza to co jest organizacji chcesz zabezpieczyć.  Ocena ważne jest, aby zdefiniować wymagania techniczne dotyczące konfigurowania i umożliwienie użytkownikom organizacje do uwierzytelniania wieloskładnikowego.

Pamiętaj odpowiedzieć na następujące czynności:

* Firma chce do zabezpieczania aplikacji firmy Microsoft 
* Jak te aplikacje są publikowane?
* Firma oferuje dostępu zdalnego, aby pracownicy mogli uzyskiwać dostęp do aplikacji w środowisku lokalnym?

Jeśli tak, jakiego typu dostępu zdalnego? Należy również ocenić, gdzie użytkownicy, którzy uzyskują dostęp do tych aplikacji zostaną umiejscowione. Ocena jest innym ważnym krokiem do określenia strategii odpowiednie uwierzytelnianie wieloskładnikowe. Pamiętaj odpowiedzieć na następujące pytania:

* Gdzie użytkownicy mają się znajdować?
* Mogą one znajdować się znajduje się gdziekolwiek?
* Czy firma chce nawiązać ograniczeniami zależnie od lokalizacji użytkownika?

Po zrozumieniu wymagań, należy również ocenić wymagania użytkownika dotyczące uwierzytelniania wieloskładnikowego. Ocena jest ważne, ponieważ określi wymagania dotyczące wdrażania uwierzytelniania wieloskładnikowego. Pamiętaj odpowiedzieć na następujące pytania:

* Zaczynasz użytkowników przy użyciu uwierzytelniania wieloskładnikowego?
* Do niektórych zastosowań będzie wymagane w celu zapewnienia dodatkowego uwierzytelniania?  
  * Jeśli tak, zawsze, gdy pochodzącego z sieciami zewnętrznymi lub uzyskiwania dostępu do określonych aplikacji lub w innych warunkach?
* Użytkownicy, będzie wymagać szkolenia dotyczące konfiguracji i implementowanie uwierzytelniania wieloskładnikowego?
* Jakie są najważniejsze scenariusze, które firma chce włączyć uwierzytelnianie wieloskładnikowe dla użytkowników?

Po udzieleniu odpowiedzi na pytania Wstecz, można określić, czy istnieją uwierzytelnianie wieloskładnikowe już wdrożone w środowisku lokalnym. Ocena ważne jest, aby zdefiniować wymagania techniczne dotyczące konfigurowania i umożliwienie użytkownikom organizacje do uwierzytelniania wieloskładnikowego. Pamiętaj odpowiedzieć na następujące pytania:

* Czy firma potrzebuje ochrony uprzywilejowanych kont za pomocą usługi MFA?
* Czy firma musi włączyć uwierzytelnianie wieloskładnikowe w przypadku niektórych aplikacji w celu zachowania zgodności?
* Czy firma musi włączyć uwierzytelnianie wieloskładnikowe dla wszystkich uprawnionych użytkowników z tych aplikacji lub tylko dla administratorów?
* Czy potrzebne są zawsze włączone uwierzytelnianie MFA lub tylko wtedy, gdy użytkownicy są zalogowani poza siecią firmową?

## <a name="next-steps"></a>Kolejne kroki
[Definiowanie strategii wdrażania tożsamości hybrydowej](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Zobacz także
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

