---
title: Funkcja pytania i zgodne, za pomocą usługi Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: Jak używać różnych metod uczenia maszynowego skuteczne odpowiadające Otwórz zakończył zapytań do istniejących wcześniej — często zadawane pytania/odpowiedzi na pytania pary.
services: machine-learning
documentationcenter: ''
author: mezmicrosoft
editor: mezmicrosoft
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: mez
manager: tihazen
ms.openlocfilehash: 33f807a4a0bbc4afd1f2fbe017f8913eccacc34b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>Funkcja pytania i zgodne, za pomocą usługi Azure Machine Learning workbench
Udzielenie odpowiedzi na pytania zakończone Otwórz jest trudne i często wymaga nakładu pracy od ekspertów z danej dziedziny (msp). Aby zmniejszyć zapotrzebowanie na wewnętrzny MSP, firm często tworzyć listy — często zadawane pytania (FAQ) jako sposób udzielania pomocy użytkownikom. W tym przykładzie których są wyświetlane różne metody learning skuteczne maszyny do dopasowania Otwórz zakończone zapytań do istniejące pary odpowiedzi na pytania / — często zadawane pytania. W tym przykładzie przedstawiono prosty proces budowania rozwiązania przy użyciu Azure Machine Learning Workbench. 

## <a name="link-to-the-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Przegląd

