---
title: Tożsamość hybrydowym - wymagań dotyczących odpowiedzi na zdarzenia Azure | Dokumentacja firmy Microsoft
description: Określić możliwości monitorowania i raportowania dla rozwiązania z tożsamością hybrydową, które mogą zostać wykorzystane przez IT, aby podjąć działania w celu identyfikacji i ograniczyć potencjalne zagrożenia
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 09dfa0bd01a7de95ba6ce3093ddf589595116aa6
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801062"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Określenie wymagań dotyczących odpowiedzi na zdarzenia dla rozwiązania z tożsamością hybrydową
Duża lub średnich firmach najprawdopodobniej będzie mieć [odpowiedzi na zdarzenia zabezpieczeń](https://technet.microsoft.com/library/cc700825.aspx) mające na celu pomoc IT podjąć odpowiednie działania na poziom zdarzenia. System zarządzania tożsamościami jest składnikiem ważne w procesie odpowiedzi na zdarzenia, ponieważ mogą zostać użyte ułatwiające identyfikowanie, kto wykonał określonej akcji w odniesieniu do obiektu docelowego. Rozwiązania z tożsamością hybrydową musi mieć możliwość zapewnienia możliwości monitorowania i raportowania, które mogą zostać wykorzystane przez IT, aby podjąć działania w celu identyfikacji i ograniczyć potencjalne zagrożenia. W planie odpowiedzi na typowe zdarzenia mają następujące fazy jako część planu:

1. Ocena początkowej.
2. Komunikat zdarzenia.
3. Formant uszkodzenia i zmniejszenia ryzyka.
4. Identyfikacja, co było naruszenia i ważności.
5. Dowód konserwacji.
6. Powiadomienia do odpowiednich osób.
7. Odzyskiwania systemu.
8. Dokumentacja.
9. Ocena uszkodzenia, a jego kosztem.
10. Proces i plan korekty.

Podczas identyfikacji jakie naruszenia i ważności fazy, konieczne będzie zidentyfikowania systemów, które zostały naruszone, pliki, które zostały dostęp i określenie czułości tych plików. System tożsamości hybrydowej powinno być możliwe spełnić te wymagania, aby ułatwić ustalenie użytkownik, który wprowadził zmiany. 

## <a name="monitoring-and-reporting"></a>Monitorowanie i raportowanie
Wiele razy systemu tożsamości może również pomóc w fazie oceny początkowej głównie w przypadku, jeśli system ma wbudowane inspekcji i funkcje raportowania. Podczas oceny początkowej administrator IT musi być w stanie zidentyfikować podejrzane działania lub systemu powinno być możliwe wyzwalacz, który go automatycznie na podstawie wstępnie skonfigurowane zadania. Wiele działań może wskazywać na atak możliwe, jednak w innych przypadkach nieprawidłowo skonfigurowany system może prowadzić do liczbę fałszywych alarmów systemu wykrywania nieautoryzowanego dostępu. 

System zarządzania tożsamościami powinna pomagać administratorów IT do identyfikowania i raportowania tych podejrzanych działań. Zazwyczaj te wymagania techniczne mogą być spełnione przez monitorowanie wszystkich systemów i o możliwości raportowania, który można wyróżnić potencjalne zagrożenia. Aby ułatwić projektowanie rozwiązania hybrydowe tożsamości, biorąc pod uwagę wymagania odpowiedzi na zdarzenia, użyj pytań poniżej:

* Czy firma występują odpowiedzi na zdarzenia zabezpieczeń w miejscu?
  * Jeśli tak, bieżący system zarządzania tożsamościami służy jako część procesu?
* Czy firma musi zidentyfikować prób logowania podejrzanych użytkowników w wielu różnych urządzeniach?
* Czy firma musi Wykrywanie potencjalnych przejęciem poświadczeń użytkownika?
* Czy firma musi inspekcji dostępu i akcji użytkownika?
* Czy firma musi znać podczas resetowania hasła przez użytkownika?

## <a name="policy-enforcement"></a>Wymuszanie zasad
Podczas fazy zmniejszenie ryzyka i kontroli uszkodzenia ważne jest szybkie zmniejszenie rzeczywistych i potencjalnych skutków ataku. Ta akcja, która spowoduje przejście na tym etapie wprowadzić różnica między pomocnicze i głównych jeden. Dokładna reakcja zależą od organizacji i rodzaj ataku, który czoła. Początkowej oceny wykazał, że zostało naruszone bezpieczeństwo konta, należy wymusić zasady, aby zablokować tego konta. To tylko jeden przykład, w których można użyć systemu zarządzania tożsamościami. Użyj pytań poniżej, aby ułatwić projektowanie rozwiązania z tożsamością hybrydową, biorąc pod uwagę sposób wymusić zasady reagowanie na zdarzenia trwającą:

* Czy firmie obowiązują zasady w miejscu aby uniemożliwić użytkownikom z dostępu do sieci w razie potrzeby?
  * Jeśli tak, czy bieżące rozwiązanie można zintegrować z system zarządzania tożsamości hybrydowej, który zamierzasz wdrożyć usługę?
* Czy firma musi się w celu wymuszenia dostępu warunkowego dla użytkowników, którzy są w kwarantannie? 

> [!NOTE]
> Upewnij się zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Definiowanie strategii ochrony danych](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) będą przekazywane dostępne opcje oraz zalety/wady każdej opcji.  Po udzieleniu odpowiedzi na te pytania można wybrać opcji najlepiej pasujące do firmy wymaga.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
[Definiowanie strategii ochrony danych](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](active-directory-hybrid-identity-design-considerations-overview.md)

