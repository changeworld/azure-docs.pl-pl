---
title: Projektowania tożsamości hybrydowej — wymagania dotyczące ochrony danych Azure | Dokumentacja firmy Microsoft
description: Podczas planowania rozwiązania z tożsamością hybrydową, należy zidentyfikować wymagania dotyczące ochrony danych firmy i które opcje są dostępne najlepiej spełnić te wymagania.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918775"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Planowanie wzmocnienia zabezpieczeń danych za pośrednictwem rozwiązania silna tożsamość
Pierwszym środkiem ochrony danych jest w celu zidentyfikowania, kto ma dostęp do tych danych. Ponadto musisz mieć rozwiązanie tożsamości, które można zintegrować z systemu w celu zapewnienia funkcji uwierzytelniania i autoryzacji. Uwierzytelnianie i autoryzacja są często mylona z cyfrą siebie nawzajem i ich ról źle zrozumiane. W rzeczywistości są one różne, jak pokazano na poniższej ilustracji:

![cykl życia urządzenia przenośnego](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Etapy cyklu życia zarządzania urządzeniami przenośnymi**

Podczas planowania rozwiązania z tożsamością hybrydową, trzeba zrozumieć, że wymagania dotyczące ochrony danych firmy i które opcje są dostępne, aby jak najlepiej spełniają te wymagania.

> [!NOTE]
> Po zakończeniu planowania zabezpieczeń danych, przejrzyj [określić wymagania dotyczące uwierzytelniania wieloskładnikowego](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) aby upewnić się, nie zostały wybrane opcje dotyczące wymagania dotyczące uwierzytelniania wieloskładnikowego wpływ decyzji użytkownik wprowadzone w tej sekcji.
> 
> 

## <a name="determine-data-protection-requirements"></a>Określanie wymagań dotyczących ochrony danych
W wieku mobilności w większości firm mają wspólny cel: zezwolić użytkownikom na produktywność na swoich urządzeniach przenośnych, podczas lokalnie lub zdalnie z dowolnego miejsca w celu zwiększenia wydajności. Firm, które mają takich wymagań będą również dane o liczbie zagrożenia, które należy zminimalizować Aby chronić dane firmowe i prywatność użytkowników jest chroniona. Każda firma może mieć różne wymagania w tym zakresie; reguły zgodności różnych, które różnią się w zgodnie z branży, które działa firma spowoduje różne decyzje. 

Istnieją jednak pewne aspekty zabezpieczeń, które powinny być zbadane i zweryfikowany, bez względu na branżę.

## <a name="data-protection-paths"></a>Ścieżki do ochrony danych
![Ścieżki do ochrony danych](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Ścieżki do ochrony danych**

Na powyższym diagramie składników tożsamości będzie pierwszy z nich do weryfikacji przed uzyskaniem dostępu do danych. Jednak w czasie, gdy nastąpił dostęp do tych danych może być w różnych stanach. Każdy numer na tym diagramie reprezentuje ścieżkę, w którym dane mogą być znajduje się w pewnym momencie w czasie. Te liczby są wyjaśnione poniżej:

1. Ochrona danych na poziomie urządzeń.
2. Ochrony danych podczas przesyłania.
3. Ochrona danych przy jednoczesnym rest w środowisku lokalnym.
4. Ochrona danych magazynowanych w chmurze.

Konieczne jest rozwiązania tożsamości hybrydowej jest w stanie korzystanie z lokalnych i zasobów do zarządzania tożsamościami do identyfikowania użytkownika przed udzieleniem dostępu do danych w chmurze. Podczas planowania rozwiązania z tożsamością hybrydową, upewnij się, że zgodnie z wymaganiami organizacji są odpowiedzieć na następujące pytania:

## <a name="data-protection-at-rest"></a>Ochrona danych w spoczynku
Niezależnie od tego, gdzie dane są magazynowane (urządzenie, chmurze lub lokalnie) należy przeprowadzić ocenę, aby zrozumieć, w związku z tym potrzeb organizacji. Dla tego obszaru upewnij się, że zostaną poproszeni o następujące pytania:

* Czy firma musi chronić dane magazynowane?
  * Jeśli tak, jest możliwość integracji z bieżącą infrastrukturę lokalną rozwiązania tożsamości hybrydowej?
  * Jeśli tak, czy rozwiązania tożsamości hybrydowej możliwość integracji z obciążeń w chmurze?
* Zarządzanie tożsamościami w chmurze jest możliwość ochrony poświadczeń użytkownika i innych danych przechowywanych w chmurze?

## <a name="data-protection-in-transit"></a>Ochrona danych podczas przesyłania
Muszą być chronione dane przesyłane między urządzeniem i centrum danych lub między urządzeniem i chmurą. Jednakże są w drodze nie musi oznaczać proces komunikacji ze składnikiem poza usługi w chmurze; Przesuwa się wewnętrznie, ponadto, na przykład między dwiema sieciami wirtualnymi. Dla tego obszaru upewnij się, że zostaną poproszeni o następujące pytania:

* Czy firma musi ochrona przenoszonych danych?
  * Jeśli tak, jest możliwość integracji z bezpiecznych kontrolek, takich jak protokół SSL/TLS rozwiązania tożsamości hybrydowej?
* Zarządzanie tożsamościami w chmurze, zachować ruch do i w magazynie katalogu (wewnątrz i między centrami danych) podpisany?

## <a name="compliance"></a>Zgodność
Przepisów ustawowych i prawnych wymagań dotyczących zgodności będzie się różnić zależnie od branży, należącego do firmy. Firmom w branżach regulowanych prawnie o wysokiej muszą spełnić, aby Zarządzanie tożsamościami obawy związane z problemów ze zgodnością. Przepisy, takie jak Sarbanes-Oxley (SOX), Health Insurance Portability i Accountability Act (HIPAA), ustawą Gramm-Leach-Bliley Act (GLBA) i Payment Card Industry Data Security Standard (PCI DSS) są ściśle dotyczące tożsamości i dostępu. Rozwiązania tożsamości hybrydowej, który przyjmie Twojej firmy musi mieć podstawowe funkcje, które będzie spełnić wymagania co najmniej jedną z tych przepisów. Dla tego obszaru upewnij się, że zostaną poproszeni o następujące pytania:

* Czy rozwiązania tożsamości hybrydowej jest zgodna z wymaganiami prawnymi w firmie?
* Opracowała rozwiązania tożsamości hybrydowej 
* w funkcje, które pozwolą firmie na być zgodny z wymaganiami prawnymi? 

> [!NOTE]
> Pamiętaj zanotować wszystkie odpowiedzi i zrozumieć uzasadnienie. [Definiowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) zostanie umieszczona nad dostępne opcje oraz zalety/wady każdej opcji.  Po udzieleniu odpowiedzi na te pytania można wybrać opcję, która najlepiej pasujące do działalności potrzebuje.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
 [Określanie wymagań dotyczących zarządzania zawartością](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

