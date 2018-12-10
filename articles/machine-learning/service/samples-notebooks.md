---
title: Samouczki usługi Azure Machine Learning w notesach Jupyter Notebook
description: Znajdź przykładowe notesy Jupyter Notebook i skorzystaj z nich, aby poznać usługę Azure Machine Learning przy użyciu języka Python.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.openlocfilehash: 5ec010d6e0539e9ba316b48dc02110dc19e4b13e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52883949"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Korzystanie z notesów Jupyter Notebook w celu eksplorowania usługi Azure Machine Learning


Dla Twojej wygody opracowaliśmy szereg notesów Jupyter Notebook w języku Python, których możesz użyć do zapoznania się z usługą Azure Machine Learning. 

Dowiedz się, jak używać usługi z dokumentacji znajdującej się w tej witrynie, oraz jak dostosować notesy do danej sytuacji. 

## <a name="prerequisite"></a>Wymagania wstępne

Wykonaj czynności opisane w [przewodniku Szybki start dotyczącym języka Python dla usługi Azure Machine Learning ](quickstart-get-started.md) w celu utworzenia obszaru roboczego i uruchomienia usługi Azure Notebooks.

## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Wypróbuj usługę Azure Notebooks: bezpłatne notesy Jupyter Notebook w chmurze

Rozpoczęcie pracy z usługą Azure Notebooks jest bardzo proste. [Zestaw Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) jest już zainstalowany i skonfigurowany w usłudze Azure Notebooks. Instalacja i przyszłe aktualizacje są automatycznie zarządzane za pomocą usług platformy Azure.
  