W tym przykładzie rozwiązano problem mapowania pytania użytkownika na istniejącym pytanie i odpowiedź pary (Q & A) jako jest zwykle zapewniany liście — często zadawane pytania (FAQ) lub w Q & pary obecne w witrynach sieci Web, takich jak [stosu Przepełnienie](https://stackoverflow.com/). Istnieje wiele metod, aby dopasować pytanie, aby jego prawidłowa odpowiedź, takich jak znajdowanie odpowiedź, która jest najbardziej podobny do pytania. Jednak w tym przykładzie otwórz zakończone pytania są dopasowywane do wcześniej zadawane pytania przez przy założeniu, że wszystkie odpowiedzi w odpowiedzi na pytanie pozwala uzyskać odpowiedzi na kilka pytań semantycznie równoważne.

Klucza kroki wymagane do świadczenia tego rozwiązania są następujące:

1. Czyszczenie i przetwarza dane tekstowe.
2. Dowiedz się więcej informacji wyrażeń, które sekwencji wielu słowa, które dostarczają więcej informacji, w kolejności niż obiekt traktowane niezależnie.
3. Wyodrębnij funkcji z danych tekstowych.
4. Nauczanie modele klasyfikacji tekstu i Ewaluacja modelu wydajności.


## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące uruchamiania w tym przykładzie są następujące:

1. [Konta Azure](https://azure.microsoft.com/free/) (bezpłatnych wersji próbnych są dostępne).
2. Zainstalowana kopia programu [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) następujące [Przewodnik Szybki start instalacji](./quickstart-installation.md) Aby zainstalować ten program i utworzyć obszaru roboczego.
3. W tym przykładzie można uruchomić na dowolnym kontekście obliczeń. Jednak zaleca się uruchamiania na maszynie wielordzeniowych z co najmniej 16GB pamięci RAM i miejsca na dysku 5GB.

## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablon:
1.  Otwórz Azure Machine Learning Workbench
2.  Na **projekty** kliknij przycisk **+** podpisywania i wybierz **nowy projekt**
3.  W **Utwórz nowy projekt** okienka, wypełnij informacje dla nowego projektu
4.  W **szablony projektów wyszukiwania** pole wyszukiwania, wpisz "Q & A dopasowania" i wybierz szablon
5.  Kliknij przycisk **Utwórz**

## <a name="data-description"></a>Opis elementu danych

Zestaw danych, w tym przykładzie jest wymiany danych zrzut stosu przechowywane w [archive.org](https://archive.org/details/stackexchange). Dane te są anonimowe zrzutu całą zawartość zamieszczone przez użytkownika na [Exchange stosu sieci](https://stackexchange.com/). Każdej lokacji w sieci jest w formacie zip oddzielne archiwum, składające się z plikami XML za pomocą 7-zip przy użyciu bzip2 kompresji. Archiwum każdej lokacji zawiera wpisów, użytkowników, głosy, komentarze, PostHistory i PostLinks. 

### <a name="data-source"></a>Źródło danych

W tym przykładzie użyto [zapisuje dane (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) i [PostLinks danych (515 MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) z witryny przepełnienie stosu. Schematu pełne informacje, zobacz [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

`PostTypeId` Pola danych wpisów wskazuje, czy post `Question` lub `Answer`. `PostLinkTypeId` Pola PostLinks danych wskazuje, czy dwa wpisy są połączone zduplikowana. Pytanie wpisów zwykle zawierają niektóre tagi, które słowa kluczowe, które kategoryzowanie zapytania za pomocą innych pytań podobne/duplikatu. Istnieją takie jak niektóre tagi z wysokiej częstotliwości `javascript`, `java`, `c#`, `php` itp., składa się z większą liczbę wpisów pytanie. W tym przykładzie, podzbiór Q & pary z `javascript` tag jest wyodrębniana.

Additionionally, post zapytania mogą być związane z wielu wpisów odpowiedzi lub pytanie zduplikowane wpisy. Aby utworzyć listę — często zadawane pytania z tych dwóch zestawów danych, są traktowane jako pewne kryteria zbierania danych. Trzy zestawy danych skompilowane są zaznaczone, przy użyciu skryptu SQL, który nie jest zawarty w tym przykładzie. Wynikowa opis danych przebiega następująco:

- `Original Questions (Q)`: Pytanie są pytanie i odpowiedzi w witrynie przepełnienie stosu.
- `Duplications (D)`: Pytanie ogłoszenia zduplikowane innych pytań istniejące (`PostLinkTypeId = 3`), które są oryginalne pytania. Powtórzeń są traktowane jako semantycznie równoważne pytań w tym sensie, że odpowiedzi na pytanie oryginalnego również odbierze nowe pytanie zduplikowane.
- `Answers (A)`: Każde pytanie oryginalnego i jego powtórzeń mogą zawierać łącza do więcej niż jeden wpisów odpowiedzi. W tym przykładzie wybiera tylko post odpowiedzi, albo jest akceptowane przez twórca (filtrowane według `AcceptedAnswerId`) lub ma najwyższy wynik (uszeregowane według `Score`) w oryginalnym pytania. 

Kombinacja tych trzech zestawów danych powoduje utworzenie pary pytań i odpowiedzi, gdy odpowiedź (A) jest mapowany na jedno pytanie oryginalnego (Q) i wiele pytań zduplikowane (D), jak pokazano na poniższym diagramie danych:

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Struktura danych

Schemat danych i łącza pobierania bezpośredniego trzech zestawów danych można znaleźć w poniższej tabeli:

| Zestaw danych | Pole | Typ | Opis
| ----------|------------|------------|--------
| [Pytania](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | Identyfikator | Ciąg | Identyfikator unikatowy pytania (klucz podstawowy)
|  | AnswerId | Ciąg | Identyfikator unikatowy odpowiedzi na pytania
|  | text0 | Ciąg | Dane pierwotne tekstu, w tym tytuł i treści pytania
|  | CreationDate | Sygnatura czasowa | Sygnatura czasowa kiedy został poproszony pytania
| [duplikatów](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | Identyfikator | Ciąg | Identyfikator unikatowy dublowania (klucz podstawowy)
|  | AnswerId | Ciąg | Identyfikator odpowiedzi, skojarzony z duplikatów
|  | text0 | Ciąg | Dane pierwotne tekstu, w tym tytuł i treść dublowania
|  | CreationDate | Sygnatura czasowa | Sygnatura czasowa kiedy został poproszony duplikatów
| [odpowiedzi](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | Identyfikator | Ciąg | Identyfikator unikatowy odpowiedzi (klucz podstawowy)
|  | text0 | Ciąg | Dane pierwotne tekstu odpowiedzi


## <a name="scenario-structure"></a>Scenariusz — struktura

Przykład dopasowywania pytań i odpowiedzi został przedstawiony za pomocą trzech typów plików. Pierwszy typ jest szereg notesów Jupyter, zawierające szczegółowe opisy całego przepływu pracy. Drugi typ jest pliki Python zawierać niestandardowe moduły Python do wyodrębnienia frazy uczenie i funkcji. Te moduły Python są ogólnego, aby obsługiwać nie tylko w tym przykładzie, ale także inne przypadki użycia. Trzeci typ to zestaw plików Python, aby dostosować parametry funkcji hyper i śledzenia wydajności modelu przy użyciu Azure Machine Learning Workbench.

Pliki w tym przykładzie są zorganizowane w następujący sposób.

| Nazwa pliku | Typ | Opis
| ----------|------------|--------
| `Image` | Folder | Folder używany do zapisywania obrazów do pliku README
| `notebooks` | Folder | Folder Notesy Jupyter
| `modules` | Folder | Folder modułów środowiska Python
| `scripts` | Folder | Folder plików języka Python
| `notebooks/Part_1_Data_Preparation.ipynb` | Jupyter Notebook | Dostęp do danych przykładowych, wstępnie przetworzyć tekstu i przygotowanie szkolenia i przetestowania zbiory danych
| `notebooks/Part_2_Phrase_Learning.ipynb` | Jupyter Notebook | Dowiedz się fraz informacyjny i tokenizacji tekstu do reprezentacji zbioru z wyrazów (dzioby)
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Jupyter Notebook | Wyodrębnij funkcje, uczenia modele klasyfikacji tekstu i ocena wydajności modelu
| `modules/__init__.py` | Plik Python | Plik init pakiet języka Python
| `modules/phrase_learning.py` | Plik Python | Modułów środowiska Python, używany do transformacji danych pierwotnych i Dowiedz się więcej informacji fraz
| `modules/feature_extractor.py` | Plik Python | Funkcje do uczenia modelu wyodrębnić modułów środowiska Python
| `scripts/naive_bayes.py` | Plik Python | Python, aby dostroić hyper parametrów w modelu prostego klasyfikatora Bayesa
| `scripts/random_forest.py` | Plik Python | Python, aby dostroić hyper parametrów w model lasu losowe
| `README.md` | Pliku markdown | Plik README języka znaczników markdown

> [!NOTE]
> Serie notesów jest wbudowany w obszarze Python 3.5.
> 

### <a name="data-ingestion-and-transformation"></a>Wprowadzanie danych i transformacja

Trzy skompilowane zestawy danych są przechowywane w magazynie obiektów Blob i są pobierane w `Part_1_Data_Preparation.ipynb` notesu.  

Przed szkolenia modele klasyfikacji tekstu, tekst pytania jest oczyszczony i wstępnie przetworzony do wykluczenia wstawki kodu. Uczenie nienadzorowane frazy jest stosowany przez materiałów szkoleniowych informacji szczegółowych sekwencji wielu word. Wyrażenia te są reprezentowane jako wyraz złożony z jednego jednostki w podrzędnym featurization zbioru z wyrazów (dzioby) używany przez modele klasyfikacji tekstu.

Szczegółowe opisy krok przetwarzania wstępnego tekstu i uczenie się frazy można znaleźć w notesach `Part_1_Data_Preparation.ipynb` i `Part_2_Phrase_Learning.ipynb`odpowiednio.

### <a name="modeling"></a>Modelowanie

W tym przykładzie jest przeznaczona do wyniku nowe pytania z istniejącym Q & pary przez tekst szkoleniowy modele klasyfikacji, gdzie każdy istniejące pytań i odpowiedzi parę jest klasą unikatowy i podzbiór zduplikowane pytania dla każdej pary pytań i odpowiedzi są dostępne jako materiałów szkoleniowych. W przykładzie oryginalnego pytania i 75% zduplikowane pytania są zachowywane szkolenia i najbardziej ostatnio oczekujących na opublikowanie 25% zduplikowane pytania są przechowywane w poziomie jako dane do ewaluacji.

Model klasyfikacji korzysta z metody zespół agregacji trzy podstawowe klasyfikatory, w tym **prostego klasyfikatora Bayesa**, **maszyny wektorowa obsługa**, i **lasu losowe**. W każdym klasyfikatora podstawowego `AnswerId` jest używana jako etykieta klasy oraz reprezentacje dzioby jest używane jako funkcje.

Przedstawia proces uczenia modelu `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Ocena

Dwa innej obliczania metryki są używane do oceny wydajności. 
1. `Average Rank (AR)`: wskazuje pozycję średnia, gdzie prawidłowa odpowiedź znajduje się na liście pobrane pary pytań i odpowiedzi (poza pełny zestaw 103 klasy odpowiedzi). 
2. `Top 3 Percentage`: wskazuje procent testu pytania, czy prawidłowa odpowiedź mogą być pobierane w pierwszych trzech wyborów w zwróconym listy punktowane. 

Obliczanie jest przedstawiona w `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Podsumowanie

W tym przykładzie pokazuje, jak tworzyć niezawodne modelu przy użyciu technik analytics dobrze znanego tekstu, takie jak Klasyfikacja uczenie i tekst frazę. Ilustrację również, jak mogą pomóc Azure Machine Learning Workbench z wydajnością modelu programowania i Śledź interakcyjne rozwiązania. 

Niektóre najważniejsze funkcje klucza, w tym przykładzie są:

- Pytania i odpowiedzi pasujących problem można skutecznie rozwiązać przy użyciu modeli klasyfikacji frazy uczenie i tekst.
- Prezentacja programowanie interakcyjne modelu za pomocą usługi Azure Machine Learning Workbench i notesu Jupyter.
- Azure Machine Learning Workbench zarządza Historia uruchomień i zapamiętanych modeli z rejestrowania metryki oceny w celu porównania. Tych funkcji umożliwia szybkie dostrajanie parametrów funkcji hyper i ułatwia identyfikowanie najbardziej modeli.


## <a name="references"></a>Dokumentacja

Hazen J. Tymotka, Krzysztof Richardson, [ _Multiword fraz drzewo wyrażeń ograniczone do modelowania ulepszone modelowania tematu konwersacji mowy_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Język rozmowy technologii Workshop (SLT) 2012 IEEE. IEEE, 2012.

Tymotka J. Hazen, [ _techniki szkolenia MCE tematu do identyfikacji rozmowy dokumentów Audio_ ](http://ieeexplore.ieee.org/abstract/document/5742980/) w transakcji IEEE na Audio, mowy i język przetwarzania, obj. 19 nie. 8, pp. 2451-2460, Nov. 2011.
