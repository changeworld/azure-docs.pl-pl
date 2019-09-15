---
title: Ocena scenariusza — Personalizacja
titleSuffix: Azure Cognitive Services
description: Personalizacja może być stosowana w każdej sytuacji, w której aplikacja może wybrać odpowiedni element, akcję lub produkt do wyświetlenia — w celu lepszego działania, osiągnięcia lepszych wyników firmy lub zwiększenia produktywności.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: diberry
ms.openlocfilehash: 87065709b16a5a0c7076584e8fc33c3830319e4f
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999645"
---
# <a name="where-can-you-use-personalizer"></a>Gdzie można używać usługi Personalizacja?

Użyj personalizacji w każdej sytuacji, w której aplikacja musi wybrać odpowiedni element, akcję lub produkt do wyświetlenia, aby zapewnić lepszy komfort, osiągać lepsze wyniki biznesowe lub zwiększyć produktywność. 

Personalizacja używa uczenia maszynowego, aby wybrać akcję, która ma być wyświetlana użytkownikowi. Wybór może się różnić w zależności od ilości, jakości i dystrybucji danych wysyłanych do usługi.

### <a name="checklist-for-applying-personalizer"></a>Lista kontrolna zastosowania personalizacji


Można zastosować personalizację w sytuacjach, w których:

* Użytkownik ma cel biznesowy lub użyteczny dla Twojej aplikacji.
* Masz miejsce w aplikacji, w którym należy wprowadzić kontekstową decyzję, którą należy pokazać użytkownikom w celu usprawnienia tego celu.
* Najlepszym wyborem może być i powinna być uczenie się od rozłącznych zachowań użytkowników i łącznego wyniku.
* Korzystanie z uczenia maszynowego na potrzeby personalizacji odbywa się zgodnie z [właściwymi wskazówkami dotyczącymi użycia](ethics-responsible-use.md) .
* Decyzja kontekstowa może być wyrażona jako Klasyfikacja najlepszej opcji (Akcja) z ograniczonego zestawu opcji.
* Sposób, w jaki zamierzone wybór działa dla aplikacji, można określić przez zmierzenie pewnego aspektu zachowania użytkownika i wyrażanie go w _wyniku nagrody_. Jest to liczba z przedziału od-1 do 1.
* Wynik nagrody nie powoduje zbyt wielu lub zewnętrznych czynników. Czas trwania eksperymentu jest niski, ponieważ wynik nagrody może być obliczany, gdy nadal ma zastosowanie.
* Kontekst dla rangi można wyrazić jako listę co najmniej 5 [funkcji](concepts-features.md) , które można uznać za właściwy wybór i który nie zawiera informacji umożliwiających identyfikację użytkownika. (PII).
* Istnieją informacje o każdej wybranej zawartości, _akcji_, jako lista co najmniej 5 [funkcji](concepts-features.md) , które należy wziąć pod uwagę, aby ułatwić sobie wybranie.
* Aplikacja może przechowywać dane przez wystarczająco dużo, aby gromadzić historię co najmniej 100 000 interakcji.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Zagadnienia dotyczące uczenia maszynowego dotyczące zastosowania personalizacji

Personalizacja jest oparta na nauce wzmacniania, która jest podejściem do uczenia maszynowego, które wyraża opinię. 

Personalizowanie nauczy się najlepiej w sytuacjach, gdy:

* Istnieje wystarczająco dużo zdarzeń, aby zachować dostęp do optymalnej personalizacji, jeśli problem zostanie odczytany z upływem czasu (na przykład preferencje w wiadomościach lub w sposób). Personalizowanie dostosuje się do ciągłej zmiany w świecie rzeczywistym, ale wyniki nie będą optymalne, jeśli nie ma wystarczającej liczby zdarzeń i danych, które należy poznać, aby odkryć i rozliczyć nowe wzorce. Należy wybrać przypadek użycia, który ma być często wystarczający. Rozważ wyszukiwanie przypadków użycia, które wystąpią co najmniej 500 razy dziennie.
* Kontekst i akcje mają wystarczającą liczbę [funkcji](concepts-features.md) , aby ułatwić uczenie się.
* Istnieje mniej niż 50 akcji do rangi na wywołanie.
* Ustawienia przechowywania danych umożliwiają personalizację gromadzenie wystarczającej ilości danych w celu przeprowadzenia oceny w trybie offline i optymalizacji zasad. Zwykle jest to co najmniej 50 000 punktów danych.

## <a name="monitor-effectiveness-of-personalizer"></a>Monitorowanie skuteczności personalizowania

Skuteczność personalizowania można monitorować okresowo przez wykonywanie [ocen w trybie offline](concepts-offline-evaluation.md).

## <a name="use-personalizer-with-recommendation-engines"></a>Używanie personalizacji z aparatami rekomendacji

Wiele firm używa aparatów rekomendacji, narzędzi marketingowych i kampanii, segmentacji odbiorców i klastrowania, filtrowania do współpracy i innych metod, aby zalecać produkty z dużego wykazu klientom.

[Repozytorium GitHub firmy Microsoft](https://github.com/Microsoft/Recommenders) zawiera przykłady i najlepsze rozwiązania dotyczące kompilowania systemów zaleceń, które są udostępniane jako notesy Jupyter. Zawiera ona przykładowe działania dotyczące przygotowywania danych, tworzenia modeli, oceniania, dostrajania i operacjonalizowaniai aparatów rekomendacji dla wielu wspólnych metod, takich jak xDeepFM, SAR, ALS, KMS, DKN.

Personalizowanie może współdziałać z aparatem rekomendacji, gdy jest obecny.

* Aparaty rekomendacji pobierają duże ilości elementów (na przykład 500 000) i zalecają podzbiór (np. 20 najważniejszych) z setek lub tysięcy opcji.
* Personalizacja wykonuje niewielką liczbę akcji z wieloma informacjami o nich i ustala ich rangę w czasie rzeczywistym dla danego rozbudowanego kontekstu, a większość aparatów rekomendacji używa tylko kilku atrybutów dotyczących użytkowników, produktów i ich interakcji.
* Personalizacja została zaprojektowana, aby samodzielnie eksplorować preferencje użytkownika przez cały czas, co zapewni lepsze wyniki, dzięki czemu zawartość będzie szybko zmieniana, taka jak Aktualności, wydarzenia na żywo, zawartość społeczności na żywo, zawartość z codziennymi aktualizacjami lub zawartość sezonowa.

Typowym zastosowaniem jest przejęcie danych wyjściowych aparatu rekomendacji (na przykład 20 najważniejszych produktów dla określonego klienta) i użycie go jako akcji wejściowych dla personalizacji.

## <a name="next-steps"></a>Następne kroki

[Etyka & odpowiedzialna za użycie](ethics-responsible-use.md).