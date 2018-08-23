---
title: Pytania i zgodne, za pomocą usługi Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: Jak dopasować Otwórz przy użyciu różnych metod uczenia maszynowego skuteczne Zakończono zapytań do istniejących wcześniej — często zadawane pytania/odpowiedzi na pytania pary.
services: machine-learning
documentationcenter: ''
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 33d95e0c17e8b9b18313cb0854532337ec76cfd1
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056061"
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>Pytania i zgodne, za pomocą usługi Azure Machine Learning workbench
Odpowiadanie na pytania Zakończono open jest trudne i często wymaga ręcznej pracy od ekspertów z danej dziedziny (msp). Aby zmniejszyć zapotrzebowanie na wewnętrzny MSP, firm często tworzyć listy często zadawanych pytań (FAQ) jako sposób wspomaganie użytkowników. W tym przykładzie przedstawiono różne metody learning skuteczne maszyny do dopasowania Otwórz Zakończono zapytań do istniejących wcześniej — często zadawane pytania/odpowiedzi na pytania pary. W przykładzie pokazano ułatwiający projektowanie procesu tworzenia rozwiązania przy użyciu usługi Azure Machine Learning Workbench. 

## <a name="link-to-the-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Przegląd

W tym przykładzie rozwiązuje problem mapowania pytania użytkownika na istniejącym pytanie i odpowiedź pary (pytania i odpowiedzi) jak jest zwykle zapewniany na liście — często zadawane pytania (FAQ) lub pytania & pary, które są obecne w witrynach sieci Web, takich jak [stosu Overflow](https://stackoverflow.com/). Dostępnych jest wiele metod, aby dopasować pytań do jego poprawną odpowiedź, takich jak Znajdowanie odpowiedzi, który jest najbardziej podobny do pytania. Jednak w tym przykładzie otwórz Zakończono pytania są dopasowywane do wcześniej zadawanymi pytaniami, przy założeniu, że każdej odpowiedzi w często zadawanych PYTAŃ mogą odpowiedzieć wielu semantycznie równoważne pytania.

Kluczowe kroki wymagane do dostarczenia tego rozwiązania są następujące:

1. Czyszczenie i przetwarzanie danych tekstowych.
2. Dowiedz się więcej i wyrażeń, które są sekwencje wielu słów, które zawierają więcej informacji, w kolejności niż obiekt, gdy traktowane niezależnie.
3. Wyodrębnianie funkcji z danych tekstowych.
4. Szkolenie modeli klasyfikacji tekstu i ocena wydajności modelu.


## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące uruchamiania w tym przykładzie są następujące:

1. [Konta platformy Azure](https://azure.microsoft.com/free/) (bezpłatne wersje próbne są dostępne).
2. Zainstalowana kopia programu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) następujące [przewodnika Szybki start dotyczącego instalacji](../service/quickstart-installation.md) zainstalować ten program i utworzyć obszar roboczy.
3. W tym przykładzie, może uruchomić w dowolnym kontekście obliczeniowym. Jednak zaleca się ją uruchomić na maszynie wielordzeniowych z co najmniej 16GB pamięci RAM i miejsca na dysku 5GB.

## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt aplikacji workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablonu:
1.  Otwieranie usługi Azure Machine Learning Workbench
2.  Na **projektów** kliknij **+** zalogować się i wybrać **nowy projekt**
3.  W **Utwórz nowy projekt** okienku, wprowadź informacje dla nowego projektu
4.  W **Wyszukaj szablony projektów** polu wyszukiwania wpisz ciąg "Q & zgodnego znaku", wybierz szablon
5.  Kliknij przycisk **Utwórz**

## <a name="data-description"></a>Opis danych

Zestawu danych użytego w tym przykładzie jest wymiany danych zrzut stosu przechowywaną w [archive.org](https://archive.org/details/stackexchange). Te dane są anonimowe zrzutu całą zawartość zamieszczaną przez użytkownika na [sieci Stack Exchange](https://stackexchange.com/). Każda lokacja w sieci jest formatowana jako osobne archiwum, składający się z plikami XML zip za pośrednictwem przy użyciu bzip2 kompresji zip 7. Archiwum w każdej lokacji zawiera wpisy, użytkownicy, głosy, komentarze, PostHistory i PostLinks. 

### <a name="data-source"></a>Źródło danych

W tym przykładzie użyto [publikuje dane (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) i [PostLinks danych (515 MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) w witrynie Stack Overflow. Uzyskać pełną schematu, zobacz [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

`PostTypeId` Pola danych wpisy wskazuje, czy wpis jest `Question` lub `Answer`. `PostLinkTypeId` Pola PostLinks danych wskazuje, czy dwa wpisy są połączone zduplikowane. Wpisy zapytania obejmują zazwyczaj kilka znaczników, które są słowami kluczowymi kategoryzujące pytania za pomocą inne pytania podobne/zduplikowane. Istnieją takie jak niektóre tagi o wysokiej częstotliwości `javascript`, `java`, `c#`, `php` itp., składają się z większej liczby wpisów pytanie. W tym przykładzie, podzbiór funkcji pytania i odpowiedzi i pary za pomocą `javascript` tag jest wyodrębniany.

Additionionally, wpis zapytania mogą być związane z wielu wpisów w odpowiedzi lub pytania zduplikowane wpisy. Aby utworzyć listę często zadawanych PYTAŃ z tymi dwoma zestawami danych, są traktowane jako kryteriów zbierania danych. Trzy rodzaje skompilowane dane są zaznaczone, przy użyciu skryptu SQL, która nie znajduje się w tym przykładzie. Wynikowy opis danych jest w następujący sposób:

- `Original Questions (Q)`: Pytanie wpisy są zadawane i odpowiedzi w witrynie Stack Overflow.
- `Duplications (D)`: Pytanie publikuje zduplikowane innych istniejących wcześniej pytań (`PostLinkTypeId = 3`), które są oryginalne pytania. Powtórzeń są uznawane za semantycznie równoważne oryginalnej pytania, w tym sensie, że odpowiedzi na oryginalne pytanie zadane również odpowiedzi na nowe pytanie zduplikowane.
- `Answers (A)`: Każdy oryginalne pytanie zadane i jego powtórzeń może połączyć się z więcej niż jeden wpisów w odpowiedzi. W tym przykładzie wybiera tylko wpis odpowiedzi, albo jest akceptowane przez oryginalny Autor (filtrowane według `AcceptedAnswerId`) lub ma najwyższy wynik (uszeregowane według `Score`) w oryginalnej pytania. 

Kombinacji tych trzech zestawów danych tworzy pary pytań i odpowiedzi, której odpowiedzi (A) jest mapowany na jeden oryginalne pytanie zadane (Q) i wiele pytań zduplikowane (D), jak pokazano na poniższym diagramie danych:

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Struktura danych

Schemat danych i łącza bezpośrednie pobieranie trzech zestawów danych można znaleźć w poniższej tabeli:

| Zestaw danych | Pole | Typ | Opis
| ----------|------------|------------|--------
| [Pytania](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | Identyfikator | Ciąg | Identyfikator unikatowy pytania (klucz podstawowy)
|  | AnswerId | Ciąg | Identyfikator unikatowy odpowiedzi na pytania
|  | Text0 | Ciąg | Dane nieprzetworzony tekst, w tym tytuł i treść zapytania
|  | CreationDate | Znacznik czasu | Sygnatura czasowa kiedy został poproszony pytanie
| [duplikatów](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | Identyfikator | Ciąg | Duplikowanie Unikatowy identyfikator (klucz podstawowy)
|  | AnswerId | Ciąg | Identyfikator odpowiedzi skojarzony z duplikowaniem
|  | Text0 | Ciąg | Dane nieprzetworzony tekst, w tym tytuł dublowania i treść
|  | CreationDate | Znacznik czasu | Sygnatura czasowa podczas duplikacji został poproszony
| [odpowiedzi](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | Identyfikator | Ciąg | Identyfikator unikatowy odpowiedzi (klucz podstawowy)
|  | text0 | Ciąg | Dane nieprzetworzony tekst odpowiedzi


## <a name="scenario-structure"></a>Struktura scenariusza

Przykład pasującego pytań i odpowiedzi jest przedstawiony za pomocą trzech typów plików. Pierwszy typ to seria notesów programu Jupyter, które pokazują krok po kroku opisy całego przepływu pracy. Drugi typ to zbiór plików Python zawierają niestandardowe moduły języka Python dla frazy uczenia i funkcji. Moduły języka Python są rodzajowe, aby obsługiwać nie tylko, w tym przykładzie, ale także innych przypadków użycia. Trzeci typ to zestaw plików języka Python, aby dostroić hiper parametrami i śledzenia wydajności modelu przy użyciu usług Azure Machine Learning Workbench.

Pliki w tym przykładzie są zorganizowane w następujący sposób.

| Nazwa pliku | Typ | Opis
| ----------|------------|--------
| `Image` | Folder | Folder używany do zapisywania obrazów w pliku README
| `notebooks` | Folder | Folder notesów programu Jupyter
| `modules` | Folder | Folder modułów języka Python
| `scripts` | Folder | W folderze plików języka Python
| `notebooks/Part_1_Data_Preparation.ipynb` | Jupyter Notebook | Dostęp do danych przykładowych, wstępne przetwarzanie tekstu, a przygotowywanie szkoleń i testowanie zestawów danych
| `notebooks/Part_2_Phrase_Learning.ipynb` | Jupyter Notebook | Dowiedz się, i fraz i tokenizację tekstu do reprezentacji zbiór z wyrazów (dzioby)
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Jupyter Notebook | Wyodrębnianie funkcji, szkolenie modeli klasyfikacji tekstu i ocena wydajności modelu
| `modules/__init__.py` | Soubor Pythonu | Plik init pakietami języka Python
| `modules/phrase_learning.py` | Soubor Pythonu | Moduły języka Python, umożliwia przekształcanie nieprzetworzonych danych i Dowiedz się więcej i fraz
| `modules/feature_extractor.py` | Soubor Pythonu | Moduły języka Python wyodrębnianie funkcji do trenowania modelu
| `scripts/naive_bayes.py` | Soubor Pythonu | Python Dostosowywanie hiperparametrów w modelu Bayesa firmy
| `scripts/random_forest.py` | Soubor Pythonu | Python Dostosowywanie hiperparametrów w model lasu Random
| `README.md` | Plik markdown | Plik README w języku znaczników markdown

> [!NOTE]
> Seria notesów została stworzona w obszarze Język Python 3.5.
> 

### <a name="data-ingestion-and-transformation"></a>Pozyskiwanie danych i transformacji

Trzy skompilowanych zestawów danych są przechowywane w magazynie obiektów Blob i są pobierane w `Part_1_Data_Preparation.ipynb` notesu.  

Przed szkolenie modeli klasyfikacji tekstu, tekst pytania jest oczyszczony i wstępnie przetworzony do wykluczenia fragmentów kodu. Uczenie nienadzorowane frazy jest stosowany przez materiał szkoleniowy, aby dowiedzieć się więcej i sekwencje wielu słów. Te wyrażenia są reprezentowane jako jednostki pojedynczego wyraz złożony w podrzędnym cechowania zbiór z wyrazów (dzioby) używane przez modeli klasyfikacji tekstu.

Szczegółowy opis krok po kroku wstępne przetwarzanie tekstu i szkolenia frazy znajdują się w notesach `Part_1_Data_Preparation.ipynb` i `Part_2_Phrase_Learning.ipynb`, odpowiednio.

### <a name="modeling"></a>Modelowanie

W tym przykładzie jest przeznaczona do oceniania nowe pytania dla istniejących funkcji pytań i odpowiedzi pary według tekstu szkolenie modeli klasyfikacji, w którym każdy istniejących pytań i odpowiedzi pary jest klasą unikatowy i podzbiór zduplikowane pytania dla każdej pary pytań i odpowiedzi są dostępne jako materiałów szkoleniowych. W tym przykładzie oryginalne pytania i 75% zduplikowane pytania są przechowywane przez szkolenie i najbardziej niedawno opublikowane 25% zduplikowane pytania są przechowywane w poziomie jako dane do ewaluacji.

Model klasyfikacji wykorzystuje metodę zespołu agregacji trzy klasyfikatorów podstawowy, w tym **Bayesa firmy**, **pomocy technicznej Vector Machine**, i **lasu Random**. W każdym klasyfikatora podstawowego `AnswerId` jest używany jako etykieta klasy i reprezentacje dzioby służy jako funkcje.

Przedstawia procesu uczenia modelu `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Ocena

Dwie metryki oceny różnych są używane do oceny wydajności. 
1. `Average Rank (AR)`: wskazuje pozycję średni, w którym poprawną odpowiedź znajduje się na liście pobrane pary pytań i odpowiedzi (z pełnym zestawem 103 klasy odpowiedzi). 
2. `Top 3 Percentage`: wskazuje procent testów pytania, że poprawną odpowiedź mogą być pobierane w opcji trzy pierwsze, w zwróconym elemencie pozycjonowane listy. 

Ocena została przedstawiona w `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Podsumowanie

W tym przykładzie podkreślono sposób używania WKT text analytics technik, takich jak frazy klasyfikacji uczenia i tekst, do tworzenia niezawodnych modeli. Prezentuje również, jak Azure Machine Learning Workbench może ułatwić rozwiązanie interaktywne rozwoju i śledzenie wydajności modelu. 

Oto niektóre kluczowe zmiany w tym przykładzie są następujące:

- Pytania i odpowiedzi pasujących problem można skutecznie rozwiązać przy użyciu modeli klasyfikacji frazy uczenia i tekst.
- Prezentacja interaktywny model programowania za pomocą usługi Azure Machine Learning Workbench i notesu programu Jupyter.
- Usługa Azure Machine Learning Workbench zarządza historii uruchamiania i nauczony modeli przy użyciu rejestrowania metryk oceny, w celu porównania. Te funkcje umożliwia szybkie dostosowywanie parametrów i ułatwia identyfikowanie najlepiej modeli.


## <a name="references"></a>Dokumentacja

Hazen J. Tymotka, Leonard Fred [ _modelowania Multiword fraz z drzewa wyrażeń ograniczone dla ulepszone modelowania temat konwersacji mowy_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Język mówiony technologii Workshop (SLT) 2012 IEEE. IEEE, 2012.

Hazen J. Tymotka, [ _techniki szkolenia MCE Identyfikacja tematów mówionego dźwięku dokumentów_ ](http://ieeexplore.ieee.org/abstract/document/5742980/) w IEEE Transactions on Audio, mowy i przetwarzanie języka, tom 19 nie. 8, pp. 2451-2460, Nov. 2011.
