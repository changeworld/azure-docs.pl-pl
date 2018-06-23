---
title: Wsadowe testowanie aplikacji LUIS - Azure | Dokumentacja firmy Microsoft
description: Umożliwia testowanie partii ciągłą pracę w swojej aplikacji, aby dostosować go i zwiększyć jej opis języka.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 3803df32d6431b8413e8df0837ed62b2e4344cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348184"
---
# <a name="batch-testing-in-luis"></a>Testowanie w LUIS partii

Testowanie partii weryfikuje użytkownika [active](luis-concept-version.md#active-version) uczonego modelu do mierzenia jego dokładności prognozy. Test partii ułatwia przeglądanie dokładność każdego zamiar i jednostek w bieżącym uczonego modelu na wykresie. Przejrzyj wyniki testu partii podjęcie odpowiednich działań, aby poprawić dokładność, takie jak dodanie więcej zniesławiających przykład celem, jeśli aplikację często nie można zidentyfikować poprawne opcje.

## <a name="group-data-for-batch-test"></a>Grupowanie danych w partii testu
Należy pamiętać, że używane do testowania partii zniesławiających dopiero zaczynasz korzystać z LUIS. Jeśli masz elementu dataset zniesławiających dzielenia zniesławiających do trzech zestawów: zniesławiających dodane do celem, zniesławiających odebranych z opublikowanych punktu końcowego i zniesławiających używany do testów partii LUIS po jego przygotowaniu. 

## <a name="a-dataset-of-utterances"></a>Element dataset zniesławiających
Przesyłanie pliku wsadowego zniesławiających, znany jako *dataset*, testowania partii. Zestaw danych jest plik w formacie JSON zawierającą maksymalnie 1000 etykietą **z systemem innym niż duplikat** zniesławiających. Można testować maksymalnie 10 zestawów danych w aplikacji. Jeśli potrzebujesz więcej testów, usunąć zestaw danych, a następnie dodaj nową.

|**Reguły**|
|--|
|* Nie zduplikowane zniesławiających|
|Brak elementów podrzędnych hierarchiczna jednostki|
|zniesławiających 1000 lub mniej|

* Duplikaty są traktowane jako zgodne dokładnie taki ciąg znaków, nie dopasowań, które są najpierw stokenizowanego. 

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>
## <a name="batch-file-format"></a>Format pliku wsadowego
Plik wsadowy składa się z zniesławiających. Każdy utterance musi mieć oczekiwanego prognozowania konwersji oraz wszelkie [rozpoznane maszyny jednostek](luis-concept-entity-types.md#types-of-entities) mają zostać wykryte. 

Przykładowy plik wsadowy następująco:

   [!code-json[Valid batch test](~/samples-luis/documentation-samples/batch-testing/travel-agent-1.json)]


## <a name="common-errors-importing-a-batch"></a>Typowe błędy importowania partii
Typowe błędy: 

> * Więcej niż 1000 zniesławiających
> * Obiekt JSON utterance, który nie ma właściwości jednostki

## <a name="batch-test-state"></a>Stan testu partii
LUIS śledzi stan każdego zestawu danych ostatniego testu. W tym datę ostatniego uruchomienia rozmiar (liczba zniesławiających w partii) i wynik ostatniego (Liczba pomyślnie przewidywane zniesławiających).

<a name="sections-of-the-results-chart"></a>
## <a name="batch-test-results"></a>Wyniki testu partii
Wynik testu partii jest wykres punktowy, znany jako błąd macierzy. Ten wykres jest porównanie sposób 4 zniesławiających w pliku i przewidywane zamiar bieżącego modelu i jednostek. 

Punktów danych na **fałszywych** i **False ujemna** sekcje wskazać błędów, które należy zbadać. W przypadku wszystkich punktów danych na **dodatnią wartość True,** i **ujemna wartość True** sekcje dokładność aplikacji jest idealne dla tego zestawu danych.

![Cztery sekcje wykresu](./media/luis-concept-batch-test/chart-sections.png)

Ten wykres ułatwia zniesławiających, które prognozuje LUIS niepoprawnie oparte na jego bieżący szkolenia. Wyniki są wyświetlane na region wykresu. Wybierz poszczególne punktów na wykresie, aby zapoznać się z informacjami utterance lub wybierz nazwę regionu, aby przejrzeć wyniki utterance w tym regionie.

![Testowanie usługi partia zadań](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Błędy w wynikach
Błędy w teście partii wskazują lokalizacji docelowych, które nie są przewidzieć zgodnie z opisem w pliku wsadowego. Błędy są oznaczone w sekcjach red wykresu. 

Sekcji dodatnią wartość false wskazuje, że utterance dopasować zamiar lub jednostki podczas nie powinien on zawierać. Ujemna wartość false oznacza, że utterance niezgodny zamiar lub jednostki po powinien mieć. 

## <a name="fixing-batch-errors"></a>Naprawianie błędów partii
Jeśli występują błędy podczas testowania partii, możesz dodać więcej zniesławiających do celem lub etykietę więcej zniesławiających z jednostką ułatwiające LUIS należy rozróżnić lokalizacji docelowych. Jeśli dodaniu zniesławiających i etykietą get ich i nadal prognozowania błędy podczas testowania partii, należy rozważyć dodanie [listy frazy](luis-concept-feature.md) funkcji z słownictwa specyficznego dla domeny ułatwiające LUIS szybciej Dowiedz się więcej. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [test partii](luis-how-to-batch-test.md)