+ Aby uruchomić **podstawowe notesy samouczków**:
  1. Przejdź do usługi [Azure Notebooks](https://notebooks.azure.com/).
    
  1. Znajdź folder **samouczki** w bibliotece **Wprowadzenie** utworzonej w ramach przewodnika Szybki start stanowiącego wymagania wstępne.
    
  1. Otwórz notes, który chcesz uruchomić.
    
+ Aby uruchomić **inne notesy**:

  1. [Zaimportuj przykładowe notesy](https://aka.ms/aml-clone-azure-notebooks) do usługi Azure Notebooks.

  1. Dodaj plik konfiguracji obszaru roboczego do biblioteki przy użyciu jednej z następujących metod:
     + Skopiuj plik **config.json** z biblioteki **Wprowadzenie** do nowo sklonowanej biblioteki.

     + Utwórz nowy obszar roboczy za pomocą kodu w pliku [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
    
  1. Otwórz notes, który chcesz uruchomić.     


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Używanie środowiska Data Science Virtual Machine (DSVM)

W środowisku DSVM jest już zainstalowany i skonfigurowany [zestaw Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) oraz serwer notesów. Wykonaj następujące kroki, aby uruchomić notesy.

1. [Utwórz środowisko DSVM](how-to-configure-environment.md#dsvm).

1. Sklonuj [repozytorium GitHub](https://aka.ms/aml-notebooks).

1. Dodaj plik konfiguracji obszaru roboczego do biblioteki przy użyciu jednej z następujących metod:
    * Do sklonowanego katalogu skopiuj plik **aml_config\config.json** utworzony podczas wykonywania czynności z przewodnika Szybki start stanowiącego wymagania wstępne.

    * Utwórz nowy obszar roboczy za pomocą kodu w pliku [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Uruchom serwer notesów z poziomu sklonowanego katalogu.

## <a name="use-your-own-jupyter-notebook-server"></a>Korzystanie z własnego serwera notesów Jupyter Notebook

Wykonaj te kroki, aby utworzyć lokalny serwer notesów Jupyter Notebook na komputerze.

1. Upewnij się, że wykonano czynności z przewodnika Szybki start stanowiącego wymagania wstępne obejmujące zainstalowanie zestawów Azure Machine Learning SDK.

1. Sklonuj [repozytorium GitHub](https://aka.ms/aml-notebooks).

1. Dodaj plik konfiguracji obszaru roboczego do biblioteki przy użyciu jednej z następujących metod:
    * Do sklonowanego katalogu skopiuj plik **aml_config\config.json** utworzony podczas wykonywania czynności z przewodnika Szybki start stanowiącego wymagania wstępne.
    
    * Utwórz nowy obszar roboczy za pomocą kodu w pliku [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Uruchom serwer notesów z poziomu sklonowanego katalogu.

1. Przejdź do folderu zawierającego notes.

1. Otwórz notes.

<a name="auto"></a>

## <a name="automated-ml-setup"></a>Konfiguracja zautomatyzowanego uczenia maszynowego 

**Te kroki mają zastosowanie tylko do notesów znajdujących się w folderze `automated-machine-learning`.**

Choć możesz użyć dowolnej z powyższych opcji, możesz również jednocześnie zainstalować środowisko i utworzyć obszar roboczy, korzystając z poniższych instrukcji. 

1. Zainstaluj środowisko [Mini-conda](https://conda.io/miniconda.html). Wybierz wersję 3.7 lub nowszą. Postępuj zgodnie z monitami, aby przeprowadzić instalację. 
   >[!NOTE]
   >Istniejącego środowiska Conda możesz użyć, jeśli jest ono w wersji 4.4.10 lub nowszej. Użyj polecenia `conda -V`, aby wyświetlić wersję. Wersję środowiska Conda możesz zaktualizować za pomocą polecenia `conda update conda`. Nie musisz specjalnie instalować środowiska mini-conda.

1. Pobierz przykładowe notesy z serwisu [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) jako plik zip i wyodrębnij zawartość do katalogu lokalnego. Notesy zautomatyzowanego uczenia maszynowego znajdują się w folderze `how-to-use-azureml/automated-machine-learning`.

1. Skonfiguruj nowe środowisko Conda. 
   1. Otwórz wiersz polecenia środowiska Conda na maszynie lokalnej.
   
   1. Przejdź do plików, które zostały wyodrębnione na maszynie lokalnej.
   
   1. Otwórz folder `automated-machine-learning`.
   
   1. W wierszu polecenia środowiska Conda wykonaj plik `automl_setup.cmd` w systemie Windows lub plik `.sh` dla Twojego systemu operacyjnego. Może to potrwać około 10 minut.

      Skrypt konfiguracji wykonuje następujące działania:
      + Tworzy nowe środowisko Conda
      + Instaluje wymagane pakiety
      + Konfiguruje widżet
      + Uruchamia notes Jupyter Notebook
      
      Skrypt przyjmuje nazwę środowiska Conda jako parametr opcjonalny. Domyślna nazwa środowiska Conda to `azure_automl`. Dokładna postać polecenia zależy od systemu operacyjnego. 
      
      Po zakończeniu skryptu w przeglądarce zostanie wyświetlona strona główna notesu Jupyter Notebook.

1. Przejdź do ścieżki, w której zapisano notesy. 

1. Otwórz folder automated-machine-learning, a następnie otwórz notes `configuration.ipynb`. 

1. Wykonaj komórki w notesie, aby zarejestrować dostawcę zasobów Machine Learning Services i utworzyć obszar roboczy.

Teraz możesz otwierać i uruchamiać notesy zapisane na maszynie lokalnej.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [repozytorium notesów na potrzeby usługi Azure Machine Learning w serwisie GitHub](https://aka.ms/aml-notebooks)

Skorzystaj z następujących samouczków:
+ [Szkolenie i wdrażanie modelu klasyfikacji obrazów przy użyciu zestawu danych MNIST](tutorial-train-models-with-aml.md)

+ [Prepare data and use automated machine learning to train a regression model with the NYC taxi data set (Przygotowywanie danych i używanie zautomatyzowanego uczenia maszynowego w celu szkolenia modelu regresji przy użyciu zestawu danych dotyczących taksówek w Nowym Jorku)](tutorial-data-prep.md)
