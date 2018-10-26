---
title: Tworzenie i wdrażanie obrazu podobieństwa modelu przy użyciu usługi Azure Machine Learning pakietu dla przetwarzania obrazów.
description: Informacje o sposobie tworzenia, uczenia, testowania i wdrażania modelu przetwarzania obrazów obraz podobieństw przy użyciu pakietu Learning maszyny platformy Azure do przetwarzania obrazów.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: b1b4e3e19adb38a69e16aaa98300972df3bb71a8
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093653"
---
# <a name="build-and-deploy-image-similarity-models-with-azure-machine-learning"></a>Tworzenie i wdrażanie modeli podobieństwa obrazów za pomocą usługi Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


W tym artykule Dowiedz się, jak używać Azure Machine Learning pakietu przetwarzania komputera (AMLPCV) do uczenia, testowania i wdrażania modelu podobieństwa obrazu. Aby uzyskać omówienie tego pakietu i jego ze szczegółową dokumentacją referencyjną, zobacz https://aka.ms/aml-packages/vision.

Dużą liczbę problemów w domenie przetwarzania komputera będzie możliwe przy użyciu obrazu podobieństwa. Te problemy obejmują budowania modeli, które odpowiadają pytania, takie jak:
+ _OBIEKT jest obecny w obrazie? Na przykład "dog", "samochód", "Publikuj" i tak dalej_
+ _Jakie klasy oka choroby ważności jest evinced przez skanowanie retinal to pacjenta?_

Podczas kompilowania i wdrażania tego modelu przy użyciu AMLPCV, możesz przejść przez następujące kroki:
1. Tworzenie zestawu danych
2. Obraz wizualizacji i adnotacji
3. Rozszerzenie obrazu
4. Głębokie definicję modelu sieci neuronowych (DNN)
5. Klasyfikator szkolenia
6. Ocena i wizualizacji
7. Wdrażanie usługi sieci Web
8. Testowanie obciążeniowe usługi sieci Web

[CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) służy jako platforma do uczenia głębokiego szkolenia jest wykonywana lokalnie na maszynie GPU oparte takie jak ([głębokiego uczenia maszyna wirtualna do nauki o danych](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)), i wdrożenie używa interfejsu wiersza polecenia platformy Azure ML Operacjonalizacji.

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Następujące konta i aplikacji, musisz skonfigurować i zainstalować:
   - Konto Eksperymentowanie w usłudze Azure Machine Learning 
   - Konto Zarządzanie modelami w usłudze Azure Machine Learning
   - Zainstalowana aplikacja Azure Machine Learning Workbench

   Jeśli te trzy nie zostały jeszcze utworzone lub zainstalowany, postępuj zgodnie z [instalacji usługi Azure Machine Learning Przewodnik Szybki Start i aplikacja Workbench](../desktop-workbench/quickstart-installation.md) artykułu. 

1. Musi być zainstalowany pakiet Learning maszyny platformy Azure do przetwarzania obrazów. Dowiedz się, jak zainstalować ten pakiet, zgodnie z opisem w https://aka.ms/aml-packages/vision.

## <a name="sample-data-and-notebook"></a>Przykładowe dane i notesu

### <a name="get-the-jupyter-notebook"></a>Pobieranie notesu programu Jupyter

Pobieranie notesu do uruchomienia przykładu opisane w tym miejscu samodzielnie.

> [!div class="nextstepaction"]
> [Pobieranie notesu programu Jupyter](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Ładowanie danych przykładowych

W poniższym przykładzie użyto zestawu danych składające się z 63 zastawy stołowe obrazów. Każdy obraz jest oznaczony jako należący do jednej z czterech różnych klas (bowl cup, sztućce, płytkę). Liczba obrazów, w tym przykładzie jest mały, aby w tym przykładzie, które mogą być wykonywane szybko. W praktyce należy podać co najmniej 100 obrazów na klasy. Wszystkie obrazy znajdują się w folderze *"... /sample_data/imgs_recycling / "* w podkatalogach o nazwie"bowl","cup","sztućce"i"tablicy".

![Zestaw danych usługi Azure Machine Learning](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi Azure Machine Learning pakietu dla przetwarzania obrazów w następujących artykułach:

+ Dowiedz się, jak [poprawić dokładność ten model](how-to-improve-accuracy-for-computer-vision-models.md).

+ Odczyt [pakietu Przegląd](https://aka.ms/aml-packages/vision).

+ Zapoznaj się z [dokumentację referencyjną](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) dla tego pakietu.

+ Dowiedz się więcej o [inne pakiety języka Python dla usługi Azure Machine Learning](reference-python-package-overview.md).
