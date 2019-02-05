---
title: Przykładowe notesy Jupyter
titleSuffix: Azure Machine Learning service
description: Znajdź przykładowe notesy Jupyter Notebook i skorzystaj z nich, aby poznać usługę Azure Machine Learning przy użyciu języka Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 5ba555ad31545e1ae1aa822ec58b0bd22ef486ac
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295155"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Korzystanie z notesów Jupyter Notebook w celu eksplorowania usługi Azure Machine Learning

Dla Twojej wygody opracowaliśmy szereg notesów Jupyter Notebook w języku Python, których możesz użyć do zapoznania się z usługą Azure Machine Learning. 

Dowiedz się, jak używać usługi z dokumentacji znajdującej się w tej witrynie, oraz jak dostosować notesy do danej sytuacji. 

Aby uruchomić serwer notesów przy użyciu tych przykładowych notesów, użyj jednej z poniższych ścieżek.  Gdy serwer zostanie już uruchomiony, znajdź notesy samouczków w folderze **tutorials** lub odkryj różne funkcje w folderze **how-to-use-azureml**.


## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Wypróbuj usługę Azure Notebooks: bezpłatne notesy Jupyter Notebook w chmurze

Rozpoczęcie pracy z usługą Azure Notebooks jest bardzo proste. [Zestaw Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) został już zainstalowany i skonfigurowany w usłudze [Azure Notebooks](https://notebooks.azure.com/). Instalacja i przyszłe aktualizacje są automatycznie zarządzane za pomocą usług platformy Azure.
  
[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Używanie środowiska Data Science Virtual Machine (DSVM)

W środowisku DSVM jest już zainstalowany i skonfigurowany [zestaw Azure Machine Learning SDK dla języka Python](https://aka.ms/aml-sdk) oraz serwer notesów. 

Po [utworzeniu maszyny wirtualnej DSVM](how-to-configure-environment.md#dsvm) wykonaj następujące czynności na maszynie wirtualnej DSVM w celu uruchomienia notesów.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]


## <a name="use-your-own-jupyter-notebook-server"></a>Korzystanie z własnego serwera notesów Jupyter Notebook

Wykonaj te kroki, aby utworzyć lokalny serwer notesów Jupyter Notebook na komputerze.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

<a name="automated-ml-setup"></a>

## <a name="automated-machine-learning-setup"></a>Konfigurowanie zautomatyzowanego uczenia maszynowego 

_Te kroki dotyczą tylko notesów w folderze **how-to-use-azureml/automated-machine-learning**._

Choć możesz użyć dowolnej z powyższych opcji, możesz również jednocześnie zainstalować środowisko i utworzyć obszar roboczy, korzystając z poniższych instrukcji. 

1. Zainstaluj środowisko [Mini-conda](https://conda.io/miniconda.html). Wybierz wersję 3.7 lub nowszą. Postępuj zgodnie z monitami, aby przeprowadzić instalację. 
   >[!NOTE]
   >Istniejącego środowiska Conda możesz użyć, jeśli jest ono w wersji 4.4.10 lub nowszej. Użyj polecenia `conda -V`, aby wyświetlić wersję. Wersję środowiska Conda możesz zaktualizować za pomocą polecenia `conda update conda`. Nie musisz specjalnie instalować środowiska mini-conda.

1. Pobierz przykładowe notesy z serwisu [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) jako plik zip i wyodrębnij zawartość do katalogu lokalnego. Notesy zautomatyzowanego uczenia maszynowego znajdują się w folderze `how-to-use-azureml/automated-machine-learning`.

1. Skonfiguruj nowe środowisko Conda. 
   1. Otwórz wiersz polecenia środowiska Conda na maszynie lokalnej.
   
   1. Przejdź do plików, które zostały wyodrębnione na maszynie lokalnej.
   
   1. Otwórz folder **automated-machine-learning**.
   
   1. W wierszu polecenia środowiska Conda wykonaj plik `automl_setup.cmd` w systemie Windows lub plik `.sh` dla Twojego systemu operacyjnego. Może to potrwać około 10 minut.

      Skrypt konfiguracji wykonuje następujące działania:
      + Tworzy nowe środowisko Conda
      + Instaluje wymagane pakiety
      + Konfiguruje widżet
      + Uruchamia notes Jupyter Notebook
      
   >[!NOTE]
   > Skrypt przyjmuje nazwę środowiska Conda jako parametr opcjonalny. Domyślna nazwa środowiska Conda to `azure_automl`. Dokładna postać polecenia zależy od systemu operacyjnego. Jest to przydatne w przypadku tworzenia nowego środowiska lub uaktualniania do nowej wersji. Na przykład za pomocą polecenia „automl_setup.cmd azure_automl_sandbox” możesz utworzyć nazwę środowiska azure_automl_sandbox. 
      
1. Po zakończeniu skryptu w przeglądarce zostanie wyświetlona strona główna notesu Jupyter Notebook.

1. Przejdź do ścieżki, w której zapisano notesy. 

1. Otwórz folder automated-machine-learning, a następnie otwórz notes **configuration.ipynb**. 

1. Wykonaj komórki w notesie, aby zarejestrować dostawcę zasobów Machine Learning Services i utworzyć obszar roboczy.

Teraz możesz otwierać i uruchamiać notesy zapisane na maszynie lokalnej.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [repozytorium notesów na potrzeby usługi Azure Machine Learning w serwisie GitHub](https://aka.ms/aml-notebooks)

Skorzystaj z następujących samouczków:
+ [Szkolenie i wdrażanie modelu klasyfikacji obrazów przy użyciu zestawu danych MNIST](tutorial-train-models-with-aml.md)

+ [Prepare data and use automated machine learning to train a regression model with the NYC taxi data set (Przygotowywanie danych i używanie zautomatyzowanego uczenia maszynowego w celu szkolenia modelu regresji przy użyciu zestawu danych dotyczących taksówek w Nowym Jorku)](tutorial-data-prep.md)
