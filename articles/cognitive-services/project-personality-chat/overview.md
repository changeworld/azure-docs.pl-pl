---
title: Czym jest projekt rozmowy dotyczącej osobowości?
titlesuffix: Azure Cognitive Services
description: W tym artykule omówiono projekt platformy Azure o nazwie Personality Chat, który jest opartym na chmurze interfejsem API służącym do rozszerzania możliwości prowadzenia rozmowy przez Twojego bota.
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: c7f7a8c65717acd5a19e92b7e0437dc4b8628909
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "62103627"
---
# <a name="what-is-project-personality-chat"></a>Czym jest projekt rozmowy dotyczącej osobowości?

Projekt Personality Chat zwiększa możliwości konwersacyjne Twojego bota dzięki obsłudze pogawędek zgodnie z wyróżniającą się, wybraną osobowością. Interfejs Personality Chat używa klasyfikatorów intencji do identyfikacji typowych intencji pogawędki i generuje odpowiedzi zgodnie z osobowością. Na podstawie intencji i współczynników ufności bot wybiera najlepszą odpowiedź z wyselekcjonowanej bazy redakcyjnej albo generuje ją w czasie rzeczywistym przy użyciu głębokich sieci neuronowych. Możesz wybrać jedną spośród trzech domyślnych osób. Model osoby zwraca odpowiedzi, które są najbardziej podobne do wybranej osobowości.

Dostępny jest dostosowywalny zestaw redakcyjny dla typowych zapytań pogawędki. Można go łatwo zintegrować przy użyciu zestawu Microsoft Bot Framework SDK. Ten zestaw SDK oszczędza czas i koszty pisania go od podstaw.

## <a name="getting-started-with-project-personality-chat"></a>Wprowadzenie do projektu Personality Chat

