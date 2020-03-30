---
title: Wyodrębnianie funkcji N-Gram z odwołania do modułu tekstowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu Wyodrębnij N-Gram w usłudze Azure Machine Learning do featurize danych tekstowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: efe09c1d516b37c23b024e07ae387772fa7e5992
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477616"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Wyodrębnianie funkcji N-Gram z odwołania do modułu tekstowego

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza). Moduł Wyodrębnij funkcje N-Gram z tekstu *umożliwia featurize* nieustrukturyzowanych danych tekstowych. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Konfiguracja modułu Wyodrębnij n-grama z modułu Text

Moduł obsługuje następujące scenariusze dotyczące używania słownika n-grama:

* [Utwórz nowy słownik n-gramowy](#create-a-new-n-gram-dictionary) z kolumny tekstu wolnego.

* [Użyj istniejącego zestawu funkcji tekstowych,](#use-an-existing-n-gram-dictionary) aby featurize kolumny tekstu wolnego.

* [Wynik lub opublikować model,](#score-or-publish-a-model-that-uses-n-grams) który używa n-gramów.

### <a name="create-a-new-n-gram-dictionary"></a>Tworzenie nowego słownika n-gramowego

1.  Dodaj moduł Wyodrębnij funkcje N-Gram z modułu Text do potoku i połącz zestaw danych zawierający tekst, który chcesz przetworzyć.

1.  Użyj **kolumny Tekst,** aby wybrać kolumnę typu ciągu zawierającą tekst, który chcesz wyodrębnić. Ponieważ wyniki są pełne, można przetwarzać tylko jedną kolumnę naraz.

1. Ustaw **tryb słownictwa,** aby **utworzyć,** aby wskazać, że tworzysz nową listę funkcji n-gramowych. 

1. Ustaw **rozmiar N-Gramów,** aby wskazać *maksymalny* rozmiar n-gramów do wyodrębnienia i przechowywania. 

    Na przykład, jeśli wprowadzisz 3, zostaną utworzone unigramy, bigramy i trygramy.

1. **Funkcja ważenia** określa sposób tworzenia wektora funkcji dokumentu i wyodrębnianie słownictwa z dokumentów.

    * **Waga binarna**: Przypisuje wartość obecności binarnej do wyodrębnionych n-gramów. Wartość dla każdego n-grama wynosi 1, gdy istnieje w dokumencie, a 0 w przeciwnym razie.

    * **Waga TF**: Przypisuje wynik częstotliwości terminów (TF) do wyodrębnionych n-gramów. Wartość dla każdego n-grama jest jego częstotliwością występowania w dokumencie.

    * **Waga IDF**: Przypisuje wynik odwrotnej częstotliwości dokumentu (IDF) do wyodrębnionych n-gramów. Wartość dla każdego n-grama jest dziennikiem wielkości ciała podzielonego przez jego częstotliwość występowania w całym ciele.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **Waga TF-IDF**: Przypisuje wynik częstotliwości/odwrotnej częstotliwości dokumentu (TF/IDF) do wyodrębnionych n-gramów. Wartość dla każdego n-grama jest jego wynik TF pomnożone przez jego wynik IDF.

1. Ustaw **minimalną długość wyrazu** na minimalną liczbę liter, które mogą być używane w dowolnym *pojedynczym słowie* w n-gramie.

1. Użyj **maksymalnej długości wyrazu,** aby ustawić maksymalną liczbę liter, które mogą być używane w dowolnym *pojedynczym słowie* w n-gramie.

    Domyślnie dozwolone jest maksymalnie 25 znaków na słowo lub token.

1. Użyj **minimalnej częstotliwości bezwzględnej dokumentu n-gram,** aby ustawić minimalne wystąpienia wymagane dla włączenia n-grama do słownika n-grama. 

    Na przykład, jeśli używasz domyślnej wartości 5, każdy n-gram musi pojawić się co najmniej pięć razy w korpusie, które mają zostać uwzględnione w słowniku n-gram. 

1.  Ustaw **maksymalny stosunek dokumentu n-gram** do maksymalnego stosunku liczby wierszy zawierających określony n-gram, w stosunku do liczby wierszy w całym ciele.

    Na przykład stosunek 1 wskazuje, że nawet jeśli określony n-gram jest obecny w każdym wierszu, n-gram można dodać do słownika n-gram. Bardziej typowo słowo, które występuje w każdym wierszu, będzie uważane za słowo szumu i zostanie usunięte. Aby odfiltrować słowa szumu zależne od domeny, spróbuj zmniejszyć ten współczynnik.

    > [!IMPORTANT]
    > Szybkość występowania poszczególnych słów nie jest jednolita. Różni się w zależności od dokumentu. Na przykład jeśli analizujesz komentarze klientów dotyczące określonego produktu, nazwa produktu może być bardzo wysoka częstotliwość i blisko słowa szumu, ale być znaczącym terminem w innych kontekstach.

1. Wybierz opcję **Normalizuj wektory operacji n-gram,** aby znormalizować wektory operacji. Jeśli ta opcja jest włączona, każdy wektor funkcji n-gram jest podzielony przez jego normę L2.

1. Prześlij potok.

### <a name="use-an-existing-n-gram-dictionary"></a>Używanie istniejącego słownika n-gramowego

1.  Dodaj moduł Wyodrębnij funkcje N-Gram z modułu Text do potoku i połącz zestaw danych zawierający tekst, który ma być przetwarzany z portem **zestawu danych.**

1.  Użyj **kolumny Tekst,** aby zaznaczyć kolumnę tekstową zawierającą tekst, który chcesz featurize. Domyślnie moduł wybiera wszystkie kolumny **ciągu**tekstowego . Aby uzyskać najlepsze wyniki, należy przetwarzać pojedynczą kolumnę naraz.

1. Dodaj zapisany zestaw danych zawierający wcześniej wygenerowany słownik n-gram i połącz go z portem **słownictwa wejściowego.** Można również połączyć **dane wyjściowe słownictwa wynikowego** wystąpienia nadrzędnego modułu Wyodrębnij funkcje N-Gram z text.

1. W **trybie słownictwa**wybierz opcję **Czytaj tylko** z listy rozwijanej.

   Opcja **ReadOnly** reprezentuje korpus wejściowy dla słownictwa wejściowego. Zamiast obliczania częstotliwości terminów z nowego zestawu danych tekstowych (na lewym wejściu), stosuje się wagi n-gramowe ze słownika wejściowego w stanie następującym.

   > [!TIP]
   > Użyj tej opcji podczas oceniania klasyfikatora tekstu.

1.  Aby uzyskać wszystkie inne opcje, zobacz opisy właściwości w [poprzedniej sekcji](#create-a-new-n-gram-dictionary).

1.  Prześlij potok.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Oceniaj lub publikuj model, który używa n-gramów

1.  Skopiuj **moduł Wyodrębnij funkcje N-Gram z modułu Text** z przepływu danych szkoleniowych do przepływu danych oceniania.

1.  Połącz **dane wyjściowe słownictwa wynikowego** z przepływu danych szkoleniowych do **słownictwa wejściowego** w przepływie danych oceniania.

1.  W przepływie pracy oceniania zmodyfikuj moduł Wyodrębnij funkcje N-Gram z tekstu i ustaw parametr **tryb słownictwa** na **ReadOnly**. Zostaw wszystko to samo.

1.  Aby opublikować potok, zapisz **słownictwa wynik** jako zestaw danych.

1.  Połącz zapisany zestaw danych z modułem Wyodrębnij funkcje N-Gram z tekstu na wykresie punktacji.

## <a name="results"></a>Wyniki

Moduł Extract N-Gram z modułu Text tworzy dwa typy danych wyjściowych: 

* **Wynikowy zestaw danych:** To dane wyjściowe są podsumowaniem analizowanego tekstu w połączeniu z wyodrębnianym n-gramami. Kolumny, które nie zostały zaznaczone w **kolumnie Tekst,** są przekazywane do danych wyjściowych. Dla każdej kolumny tekstu, który analizujesz, moduł generuje następujące kolumny:

  * **Macierz wystąpień n-gramów**: Moduł generuje kolumnę dla każdego n-grama znalezionego w całkowitej corpus i dodaje wynik w każdej kolumnie, aby wskazać wagę n-grama dla tego wiersza. 

* **Słownictwo wynikowe**: Słownictwo zawiera rzeczywisty słownik n-gram, wraz z terminem oceny częstotliwości, które są generowane w ramach analizy. Można zapisać zestaw danych do ponownego użycia z innym zestawem danych wejściowych lub do późniejszej aktualizacji. Można również ponownie użyć słownictwa do modelowania i oceniania.

### <a name="result-vocabulary"></a>Słownictwo wynikowe

Słownictwo zawiera słownik n-gram z terminem oceny częstotliwości, które są generowane w ramach analizy. Wyniki DF i IDF są generowane niezależnie od innych opcji.

+ **Identyfikator:** Identyfikator wygenerowany dla każdego unikatowego n-grama.
+ **NGram**: N-gram. Spacje lub inne separatory wyrazów są zastępowane znakiem podkreślenia.
+ **DF**: Termin ocena częstotliwości dla n-gram w oryginalnym korpusie.
+ **IDF**: Odwrotny wynik częstotliwości dokumentu dla n-gram w oryginalnym korpusie.

Można ręcznie zaktualizować ten zestaw danych, ale mogą być wprowadzania błędów. Przykład:

* Błąd jest wywoływany, jeśli moduł znajdzie zduplikowane wiersze z tym samym kluczem w słowniku wejściowym. Upewnij się, że żadne dwa wiersze w słownictwie nie mają tego samego słowa.
* Schemat wejściowy zestawów danych słownictwa musi być dokładnie zgodny, w tym nazwy kolumn i typy kolumn. 
* Kolumna **Identyfikator** i kolumna **DF** muszą być typu całkowitej. 
* Kolumna **IDF** musi być typu float.

> [!Note]
> Nie podłączaj danych wyjściowych bezpośrednio do modułu Model pociągu. Należy usunąć kolumny tekstu wolnego, zanim są one podawane do modelu pociągu. W przeciwnym razie kolumny tekstu wolnego będą traktowane jako funkcje kategoryczne.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning.
