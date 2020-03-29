---
title: Hybrydowy projekt tożsamości — wymagania dotyczące ochrony danych Platformy Azure | Dokumenty firmy Microsoft
description: Planując rozwiązanie tożsamości hybrydowej, określ wymagania dotyczące ochrony danych dla Twojej firmy i jakie opcje są dostępne, aby najlepiej spełnić te wymagania.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64918775"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Planowanie zwiększenia bezpieczeństwa danych dzięki silnemu rozwiązaniu tożsamości
Pierwszym krokiem w ochronie danych jest określenie, kto może uzyskać dostęp do tych danych. Ponadto należy mieć rozwiązanie tożsamości, które można zintegrować z systemem, aby zapewnić możliwości uwierzytelniania i autoryzacji. Uwierzytelnianie i autoryzacja są często mylone ze sobą, a ich role są źle rozumiane. W rzeczywistości są one różne, jak pokazano na poniższym rysunku:

![cykl życia urządzeń mobilnych](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Etapy cyklu życia zarządzania urządzeniami przenośnymi**

Planując rozwiązanie tożsamości hybrydowej, musisz zrozumieć wymagania dotyczące ochrony danych dla twojej firmy i jakie opcje są dostępne, aby najlepiej spełnić te wymagania.

> [!NOTE]
> Po zakończeniu planowania zabezpieczeń danych, przejrzyj [określ wymagania dotyczące uwierzytelniania wieloskładnikowego,](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) aby upewnić się, że decyzje podjęte w tej sekcji nie miały wpływu na wybór dotyczący wymagań dotyczących uwierzytelniania wieloskładnikowego.
> 
> 

## <a name="determine-data-protection-requirements"></a>Określanie wymogów ochrony danych
W dobie mobilności większość firm ma wspólny cel: umożliwić użytkownikom produktywność na swoich urządzeniach mobilnych, lokalnie lub zdalnie z dowolnego miejsca w celu zwiększenia produktywności. Firmy, które mają takie wymagania, będą również zaniepokojone liczbą zagrożeń, które należy złagodzić, aby zapewnić bezpieczeństwo danych firmy i zachować prywatność użytkownika. Każda firma może mieć różne wymagania w tym zakresie; różne zasady zgodności, które będą się różnić w zależności od branży, w której działa firma, doprowadzą do podjęcia różnych decyzji projektowych. 

Istnieją jednak pewne aspekty bezpieczeństwa, które należy zbadać i zweryfikować, niezależnie od branży.

## <a name="data-protection-paths"></a>Ścieżki ochrony danych
![ścieżki ochrony danych](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Ścieżki ochrony danych**

Na powyższym diagramie składnik tożsamości będzie pierwszym, który zostanie zweryfikowany przed dostępem do danych. Jednak te dane mogą być w różnych stanach w czasie, gdy był dostępny. Każda liczba na tym diagramie reprezentuje ścieżkę, w której dane mogą znajdować się w pewnym momencie w czasie. Liczby te są wyjaśnione poniżej:

1. Ochrona danych na poziomie urządzenia.
2. Ochrona danych podczas transportu.
3. Ochrona danych w miejscu spoczynku.
4. Ochrona danych w spoczynku w chmurze.

Konieczne jest, że rozwiązanie tożsamości hybrydowej jest w stanie korzystać z zasobów zarządzania tożsamością lokalną i w chmurze, aby zidentyfikować użytkownika, zanim udzieli dostępu do danych. Planując rozwiązanie tożsamości hybrydowej, upewnij się, że odpowiedzi na następujące pytania są wymagane zgodnie z wymaganiami organizacji:

## <a name="data-protection-at-rest"></a>Ochrona danych w spoczynku
Niezależnie od tego, gdzie dane są w spoczynku (urządzenie, chmura lub lokalnie), ważne jest, aby przeprowadzić ocenę, aby zrozumieć potrzeby organizacji w tym zakresie. W tym obszarze należy zadać następujące pytania:

* Czy Twoja firma musi chronić dane w spoczynku?
  * Jeśli tak, czy rozwiązanie tożsamości hybrydowej jest w stanie zintegrować się z bieżącą infrastrukturą lokalną?
  * Jeśli tak, czy rozwiązanie tożsamości hybrydowej jest w stanie zintegrować się z obciążeniami znajdującymi się w chmurze?
* Czy zarządzanie tożsamością w chmurze jest w stanie chronić poświadczenia użytkownika i inne dane przechowywane w chmurze?

## <a name="data-protection-in-transit"></a>Ochrona danych w tranzycie
Dane przesyłane między urządzeniem a centrum danych lub między urządzeniem a chmurą muszą być chronione. Jednak bycie w tranzycie nie musi oznaczać procesu komunikacji ze składnikiem spoza usługi w chmurze; porusza się wewnętrznie, również, na przykład między dwiema sieciami wirtualnymi. W tym obszarze należy zadać następujące pytania:

* Czy Twoja firma musi chronić dane podczas przesyłania?
  * Jeśli tak, czy rozwiązanie tożsamości hybrydowej jest w stanie zintegrować się z bezpiecznymi formantami, takimi jak SSL/TLS?
* Czy zarządzanie tożsamościami w chmurze zachowuje ruch do i w obrębie magazynu katalogów (w centrach danych i między nimi)?

## <a name="compliance"></a>Zgodność
Przepisy, przepisy i wymagania dotyczące zgodności z przepisami będą się różnić w zależności od branży, do której należy Twoja firma. Firmy z branż o wysokim poziomie regulacji muszą zająć się problemami związanymi z zarządzaniem tożsamościami związanymi z kwestiami zgodności. Przepisy takie jak Sarbanes-Oxley (SOX), Health Insurance Portability and Accountability Act (HIPAA), Gramm-Leach-Bliley Act (GLBA) i Payment Card Industry Data Security Standard (PCI DSS) są surowe w odniesieniu do tożsamości i dostępu. Rozwiązanie tożsamości hybrydowej, które firma przyjmie, musi mieć podstawowe możliwości, które spełnią wymagania jednego lub więcej z tych przepisów. W tym obszarze należy zadać następujące pytania:

* Czy rozwiązanie tożsamości hybrydowej jest zgodne z wymogami regulacyjnymi obowiązującymi w Twojej firmie?
* Czy rozwiązanie tożsamości hybrydowej zostało zbudowane 
* w zakresie możliwości, które umożliwią Twojej firmie spełnianie wymogów regulacyjnych? 

> [!NOTE]
> Pamiętaj, aby zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Zdefiniuj strategię ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) będzie wykraczać poza dostępne opcje i zalety / wady każdej opcji.  Odpowiadając na te pytania, wybierzesz opcję, która najlepiej odpowiada Twoim potrzebom biznesowym.
> 
> 

## <a name="next-steps"></a>Następne kroki
 [Określanie wymagań dotyczących zarządzania zawartością](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień projektowych](plan-hybrid-identity-design-considerations-overview.md)