Na stronie laboratorium projektu Personality Chat możesz porozmawiać z dostępną wersją demonstracyjną oraz poprosić o wczesny dostęp, gdy usługa stanie się dostępna.
Już dziś możesz również zintegrować dostosowywalną bibliotekę tylko na potrzeby redakcji ze swoimi botami za pomocą zestawu Microsoft Bot Framework SDK. <br>
[Przykłady: integrowanie interfejsu Personality Chat z botem](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[Wypróbuj bibliotekę Personality Chat](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>Generowanie odpowiedzi przy użyciu sieci neuronowych

Interfejs Personality Chat używa modeli uczenia głębokiego do uczenia ogólnych wzorców konwersacji i generowania odpowiedzi. Model generowania odpowiedzi jest cykliczną siecią neuronową. Ten model jest uczony na milionach konwersacji, dzięki którym poznaje on wzorce interakcji międzyludzkich. Za pomocą przyswojonych wzorców struktury zdań są tworzone nowe zapytania i generowane są odpowiedzi. Podczas generowania tej nowej odpowiedzi model przeszukuje „zapisane słownictwo” i wybiera n najlepszych pierwszych słów odpowiedzi. Korzystając z wyszukiwania promieniowego, kontynuuje wyszukiwanie n najlepszych drugich słów dla każdego z wygenerowanych pierwszych słów. Odpowiedź jest uważana za kompletną, gdy model wybierze słowo kończące zdanie. Gdy ma już wszystkie odpowiedzi, wybiera n najlepszych odpowiedzi na podstawie oceny prawdopodobieństwa dla pełnej odpowiedzi.

Model uczy się generowania właściwych kontekstowo odpowiedzi mających właściwą „strukturę”. Na przykład pytanie takie jak „Czy chcesz teraz rozmawiać?” mówi trochę o strukturze wiarygodnej odpowiedzi: prawdopodobnie zacznie się od jakiejś formy słowa „tak” lub „nie”, a zaimkiem będzie prawdopodobnie „Ja”. Odpowiedź „nie” najprawdopodobniej będzie zawierać grzeczne wytłumaczenie itd.

System próbuje nauczyć się modelu języka dla podstawowej struktury konwersacji. Ta struktura powinna dopuszczać częściowy wpływ na odpowiedź ograniczeń zewnętrznych, takich jak tematy, osobowość itd.  Ponieważ te ograniczenia są przyswajane z szerokich wzorców, są one odpowiednie do zastosowania (między innymi) w pogawędkach.

![Model sekwencja-sekwencja do generowania odpowiedzi](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>Modelowanie osobowości

 W przypadku każdego modelu konwersacyjnego sterowanego danymi wyzwaniem jest zapewnianie spójnej i konsekwentnej „osobowości”. Osoba jest definiowana jako charakter ujawniany podczas interakcji w trakcie konwersacji. Osobę można uważać za składającą się z elementów tożsamości, zachowania językowego i stylu interakcji. W bieżącej wersji interfejsu Personality Chat główny nacisk został położony na zachowanie językowe i styl interakcji.

Ten model reprezentuje każdą osobę mówiącą jako wektor lub osadzenie. Koduje on informacje od osoby mówiącej, które mają wpływ na zawartość i styl ich odpowiedzi. Model następnie uczy się reprezentacji osoby mówiącej na podstawie zawartości konwersacji pochodzącej od różnych osób. Osoby mówiące odpowiadające w podobny sposób mają tendencję do podobnych osadzeń i zajmują pozycje blisko siebie w przestrzeni wektorów. Dzięki temu dane uczenia osób mówiących zbliżone do siebie w przestrzeni wektorów pomagają zwiększyć możliwość uogólnienia modelu osoby mówiącej. Model skutecznie grupuje osoby mówiące, które mają podobne reprezentacje, w przestrzeni wektorów w celu utworzenia klastra osoby mającego „identyfikator osoby”.

W przypadku osób domyślnych atrybuty i wyselekcjonowane odpowiedzi służą do znajdowania najlepiej dopasowanego klastra osoby mówiącej. Ten klaster jest następnie wybierany jako identyfikator osoby dla każdej domyślnej osobowości. Dalsze dostosowanie może nastąpić dla dowolnego typu osobowości po uzyskaniu zestawu odpowiedzi bota/marki. Następnie są tworzone konwersacje, które dokładnie emulują daną osobę, na przykład zachowanie językowe odpowiedzi i inne główne cechy.

![Modelowanie osoby przy użyciu klastrów osób mówiących](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>Interpretacja intencji pogawędki

Interfejs Personality Chat ma klasyfikatory intencji w celu zapewnienia odpowiedzi w przypadku intencji pogawędki. Te klasyfikatory nie będą utrudniać zadań ani zapytań informacyjnych. Ogólny klasyfikator czatu określa, czy zapytanie ma intencję pogawędki lub niezobowiązującej rozmowy. Podejmuje on decyzję, korzystając z klasyfikatorów leksykalnych i semantycznych oraz łącząc ich wyniki. Te intencje są uczone przy użyciu danych konwersacyjnych (jako pozytywnych próbek intencji) i zapytań dotyczących wyszukiwania/zadań (jako negatywnych próbek intencji).

Inne klasyfikatory intencji podrzędnych są używane do identyfikowania podklas pogawędek, aby ograniczyć typy pogawędek, na które reaguje usługa, na przykład: życzenia, gratulacje, opinie itd. Te klasyfikatory uczenia głębokiego, wykorzystujące model semantyczny CDSSM (Convolutional Deep Structured Semantic Model), konwertują wszystkie zapytania w wektory. Są one uczone przy użyciu dziesiątek tysięcy wyselekcjonowanych zapytań dla intencji podrzędnych. Następnie klasyfikator dopasowuje zapytanie do istniejących, zidentyfikowanych klas intencji, wyszukując najlepiej dopasowany obszar w przestrzeni wektorów.

W celu uniemożliwienia zwrócenia niekorzystnych odpowiedzi wprowadzono cały szereg kontroli, opierając się na wiedzy z inteligentnych agentów, takich jak Zo. Domyślnie projekt Personality Chat jest ustawiony do odpowiadania wyłącznie na rozpoznane intencje użytkownika. Zaleca się przetestowanie, czy projekt Personality Chat nadaje się do Twojej sytuacji. Zapraszamy do przekazywania opinii, jeśli znajdziesz coś, co wymaga dalszego uczenia.
