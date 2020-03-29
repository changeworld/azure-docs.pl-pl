---
title: Ocena funkcji - Personalizer
titleSuffix: Azure Cognitive Services
description: Po uruchomieniu oceny w zasobie Personalizer z witryny Azure portal Personalizer zawiera informacje o tym, jakie funkcje kontekstu i akcje mają wpływ na model.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70242412"
---
# <a name="feature-evaluation"></a>Ocena funkcji

Po uruchomieniu oceny w zasobie Personalizer z [witryny Azure portal,](https://portal.azure.com)Personalizer zawiera informacje o tym, jakie funkcje kontekstu i akcje mają wpływ na model. 

Jest to przydatne w celu:

* Wyobraź sobie dodatkowe funkcje, z których możesz skorzystać, aby uzyskać inspirację z tego, jakie funkcje są ważniejsze w modelu.
* Zobacz, jakie funkcje nie są ważne, i potencjalnie je usunąć lub dokładniej przeanalizować, co może mieć wpływ na użycie.
* Zapewnij zespołom redakcyjnym lub kuratorowskim wskazówki dotyczące nowych treści lub produktów, które warto wprowadzić do katalogu.
* Rozwiązywanie typowych problemów i błędów, które występują podczas wysyłania funkcji do Personalizer.

Ważniejsze cechy mają silniejsze wagi w modelu. Ponieważ te funkcje mają większą wagę, wydają się być obecne, gdy Personalizer uzyskuje wyższe nagrody.

## <a name="getting-feature-importance-evaluation"></a>Uzyskiwanie oceny ważności funkcji

Aby wyświetlić wyniki ważności funkcji, należy przeprowadzić ocenę. Ocena tworzy czytelne dla człowieka etykiety elementów na podstawie nazw funkcji obserwowanych w okresie oceny.

Wynikowe informacje o ważności funkcji reprezentuje bieżący model online Personalizer. Ocena analizuje znaczenie funkcji modelu zapisanego w dniu zakończenia okresu próbnego, po przejściu wszystkich szkoleń przeprowadzonych podczas oceny, z aktualną polityką uczenia się online. 

Wyniki ważności funkcji nie reprezentują innych zasad i modeli przetestowanych lub utworzonych podczas oceny.  Ocena nie będzie zawierać funkcji wysłanych do Personalizer po zakończeniu okresu oceny.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Jak interpretować ocenę ważności funkcji

Personalizer ocenia funkcje, tworząc "grupy" funkcji o podobnym znaczeniu. Można powiedzieć, że jedna grupa ma ogólnie większe znaczenie niż inne, ale w obrębie grupy kolejność funkcji jest alfabetycznie.

Informacje o każdej funkcji obejmują:

* Czy funkcja pochodzi z kontekstu lub akcji.
* Klucz funkcji i wartość.

Na przykład aplikacja do zamawiania lodów może zobaczyć "Context.Weather:Hot" jako bardzo ważną funkcję.

Personalizer wyświetla korelacje funkcji, które, jeśli są brane pod uwagę razem, dają wyższe nagrody.

Na przykład może zostać wyświetlony "Context.Weather:Hot *with* Action.MenuItem:IceCream" oraz "Context.Weather:Cold *with* Action.MenuItem:WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Działania, które można podjąć w oparciu o ocenę funkcji

### <a name="imagine-additional-features-you-could-use"></a>Wyobraź sobie dodatkowe funkcje, z których możesz korzystać

Uzyskaj inspirację z ważniejszych funkcji modelu. Jeśli na przykład w aplikacji mobilnej wideo widzisz "Context.MobileBattery:Low", możesz pomyśleć, że typ połączenia może również sprawić, że klienci zdecydują się zobaczyć jeden klip wideo nad drugim, a następnie dodać funkcje dotyczące typu łączności i przepustowości do aplikacji.

### <a name="see-what-features-are-not-important"></a>Zobacz, jakie funkcje nie są ważne

Potencjalnie usunąć nieistotne funkcje lub dalszej analizy, co może mieć wpływ na użycie. Funkcje mogą być niskie z wielu powodów. Jednym z nich może być to, że rzeczywiście funkcja nie wpływa na zachowanie użytkownika. Ale może to również oznaczać, że funkcja nie jest widoczna dla użytkownika. 

Na przykład witryna wideo może zobaczyć, że "Action.VideoResolution= 4k" jest funkcją o niskim znaczeniu, sprzeczną z badaniami użytkowników. Przyczyną może być to, że aplikacja nawet nie wspomina ani nie pokazuje rozdzielczości wideo, więc użytkownicy nie zmienią swojego zachowania na jej podstawie.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Zapewnienie wskazówek dla zespołów redakcyjnych lub kuratorskich

Podaj wskazówki dotyczące nowych treści lub produktów, które warto wprowadzić do katalogu. Personalizer ma być narzędziem, które zwiększa ludzką wiedzę i zespoły. Jednym ze sposobów jest dostarczanie grupom redakcyjnym informacji o produktach, artykułach lub treściach, które napędzają zachowanie. Na przykład scenariusz aplikacji wideo może pokazać, że istnieje ważna funkcja o nazwie "Action.VideoEntities.Cat:true", monitując zespół redakcyjny, aby przynieść więcej filmów o kotach.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Rozwiązywanie typowych problemów i błędów

Typowe problemy i błędy można rozwiązać, zmieniając kod aplikacji, dzięki czemu nie będzie wysyłać nieodpowiednie lub nieprawidłowo sformatowane funkcje do Personalizer. 

Typowe błędy podczas wysyłania funkcji są następujące:

* Wysyłanie danych osobowych( DANE OSOBOWE). Informacje umożliwiające identyfikację pojedynczą (takie jak imię i nazwisko, numer telefonu, numery kart kredytowych, adresy IP) nie powinny być używane z personalizatorem. Jeśli aplikacja musi śledzić użytkowników, należy użyć nieidentyfikującego identyfikatora UUID lub innego numeru Identyfikatora użytkownika. W większości scenariuszy jest to również problematyczne.
* Przy dużej liczbie użytkowników jest mało prawdopodobne, że interakcja każdego użytkownika będzie ważyć więcej niż cała interakcja populacji, więc wysyłanie identyfikatorów użytkowników (nawet jeśli nie-PII) prawdopodobnie doda więcej hałasu niż wartość do modelu.
* Wysyłanie pól daty i godziny jako precyzyjnych znaczników czasu zamiast featurized wartości czasu. O funkcje, takie jak Context.TimeStamp.Day=Poniedziałek lub "Context.TimeStamp.Hour"="13" jest bardziej przydatne. Dla każdego z nich będą znajdować się co najwyżej 7 lub 24 wartości obiektów. Ale "Context.TimeStamp":"1985-04-12T23:20:50.52Z" jest tak precyzyjny, że nie będzie sposobu, aby się z niego uczyć, ponieważ nigdy się nie powtórzy.

## <a name="next-steps"></a>Następne kroki

Poznaj [skalowalność i wydajność](concepts-scalability-performance.md) dzięki personalizerowi.

