---
title: Obraz klasyfikacji CNTK wewnątrz Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: Szkolenie, oceny i wdrażania przy użyciu usługi Azure ML Workbench model klasyfikacji niestandardowego obrazu.
services: machine-learning
documentationcenter: ''
author: PatrickBue
ms.author: pabuehle
manager: mwinkle
ms.reviewer: marhamil, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 5ff6502b0ed023f6fe8a9475a0e81991a9918cc5
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850175"
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Za pomocą usługi Azure Machine Learning Workbench klasyfikacji obrazu

Podejścia klasyfikacji obrazu może służyć do rozwiązuje wiele problemów wizji komputera.
Należą do budowania modeli, które odpowiedzi na pytania takie jak: *jest OBIEKTEM obecnych w obrazie?* której można na przykład obiekt *dog*, *samochodu*, lub  *Wyślij*. Lub pytania bardziej złożonych, takich jak: *klasę ważności choroby oczu jest evinced przez ten pacjenta skanowania retinal?*.

Ten samouczek adresy rozwiązywania takich problemów. Zostanie przedstawiony sposób uczenia, oceny i wdrażania własnych obrazów klasyfikacji model przy użyciu [kognitywnych zestawu narzędzi firmy Microsoft (CNTK) ](https://docs.microsoft.com/cognitive-toolkit/) szkoleniowe bezpośrednich.
Przykład obrazy są dostarczane, ale czytnik można również przełączyć własny zestaw danych i ich własnych niestandardowych modeli uczenia.

Wizja komputera rozwiązań tradycyjnie wymagane specjalistyczną wiedzę ręcznie zidentyfikować i wdrażanie tak zwane *funkcji*, która zaznacz odpowiednie informacje w obrazach.
Ta metoda ręczna została zmieniona w 2012 z Słynne [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) uczenia papier, a obecnie bezpośrednich [1], głębokie sieci neuronowe (DNN) są używane automatycznie znaleźć te funkcje.
DNNs doprowadziło do ogromnych poprawy w polu nie tylko dla klasyfikacji obrazu, ale również dla innych problemów wizji komputera, takich jak obiekt wykrywania i podobieństwa obrazu.


## <a name="link-to-the-gallery-github-repository"></a>Połącz z repozytorium GitHub galerii
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Przegląd

W tym samouczku jest podzielony na trzy części:

- Część 1 przedstawiono sposób uczenia, oceny i wdrażania obrazu systemu klasyfikacji za pomocą wstępnie przeszkolone DNN jako featurizer i szkolenia SVM na jego dane wyjściowe.
- Część 2 następnie pokazano, jak poprawić dokładność, na przykład uściślenie DNN zamiast używać go jako featurizer stałym.
- Część 3 uwzględniono również sposób użyć zamiast obrazów podanym przykładzie własny zestaw danych, a w razie potrzeby, jak utworzyć własny zestaw danych przez oskrobaniu obrazy z sieci.

Gdy doświadczenia z uczenia maszynowego i CNTK nie jest wymagana, jest przydatne dla zrozumienia podstawowych zasad. Numery dokładność, szkolenia czasu itp., zgłoszone w samouczku są tylko dla odwołania, a rzeczywistymi wartościami podczas uruchamiania kodu prawie na pewno różnią się.


## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące uruchamiania w tym przykładzie są następujące:

1. [Konta Azure](https://azure.microsoft.com/free/) (bezpłatnych wersji próbnych są dostępne).
2. [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) następujące [Przewodnik Szybki start instalacji](../service/quickstart-installation.md) Aby zainstalować ten program i utworzyć obszaru roboczego.  
3. Komputer z systemem Windows. System operacyjny Windows jest niezbędne, ponieważ Workbench obsługuje tylko systemu Windows i MacOS podczas kognitywnych zestawu narzędzi firmy Microsoft (który używamy jako biblioteki learning głębokie) obsługuje tylko systemu Windows i Linux.
4. Dedykowanego procesora GPU nie jest wymagana do wykonania w część 1, szkolenia SVM, jednak wymagana jest uściślenie z DNN opisanych w części 2. Jeśli brakuje silne procesora GPU, aby uczenia na wiele procesorów graficznych lub nie masz komputera z systemem Windows należy rozważyć przy użyciu platformy Azure głębokie Learning maszyny wirtualnej z systemem operacyjnym Windows. Zobacz [tutaj](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) Przewodnik wdrożenia kliknij 1. Po wdrożeniu, połączenie z maszyną Wirtualną za pomocą Podłączania pulpitu zdalnego, zainstaluj istnieje Workbench i wykonywania kodu lokalnie z maszyny Wirtualnej.
5. Różne bibliotek języka Python, takie jak OpenCV muszą być zainstalowane. Kliknij przycisk *Otwórz okno wiersza polecenia* z *pliku* menu Narzędzia Workbench i uruchom następujące polecenia, aby zainstalować te zależności:  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl` Po pobraniu OpenCV kółka z http://www.lfd.uci.edu/~gohlke/pythonlibs/ (dokładnej nazwy pliku i wersja zmienić)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`
    - `jupyter nbextension enable --py widgetsnbextension`

### <a name="troubleshooting--known-bugs"></a>Rozwiązywania problemów / znanych błędów
- Procesora GPU jest wymagany dla część 2, a w przeciwnym razie "Partii normalizacji szkolenia na Procesorze nie została jeszcze zaimplementowana", jest zgłaszany błąd podczas próby zaktualizowania DNN.
- Błędy braku pamięci podczas uczenia DNN można uniknąć przez zmniejszenie rozmiaru minibatch (zmiennej `cntk_mb_size` w `PARAMETERS.py`).
- Kod został przetestowany przy użyciu CNTK 2.2 i powinny również wykonywania na starszą (maksymalnie v2.0) i nowszych wersji bez żadnej lub tylko drobne zmiany.
- W momencie pisania Azure Machine Learning Workbench wystąpiły problemy z wyświetlanie notesów większych niż 5 MB. Notesów to duży rozmiar może nastąpić, jeśli notesu zostanie zapisany pod wszystkie komórki wyświetlany w danych wyjściowych. Jeśli ten błąd wystąpi, należy otworzyć wiersz polecenia z menu Plik wewnątrz Workbench wykonania `jupyter notebook`Otwórz notesu, wyczyść wszystkie dane wyjściowe i Zapisz notesu. Po wykonaniu tych kroków, notesu zostanie prawidłowo wewnątrz Azure Machine Learning Workbench ponownie otworzyć.
- Wszystkie skrypty znajdujące się w tym przykładzie muszą być wykonywane lokalnie, a nie na przykład środowiska zdalnego docker. Wszystkie notesy muszą zostać wykonane z jądra ustawioną jądra lokalnego projektu o nazwie "NAZWAPROJEKTU lokalne" (np. "myImgClassUsingCNTK lokalnego").

    
## <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt workbench

Aby utworzyć nowy projekt za pomocą tego przykładu jako szablon:
1.  Otwórz program Azure Machine Learning Workbench.
2.  Na **projekty** kliknij przycisk **+** podpisywania i wybierz **nowy projekt**.
3.  W **Utwórz nowy projekt** okienka, wypełnij informacje dla nowego projektu.
4.  W **szablony projektów wyszukiwania** pole wyszukiwania, wpisz "Obrazu klasyfikacji" i wybierz szablon.
5.  Kliknij przycisk **Utwórz**.

Wykonanie tych czynności tworzy struktury projektu, pokazano poniżej. Katalog projektu jest ograniczone za mniej niż 25 (w MB), ponieważ Azure Machine Learning Workbench tworzy kopię tego folderu po każdym uruchomieniu (Aby włączyć historię uruchamiania). W związku z tym wszystkich obrazów i plików tymczasowych są zapisywane do i z katalogu *~/Desktop/imgClassificationUsingCntk_data* (nazywane *DATA_DIR* w tym dokumencie).

  Folder| Opis
  ---|---
  aml_config/|                           Katalog zawierający pliki konfiguracji usługi Azure Machine Learning Workbench
  bibliotek /|                              Katalog zawierający wszystkie funkcje pomocy Python i Jupyter
  notesów /|                              Katalog zawierający wszystkie notesy
  zasoby /|                              Katalog zawierający wszystkie zasoby (na przykład adres url sposób obrazy)
  skrypty /|                              Katalog zawierający wszystkie skrypty
  PARAMETERS.py|                       Skrypt w języku Python określenia wszystkich parametrów
  Readme.MD|                           Ten dokument readme


## <a name="data-description"></a>Opis elementu danych

W tym samouczku używana jako uruchamianie przykładu górny treści zestawu danych tekstury odzieży składające się z maksymalnie 428 obrazów. Każdego obrazu ma adnotacje w postaci jednego z trzech różnych tekstury (leopard kropkami, rozłożone,). Firma Microsoft przechowywane liczbę obrazów mała, aby szybko mogą być wykonywane w tym samouczku. Jednak kod jest dobrze przetestowane i działa z dziesiątkami tysięcy obrazów lub więcej. Wszystkie obrazy zostały oskrobane za pomocą wyszukiwania usługi Bing obrazu i ręcznie adnotacji opisane w [część 3](#using-a-custom-dataset). Obraz adresy URL z ich odpowiednich atrybutów są wymienione w */resources/fashionTextureUrls.tsv* pliku.

Skrypt `0_downloadData.py` pobierze wszystkie obrazy do *DATA_DIR/obrazów/fashionTexture/* katalogu. Niektóre 428 adresy URL są prawdopodobnie uszkodzone. Nie stanowi to problemu, a tylko oznacza, że mamy nieco mniejsze obrazy do celów szkoleniowych i testów. Wszystkie skrypty znajdujące się w tym przykładzie muszą być wykonywane lokalnie, a nie na przykład środowiska zdalnego docker.

Na poniższej ilustracji przedstawiono przykłady dla atrybutów z kropkami (po lewej), rozłożone (na środku) i leopard (po prawej). Adnotacje zostały wykonane zgodnie z elementu odzieży górny treści.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>Część 1 - Model uczenie i Ewaluacja

W pierwszej części tego samouczka możemy są szkolenie systemu używającego, ale nie modyfikuje wstępnie przeszkolone sieci neuronowej głębokość. To wstępnie przeszkolone DNN jest używany jako featurizer i SVM liniowego jest uczony do prognozowania atrybut (kropkami, rozłożony lub leopard) danego obrazu.

Firma Microsoft teraz opisem tej metody w szczegółów krok po kroku i pokazać, które skrypty wymagane do wykonania. Aby sprawdzić, które pliki są zapisywane i gdzie są one zapisywane zaleca się po każdym kroku.

Wszystkie parametry ważne są określone, a podana krótkie objaśnienie, w jednym miejscu: `PARAMETERS.py` pliku.




### <a name="step-1-data-preparation"></a>Krok 1: Przygotowanie danych
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

Notesu `showImages.ipynb` może służyć do wizualizacji obrazów i popraw ich adnotacji, zgodnie z potrzebami. Aby uruchomić notesu, otwórz go w Workbench Learning Azure maszyny w "Start notesu Server", jeśli ta opcja jest wyświetlana, zmień jądra lokalnego projektu o nazwie "NAZWAPROJEKTU hodowla w lokalnym" (np. "myImgClassUsingCNTK lokalny"), kliknij przycisk, a następnie wykonaj wszystkie komórki w notesu. Zobacz sekcję dotyczącą rozwiązywania problemów w tym dokumencie, jeśli wystąpi błąd, strona skarżąca czy notesu jest zbyt duży do wyświetlenia.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Teraz uruchom skrypt o nazwie `1_prepareData.py`, który przypisuje wszystkie obrazy szkolenia albo ustaw lub ustaw testu. To przypisanie wykluczają się wzajemnie — Brak obrazu szkolenia służy również do testowania lub na odwrót. Domyślnie losowe 75% obrazów z każdej klasy atrybutu są przypisane do szkolenia, a pozostałe 25% są przypisane do testowania. Wszystkie dane generowane przez skrypt są zapisywane w *DATA_DIR/proc/fashionTexture/* folderu.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>Krok 2: Uściślenie sieci Neuronowej głębokość
`Script: 2_refineDNN.py`

Jak możemy wyjaśniono w część 1 z tego samouczka, wstępnie przeszkolone DNN jest przechowywana w stałych (to znaczy go nie jest precyzyjnych). Jednak skrypt o nazwie `2_refineDNN.py` jest nadal wykonywane w część 1, ponieważ ładuje wstępnie przeszkolone [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) modelu [2] i modyfikuje je, na przykład, aby umożliwić wyższej rozdzielczości obrazu wejściowego. Ten krok jest szybkie (w sekundach) i nie wymaga procesora GPU.

W części 2 samouczka modyfikację PARAMETERS.py pliku przyczyny `2_refineDNN.py` skryptu można również dostosować wstępnie przeszkolone DNN. Domyślnie przeprowadzana 45 epok szkoleń podczas uściślenia.

W obu przypadkach ostateczne modelu następnie są zapisywane do pliku *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>Krok 3: Ocena DNN dla wszystkich obrazów
`Script: 3_runDNN.py`

Firma Microsoft może teraz użyć (prawdopodobnie precyzyjnych) DNN w ostatnim kroku do featurize naszych obrazów. Jako dane wejściowe DNN, biorąc pod uwagę obrazu, dane wyjściowe są wektor elementów przestawnych 512 przedostatni warstwy modelu. Wektor ten jest wymiarów dużo mniejsze niż samego obrazu. Niemniej jednak powinna zawierać (i nawet Wyróżnij) wszystkie informacje w odpowiednich rozpoznawanie atrybut obrazu, jeśli element odzieży ma kropkami, rozłożony obrazu lub leopard tekstury.

Wszystkie oświadczenia obrazu DNN są zapisywane do pliku *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>Krok 4: Obsługa wektor maszyny szkolenia
`Script: 4_trainSVM.py`

Elementów przestawnych 512 reprezentacje obliczona w ostatnim kroku teraz są używane w celu przeszkolenia klasyfikatora SVM: podana jako dane wejściowe obrazu, SVM generuje wynik dla każdego atrybutu obecności. W naszym przykładzie zestawu danych oznacza, że wynik dla "rozłożone", dla "kropkowanej" i "leopard".

Skrypt `4_trainSVM.py` ładuje obrazy szkolenia, przygotowuje SVM dla innej wartości parametru uregulowania (zapas czasu) C i przechowuje SVM o najwyższym dokładności. Dokładność klasyfikacji drukowany w konsoli i Workbench na wykresie. Dla danych podany tekstury te wartości powinny być około 100% i 88% odpowiednio. Na koniec przeszkolone SVM są zapisywane do pliku *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>Krok 5: Ocena i wizualizacji
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

Dokładność klasyfikatora przeszkolone obrazu można mierzone przy użyciu skryptu `5_evaluate.py`. Wyniki skryptu, wszystkie obrazy testu przy użyciu klasyfikatora przeszkolone SVM przypisuje każdego obrazu atrybut o najwyższym wynik i porównuje atrybuty przewidywane z adnotacjami prawdy podstaw.

Dane wyjściowe skryptu `5_evaluate.py` są wyświetlane poniżej. Dokładność klasyfikacji każdej z klas pojedynczych jest obliczana oraz dokładność dla zestawu testowego Pełna ("dokładność ogólnej") a średnią za pośrednictwem umożliwiające indywidualne ("średnio klasy dokładności"). 100% odpowiada najlepsze możliwe dokładność i 0% najgorszą. Losowe odgadnięcie średnio zwróci średnio klasy dokładność 1 przez liczbę atrybutów: w tym przypadku będzie to dokładność 33,33%. Wyniki te znacznie zwiększyć podczas korzystania z wyższej rozdzielczości wejściowych takich jak `rf_inputResoluton = 1000`, ale kosztem wydłużenie czasu obliczeń DNN.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

Oprócz dokładność krzywą ROC jest kreślony z odpowiedniego obszaru — w obszarze krzywej (lewych); i macierzy pomyłek jest wyświetlany (po prawej):

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Na koniec notesu `showResults.py` podano przewijanie obrazów testu i wizualizować wyniki odpowiednich klasyfikacji. Zgodnie z objaśnieniem w krok 1, co notesu w tym przykładzie musi używać jądra lokalnego projektu o nazwie "NAZWAPROJEKTU lokalnego":
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>Krok 6: wdrożenia
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

Teraz można opublikować przeszkolone systemu jako interfejs API REST. Wdrożenia opisanej w notesie `deploy.ipynb`i w oparciu o funkcje środowiska roboczego Azure Machine Learning (Pamiętaj, aby ustawić jako jądra jądra lokalnego projektu o nazwie "NAZWAPROJEKTU lokalnego"). W sekcji także doskonała wdrożenia [samouczek IRIS](tutorial-classifying-iris-part-3.md) wdrożenia więcej powiązane informacje.

Po wdrożeniu można wywołać usługę sieci web przy użyciu skryptu `6_callWebservice.py`. Należy pamiętać, że adres IP (lokalnej lub w chmurze) usługa sieci web musi najpierw należy ustawić w skrypcie. Notesu `deploy.ipynb` wyjaśniono, jak znaleźć ten adres IP.








## <a name="part-2---accuracy-improvements"></a>Część 2 - poprawy dokładności

W części 1 możemy pokazano sposób klasyfikować obrazu, szkolenia liniowej maszyny wektor pomocy technicznej na wyjściu elementów przestawnych 512 głębokie sieci neuronowe. Ten DNN została wstępnie przeszkolone na miliony obrazów i przedostatni warstwy zwracane w postaci wektorowej funkcji. Ta metoda jest szybka, ponieważ DNN jest używany jako — jest, ale mimo to często powoduje dobrych wyników.

Firma Microsoft teraz istnieje kilka sposobów poprawy dokładności model, część 1. Głównie możemy udoskonalić DNN niż on rozwiązany.

### <a name="dnn-refinement"></a>Uszczegółowienie DNN

Zamiast SVM co można zrobić klasyfikacji bezpośrednio w sieci neuronowej. Jest to osiągane przez dodanie ostatniego nową warstwę do wstępnie przeszkolone DNN, która przyjmuje 512-elementów przestawnych z warstwy przedostatni jako dane wejściowe. Zaletą tej klasyfikacji w DNN jest teraz mogą być retrained całej sieci przy użyciu backpropagation. Takie podejście, często prowadzi do znacznie lepszą dokładność klasyfikacji porównaniu z użyciem wstępnie przeszkolone DNN jako — jest jednak kosztem znacznie dłużej szkolenia (nawet w przypadku procesora GPU).

Uczenie sieci Neuronowej zamiast SVM odbywa się przez zmianę zmiennej `classifier` w `PARAMETERS.py` z `svm` do `dnn`. Następnie zgodnie z opisem w części 1, wszystkich skryptach z wyjątkiem Przygotowanie danych (krok 1) i szkolenia SVM (krok 4) trzeba ponownie uruchamiać. Uszczegółowienie DNN wymaga procesora GPU. Jeśli GPU nie został znaleziony lub zablokowaniu procesora GPU (na przykład przez poprzedniego uruchomienia CNTK) następnie skryptu `2_refineDNN.py` zgłasza błąd. Szkolenia DNN może zgłosić błąd braku pamięci na niektórych procesorach GPU, które można uniknąć przez zmniejszenie rozmiaru minibatch (zmiennej `cntk_mb_size` w `PARAMETERS.py`).

Po zakończeniu szkolenia precyzyjnych modelu są zapisywane *DATA_DIR/proc/fashionTexture/cntk_refined.model*, oraz które pokazuje, jak zmienić błędy klasyfikacji szkoleniowych i testów podczas uczenia wykresu. Należy pamiętać, że powierzchni, że błąd na zestaw szkoleniowy jest znacznie mniejszy niż w zestawie testów. To zachowanie nadmiernie dopasowane tak zwane można zmniejszyć, na przykład za pomocą wyższa wartość dla szybkości przerwany `rf_dropoutRate`.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Jak widać na powierzchni poniżej, dokładność, przy użyciu uściślenia DNN dla udostępnionego zestawu danych jest 92.35% i % 88.92 przed (część 1). W szczególności "kropkowanej" obrazy znacznie, zwiększenia z obszaru — w obszarze krzywą ROC z 0,98 z uściślenia vs. 0,94 przed. Używamy małym zestawie danych, i dlatego różnią się rzeczywista dokładność, wykonywanie kodu. Jest to spowodowane stochastycznego skutki takich jak losowe podziału obrazy do celów szkoleniowych i testów zestawów.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Uruchom śledzenie historii

W magazynie Azure Machine Learning Workbench historii każdego uruchamiania na platformie Azure umożliwia porównanie co najmniej dwa przebiegi, które są nawet tygodnie od siebie. To jest omówiona szczegółowo w artykule [samouczek Iris](tutorial-classifying-iris-part-2.md). Również przedstawiono poniższe zrzuty ekranu, którym możemy porównać dwa przebiegi skryptu `5_evaluate.py`, przy użyciu albo uściślenia DNN oznacza to, `classifier = "dnn"`(liczba wykonywania 148) lub SVM szkolenia, oznacza to, `classifier = "svm"` (liczba wykonywania 150).

W pierwszym zrzut ekranu Uszczegółowienie DNN prowadzi do umożliwiające lepsze niż szkolenia SVM dla wszystkich klas. Drugi zrzut ekranu przedstawia wszystkie metryki, które są śledzone, łącznie z klasyfikatora został. To śledzenie odbywa się w skrypcie `5_evaluate.py` przez wywołanie metody Azure Machine Learning Workbench rejestratora. Ponadto skrypt zapisuje ROC krzywej i nieporozumień macierzy *generuje* folderu. To *generuje* folder jest specjalne, w tym jego zawartości jest również śledzona przez funkcję historii Workbench, a więc pliki wyjściowe są dostępne w dowolnym momencie, niezależnie od tego, czy zostały zastąpione kopiami lokalnymi.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/> </p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>Dostrajanie parametrów

Jak ma wartość true dla większości machine learning projektów, uzyskiwania dobrych wyników o nowy zestaw danych wymaga parametru dokładne dostrajania, a także decyzje dotyczące projektu innej obliczania. Ułatwiające te zadania zostały podane wszystkie parametry ważne i krótki opis podany w jednym miejscu: `PARAMETERS.py` pliku.

Najbardziej obietnicy drogi ulepszeń, należą:

- Jakość danych: Upewnij się, zestawy szkoleniowe i testu mają wysokiej jakości. Oznacza to, że obrazy są poprawnie adnotacjami, niejednoznaczne obrazów usunięte (na przykład odzieży elementów z paski i kropki) i wykluczają się wzajemnie atrybuty (to znaczy wybierany tak, aby każdego obrazu należy dokładnie jeden atrybut).

- Jeśli obiekt odsetek jest mały obraz nie będą działać poprawnie są określane podejścia klasyfikacji obrazu. W takim przypadku należy rozważyć użycie metody wykrywania obiektu zgodnie z opisem w tym [samouczek](https://github.com/Azure/ObjectDetectionUsingCntk).
- Uszczegółowienie DNN: parametr raczej najważniejszych uzyskać prawo jest tempo uczenia `rf_lrPerMb`. Jeśli dokładności szkolenia (pierwszy rysunek w część 2) nie jest bliski 0 – 5%, najprawdopodobniej jest to spowodowane błędną tempo uczenia. Parametry, począwszy od `rf_` mniej ważne. Zazwyczaj błąd szkolenia należy zmniejszyć wykładniczo i być 0% po szkolenia.

- Dane wejściowe rozpoznawania: domyślna rozdzielczość obrazu to 224 x 224 pikseli. Przy użyciu nowszej rozdzielczość obrazu (parametr: `rf_inputResoluton`), na przykład 448 x 448 lub 896 x 896 pikseli często znaczących zwiększa dokładność, ale spowalnia uściślenia DNN. **Przy użyciu wyższej rozdzielczości obrazu jest niemal wolne obiad i prawie zawsze zwiększa dokładność**.

- Nadmierne dopasowywania DNN: Unikaj duży odstęp między szkoleniowe i dokładność testu podczas uściślenia DNN (pierwszy rysunek w część 2). Ta przerwa można zmniejszyć przy użyciu stawki przerwany `rf_dropoutRate` 0,5 lub więcej oraz przez odpowiednie zwiększenie wagi regularizer `rf_l2RegWeight`. Przy użyciu szybkość wysokiej przerwany może być szczególnie przydatne, jeśli rozdzielczość obrazu wejściowego DNN jest duża.

- Spróbuj użyć DNNs głębiej, zmieniając `rf_pretrainedModelFilename` z `ResNet_18.model` albo `ResNet_34.model` lub `ResNet_50.model`. Model Resnet 50 nie jest tylko głębiej, ale jego dane wyjściowe przedostatni warstwy jest rozmiar 2048 elementów przestawnych (vs. 512 elementów przestawnych modeli ResNet 18 i ResNet 34). Ta zwiększona wymiar może być szczególnie przydatne podczas uczenia SVM klasyfikatora.

## <a name="part-3---custom-dataset"></a>Część 3 - niestandardowy zestaw danych

W części 1 i 2 Firma Microsoft uczenia i obliczone model klasyfikacji obrazu przy użyciu obrazów podana treść górny odzieży tekstury. Mamy teraz pokazują, jak użyć niestandardowego zestawu danych dostarczane przez użytkownika. Lub, jeśli nie jest dostępny, jak do generowania i Adnotuj takie zestawu danych za pomocą usługi Bing wyszukiwania obrazu.

### <a name="using-a-custom-dataset"></a>Używanie niestandardowego zestawu danych

Po pierwsze umożliwia ma przyjrzeć się struktura folderów dla danych tekstury odzieży. Należy pamiętać, jak wszystkie obrazy dla różnych atrybutów znajdują się w odpowiednich podfoldery *kropkowanej*, * leopard, i *rozłożone* w *DATA_DIR/obrazów/fashionTexture/*. Należy też zauważyć, jak nazwa folderu obrazu występuje również w `PARAMETERS.py` pliku:
```python
datasetName = "fashionTexture"
```

Używanie niestandardowego zestawu danych jest tak proste, jak odtwarzanie tej struktury folderów skutkującej wszystkie obrazy w podfolderach, zgodnie z ich atrybutów oraz kopiowania tych folderów do nowego katalogu określonego przez użytkownika *DATA_DIR/obrazów/newDataSetName/*. Tylko zmiany kodu, wymagane jest skonfigurowanie `datasetName` zmienną *newDataSetName*. Skrypty 1-5, następnie mogą być wykonywane w kolejności i wszystkich plików pośrednich są zapisywane w *DATA_DIR/proc/newDataSetName/*. Inne zmiany w kodzie nie są wymagane.

Należy pamiętać, że dokładnie jeden atrybut można przypisać każdego obrazu. Na przykład może być problem mieć atrybuty "zwierzę" i "leopard", ponieważ obraz "leopard" czy też należą do "zwierzę". Ponadto jest najlepszym rozwiązaniem jest niejednoznaczny i dlatego trudne dodawać adnotacje do usunięcia.



### <a name="image-scraping-and-annotation"></a>Obraz skrobanie i adnotacji

Zbieranie wystarczająco dużą liczbę obrazów adnotacjami uczenie i testowanie, które może być trudne. Jednym ze sposobów rozwiązania tego problemu jest scrape obrazów z Internetu. Na przykład poniżej wyników wyszukiwania usługi Bing obrazu dla zapytania *obrazów na koszulki rozkładane*. Zgodnie z oczekiwaniami, większość obrazów w rzeczywistości są rozkładane koszulki. Kilka obrazów nieprawidłowe lub niejednoznaczne (takich jak kolumna 1, wiersz 1; lub 3 kolumny, wiersz 2), można zidentyfikować i łatwo usunąć:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

Aby wygenerować dużą i różnych zestawu danych, można użyć wielu zapytań. Na przykład 7\*3 = 21 zapytania mogą być syntezatora automatycznie przy użyciu kombinacji wszystkich elementów odzieży {bluzka, hoodie, pulower, Sweter, shirt, obrazów na koszulki, kamizelka} i {leopard rozłożone, zapisie kropkowo-,} atrybutów. Pobieranie obrazów 50 pierwszych na zapytanie może doprowadzić do maksymalnie 21 * 50 = 1050 obrazów.

Zamiast ręcznego pobierania obrazów z wyszukiwania usługi Bing obrazu, jest znacznie ułatwia zamiast tego użyć [kognitywnych API wyszukiwania usługi Bing obrazu](https://www.microsoft.com/cognitive-services/bing-image-search-api) zwraca zestaw adresów URL obrazu podany ciąg zapytania.

Niektóre obrazy pobrane są dokładne lub w jego pobliżu duplikaty (na przykład różnią się tylko artefakty rozwiązania lub jpg). Te duplikaty powinna zostać usunięta, dzięki czemu podział szkoleniowych i testów nie zawierają tej samej obrazów. Usuwanie obrazów zduplikowane można osiągnąć za pomocą podejście oparte na wyznaczania wartości skrótu, który działa w dwóch krokach: (i) najpierw ciągu skrótu jest obliczana dla obrazów wszystkich; (ii) w drugim przebiegu za pośrednictwem obrazów tylko te obrazy są przechowywane z ciągiem wyznaczania wartości skrótu, który nie ma jeszcze widoczne. Inne obrazy są odrzucane. Znaleziono `dhash` podejście w bibliotece języka Python `imagehash` i opisanych w tym [blog](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html) można również wykonać za pomocą parametru `hash_size` ustawioną 16. Jest OK niepoprawnie Usuń niektóre obrazy z systemem innym niż duplikat tak długo, jak większość rzeczywistych duplikaty do usunięcia.





## <a name="conclusion"></a>Podsumowanie

Niektóre najważniejsze funkcje klucza, w tym przykładzie są:
- Kod do uczenia, oceny i wdrażania obrazów modele klasyfikacji.
- Pokaz obrazów, pod warunkiem, ale łatwo dostosowywalne (jeden wiersz zmiana) można używać własnych obrazów zestawu danych.
- Stan grafiki zaimplementowana do uczenia Wysoka dokładność modeli opartych na Transfer Learning ekspertów funkcje.
- Programowanie interakcyjne modelu za pomocą usługi Azure Machine Learning Workbench i notesu Jupyter.


## <a name="references"></a>Dokumentacja

[1] Alexowi Krizhevsky, Ilya Sutskever i Geoffrey E. Hinton, [ _ImageNet klasyfikacji z Convolutional głębokie sieci neuronowe_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf). NIPS 2012.  
[2] Kaiming He, Xiangyu Zhang Shaoqing Ren i Jian Sun, [ _głębokość pozostałych uczenia rozpoznawania obrazu_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf). CVPR 2016.
