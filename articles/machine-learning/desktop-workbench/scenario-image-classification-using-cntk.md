---
title: Klasyfikacja przy użyciu narzędzi CNTK wewnątrz usługi Azure Machine Learning Workbench obrazów | Dokumentacja firmy Microsoft
description: Uczenie, ocenę i wdrożyć model klasyfikacji obrazu niestandardowego za pomocą aplikacji Azure ML Workbench.
services: machine-learning
documentationcenter: ''
author: PatrickBue
ms.author: pabuehle
manager: mwinkle
ms.reviewer: marhamil, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 48c21638fe5756e6527288ed0fdc73dd9e331afd
ms.sourcegitcommit: baed5a8884cb998138787a6ecfff46de07b8473d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "35622217"
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Klasyfikacja obrazów przy użyciu usługi Azure Machine Learning Workbench

Metody klasyfikacji obrazów, można rozwiązać wiele problemów przetwarzania obrazów.
Należą do budowania modeli, które odpowiedzi na pytania takie jak: *jest OBIEKTEM obecnych w obrazie?* gdzie obiekt może być na przykład *pies*, *samochodu*, lub  *Dostarczaj*. Lub bardziej złożone pytania, takie jak: *jakie klasy oka choroby ważności jest evinced przez skanowanie retinal to pacjenta?*.

