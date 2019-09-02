---
title: 'Wyodrębnij N-gram: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak cechowanie dane tekstowe przy użyciu modułu wyodrębnianie N-gramów w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 450c205f92fc65cad4e552aef3a1f28157d25ab6
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210873"
---
# <a name="extract-n-gram-features-from-text"></a>Wyodrębnij funkcje N-gramowe z tekstu

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Użycie **funkcji Wyodrębnij N-gramy z modułu tekstowego** do *cechowanie* danych tekstowych bez struktury.

Ten moduł obsługuje następujące scenariusze dotyczące korzystania ze słownika n-gram:

* [Utwórz nowy słownik n-gramowy](#create-a-new-n-gram-dictionary) z kolumny bezpłatnego tekstu.

* [Użyj istniejącego zestawu funkcji tekstowych](#use-an-existing-n-gram-dictionary) , aby cechowanie niezależną kolumnę tekstową.

* [Ocena lub publikacja modelu](#score-or-publish-a-model-that-uses-n-grams) używającego n-gramów.

## <a name="configure-extract-n-gram-features-from-text"></a>Skonfiguruj funkcje wyodrębniania N-gramów z tekstu

### <a name="create-a-new-n-gram-dictionary"></a>Tworzenie nowego słownika n-gramowego

1.  Dodaj **funkcje Wyodrębnij N-gram z modułu tekstowego** do eksperymentu i Połącz zestaw danych z tekstem, który chcesz przetworzyć.

1.  **Kolumna tekstowa** służy do wybierania kolumny typu **ciąg** , która zawiera tekst, który ma zostać wyodrębniony. Ponieważ wyniki są pełne, można przetwarzać tylko pojedyncze kolumny naraz.

1. Ustaw **tryb słownictwa** na **Utwórz** , aby wskazać, że tworzysz nową listę funkcji n-gramowych. 

1. Ustaw **rozmiar n-gramy** , aby wskazać *Maksymalny* rozmiar n-gramów, które mają zostać wyodrębnione i zapisane. 

    Na przykład, jeśli wprowadzisz 3, unigrams, i trigrams zostanie utworzony.

1. **Funkcja ważenia** określa sposób tworzenia wektora funkcji dokumentu oraz wyodrębniania słownictwa z dokumentów.

    * **Waga binarna**: Przypisuje wartość obecności binarnej do wyodrębnionych n-gramów. Wartość dla każdego n-gramu jest równa 1, gdy istnieje w danym dokumencie i 0 w przeciwnym razie.

    * **Waga TF**: Przypisuje wynikowe wyniki częstotliwości (**TF**) do wyodrębnionych n-gramów. Wartość dla każdego n-gramu jest częstotliwością występowania w danym dokumencie.

    * **Waga IDF**: Przypisuje wynikowy wskaźnik częstotliwości dokumentu (**IDF**) do wyodrębnionych n-gramów. Wartość każdego n-grama to dziennik o rozmiarze korpus podzielonym według częstotliwości występowania w całej korpus. `IDF = log of corpus_size / document_frequency`
 
    *  **TF — waga IDF**: Przypisuje wynikowy wskaźnik częstotliwości/dokumentu odwracania (**TF/IDF**) do wyodrębnionych n-gramów. Wartość dla każdego n-gramu to wynik TF pomnożony przez jego ocenę IDF.

1. Ustaw **minimalną długość wyrazu** na minimalną liczbę liter, które mogą być używane w *pojedynczym wyrazie* w n-gramowej.

1. Aby ustawić maksymalną liczbę liter, które mogą być używane w pojedynczym wyrazie w n -gramach, należy użyć **maksymalnej długości wyrazu** .

    Domyślnie dozwolone są maksymalnie 25 znaków na słowo lub token.

1. Użyj **minimalnej częstotliwości bezwzględnej dokumentu n-gramowego**, aby ustawić minimalne wystąpienia wymagane przez wszystkie n-gramy do uwzględnienia w słowniku n-gramów. 

    Jeśli na przykład zostanie użyta wartość domyślna 5, wszystkie n-gramy muszą pojawić się co najmniej pięć razy w korpus, aby uwzględnić je w słowniku n-gram. 

1.  Ustaw **maksymalny współczynnik dokumentu n-gramowego** na maksymalny stosunek liczby wierszy zawierających określoną liczbę n-gramów względem liczby wierszy w ogólnej korpus.

    Na przykład, stosunek 1 wskazuje, że nawet jeśli konkretny n-gram jest obecny w każdym wierszu, można dodać n-gram do słownika n-gram. Zwykle wyraz występujący w każdym wierszu jest traktowany jako słowo ignorowane i zostałby usunięty. Aby odfiltrować słowa akustyczne zależne od domeny, spróbuj zmniejszyć ten współczynnik.

    > [!IMPORTANT]
    > Częstotliwość występowania określonych słów nie jest jednorodna, ale różni się od dokumentu do dokumentu. Na przykład jeśli analizujesz Komentarze klienta dotyczące określonego produktu, nazwa produktu może być bardzo wysoka i znajdować się w wyrazie szumu, ale być znaczącym terminem w innych kontekstach.

1. Wybierz opcję **normalizuje wektory funkcji n-gramy** , aby znormalizować wektory funkcji. Jeśli ta opcja jest włączona, każdy wektor funkcji n-gram jest dzielony przez normę L2.

1. Uruchom eksperyment.

### <a name="use-an-existing-n-gram-dictionary"></a>Użyj istniejącego słownika n-gramowego

1.  Dodaj **funkcje Wyodrębnij N-gram z modułu tekstowego** do eksperymentu i Połącz zestaw danych z tekstem, który chcesz przetworzyć na port **DataSet** .

1.  **Kolumna tekstowa** służy do zaznaczania kolumny tekstowej zawierającej tekst, który ma zostać cechowanie. Domyślnie moduł wybiera wszystkie kolumny typu String. Aby uzyskać najlepsze wyniki, przetwórz pojedynczą kolumnę w czasie.

1. Dodaj zapisany zestaw danych zawierający wcześniej wygenerowany słownik n-gram, a następnie połącz go z portem **słownictwa wejściowego** . Istnieje również możliwość połączenia danych wyjściowych **słownika wynikowego** wystąpienia **funkcji Wyodrębnij N-gram z modułu tekstowego** .

1. W przypadku **trybu słownictwa**wybierz z listy rozwijanej następującą opcję aktualizacji:

   * **Tylko do odczytu**: Reprezentuje korpus wejściowy dla słownictwa wejściowego. Zamiast obliczania częstotliwości okresów z nowego tekstu zestawu danych (po lewej stronie), wagi n-gramy ze słownika wejściowego są stosowane w miarę jak.

    > [!TIP]
    > Użyj tej opcji podczas oceniania klasyfikatora tekstu.

1.  Wszystkie inne opcje można znaleźć w opisach właściwości w [poprzedniej sekcji](#create-a-new-n-gram-dictionary).

1.  Uruchom eksperyment.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Ocena lub publikacja modelu używającego n-gramów

1.  Skopiuj **funkcje Wyodrębnij N-gram z modułu tekstowego** z przepływu danych szkoleniowego do przepływu danych oceniania.

1.  Połącz dane wyjściowe słownictwa z przepływu danych szkoleniowego do słownictwa **wejściowego** na przepływu danych oceniania.

1.  W przepływie pracy oceny zmodyfikuj **funkcje Wyodrębnij N-gramy z modułu tekstowego** i wprowadź te zmiany, pozostawiając wszystkie inne elementy:

    - Ustaw parametr **trybu słownictwa** na **tylko do odczytu**.

1.  Aby opublikować eksperyment, Zapisz słownictwo **wyniku** jako zestaw danych.

1.  Następnie podłącz zapisany zestaw danych do **funkcji Wyodrębnij N-gram z modułu tekstowego** na wykresie oceniania.

## <a name="results"></a>Wyniki

**Funkcja Wyodrębnij N-gramy z modułu tekstowego** tworzy dwa typy danych wyjściowych: 

* **Zestaw danych wyników**: Podsumowanie analizowanego tekstu połączonego z oddzielonymi n-gramami. Kolumny, które nie zostały wybrane w **kolumnie tekstowej** , są przekazywane do danych wyjściowych. Dla każdej kolumny analizowanego tekstu moduł generuje następujące kolumny:

  * **Macierz wystąpień n-gram**: Moduł generuje kolumnę dla każdego n-gramu znalezionego w łącznej korpus i dodaje ocenę w każdej kolumnie, aby wskazać wagę n-grama dla tego wiersza. 

* **Słownictwo wyniku**: Słownictwo zawiera rzeczywisty słownik n-gramowy wraz z wynikami częstotliwości, które są generowane w ramach analizy. Zestaw danych można zapisać do ponownego użycia z innym zestawem danych wejściowych lub w późniejszej aktualizacji. Możesz również ponownie użyć słownika do modelowania i oceniania.

### <a name="result-vocabulary"></a>Słownictwo wyniku

Słownictwo zawiera słownik n-gram z wynikami częstotliwości, które są generowane w ramach analizy. Wyniki **DF** i **IDF** są generowane niezależnie od innych opcji.

+ **ID**: Identyfikator wygenerowany dla każdego unikatowego n-gramu.
+ **NGram**: N-gram. Spacje lub inne separatory wyrazów są zastępowane znakiem podkreślenia.
+ **DF**: Wartość współczynnika częstotliwości dla n-grama w oryginalnym korpus.
+ **IDF**: Wynik dotyczący częstotliwości odwracania dokumentu dla n-gramów w oryginalnym korpus.

Możesz ręcznie zaktualizować ten zestaw danych. można jednak wprowadzić błędy. Przykład:

* Błąd jest zgłaszany, jeśli moduł znajdzie zduplikowane wiersze z tym samym kluczem w słowniku wejściowym. Upewnij się, że żadne dwa wiersze w słowniku nie mają tego samego wyrazu.
* Schemat wejściowy zestawów danych słownictwa musi dokładnie pasować, włącznie z nazwami kolumn i typami kolumn. 
* Wartość kolumny **ID** i kolumny wyniku **DF** musi być typu Integer. 
* Kolumna **IDF** musi być typu zmiennoprzecinkowego.

> [!Note]
> Nie Nawiązuj bezpośrednio połączenia danych wyjściowych do modułu **uczenia modelu** . Kolumny wolnych danych należy usunąć przed wprowadzeniem do nich w **modelu uczenia**. w przeciwnym razie kolumny tekstu wolnego będą traktowane jako funkcje kategorii.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 
