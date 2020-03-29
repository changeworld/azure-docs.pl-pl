---
title: Wytyczne dotyczące odpowiedzialnego wdrażania technologii syntetycznego głosu
titleSuffix: Azure Cognitive Services
description: Ogólne wytyczne firmy Microsoft dotyczące projektowania przy użyciu technologii syntetycznego głosu. Zostały one opracowane w badaniach, które Microsoft przeprowadził z talentem głosowym, konsumentów, a także osób z zaburzeniami mowy, aby poprowadzić odpowiedzialny rozwój syntetycznego głosu.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836761"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Wytyczne dotyczące odpowiedzialnego wdrażania technologii syntetycznego głosu
Oto ogólne wytyczne firmy Microsoft dotyczące projektowania przy użyciu technologii syntetycznego głosu. Zostały one opracowane w badaniach, które Microsoft przeprowadził z talentem głosowym, konsumentów, a także osób z zaburzeniami mowy, aby poprowadzić odpowiedzialny rozwój syntetycznego głosu.

## <a name="general-considerations"></a>Zagadnienia ogólne
W przypadku wdrażania technologii mowy syntetycznej w większości scenariuszy obowiązują następujące wskazówki.

### <a name="disclose-when-the-voice-is-synthetic"></a>Ujawnij, kiedy głos jest syntetyczny
Ujawnienie, że głos jest generowany komputerowo, nie tylko minimalizuje ryzyko szkodliwych skutków oszustwa, ale także zwiększa zaufanie do organizacji dostarczającej głos. Dowiedz się więcej o [tym, jak ujawnić](concepts-disclosure-guidelines.md).

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Wybierz odpowiednie typy głosu dla danego scenariusza
Należy dokładnie rozważyć kontekst stosowania i potencjalne szkody związane z używaniem syntetycznego głosu. Na przykład syntetyczne głosy o wysokiej wierności mogą nie być odpowiednie w scenariuszach wysokiego ryzyka, takich jak wiadomości osobiste, transakcje finansowe lub złożone sytuacje, które wymagają ludzkiej zdolności adaptacji lub empatii. Użytkownicy mogą mieć również różne oczekiwania dotyczące typów głosu. Na przykład, podczas słuchania wrażliwych wiadomości czytanych przez syntetyczny głos, niektórzy użytkownicy wolą bardziej empatyczne i ludzkie czytanie wiadomości, podczas gdy inni woleli bardziej monotonny, bezstronny głos. Należy rozważyć przetestowanie aplikacji, aby lepiej zrozumieć preferencje użytkownika.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Bądź przejrzysty w kwestii możliwości i ograniczeń
Użytkownicy są bardziej prawdopodobne, że wyższe oczekiwania podczas interakcji z wysokiej wierności syntetycznych agentów głosowych. W związku z tym, gdy możliwości systemu nie spełniają tych oczekiwań, zaufanie może ucierpieć i może spowodować nieprzyjemne, a nawet szkodliwe doświadczenia.

### <a name="provide-optional-human-support"></a>Zapewnij opcjonalne wsparcie dla ludzi
W niejednoznacznych scenariuszach transakcyjnych (na przykład centrum obsługi połączeń), użytkownicy nie zawsze ufają agentowi komputerowemu, aby odpowiednio odpowiadał na ich żądania. W takich sytuacjach konieczne może być wsparcie człowieka, niezależnie od realistycznej jakości głosu lub możliwości systemu.

## <a name="considerations-for-voice-talent"></a>Uwagi dotyczące talentu głosów
Podczas pracy z talentem głos, takich jak aktorzy głosowi, do tworzenia syntetycznych głosów, poniższe wytyczne stosuje.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Uzyskaj znaczącą zgodę od talentów głosowych
Talent głosowy oczekuje kontroli nad czcionką głosową (jak i gdzie będzie używana) i zostanie skompensowany za każdym razem, gdy jest używany. Właściciele systemów powinni zatem uzyskać wyraźną pisemną zgodę od talentów głosowych i mieć jasne specyfikacje umowne dotyczące przypadków użycia, czasu użytkowania, rekompensaty itd. Niektóre talentgłosowe nie są świadome potencjalnych złośliwych zastosowań tej technologii i powinny być edukowane przez właścicieli systemów na temat możliwości tej technologii. Aby uzyskać więcej informacji na temat talentu głos i zgody, przeczytaj nasze [Ujawnienie dla Voice Talent](https://aka.ms/disclosure-voice-talent).


## <a name="considerations-for-those-with-speech-disorders"></a>Względy dla osób z zaburzeniami mowy
Podczas pracy z osobami z zaburzeniami mowy, aby utworzyć lub wdrożyć technologię syntetycznego głosu, obowiązują następujące wytyczne.

### <a name="provide-guidelines-to-establish-contracts"></a>Zawieranie wytycznych dotyczących zawierania umów
Zapewnij wytyczne dotyczące zawierania umów z osobami, które używają syntetycznego głosu do pomocy w mówieniu. Umowa powinna rozważyć określenie stron, które są właścicielami głosu, czasu użytkowania, kryteriów przeniesienia własności, procedur usuwania czcionki głosowej oraz zapobiegania nieautoryzowanemu dostępowi. Ponadto włącz umowny transfer własności czcionek głosowych po śmierci na członków rodziny, jeśli ta osoba wyraziła na to zgodę.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Uwzględnianie niespójności we wzorcach mowy
Dla osób z zaburzeniami mowy, które nagrywają własne czcionki głosowe, niespójności w ich wzorze mowy (slurring lub niezdolność do wymawiania niektórych słów) może skomplikować proces nagrywania. W takich przypadkach syntetyczna technologia głosowa i sesje nagraniowe powinny je pomieścić (czyli zapewnić przerwy i dodatkową liczbę sesji nagraniowych).

### <a name="allow-modification-over-time"></a>Zezwalaj na modyfikację w czasie
Osoby z zaburzeniami mowy pragną dokonać aktualizacji ich syntetycznego głosu, aby odzwierciedlić starzenie się (na przykład dziecko osiągające dojrzałość płciową). Osoby fizyczne mogą mieć również preferencje stylistyczne, które zmieniają się w czasie, i mogą chcieć wprowadzić zmiany w wysokości, akcentu lub innych cechach głosowych.


## <a name="reference-docs"></a>Dokumenty referencyjne

* [Ujawnienie talentu głosów](https://aka.ms/disclosure-voice-talent)
* [Przegląd Gating](concepts-gating-overview.md)
* [Jak ujawnić](concepts-disclosure-guidelines.md)
* [Wzorce projektu ujawniania informacji](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Następne kroki

* [Ujawnienie talentu głosów](https://aka.ms/disclosure-voice-talent)
* [Jak ujawnić](concepts-disclosure-guidelines.md)
* [Wzorce projektu ujawniania informacji](concepts-disclosure-patterns.md)
