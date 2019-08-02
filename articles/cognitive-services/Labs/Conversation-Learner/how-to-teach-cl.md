---
title: Jak uczyć się Conversation Learner-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak uczyć się Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: a18d4c31da4ffeefebd4bda9aa441fdfec062be9
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705263"
---
# <a name="how-to-teach-with-conversation-learner"></a>Nauczanie z użyciem Ucznia konwersacji 

W tym dokumencie wyjaśniono, jakie sygnały Conversation Learner świadome i w jaki sposób uczy się.  

Nauczanie może być rozłożone na dwa różne kroki: Wyodrębnianie jednostek i wybór akcji.

## <a name="entity-extraction"></a>Wyodrębnianie jednostek

W obszarze okładek Conversation Learner używa do wyodrębniania jednostek [Luis](https://www.luis.ai) .  Jeśli znasz już LUIS, to działanie ma zastosowanie do wyodrębniania jednostek w Conversation Learner.

Modele wyodrębniania jednostek są świadome *zawartości* i *kontekstu* w wypowiedź użytkownika.  Na przykład, jeśli wyraz "Seattle" został oznaczony jako miasto w jednej wypowiedź, na przykład "Czym jest Pogoda w Seattle", wyodrębnianie jednostek jest w stanie rozpoznać tę samą zawartość ("Seattle") jako miasto w innym wypowiedź, jak "populacja Seattle", nawet jeśli wyrażenia długości są bardzo różne.  W przeciwnym razie, jeśli "Francis" został rozpoznany jako nazwa w "Zaplanuj spotkanie z Francis", wówczas nową, niewidzianą nazwę można rozpoznać w podobnym kontekście, np. "Konfigurowanie spotkania z działaniem".  Uczenie maszynowe wnioskuje o uczestnictwo w zawartości, kontekście lub obu, na podstawie przykładów szkoleniowych.

Obecnie wyodrębnianie jednostek jest świadome tylko zawartości bieżącego wypowiedźu.  W przeciwieństwie do wyboru akcji (poniżej), nie ma informacji o historii okien dialogowych, takich jak wcześniejsze wyłączania systemu, wcześniejszego użytkownika lub wcześniej rozpoznanych jednostkach.  W związku z tym zachowanie funkcji wyodrębniania jednostek jest "udostępniane" dla wszystkich wyrażenia długości.  Na przykład jeśli użytkownik wypowiedź "chcę, aby Apple" miał "Apple" oznaczony jako typ jednostki "owoce" w jednym wypowiedź użytkownika, model wyodrębniania jednostek będzie oczekiwać, że ten wypowiedź ("chcę, aby Apple") powinien mieć zawsze "Apple" oznaczony jako "owoc".

Jeśli wyodrębnianie jednostek nie zachowuje się zgodnie z oczekiwaniami, Oto możliwe środki zaradcze:

- Pierwszą rzeczą, którą należy wykonać, jest dodanie więcej przykładów szkoleniowych — w szczególności przykłady, które ujawniają typowe kontekstu jednostki (otaczające wyrazy) lub wyjątki
- Rozważ dodanie do akcji właściwości "oczekiwana jednostka", jeśli jest to konieczne.  Aby uzyskać więcej informacji, zobacz Samouczek dotyczący oczekiwanych jednostek.
- Chociaż istnieje możliwość dodania ręcznego przetwarzania do `EntityExtractionCallback` programu w celu wyodrębnienia jednostek przy użyciu kodu, jest to najmniej zalecane podejście, ponieważ nie będzie ono korzystać z ulepszeń uczenia maszynowego w miarę dojrzewania systemu.

## <a name="action-selection"></a>Wybór akcji

Wybór akcji korzysta z cyklicznej sieci neuronowych, która przyjmuje jako wejściową całą historię konwersacji.  W rezultacie wybór akcji jest procesem stanowym, który ma świadomość poprzedniego wyrażenia długości użytkownika, wartości jednostek i wyrażenia długości systemowej.  

Niektóre sygnały są w sposób naturalny preferowany przez proces uczenia.  Innymi słowy, jeśli Conversation Learner może wyjaśnić decyzje dotyczące wyboru akcji przy użyciu sygnałów "bardziej preferowanych". Jeśli nie, wówczas będzie używać sygnałów "mniej preferowanych".

Poniżej znajduje się tabela przedstawiająca wszystkie sygnały w Conversation Learner oraz te, które są używane przez wybór akcji.  Zwróć uwagę, że kolejność wyrazów w wyrażenia długości użytkownika jest ignorowana.

Wysłać | Preferencja (1 = najbardziej preferowany) | Uwagi
--- | --- | --- 
Działanie systemowe w poprzednim przewróceniu | 1 | 
Jednostki obecne w bieżącym przewróceniu | 1 | 
Określa, czy jest to pierwszy tryb | 1 |
Dokładne dopasowanie wyrazów w bieżącym użytkowniku wypowiedź | 2 | 
Podobne — słowa w bieżącym użytkowniku wypowiedź | 3 | 
Akcje systemu przed wcześniejszym włączeniem | 4 |
Jednostki obecne w zamianach przed bieżącą zmianą | 4 | 
Wyrażenia długości użytkownika przed bieżącą zmianą | 5 | 

> [!NOTE]
> Wybór akcji nie pobiera zawartości akcji systemowych — tekstu, zawartości karty lub nazwy interfejsu API lub zachowania — tylko tożsamość działania systemu.  W związku z tym zmiana zawartości akcji nie spowoduje zmiany zachowania modelu wyboru akcji.
>
> Ponadto, że zawartość/wartości jednostek nie są używane — tylko obecność/nieobecność.

Jeśli wybór akcji nie zachowuje się zgodnie z oczekiwaniami, Oto możliwe środki zaradcze:

- Dodaj więcej okien dialogowych uczenia, w szczególności okna dialogowe pokazujące, które sygnały wyboru akcji powinny zwrócić uwagę na to.  Na przykład jeśli wybór akcji powinien preferować jeden sygnał nad innym, podaj przykłady pokazujące preferowany sygnał w tym samym stanie, a inne sygnały są różne.  Niektóre sekwencje mogą poznawać kilku okna dialogowego szkoleń.
- Dodaj jednostki "wymagane" i "niekwalifikujące się" do definicji akcji.  Te limity, gdy akcje są dostępne i mogą być przydatne do wyrażania reguł biznesowych i niektórych typowych wzorców. 

## <a name="updates-to-models"></a>Aktualizacje modeli

Za każdym razem, gdy dodajesz lub edytujesz okno dialogowe jednostki, akcji lub szkolenia w interfejsie użytkownika, generuje ono żądanie ponownego uczenia modelu wyodrębniania jednostek oraz modelu wyboru akcji.  To żądanie jest umieszczane w kolejce, a ponowne szkolenie jest wykonywane asynchronicznie.  Gdy nowy model jest dostępny, jest używany od tego momentu w celu wyodrębnienia jednostek i wyboru akcji.  Ten proces ponownego uczenia często trwa około 5 sekund, ale może być dłuższy, jeśli model jest skomplikowany lub jeśli obciążenie usługi szkoleń jest wysokie.

Ponieważ szkolenia są wykonywane asynchronicznie, możliwe, że wprowadzone zmiany nie są natychmiast odzwierciedlane.  Jeśli wyodrębnianie jednostek lub wybór akcji nie zachowuje się zgodnie z oczekiwaniami na podstawie zmian wprowadzonych w ciągu ostatnich 5-10 sekund, może to być przyczyną problemu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wartości domyślne i granice](./cl-values-and-boundaries.md)