Ten samouczek adresy rozwiązywania takich problemów. Firma Microsoft pokazują, jak uczenie, ocenę i wdrażanie własnych obrazów Klasyfikacja modelu przy użyciu [Microsoft Cognitive Toolkit (CNTK) ](https://docs.microsoft.com/cognitive-toolkit/) potrzeby uczenia głębokiego.
Znajdują się przykładowe obrazy, ale czytnik można również przenieść zestaw własnych danych i własne niestandardowe modele uczenia.

Rozwiązania tradycyjnie wymagane specjalistyczną wiedzę ręcznie zidentyfikuj i zaimplementować tak zwane wizualizacji komputerowej *funkcji*, która zaznacz odpowiednie informacje w obrazach.
To podejście ręcznego zmienione w 2012 z sławę [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] głębokiego uczenia się papier i w chwili obecnej, aby automatycznie znaleźć te funkcje są używane głębokie sieci neuronowych (DNN).
Jednokierunkowe sieci Dnn doprowadziło do ogromnych poprawę pola nie tylko dla Klasyfikacja obrazów, ale także do innych problemów przetwarzania obrazów, takich jak wykrywanie obiektów i podobieństwa obrazu.


## <a name="link-to-the-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Przegląd

W tym samouczku jest podzielony na trzy części:

- Część 1 pokazuje, jak szkolenie, oceny i wdrażania na systemie klasyfikacji obrazów przy użyciu wstępnie przeszkolonych DNN featurized i szkolenia SVM na jego dane wyjściowe.
- Część 2 następnie pokazano, jak zwiększyć dokładność, na przykład rafinacja DNN, zamiast używać go jako stały featurized.
- Część 3 opisano, jak używać własnego zestawu danych zamiast obrazów podany przykład, a jeśli to konieczne, jak utworzyć swój własny zestaw danych przez oskrobaniu obrazy z sieci.

Doświadczenia dzięki uczeniu maszynowemu i CNTK nie jest wymagana, ale jest pomaga w zrozumieniu podstawowych zasad. Dokładność liczb, szkolenia, godziny itd., zgłaszane w tym samouczku są przeznaczone tylko dla odniesienia, a rzeczywiste wartości podczas uruchamiania kodu prawie na pewno różnią się.


## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące uruchamiania w tym przykładzie są następujące:

1. [Konta platformy Azure](https://azure.microsoft.com/free/) (bezpłatne wersje próbne są dostępne).
2. [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) następujące [przewodnika Szybki start dotyczącego instalacji](../service/quickstart-installation.md) zainstalować ten program i utworzyć obszar roboczy.  
3. Maszyna Windows. System operacyjny Windows jest konieczne, ponieważ Workbench obsługuje tylko Windows i MacOS, podczas firmy Microsoft, Cognitive Toolkit (które zostaną wykorzystane jako biblioteka uczenia głębokiego) obsługuje tylko Windows i Linux.
4. Dedykowanego procesora GPU nie jest wymagane do wykonywania szkolenia SVM w części 1, jednak jest to potrzebne dla rafinacja z systemu DNN opisane w części 2. Jeśli nie mają silnego procesora GPU, aby uczyć się na wielu procesorach GPU lub nie masz maszyny Windows rozważyć, przy użyciu maszyna wirtualna do uczenia głębokiego platformy Azure z systemem operacyjnym Windows. Zobacz [tutaj](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) Przewodnik wdrożenia 1 kliknięcia. Po wdrożeniu łączenie z maszyną Wirtualną za pośrednictwem połączenia pulpitu zdalnego, zainstaluj środowisko robocze istnieje i wykonywania kodu lokalnie z maszyny Wirtualnej.
5. Różnych bibliotek języka Python, takich jak biblioteki OpenCV muszą być zainstalowane. Kliknij przycisk *Otwórz wiersz polecenia* z *pliku* menu w aplikacji Workbench i uruchom następujące polecenia, aby zainstalować te zależności:  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl` Po pobraniu OpenCV koła od http://www.lfd.uci.edu/~gohlke/pythonlibs/ (dokładnej nazwy pliku i wersja zmienić)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`
    - `jupyter nbextension enable --py widgetsnbextension`

### <a name="troubleshooting--known-bugs"></a>Rozwiązywania problemów / znanych błędów
- Procesora GPU jest wymagany w przypadku część 2, a w przeciwnym razie "Normalizacji szkolenie dotyczące procesora CPU nie została jeszcze zaimplementowana partii" zostaje zgłoszony błąd podczas próby zaktualizowania DNN.
- Można uniknąć błędów braku pamięci podczas szkolenia DNN, zmniejszając rozmiar minibatch (zmienna `cntk_mb_size` w `PARAMETERS.py`).
- Kod został przetestowany przy użyciu CNTK 2.2 i powinien również uruchamiania na starszych (Strzałka w górę do wersji 2.0) i nowszych wersji bez lub tylko drobne zmiany.
- W czasie pisania Azure Machine Learning Workbench wystąpiły problemy z przedstawiający notesów większych niż 5 MB. Notesy to duży rozmiar może się zdarzyć, jeśli notes zostaje zapisana wraz z wszystkie komórki wyświetlane dane wyjściowe. Jeśli ten błąd wystąpi, należy otworzyć wiersz polecenia z menu Plik wewnątrz aplikacji Workbench, wykonaj `jupyter notebook`, otworzyć notes, wyczyść wszystkie dane wyjściowe i Zapisz notesu. Po wykonaniu tych kroków, Notes zostanie prawidłowo w usłudze Azure Machine Learning Workbench ponownie otworzyć.
- Wszystkie skrypty znajdujące się w tym przykładzie muszą być wykonywane lokalnie, a nie na przykład środowisku zdalnym platformy docker. Wszystkie notesy muszą być wykonywane za pośrednictwem jądra równa jądra lokalnego projektu o nazwie "PROJECTNAME lokalne" (np. "myImgClassUsingCNTK lokalnego").

    
## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt aplikacji workbench

Aby utworzyć nowy projekt, używając w tym przykładzie jako szablonu:
1.  Otwórz program Azure Machine Learning Workbench.
2.  Na **projektów** kliknij **+** zalogować się i wybrać **nowy projekt**.
3.  W **Utwórz nowy projekt** okienku, wprowadź informacje dla nowego projektu.
4.  W **Wyszukaj szablony projektów** polu wyszukiwania wpisz "Image klasyfikacji" i wybierz szablon.
5.  Kliknij pozycję **Utwórz**.

Te kroki wykonywane tworzy strukturę projektu, pokazano poniżej. Katalog projektu jest ograniczona do być krótsza niż 25 MB, ponieważ usługi Azure Machine Learning Workbench tworzy kopię tego folderu po każdym uruchomieniu (w celu włączenia historii uruchamiania). Dzięki temu wszystkie obrazów i plików tymczasowych są zapisywane do i z katalogu *~/Desktop/imgClassificationUsingCntk_data* (nazywane *DATA_DIR* w tym dokumencie).

  Folder| Opis
  ---|---
  aml_config/|                           Katalog zawierający pliki konfiguracji usługi Azure Machine Learning Workbench
  biblioteki /|                              Katalog zawierający wszystkie funkcje pomocnika języka Python i Jupyter
  Notesy /|                              Katalog zawierający wszystkie notesy
  zasobów /|                              Katalog zawierający wszystkie zasoby (na przykład adres url obrazów Moda)
  skrypty /|                              Katalog zawierający wszystkie skrypty
  PARAMETERS.py|                       Skrypt języka Python, określając wszystkie parametry
  Readme.MD|                           Ten dokument readme


## <a name="data-description"></a>Opis danych

Ten samouczek używa jako uruchomieniem przykładu górny treści zestawu danych tekstury odzieży składający się z maksymalnie 428 obrazów. Każdy obraz jest oznaczona jako jeden z trzech różne tekstury (leopard kropkowana, rozłożone,). Firma Microsoft przechowywane liczbę obrazów mały, aby w tym samouczku, które mogą być wykonywane szybko. Jednak kod jest dobrze przetestowanych i współpracuje z dziesiątkami tysięcy obrazów lub więcej. Wszystkie obrazy zostały pozyskany przy użyciu wyszukiwania obrazów Bing i ręcznie adnotację opisany w [część 3](#using-a-custom-dataset). Obraz, adresy URL z ich odpowiednich atrybutów są wymienione w */resources/fashionTextureUrls.tsv* pliku.

Skrypt `0_downloadData.py` pobiera wszystkie obrazy do *DATA_DIR/obrazy/fashionTexture/* katalogu. Niektóre 428 adresy URL są prawdopodobnie uszkodzone. Nie jest problemem, a po prostu oznacza, że mamy nieco mniej obrazów celów szkoleniowych i testów. Wszystkie skrypty znajdujące się w tym przykładzie muszą być wykonywane lokalnie, a nie na przykład środowisku zdalnym platformy docker.

Na poniższej ilustracji pokazano przykłady dla atrybutów z kropkami (po lewej), rozłożone (środkowa) i leopard (po prawej). Adnotacje zostały wykonane zgodnie z elementu odzieży górny treści.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>Część 1 — modelowanie, uczenie i ocenę

W pierwszej części tego samouczka firma Microsoft jest szkolenie system, który używa, lecz nie modyfikowanie wstępnie przeszkolonych sieci neuronowej. To wstępnie przeszkolonych DNN jest używany jako featurized i liniowej SVM jest uczony do prognozowania atrybutu (kropkowane, rozłożony lub leopard) danego obrazu.

Firma Microsoft teraz opisane tego podejścia w szczegółowych, krok po kroku i Pokaż skrypty, które należy wykonać. Aby sprawdzić, które pliki są zapisywane i gdzie są zapisywane w zaleca się po każdym kroku.

Wszystkie parametry ważne są określone, a podano krótkie objaśnienie, w jednym miejscu: `PARAMETERS.py` pliku.




### <a name="step-1-data-preparation"></a>Krok 1: Przygotowanie danych
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

Notes `showImages.ipynb` może służyć do wizualizacji, obrazów i poprawić ich adnotacji, zgodnie z potrzebami. Aby uruchomić Notes, otwórz go w usłudze Azure Machine Learning Workbench kliknij na "Uruchamianie notesu serwera", jeśli ta opcja jest wyświetlana, zmień jądra lokalnego projektu o nazwie "PROJECTNAME hodowla w lokalnym" (np. "myImgClassUsingCNTK lokalny"), a następnie wykonaj wszystkie komórki w Notes. Zobacz sekcję dotyczącą rozwiązywania problemów w tym dokumencie, jeśli wystąpi błąd, skarżących się, że Notes jest zbyt duży do wyświetlenia.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Teraz uruchom skrypt o nazwie `1_prepareData.py`, które przypisuje wszystkich obrazów do szkolenia albo ustawić, lub zestaw testów. To przypisanie jest wzajemnie wykluczających się — brak obrazu szkolenia służy także do testowania lub na odwrót. Domyślnie losowy 75% obrazów z każdej klasy atrybutu są przypisane do szkoleń, a pozostałe 25% są przypisane do testowania. Wszystkie dane generowane przez skrypt są zapisywane w *DATA_DIR/proc/fashionTexture/* folderu.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>Krok 2: Doprecyzowanie sieci Neuronowej
`Script: 2_refineDNN.py`

Jak możemy wyjaśniono w części 1 tego samouczka, wstępnie szkolone DNN jest przechowywana w stałej (czyli nie jest dostosowany). Jednak skrypt o nazwie `2_refineDNN.py` jest nadal wykonywane w część 1, ponieważ ładowania wstępnie przeszkolonych [siecią ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) modelu [2] i modyfikuje je na przykład, aby umożliwić wyższa rozdzielczość obrazu wejściowego. Ten krok jest szybkie (w sekundach) i nie wymaga procesora GPU.

W części 2 samouczka modyfikację PARAMETERS.py pliku powoduje, że `2_refineDNN.py` skrypt, aby również doprecyzować, wstępnie szkolone DNN. Domyślnie przeprowadzamy 45 epok szkolenia podczas uściślenia.

W obu przypadkach końcowego modelu są następnie zapisywane do pliku *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>Krok 3: Oceniająca wszystkie obrazy DNN
`Script: 3_runDNN.py`

Firma Microsoft może teraz używać (prawdopodobnie dostosowany) systemu DNN w ostatnim kroku do cechowanie naszych obrazów. Podany obraz jako dane wejściowe DNN, wynikiem jest wektor wartości zmiennoprzecinkowe 512 przedostatni warstwy modelu. Ta wektor jest dużo mniejsze wymiarowego niż sam obraz. Niemniej jednak powinien zawierać (i nawet wyróżnić) wszystkie informacje w odpowiednich atrybut obrazu, który jeśli element odzieży ma kropkowana, rozłożonych, rozpoznawał obrazu lub tekstury leopard.

Wszystkie oświadczenia obraz DNN są zapisywane do pliku *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>Krok 4: Obsługa Vector Machine szkolenia
`Script: 4_trainSVM.py`

Wartości zmiennoprzecinkowe 512 reprezentacje obliczane w ostatnim kroku są obecnie używane do trenowania klasyfikatora SVM: podany obraz jako dane wejściowe, SVM wyświetla wynik dla każdego atrybutu być obecne. W naszym przykładzie zestawie danych oznacza to wynik dla "rozłożone", dla "kropkowana" i "leopard".

Skrypt `4_trainSVM.py` ładuje uczone obrazy, szkolenie modeli SVM dla różnych wartości parametru uregulowania (slack) C i utrzymuje SVM o największej dokładności. Dokładność klasyfikacji są drukowane w konsoli i oznaczane na wykresach w aplikacji Workbench. Dla danych tekstury podana te wartości należy odpowiednio około 100% i 88%. Na koniec uczonego SVM są zapisywane do pliku *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>Krok 5: Ocena i wizualizacji
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

Dokładność klasyfikatora uczonego obrazu można zmierzyć przy użyciu skryptu `5_evaluate.py`. Wyniki skryptu, wszystkie obrazy testu za pomocą uczonego klasyfikatora SVM przypisuje każdego obrazu atrybutu z najwyższym wynikiem i porównuje przewidywane atrybuty z adnotacjami prawdziwość podstaw.

Dane wyjściowe skryptu `5_evaluate.py` znajdują się poniżej. Dokładność klasyfikacji każdego poszczególnych klas jest obliczana oraz dokładność dla zestawu pełnego testu ("dokładność ogólnej") i średnią za pośrednictwem poszczególnych dokładności ("ogólną średniej klasy dokładność"). 100% odpowiada najlepszą dokładnością i najgorszą 0%. Losowe zgadywania średnio wywołałoby średniej klasy dokładność 1 przez liczbę atrybutów: w tym przypadku dokładność ta będzie 33,33%. Te wyniki znacznie poprawić korzystając z wyższej rozdzielczości wejściowe takich jak `rf_inputResoluton = 1000`, ale kosztem wydłużenie czasu obliczeń DNN.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

Oprócz dokładności WIELOKLASOWA krzywa jest wykreślany przy użyciu odpowiednich obszaru w krzywej (po lewej); a macierz pomyłek (po prawej):

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Na koniec notesu `showResults.py` jest udostępniane na przewijanie obrazów testowych i Wizualizuj wyniki odpowiedniej klasyfikacji. Jak wyjaśniono w step1, co Notes, w tym przykładzie musi używane jądro lokalnego projektu o nazwie "PROJECTNAME lokalnego":
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>Krok 6: wdrożenia
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

Uczony system teraz mogą być publikowane jako interfejs API REST. Wdrożenie zostało wyjaśnione w notesie `deploy.ipynb`i na podstawie funkcjonalności w aplikacji Azure Machine Learning Workbench (należy pamiętać, by ustawić je jako jądra jądra lokalnego projektu o nazwie "PROJECTNAME lokalnego"). W sekcji również doskonałe wdrożenie [samouczek IRIS](tutorial-classifying-iris-part-3.md) więcej wdrożenia powiązane informacje.

Po wdrożeniu usługi sieci web może być wywoływany przy użyciu skryptu `6_callWebservice.py`. Należy pamiętać, że adres IP (lokalnym lub w chmurze) usługę sieci web musi najpierw należy ustawić w skrypcie. Notes `deploy.ipynb` wyjaśnia, jak znaleźć ten adres IP.








## <a name="part-2---accuracy-improvements"></a>Część 2 - poprawy dokładności

W części 1 pokazaliśmy klasyfikowania obrazów przez nauczenie liniowej maszyny wektor pomocy technicznej dla danych wyjściowych wartości zmiennoprzecinkowe 512 głębokie sieci neuronowe. To DNN został wstępnie przeszkolonych milionów obrazów i warstwy przedostatni zwrócony jako wektor funkcji. To podejście jest szybkie ponieważ DNN jest używany jako — jest jednak mimo to często zapewnia dobre wyniki.

Firma Microsoft teraz istnieje kilka sposobów, aby poprawić dokładność model, część 1. Głównie możemy udoskonalić DNN niż utrzymywanie rozwiązać ten problem.

### <a name="dnn-refinement"></a>DNN refinement

Zamiast SVM jeden czy klasyfikacji bezpośrednio w sieci neuronowych. Jest to osiągane przez dodanie nowej warstwy ostatniego do wstępnie przeszkolonych DNN, która przyjmuje wartości zmiennoprzecinkowe 512 z przedostatni warstwy jako dane wejściowe. Zaletą sposób klasyfikacji w DNN jest teraz w całej sieci może być retrained przy użyciu różnicowaniem. Takie podejście, często prowadzi do znacznie lepiej dokładności klasyfikacji w porównaniu do przy użyciu wstępnie przeszkolonych DNN jako — jest jednak kosztem znacznie dłuższy czas szkolenia (nawet w przypadku procesora GPU).

Uczenie sieci Neuronowej zamiast SVM odbywa się przez zmianę zmiennej `classifier` w `PARAMETERS.py` z `svm` do `dnn`. Następnie zgodnie z opisem w części 1, wszystkie skrypty, z wyjątkiem przygotowywania danych (krok 1) i szkolenia SVM (krok 4) należy wykonać ponownie. DNN refinement wymaga procesora GPU. następnie skrypt czy GPU nie został znaleziony, czy procesor GPU jest zablokowane (na przykład przez poprzedniego uruchomienia CNTK) `2_refineDNN.py` zgłasza błąd. Szkolenia DNN może zgłosić błąd braku pamięci w niektórych procesorach GPU, których należy unikać zmniejszając rozmiar minibatch (zmienna `cntk_mb_size` w `PARAMETERS.py`).

Po zakończeniu szkolenia dostosowany model jest zapisywany do *DATA_DIR/proc/fashionTexture/cntk_refined.model*, oraz wykres pokazuje, jak zmienić błędy klasyfikacji szkolenia i testowania podczas szkolenia. Należy pamiętać, że powierzchni, że błąd na zestaw szkoleniowy jest znacznie mniejszy niż w zestawie testów. To tak zwane zachowanie nadmiernie dopasowane można zmniejszyć, na przykład za pomocą wyższa wartość współczynnika przerwany `rf_dropoutRate`.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Jak widać na poniższej powierzchni, dokładności, przy użyciu refinement DNN podany zestaw danych jest 92.35% w porównaniu ze % 88.92 przed (część 1). W szczególności obrazów "kropkowana" poprawić znacznie, przy użyciu obszaru — w obszarze krzywej ROC z 0,98 za pomocą programu vs uściślenia. 0,94 przed. Użyjemy małego zestawu danych, a więc rzeczywista dokładności, uruchamiając kod są różne. Tę rozbieżność wynika z stochastycznego efekty, np. losowe podziału obrazów do szkolenia i testowania zestawów.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Uruchom śledzenie historii

Magazynów Azure Machine Learning Workbench, historię każdego działania na platformie Azure, aby zezwolić na porównania dwóch lub więcej przebiegów, które są nawet wtedy, rozdzielić tygodni. To jest omówiona szczegółowo w [samouczek Iris](tutorial-classifying-iris-part-2.md). Również jest zilustrowany na następujących zrzutach ekranu, którym możemy porównać dwa uruchomienia skryptu `5_evaluate.py`, za pomocą obu refinement DNN oznacza to, `classifier = "dnn"`(przebieg 148) lub SVM szkolenia, oznacza to, `classifier = "svm"` (przebieg 150).

W pierwszym zrzut ekranu ulepszanie DNN prowadzi do dokładności lepsze niż szkolenia SVM dla wszystkich klas. Drugi zrzut ekranu przedstawia wszystkich metryk, które są śledzone, tym, co było klasyfikatora. W skrypcie odbywa się to śledzenie `5_evaluate.py` przez wywołanie metody rejestratora Azure Machine Learning Workbench. Ponadto skrypt zapisuje WIELOKLASOWA krzywa i pomyłek macierz *generuje* folderu. To *generuje* folderu jest wyjątkowy, jego zawartość również jest śledzona przez funkcję historii Workbench i dlatego pliki wyjściowe jest możliwy w dowolnym momencie, niezależnie od tego, czy zostały zastąpione kopiami lokalnymi.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>Szczegółowego dostosowywania parametrów

Podobnie jak wartość true dla większości usługi machine learning projektów, pobieranie dobre wyniki dla nowego zestawu danych wymaga, aby zachować ostrożność parametr dostosowywania, a także ocena różne decyzje. Aby ułatwić te zadania, wszystkich ważnych parametrów są określone, a podano krótkie objaśnienie, w jednym miejscu: `PARAMETERS.py` pliku.

Najbardziej obiecujących naruszania ulepszeń należą:

- Jakość danych: Upewnij się, zestawy szkoleniowe i testowe wysokiej jakości. Oznacza to, że obrazy są poprawnie adnotacjami niejednoznaczne obrazy usunięte (na przykład odzieży elementów z rozkłada i kropki) i atrybuty wzajemnie się wykluczają (oznacza to, że wybrany tak, aby każdy obraz, należy dokładnie jeden atrybut).

- Jeśli obiekt zainteresowań jest niewielka w obrazie podejścia klasyfikacji obrazów są znane nie będą działać poprawnie. W takich przypadkach należy wziąć pod uwagę przy użyciu metody wykrywania obiektów, zgodnie z opisem w tym [samouczek](https://github.com/Azure/ObjectDetectionUsingCntk).
- DNN refinement: prawdopodobnie najważniejszych parametr, aby uzyskać odpowiednie jest tempo uczenia `rf_lrPerMb`. Jeśli dokładności na szkolenie (pierwszy rysunek w części 2) nie jest bliskie 0 – 5%, najprawdopodobniej jest to spowodowane błędną tempo uczenia. Inne parametry, począwszy od `rf_` mniej ważne. Zazwyczaj błąd szkolenie powinno dekrementacja wykładniczo, a być 0% po szkolenia.

- Wprowadź rozdzielczość: domyślna rozdzielczość obrazu to 224 x 224 pikseli. Za pomocą wyższa rozdzielczość obrazu (parametr: `rf_inputResoluton`), na przykład 448 x 448 lub 896 x 896 pikseli często znaczące zwiększa dokładność, ale spowalnia DNN uściślenia. **Za pomocą wyższa rozdzielczość obrazu jest niemal darmo i w prawie zawsze zwiększa dokładność**.

- Dopasowywania nadmiernego DNN: Unikaj duży odstęp między szkolenia i dokładność testów podczas DNN refinement (pierwszy rysunek w części 2). Można zmniejszyć tę lukę przy użyciu stawek przerwany `rf_dropoutRate` najmniej 0,5 i zwiększając wagi regularizer `rf_l2RegWeight`. Może być szczególnie przydatne w przypadku wysokiej rozdzielczości wejściowe obrazu DNN stawka wysokiej przerwany.

- Spróbuj użyć bardziej sieci, zmieniając `rf_pretrainedModelFilename` z `ResNet_18.model` do jednej `ResNet_34.model` lub `ResNet_50.model`. Model siecią Resnet-50 nie jest tylko bardziej, ale jego dane wyjściowe przedostatni warstwy jest rozmiar 2048 wartości zmiennoprzecinkowe (vs. 512 wartości zmiennoprzecinkowe modeli siecią ResNet-18 i siecią ResNet-34). Ten wymiar zwiększone może być szczególnie korzystne, gdy szkolenia SVM klasyfikatora.

## <a name="part-3---custom-dataset"></a>Część 3 — niestandardowy zestaw danych

W części 1 i 2 Firma Microsoft przeszkolonych i obliczane model klasyfikacji obrazów przy użyciu obrazów tekstury odzieży podana treść górnej. Teraz pokazujemy, jak użyć niestandardowego zestawu danych wprowadzonych przez użytkownika. Lub, jeśli nie jest dostępny, w jaki sposób wygenerować i dodawać adnotacje do takiego zestawu danych za pomocą usługi Bing wyszukiwania obrazów.

### <a name="using-a-custom-dataset"></a>Używanie niestandardowego zestawu danych

Najpierw Przyjrzyjmy się jej przyjrzeć strukturę folderów dla odzieży danych tekstury. Uwaga jak wszystkie obrazy dla różnych atrybutów znajdują się w odpowiednich podfoldery *kropkowana*, * leopard, i *rozłożone* na *DATA_DIR/obrazy/fashionTexture/*. Pamiętaj również, jak nazwa folderu obrazu występuje także w `PARAMETERS.py` pliku:
```python
datasetName = "fashionTexture"
```

Za pomocą niestandardowego zestawu danych jest tak proste, jak odtwarzanie tej struktury folderów których wszystkie obrazy w podfolderach, zgodnie z ich atrybutów oraz kopiowania tych podfolderów do nowego katalogu określonego przez użytkownika *DATA_DIR/obrazy/newDataSetName/*. Jedyna wymagana zmiana kodu jest ustalenie `datasetName` zmienną *newDataSetName*. Skrypty 1-5, następnie mogą być wykonywane w kolejności, a wszystkie pośrednie pliki są zapisywane w *DATA_DIR/proc/newDataSetName/*. Żadne inne zmiany kodu są wymagane.

Należy pamiętać, że dokładnie jeden atrybut można przypisać każdego obrazu. Na przykład byłoby niewłaściwego mieć atrybuty "zwierzę" i "leopard", ponieważ obraz "leopard" czy też należą do "zwierzę". Ponadto jest najlepszym rozwiązaniem jest niejednoznaczny i dlatego trudne, dodawać adnotacje do usunięcia.



### <a name="image-scraping-and-annotation"></a>Obraz skrobanie i adnotacji

Zbieranie wystarczająco dużą liczbę adnotacjami obrazów do szkolenia i testowania może być trudne. Jednym ze sposobów, aby rozwiązać ten problem jest scrape obrazów z Internetu. Na przykład poniżej przedstawiono wyniki wyszukiwania obrazów Bing dla zapytania *rozkładane koszulki*. Zgodnie z oczekiwaniami, większość obrazów w rzeczywistości są rozkładane koszulki. Kilka obrazów niepoprawne lub kolumna niejednoznaczna (na przykład kolumna 1, 1; wiersza lub kolumny 3, wiersz 2) można zidentyfikowane i łatwo usunąć:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

Aby wygenerować duże i zróżnicowany zestaw danych, należy używać wielu zapytań. Na przykład 7\*3 = 21 zapytania może zostać przekształcony automatycznie przy użyciu wszystkich kombinacjach elementów odzieży {bluzka hoodie, pulower, Sweter, koszulka, koszulki, kamizelka} i atrybutów {leopard rozłożone, kropkami,}. Pobieranie obrazów 50 pierwszych na zapytanie może doprowadzić do maksymalnie 21 * 50 = 1050 obrazów.

Zamiast ręcznego pobierania obrazów z wyszukiwania obrazów Bing, a dużo łatwiej jest użyć zamiast tego [API wyszukiwania obrazów Bing Cognitive Services](https://www.microsoft.com/cognitive-services/bing-image-search-api) zwraca zestaw adresów URL obrazów danego ciągu zapytania.

Niektóre z pobranego obrazy są dokładne lub w pobliżu duplikaty (na przykład różnią się tylko artefakty rozwiązania lub jpg). Te duplikaty powinny zostać usunięte, tak aby podziału szkoleniowe i testowe nie zawierają tych samych obrazów. Usuwanie zduplikowanych obrazów można osiągnąć, stosując podejście oparte na wyznaczania wartości skrótu, co sprawdza się w dwóch krokach: (i) najpierw ciągu skrótu jest obliczana dla wszystkich obrazów; (ii) w drugim przebiegu za pośrednictwem obrazów tylko te obrazy są przechowywane w usłudze ciąg wyznaczania wartości skrótu, który nie jest jeszcze widoczny. Inne obrazy są odrzucane. Znaleźliśmy `dhash` podejście w ramach biblioteki Python `imagehash` i opisane w tym [blog](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html) do wykonania, za pomocą parametru `hash_size` równa 16. Jest OK niepoprawnie usunąć niektóre obrazy niepowieloną tak długo, jak większości rzeczywistych duplikaty do usunięcia.





## <a name="conclusion"></a>Podsumowanie

Oto niektóre kluczowe zmiany w tym przykładzie są następujące:
- Kod uczenie, ocenę i wdrażanie modeli klasyfikacji obrazów.
- Pokaz obrazów, dostarczone, ale łatwe potężnej (jeden wiersz zmiana) można używać własnego obrazu zestawu danych.
- Funkcje ekspertów z najnowocześniejszych zaimplementowana do uczenia modeli wysokiej dokładności, w oparciu o uczenie transferu.
- Interaktywny model programowania za pomocą usługi Azure Machine Learning Workbench i notesu programu Jupyter.


## <a name="references"></a>Dokumentacja

[1] Alex Krizhevsky Ilyę Sutskevera i Geoffrey E. Hinton, [ _Klasyfikacja sieci ImageNet przy użyciu szczegółowego Splotowych sieci neuronowych_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf). NIPS 2012.  
[2] Kaiming He, Xiangyu Zhang, Shaoqing Ren i Jian Sun, [ _głębokiego uczenia się dla rozpoznawania obrazów resztkowe_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf). CVPR 2016.
