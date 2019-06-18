---
title: Projektowania tożsamości hybrydowej — wymagań dotyczących odpowiedzi na zdarzenia usługi Azure | Dokumentacja firmy Microsoft
description: Określić możliwości monitorowania i raportowania dla rozwiązania tożsamości hybrydowej, które mogą zostać wykorzystane przez IT, aby podjąć działania w celu identyfikowania i Eliminuj potencjalne zagrożenia
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
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
ms.openlocfilehash: 52b5e37c29e4b3df3f171f683266b5d0a3e0c95d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109274"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Określanie wymagań dotyczących reagowania na zdarzenia, dla Twojego rozwiązania z tożsamością hybrydową
Dużych i średnich organizacji najprawdopodobniej będą mieć [reagowania na zdarzenia zabezpieczeń](https://technet.microsoft.com/library/cc700825.aspx) mające na celu pomóc IT podjąć odpowiednie działania na poziomie wystąpienia zdarzenia. Systemu zarządzania tożsamościami jest ważnym elementem procesu reagowania na zdarzenia, ponieważ może służyć do pomocy w identyfikacji, kto wykonał określonej akcji w odniesieniu do obiektu docelowego. Rozwiązania z tożsamością hybrydową musi mieć możliwość zapewnienia możliwości monitorowania i raportowania, które mogą zostać wykorzystane przez IT, aby wykonać działania, aby identyfikować i minimalizować potencjalne zagrożenie. W ramach planu reagowania na zdarzenia typowe mają następujące fazy w ramach planu:

1. Wstępną ocenę.
2. Zdarzenia komunikacji.
3. Formant uszkodzenia i zmniejszenia ryzyka.
4. Identyfikacja, co było naruszenia i ważności.
5. Zachowywanie dowodów.
6. Powiadomienie do odpowiednich osób.
7. Odzyskiwanie systemu.
8. Dokumentacja.
9. Ocena szkód i kosztów.
10. Proces i plan korekty.

Podczas identyfikacji czym one naruszenia i faza ważność, konieczne będzie zidentyfikowanie systemów których bezpieczeństwo mogło zostać naruszone, pliki, które były używane, aby ustalić istotność tych plików. System tożsamości hybrydowej powinno być możliwe spełnić te wymagania, aby ułatwić ustalenie użytkownika, który je wprowadził. 

## <a name="monitoring-and-reporting"></a>Monitorowanie i raportowanie
Wiele razy systemu tożsamości może również pomóc w fazie początkowej oceny głównie w przypadku, jeśli system ma wbudowane inspekcji i możliwości raportowania. Podczas początkowej oceny administrator IT musi być w stanie identyfikować podejrzane działania lub system powinien móc wywoływany go automatycznie na podstawie wstępnie skonfigurowane zadania. Wiele działań może wskazywać na atak możliwe, jednak w innych przypadkach źle skonfigurowany system może prowadzić do liczby wyników fałszywie dodatnich systemu wykrywania nieautoryzowanego dostępu. 

Systemu zarządzania tożsamościami powinien pomóc administratorom IT do identyfikowania i raportowania tych podejrzanych działań. Zazwyczaj te wymagania techniczne mogą być spełnione przez wszystkie systemy monitorowania i równoważy możliwości raportowania, który można wyróżnić potencjalnych zagrożeń. Użyj pytania poniżej, aby ułatwić projektowanie swoje rozwiązania tożsamości hybrydowej, biorąc pod uwagę reagowania na zdarzenia wymagania:

* Czy Twoja firma występują reagowania na zdarzenia zabezpieczeń w miejscu?
  * Jeśli tak, bieżącego systemu zarządzania tożsamościami służy jako część procesu?
* Czy firma musi zidentyfikować prób logowania podejrzanych użytkowników na różnych urządzeniach?
* Czy firma potrzebuje do wykrywania potencjalnych ze złamanymi zabezpieczeniami poświadczenia użytkownika?
* Czy firma potrzebuje inspekcji dostępu i akcji użytkownika?
* Czy firma musi wiedzieć, kiedy użytkownik resetuje hasła?

## <a name="policy-enforcement"></a>Wymuszanie zasad
Podczas kontroli uszkodzenia i fazy zmniejszenie ryzyka ważne jest szybkie zmniejszenie rzeczywistych i potencjalnych skutków ataku. Tę akcję, która spowoduje przejście na tym etapie można zdecydować o WERSJA_POMOCNICZA i główne jeden. Dokładna będzie zależeć od Twojej organizacji i rodzaj ataku, który napotkasz. Wstępną ocenę zawarte na tym, że konto zostało naruszone, należy wymusić zasady, aby zablokować tego konta. To tylko jeden przykład, w którym będą wykorzystywane systemu zarządzania tożsamościami. Użyj pytania poniżej, aby ułatwić projektowanie rozwiązania z tożsamością hybrydową, biorąc pod uwagę sposób zostaną wymuszone zasady do reagowania na zdarzenie bieżące:

* Czy firma dysponuje zasady w miejscu aby uniemożliwić użytkownikom z dostępu do sieci w razie potrzeby?
  * Jeśli tak, czy bieżący rozwiązanie można zintegrować z systemu zarządzania tożsamościami hybrydowego, który ma przyjąć?
* Czy firma potrzebuje wymuszanie dostępu warunkowego dla użytkowników, którzy są w kwarantannie? 

> [!NOTE]
> Pamiętaj zanotować wszystkie odpowiedzi i zrozumieć uzasadnienie. [Definiowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) zostanie umieszczona nad dostępne opcje oraz zalety/wady każdej opcji.  Po udzieleniu odpowiedzi na te pytania można wybrać opcję, która najlepiej pasujące do działalności potrzebuje.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
[Definiowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

