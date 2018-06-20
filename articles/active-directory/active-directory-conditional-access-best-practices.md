---
title: Najlepsze rozwiązania dla dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Co to jest się, że należy unikać zrobić podczas konfigurowania zasad dostępu warunkowego i Dowiedz się więcej o rzeczy, o których należy wiedzieć.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 88e6b1a64d4ceff64c1ee74a297e7af0c5a27379
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232508"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory

Z [dostępu warunkowego w usłudze Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), można kontrolować sposób autoryzowanym użytkownikom dostępu aplikacji w chmurze. Ten artykuł zawiera informacje o:

- Rzeczy, o których należy wiedzieć 
- Co to jest należy unikać wykonywania podczas konfigurowania zasad dostępu warunkowego. 

W tym artykule przyjęto założenie, że należy zapoznać się pojęć i terminologii opisane w temacie [co to jest dostępu warunkowego w usłudze Azure Active Directory?](active-directory-conditional-access-azure-portal.md)



## <a name="whats-required-to-make-a-policy-work"></a>Co to są wymagane do zmiany celu zasad pracy?

Podczas tworzenia nowej zasady istnieją nie użytkowników, grup, aplikacji lub wybrać kontroli dostępu.

![Aplikacje w chmurze](./media/active-directory-conditional-access-best-practices/02.png)


Aby działać zgodnie z zasadami, należy skonfigurować:


|Co           | Jak                                  | Dlaczego|
|:--            | :--                                  | :-- |
|**Aplikacje w chmurze** |Musisz wybrać co najmniej jedną aplikację.  | Celem zasad dostępu warunkowego jest umożliwiają kontrolowanie sposobu autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze.|
| **Użytkownicy i grupy** | Musisz wybrać co najmniej jednego użytkownika lub grupę, który jest upoważniony do dostępu do aplikacji w wybranej chmurze. | Zasady dostępu warunkowego, który nie ma użytkowników i grupy przypisane, nigdy nie zostanie wywołany. |
| **Kontrola dostępu** | Musisz wybrać kontroli dostępu co najmniej jeden. | Jeśli warunki są spełnione, procesor zasad musi wiedzieć, co należy zrobić.|




## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="how-are-assignments-evaluated"></a>Jak są analizowane przypisania

Wszystkie przypisania są logicznie **and**. Jeśli masz więcej niż jednego przypisania skonfigurowane, wszystkie przypisania muszą być spełnione do wyzwalania zasad.  

Jeśli potrzebujesz Konfigurowanie warunku lokalizacji, która ma zastosowanie do wszystkich połączeń z spoza sieci organizacji:

- Obejmują **wszystkich lokalizacji**
- Wyklucz **wszystkich zaufanych adresów IP**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Co zrobić, jeśli są blokowane z portalu administratora usługi Azure AD?

Jeśli są zablokowane portalu usługi Azure AD z powodu niepoprawnego ustawienia w zasadach dostępu warunkowego:

- Sprawdź, czy istnieją inne administratorami w organizacji, które jeszcze nie są blokowane. Administrator o dostęp do portalu Azure można wyłączyć zasady, które jest wpływające na swojego konta. 

- Jeśli żaden z administratorami w organizacji można zaktualizować zasad, musisz przesłać żądanie obsługi. Pomoc techniczna firmy Microsoft można przejrzeć i zaktualizować zasady dostępu warunkowego, które uniemożliwiają dostęp.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Co się stanie, jeśli masz zasady w klasycznym portalu Azure i portalu Azure skonfigurowane?  

Obie zasady są wymuszane przez usługę Azure Active Directory, a użytkownik uzyskuje dostęp tylko wtedy, gdy są spełnione wszystkie wymagania.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Co się stanie, jeśli masz zasady w portalu usługi Intune Silverlight i portalu Azure?

