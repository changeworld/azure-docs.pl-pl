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
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: b5d38ffeda3600fd90c4ee84acdd29ed599886ae
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707947"
---
# <a name="what-is-personalizer"></a>Co to jest usługa Personalizacja?

Personalizacja Azure to oparta na chmurze usługa interfejsu API, która umożliwia aplikacji wybranie najlepszego środowiska do wyświetlania użytkownikom, uczenie się od ich zbiorczego zachowania w czasie rzeczywistym.

* Podaj informacje o użytkownikach i zawartości oraz Odbierz najważniejsze akcje, aby pokazać użytkowników. 
* Nie ma potrzeby czyszczenia i etykietowania danych przed użyciem narzędzia Personalizacja.
* Prześlij opinię na temat personalizowania, gdy jest to wygodne. 
* Wyświetlanie analizy w czasie rzeczywistym. 

Zobacz pokaz działania narzędzia [Personalizacja](https://personalizercontentdemo.azurewebsites.net/)

## <a name="how-does-personalizer-work"></a>Jak działa program Personalizuj?

Personalizacja korzysta z modeli uczenia maszynowego w celu ustalenia, jakie działania mają najwyższy rangę w kontekście. Aplikacja kliencka zawiera listę możliwych akcji z informacjami o nich; i informacje o kontekście, które mogą zawierać informacje o użytkowniku, urządzeniu itp. Personalizacja określa akcję do wykonania. Gdy aplikacja kliencka użyje wybranej akcji, zapewnia ona informacje zwrotne do personalizacji w formie nagrody. Po otrzymaniu opinii Personalizowanie automatycznie aktualizuje własny model używany do przyszłych rangi. W miarę upływu czasu Personalizacja będzie szkolić jeden model, który może sugerować najlepszą akcję do wyboru w każdym kontekście na podstawie ich funkcji.

## <a name="how-do-i-use-the-personalizer"></a>Jak mogę użyć personalizacji?

![Używanie narzędzia personalizacji do wybierania wideo, które ma być wyświetlane użytkownikowi](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Wybierz środowisko w aplikacji, aby spersonalizować.
1. Utwórz i skonfiguruj wystąpienie usługi personalizacji w Azure Portal. Każde wystąpienie jest pętlą personalizacji.
1. Użyj [interfejsu API rangi](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) , aby wywołać narzędzia do personalizowania z informacjami (_funkcjami_) dotyczącymi użytkowników i zawartością (_Akcje_). Nie musisz podawać czystych danych z etykietami przed użyciem narzędzia Personalizacja. Interfejsy API mogą być wywoływane bezpośrednio lub przy użyciu zestawów SDK dostępnych dla różnych języków programowania.
1. W aplikacji klienckiej Pokaż użytkownika akcję wybraną przez program Personalizujer.
1. Za pomocą [interfejsu API nagradzania](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) można przekazać Opinie do programu Personalizacja wskazujące, czy użytkownik zaznaczył akcję Personalizuj. To jest _[wynik nagrody](concept-rewards.md)_ .
1. Wyświetl analizę w Azure Portal, aby oszacować, jak działa system oraz jak dane pomagają w personalizacji.

## <a name="where-can-i-use-personalizer"></a>Gdzie mogę używać personalizacji?

Na przykład aplikacja kliencka może dodać personalizację do:

* Personalizowanie artykułu wyróżnionego w witrynie sieci Web wiadomości.    
* Optymalizacja umieszczania w usłudze AD w witrynie sieci Web.
* Wyświetl spersonalizowany "zalecany element" w witrynie internetowej dotyczącej zakupów.
* Sugeruj elementy interfejsu użytkownika, takie jak filtry, aby zastosować je do określonego zdjęcia.
* Wybierz odpowiedź bot rozmowy, aby wyjaśnić intencję użytkownika lub zasugerować akcję.
* Ustalanie priorytetów sugestii, jakie użytkownik powinien wykonać w następnym kroku w procesie biznesowym.

Program personalizujer nie jest usługą do utrwalania informacji o profilu użytkownika i zarządzania nimi, a także do rejestrowania preferencji lub historii poszczególnych użytkowników. Personalizacja uczy się od funkcji każdej interakcji w akcji kontekstu w jednym modelu, który może uzyskać maksymalne nagrody w przypadku wystąpienia podobnych funkcji. 

## <a name="personalization-for-developers"></a>Personalizacja dla deweloperów

Usługa personalizacji ma dwa interfejsy API:

* *Ranga*: Użyj interfejsu API rangi, aby określić, która _Akcja_ ma być wyświetlana w bieżącym _kontekście_. Akcje są wysyłane jako tablica obiektów JSON, z IDENTYFIKATORem i informacjami o każdej znich. kontekst jest wysyłany jako inny obiekt JSON. Interfejs API zwraca identyfikator akcji, że aplikacja powinna być renderowana dla użytkownika.
* *Wynagrodzenie*: gdy użytkownik współdziała z Twoją aplikacją, mierzę, jak dobrze działa Personalizacja jako liczbę z zakresu od 0 do 1 i wyślij ją jako [wynik nagrody](concept-rewards.md). 

![Podstawowa sekwencja zdarzeń dla personalizacji](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Następne kroki

* [Co nowego w personalizacji?](whats-new.md)
* [Jak działa program Personalizuj?](how-personalizer-works.md)
* [Co to jest uczenie wzmacniania?](concepts-reinforcement-learning.md)
* [Informacje o funkcjach i akcjach dotyczących żądania rangi](concepts-features.md)
* [Dowiedz się więcej na temat określania wyniku żądania nagrody](concept-rewards.md)
* [Korzystanie z demonstracji interaktywnej](https://personalizationdemo.azurewebsites.net/)
