---
title: Wytyczne dotyczące projektowania ujawniania informacji
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do wytycznych dotyczących projektowania ujawniania informacji i ocena poziomu ujawniania informacji.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: fe38c6b7cfb1abbaf3f1079dd8bff66b51b98091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776389"
---
# <a name="disclosure-design-guidelines"></a>Wytyczne dotyczące projektowania ujawniania
Dowiedz się, jak budować i utrzymywać zaufanie do klientów, zachowując przejrzystość na temat syntetycznego charakteru twojego głosu.

## <a name="what-is-disclosure"></a>Co to jest ujawnienie?

Ujawnienie jest sposobem poinformowania ludzi, że&#39;ponownego interakcji z lub słuchania głosu, który jest generowany syntetycznie.

## <a name="why-is-disclosure-necessary"></a>Dlaczego ujawnienie jest konieczne?

Potrzeba ujawnienia syntetycznych początków głosu generowanego komputerowo jest stosunkowo nowa. W przeszłości, generowane komputerowo głosy były oczywiście, że nikt nigdy nie pomylić je do prawdziwej osoby. Z każdym dniem poprawia się jednak realizm syntetycznych głosów, które stają się coraz bardziej nie do odróżnienia od ludzkich głosów.

## <a name="goals"></a>Cele
Oto zasady, o których należy pamiętać podczas projektowania syntetycznych doświadczeń głosowych:

**Wzmacnianie zaufania**
<br>Projekt z zamiarem niepowodzenia testu Turinga bez poniżania doświadczenia. Pozwól użytkownikom na fakt, że są one interakcji z syntetycznym głosem, pozwalając im na bezproblemowe angażowanie się w doświadczenie.

**Dostosuj się do kontekstu użytkowania**
<br>Dowiedz się, kiedy, gdzie i w jaki sposób użytkownicy będą wchodzić w interakcje z syntetycznym głosem, aby zapewnić odpowiedni rodzaj ujawnienia we właściwym czasie.

**Ustal jasne oczekiwania**
<br>Pozwól użytkownikom łatwo odkryć i zrozumieć możliwości agenta. Zaoferuj możliwości, aby dowiedzieć się więcej o technologii syntetycznego głosu na życzenie.

**Ogarnij porażkę**
<br>Użyj momentów niepowodzenia, aby wzmocnić możliwości agenta.

## <a name="how-to-use-this-guide"></a>Jak korzystać z tego przewodnika

Ten przewodnik pomaga określić, które wzorce ujawniania są najlepiej dopasowane do syntetycznego głosu. Następnie przedstawiamy przykłady, jak i kiedy z nich korzystać. Każdy z tych wzorców ma na celu maksymalizację przejrzystości z użytkownikami na temat mowy syntetycznej, pozostając wiernym projektowi skoncentrowanego na człowieku.

Biorąc pod uwagę ogromną ilość wskazówek projektowych dotyczących doświadczeń głosowych, skupiamy się tutaj w szczególności na:

1. [**Ocena ujawnienia**](#disclosure-assessment): Proces określania rodzaju ujawnienia zalecanego dla doświadczenia syntetycznego głosu

2. [**Jak ujawnić**](concepts-disclosure-patterns.md): Przykłady wzorców ujawniania informacji, które można zastosować do syntetycznego głosu

3. [**Kiedy ujawnić**](concepts-disclosure-patterns.md#when-to-disclose): Optymalne momenty do ujawnienia w całej podróży użytkownika

## <a name="disclosure-assessment"></a>Ocena ujawniania informacji
Należy wziąć pod uwagę użytkowników&#39; oczekiwania dotyczące interakcji i kontekstu, w którym będą doświadczać głosu. Jeśli kontekst jasno mówi, że &quot;głos syntetyczny jest głos,&quot; ujawnienie może być minimalne, chwilowe, a nawet niepotrzebne. Główne typy kontekstu, które wpływają na ujawnienie obejmują typ persona, typ scenariusza i poziom ekspozycji. Pomaga również zastanowić się, kto może słuchać.

### <a name="understand-context"></a>Zrozumienie kontekstu

Ten arkusz służy do określania kontekstu syntetycznego głosu. Zastosuj to w następnym kroku, w którym określisz poziom ujawnienia.

|                                    | Kontekst użytkowania                                                                                                                                                                                                                                                                                                                                                       | Potencjalne zagrożenia & wyzwania                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Typ persony**               | **Jeśli zastosowanie ma którakolwiek z poniższych aplikacji, twoja persona mieści się w kategorii "Persona podobna do człowieka":**<br><br><ul><li> Persona uosabia prawdziwego człowieka, czy jest to fikcyjna reprezentacja, czy nie. (np. zdjęcie lub wygenerowane komputerowo renderowanie prawdziwej osoby)<br><br><li> Syntetyczny głos opiera się na głosie powszechnie rozpoznawalnej prawdziwej osoby (np. celebrytki, postaci politycznej) | Im bardziej ludzkie reprezentacje dajesz swoją personę, tym bardziej prawdopodobne jest, że użytkownik skojarzy ją z prawdziwą osobą lub sprawi, że uwierzą, że treść jest wypowiadana przez prawdziwą osobę, a nie przez komputer. </ul>                                                                                                                                                                      |
| **2. Typ scenariusza**            | **Jeśli zastosowanie ma którakolwiek z poniższych opcji, środowisko głosowe mieści się w kategorii "Poufne":**<br><br><ul><li> Uzyskiwanie lub wyświetlanie danych osobowych od użytkownika <br><br> <li> Transmisje wiadomości/informacji wrażliwych na czas (np. alarm alarmowy)<br><br><li> Ma na celu pomóc prawdziwym ludziom komunikować się ze sobą (np. czyta osobiste e-maile / teksty)<br><br> <li> Zapewnia pomoc medyczną/zdrowotną </ul>            | Użycie syntetycznego głosu może nie być właściwe lub godne zaufania dla osób korzystających z niego, gdy tematy są związane z wrażliwymi, osobistymi lub pilnymi sprawami. Mogą również oczekiwać takiego samego poziomu empatii i świadomości kontekstowej jak prawdziwy człowiek. |
| **3. Poziom narażenia** |**Twoje doświadczenie głosowe najprawdopodobniej mieści się w kategorii "Wysoka", jeśli:** <br><br><ul><li>Użytkownik będzie często lub wchodził w interakcje z syntetycznym głosem lub przez długi czas </ul>                                                                                                                                                                             | Znaczenie bycia przejrzystym i budowania zaufania z użytkownikami jest jeszcze większe przy ustanawianiu długoterminowych relacji.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Określanie poziomu ujawniania informacji

Użyj poniższego diagramu, aby ustalić, czy środowisko głosu syntetycznego wymaga wysokiego lub niskiego ujawnienia na podstawie kontekstu użycia.

  ![Wykres oceny ujawniania informacji](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="reference-docs"></a>Dokumenty referencyjne

* [Ujawnienie talentu głosów](https://aka.ms/disclosure-voice-talent)
* [Wytyczne dotyczące odpowiedzialnego wdrażania technologii syntetycznego głosu](concepts-guidelines-responsible-deployment-synthetic.md)
* [Przegląd Gating](concepts-gating-overview.md)

## <a name="next-steps"></a>Następne kroki

* [Wzorce projektowania ujawniania](concepts-disclosure-patterns.md)