Obie zasady są wymuszane przez usługę Azure Active Directory, a użytkownik uzyskuje dostęp tylko wtedy, gdy są spełnione wszystkie wymagania.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Co się dzieje w przypadku wielu zasad dla tego samego użytkownika skonfigurowane?  

Dla każdego logowania Azure Active Directory ocenia wszystkie zasady i zapewnia, że przed udzielony dostęp do użytkownika spełniono wszystkie wymagania.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>Dostęp warunkowy działa z programem Exchange ActiveSync?

Tak, można użyć programu Exchange ActiveSync w zasadach dostępu warunkowego.






## <a name="what-you-should-avoid-doing"></a>Co należy zrobić

Dostęp warunkowy framework zapewnia elastyczność dużą konfiguracji. Jednak elastyczność oznacza także, należy dokładnie przejrzeć wszystkie zasady konfiguracji przed zwolnieniem go, aby uniknąć niepożądane wyniki. W tym kontekście, należy zwrócić szczególną uwagę na przydziały wpływające na zestawy, takie jak **wszyscy użytkownicy / grupy / aplikacji w chmurze**.

W środowisku należy unikać następujące konfiguracje:


**Dla wszystkich użytkowników wszystkich aplikacji w chmurze:**

- **Blokowanie dostępu** — ta konfiguracja zablokuje całej organizacji, który ostatecznie nie jest dobrym rozwiązaniem.

- **Wymagają zgodnego urządzenia** — dla użytkowników, która nie zarejestrowali swoich urządzeń, ale ta zasada blokuje dostęp tym do uzyskiwania dostępu do portalu usługi Intune. Jeśli jesteś administratorem bez zarejestrowanego urządzenia te zasady blokuje powrót do portalu Azure do zmiany zasad.

- **Wymagane było przyłączenie do domeny** — ten blok zasady dostępu ma również możliwość zablokowania dostępu dla wszystkich użytkowników w organizacji, jeśli nie masz jeszcze urządzenia przyłączone do domeny.


**Dla wszystkich użytkowników, wszystkie aplikacje w chmurze, wszystkie platformy urządzeń:**

- **Blokowanie dostępu** — ta konfiguracja zablokuje całej organizacji, który ostatecznie nie jest dobrym rozwiązaniem.


## <a name="how-should-you-deploy-a-new-policy"></a>Jak można wdrażanie nowych zasad

W pierwszej kolejności należy ocenić, za pomocą zasad [co zrobić, jeśli narzędzie](active-directory-conditional-access-whatif.md).

Gdy wszystko będzie gotowe do wdrożenia nowych zasad w środowisku, należy to zrobić w fazach:

1. Stosowanie zasad dla małych zbioru użytkowników i sprawdź, czy działa zgodnie z oczekiwaniami. 

2.  Po rozwinięciu zasad, aby dołączyć więcej użytkowników nadal do wykluczenia w przypadku wszystkich administratorów z zasad. Dzięki temu administratorzy nadal mieć dostęp, a można aktualizować zasady, jeśli wymagana jest zmiana.

3. Zastosuj zasady dla wszystkich użytkowników, tylko wtedy, gdy jest to naprawdę konieczne. 

Najlepszym rozwiązaniem Utwórz konto użytkownika, który jest:

- Dedykowany administrowanie zasadami 
- Wykluczone z wszystkich zasad


## <a name="policy-migration"></a>Migracja zasad

Należy rozważyć przeprowadzenie migracji zasad, które nie zostały utworzone w portalu Azure ponieważ:

- Można teraz adresów scenariusze, które nie może obsłużyć przed.

- Pozwala zmniejszyć liczbę zasad, które trzeba zarządzać konsolidując je.   

- Można zarządzać z zasad dostępu warunkowego w jednej centralnej lokalizacji.

- Klasyczny portal Azure została wycofana.   


Aby uzyskać więcej informacji, zobacz [migracji klasycznego zasad w portalu Azure](active-directory-conditional-access-migration.md).


## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).
