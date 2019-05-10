---
title: Scenariusz oceny - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer można stosować w dowolnej sytuacji, w której aplikacji można wybrać odpowiednie element, akcji lub produktu do wyświetlenia — Aby ulepszyć środowisko pracy, osiągać lepsze wyniki biznesowe lub zwiększyć wydajność.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: e51ef9afd0e49b690a4f9cab09fdfbd3e86eee66
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441041"
---
# <a name="where-can-you-use-personalizer"></a>Gdzie można używać usługi Personalizacja?

Użyj Personalizer w sytuacji, gdy Twoja aplikacja potrzebuje do wybierz element w prawo, akcji lub produktu do wyświetlenia — Aby ulepszyć środowisko pracy, osiągać lepsze wyniki biznesowe lub zwiększyć wydajność. 

Personalizer korzysta z uczenia maszynowego, aby wybrać akcję dla użytkownika. Wybór zależy znaczne zwiększenie ilości, jakości i dystrybucji danych wysyłanych do usługi.

### <a name="checklist-for-applying-personalizer"></a>Lista kontrolna dotycząca stosowanie Personalizer


Personalizer można stosować w sytuacjach, gdzie:

* Masz firmy lub cel jego użyteczność aplikacji.
* Odbywa się się w Twojej aplikacji, gdzie udostępniających kontekstowych decyzji co do wyświetlenia użytkownikom poprawi tego celu.
* Najlepszy wybór można i powinny zostać uzyskane od wynik za wynagrodzeniem zachowanie i łączna liczba zbiorowe użytkownika.
* Korzystanie z uczenia maszynowego na potrzeby personalizacji następuje [wytycznych Użyj odpowiada](ethics-responsible-use.md) i wybranej opcji.
* Kontekstowe decyzja może być wyrażona jako klasyfikacji do najlepszej opcji (Akcja) z ograniczony zestaw opcji.
* Jak dobrze wybór w rankingu, pracował dla twojej aplikacji można określić mierząc niektóre aspekty zachowania użytkowników i przedstawienie jej w _nagradzaj wynik_. To jest liczbą z zakresu od -1 do 1.
* Wynik za wynagrodzeniem nie Przenieś zbyt wiele czynników mylących lub zewnętrznych czynników. Czas trwania eksperymentu jest tyle niskie, że wynik za wynagrodzeniem może zostać obliczony, gdy są one nadal obowiązują.
* Kontekst dla ranga można wyrazić jako lista co najmniej 5 [funkcji](concepts-features.md) , jeśli uważasz może pomóc wybrać odpowiednie opcje, a które nie zawierają danych osobowych. (PII).
* Informacje dotyczące zawartości każdej z opcji, _akcji_, jako lista co najmniej 5 [funkcji](concepts-features.md) traktować pomocy Personalizer wprowadzi właściwym wyborem.
* Aplikacja może przechowywać dane dla długotrwałych wystarczających do historii interakcji co najmniej 100 000.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Uwagi dotyczące stosowania Personalizer uczenia maszynowego

Personalizer opiera się na wzmocnienie uczenia i podejście, machine Learning, czyli prowadzone przez opinii, że nadajesz mu. 

Personalizer dowiesz się najlepiej w sytuacjach, gdzie:

* Ma wystarczającej liczby zdarzeń na bieżąco optymalne personalizacji, jeśli problem drifts wraz z upływem czasu (takie jak preferencje w wiadomości lub sposób). Personalizer dostosuje się do ciągłych zmian w rzeczywistych warunkach, ale wyniki nie będą optymalne, jeśli nie ma wystarczającej liczby zdarzeń i danych, aby dowiedzieć się więcej z próbę odnalezienia i rozliczenia w nowych wzorców. Należy wybrać wystarczająco często wykonywanymi przypadek użycia. Należy wziąć pod uwagę, wyszukiwanie przypadków użycia, które zdarzają się co najmniej 500 razy dziennie.
* Kontekstu i działania jest dosyć [funkcji](concepts-features.md) aby ułatwić szkolenia.
* Ma mniej niż 50 akcji do rangi na wywołanie.
* Ustawienia przechowywania danych umożliwiają Personalizer zbierania wystarczającej ilości danych do wykonywania oceny w trybie offline i zasady optymalizacji. Jest to zazwyczaj punktów danych co najmniej 50 000.

## <a name="monitor-effectiveness-of-personalizer"></a>Monitor skuteczności Personalizer

Można monitorować skuteczność Personalizer okresowo wykonując [ocen w trybie offline](concepts-offline-evaluation.md).

## <a name="use-personalizer-with-recommendation-engines"></a>Za pomocą usług Personalizer aparaty rekomendacji

Wiele firm używa aparaty rekomendacji, Narzędzia marketingowe i działających na, segmentacji odbiorców i klastrowania, filtrowania z wykorzystaniem współpracy i innych środków do polecaj produkty z katalogu dużych klientów.

[Repozytorium usługi GitHub firmy Microsoft Recommenders](https://github.com/Microsoft/Recommenders) zawiera przykłady i najlepsze rozwiązania dotyczące tworzenia systemy rekomendacji, podana jako notesów programu Jupyter. Prezentuje przykłady pracy przygotowywania danych, budowanie modeli, oceny, dostosowywania i operacjonalizacji aparaty rekomendacji, w przypadku wielu typowych metod xDeepFM, w tym SAR, ALS, KMS, DKN.

Personalizer może współpracować z aparat rekomendacji, gdy nie jest obecny.

* Aparaty rekomendacji zająć dużo elementów (na przykład, 500 000) i zaleca się to podzbiór (np. 20 najpopularniejszych) z setek lub tysięcy opcji.
* Personalizer przyjmuje niewielką liczbę akcji z dużą ilością informacji o nich i szereguje je w czasie rzeczywistym dla danego kontekstu rozbudowane, podczas gdy większość aparaty rekomendacji tylko kilka atrybutów dotyczących użytkowników, produktów i ich interakcje.
* Personalizer zaprojektowano w celu autonomicznie zapoznaj się z preferencjami użytkownika cały czas, który umożliwia uzyskanie lepsze wyniki, gdzie zawartość zmienia się szybko, takich jak wiadomości, wydarzeń na żywo na żywo zawartości społeczności, zawartości przy użyciu codzienne aktualizacje lub sezonowych zawartości.

Zazwyczaj jest używane do dane wyjściowe to aparat rekomendacji (na przykład o 20 najpopularniejszych produktów dla niektórych klientów) i używać go jako danych wejściowych akcji dla Personalizer.

## <a name="next-steps"></a>Kolejne kroki

[Etyki & aj odpowiada](ethics-responsible-use.md).