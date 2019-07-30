---
title: Co to jest usługa Personalizacja?
titleSuffix: Azure Cognitive Services
description: Personalizacja to oparta na chmurze usługa interfejsu API, która pozwala na wybranie najlepszego środowiska, które ma być widoczne dla użytkowników, a także uczenie się przed zachowaniem ich w czasie rzeczywistym.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 99750971e11171c0b315cac38089c216d42c7ba6
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663663"
---
# <a name="what-is-personalizer"></a>Co to jest usługa Personalizacja?

Usługa Personalizacja platformy Azure to chmurowa usługa interfejsu API, która pozwala wybrać najlepsze środowiska dla użytkowników na podstawie ich zachowania w czasie rzeczywistym.

* Podaj informacje o użytkownikach i zawartości oraz Odbierz najważniejsze akcje, aby pokazać użytkowników. 
* Nie ma potrzeby czyszczenia i etykietowania danych przed użyciem narzędzia Personalizacja.
* Prześlij opinię na temat personalizowania, gdy jest to wygodne. 
* Wyświetlanie analizy w czasie rzeczywistym. 
* Użyj personalizacji jako części większego nakładu na naukę danych, aby zweryfikować istniejące eksperymenty.

## <a name="how-does-personalizer-work"></a>Jak działa program Personalizuj?

Personalizacja korzysta z modeli uczenia maszynowego w celu ustalenia, jakie działania mają najwyższy rangę w kontekście. Aplikacja kliencka zawiera listę możliwych akcji z informacjami o nich; i informacje o kontekście, które mogą zawierać informacje o użytkowniku, urządzeniu itp. Personalizacja określa akcję do wykonania. Gdy aplikacja kliencka użyje wybranej akcji, zapewnia ona informacje zwrotne do personalizacji w formie nagrody. Po otrzymaniu opinii Personalizowanie automatycznie aktualizuje własny model używany do przyszłych rangi.

## <a name="how-do-i-use-the-personalizer"></a>Jak mogę użyć personalizacji?

![Używanie narzędzia personalizacji do wybierania wideo, które ma być wyświetlane użytkownikowi](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Wybierz środowisko w aplikacji, aby spersonalizować.
1. Utwórz i skonfiguruj wystąpienie usługi personalizacji w Azure Portal. Każde wystąpienie jest pętlą personalizacji.
1. Użyj zestawu SDK do wywołania narzędzia personalizacji z informacjami (_funkcjami_) dotyczącymi użytkowników i zawartością (_Akcje_). Nie musisz podawać czystych danych z etykietami przed użyciem narzędzia Personalizacja. 
1. W aplikacji klienckiej Pokaż użytkownika akcję wybraną przez program Personalizujer.
1. Użyj zestawu SDK, aby przekazać informacje zwrotne do personalizacji wskazujące, czy użytkownik wybrał akcję Personalizuj. Jest to _wynik nagrody_, zazwyczaj z zakresu od-1 do 1.
1. Wyświetl analizę w Azure Portal, aby oszacować, jak działa system oraz jak dane pomagają w personalizacji.

## <a name="where-can-i-use-personalizer"></a>Gdzie mogę używać personalizacji?

Na przykład aplikacja kliencka może dodać personalizację do:

* Personalizowanie artykułu wyróżnionego w witrynie sieci Web wiadomości.    
* Optymalizacja umieszczania w usłudze AD w witrynie sieci Web.
* Wyświetl spersonalizowany "zalecany element" w witrynie internetowej dotyczącej zakupów.
* Sugeruj elementy interfejsu użytkownika, takie jak filtry, aby zastosować je do określonego zdjęcia.
* Wybierz odpowiedź bot rozmowy, aby wyjaśnić intencję użytkownika lub zasugerować akcję.
* Ustalanie priorytetów sugestii, jakie użytkownik powinien wykonać w następnym kroku w procesie biznesowym.

## <a name="personalization-for-developers"></a>Personalizacja dla deweloperów

Usługa personalizacji ma dwa interfejsy API:

* Wysyłaj informacje (_funkcje_) dotyczące użytkowników i zawartość (_Akcje_) do personalizacji. Personalizacja reaguje na górną akcję.
* Wyślij opinię do programu Personalizowanie o tym, jak dobrze Klasyfikacja działała jako liczba zazwyczaj z zakresu od 0 do 1 (poprzednia sekcja-1 i 1). 

![Podstawowa sekwencja zdarzeń dla personalizacji](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Kolejne kroki

* [Szybki start: Utwórz pętlę opinii wC#](csharp-quickstart-commandline-feedback-loop.md)
* [Korzystanie z demonstracji interaktywnej](https://personalizationdemo.azurewebsites.net/)
