---
title: 'Samouczek: Tworzenie pierwszego eksperymentu ml'
titleSuffix: Azure Machine Learning
description: W tym samouczku zostanie rozpoczęte korzystanie z zestawu SDK języka Python usługi Azure Machine Learning działającego w notesach jupytera.  W części 1 tworzysz obszar roboczy, w którym będziesz zarządzać eksperymentami i modelami uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: 820332b0692c0c863ed23912fe9913c419769155
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273005"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Samouczek: Pierwsze rozpoczęcie tworzenia pierwszego eksperymentu uczenia maszynowego za pomocą zestawu SDK języka Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym samouczku należy wykonać kompleksowe kroki, aby rozpocząć pracę z zestawem SDK języka Python usługi Azure Machine Learning działającym w notesach jupytera. Ten samouczek jest **częścią pierwszej serii dwuczęściowych samouczków**i obejmuje konfigurację i konfigurację środowiska języka Python, a także tworzenie obszaru roboczego do zarządzania eksperymentami i modelami uczenia maszynowego. [**Część druga**](tutorial-1st-experiment-sdk-train.md) opiera się na tym, aby wyszkolić wiele modeli uczenia maszynowego i wprowadzić proces zarządzania modelami przy użyciu zarówno usługi Azure Machine Learning studio, jak i SDK.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz [obszar roboczy usługi Azure Machine Learning](concept-workspace.md) do użycia w następnym samouczku.
> * Sklonuj notes samouczków do folderu w obszarze roboczym.
> * Utwórz wystąpienie obliczeniowe oparte na chmurze z zainstalowanym i wstępnie skonfigurowanym zestawem SDK języka Python usługi Azure Machine Learning.


Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy usługi Azure Machine Learning to podstawowy zasób w chmurze używany do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego. Wiąże subskrypcję platformy Azure i grupę zasobów z łatwo zużywanym obiektem w usłudze. 

Tworzenie obszaru roboczego za pośrednictwem witryny Azure portal, konsoli opartej na sieci Web do zarządzania zasobami platformy Azure. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Zanotuj **swój obszar roboczy** i **subskrypcję**. Będą one potrzebne, aby zapewnić utworzenie eksperymentu we właściwym miejscu. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Uruchamianie notesu w obszarze roboczym

W tym samouczku użyto serwera notesu w chmurze w obszarze roboczym, aby uzyskać środowisko wolne od instalacji i wstępnie skonfigurowane. Użyj [własnego środowiska,](how-to-configure-environment.md#local) jeśli wolisz mieć kontrolę nad środowiskiem, pakietami i zależnościami.

Postępuj zgodnie z tym klipem wideo lub skorzystaj ze szczegółowych czynności poniżej, aby sklonować i uruchomić samouczek z obszaru roboczego. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]



### <a name="clone-a-notebook-folder"></a>Klonowanie folderu notesu

Wykonaj następującą konfigurację eksperymentu i uruchom kroki w usłudze Azure Machine Learning studio, skonsolidowanym interfejsie, który zawiera narzędzia uczenia maszynowego do wykonywania scenariuszy do nauki o danych dla praktyków nauki o danych na wszystkich poziomach umiejętności.

1. Zaloguj się do [studia usługi Azure Machine Learning](https://ml.azure.com/).

1. Wybierz subskrypcję i utworzony obszar roboczy.

1. Po lewej stronie **wybierz pozycję Notesy.**

1. Otwórz folder **Przykłady.**

1. Otwórz folder **Python.**

1. Otwórz folder z numerem wersji.  Ta liczba reprezentuje bieżącą wersję zestawu SDK języka Python.

1. Wybierz **"..."** po prawej stronie folderu **samouczków,** a następnie wybierz pozycję **Klonuj**.

    ![Folder Klonowanie](./media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. Zostanie wyświetlona lista folderów przedstawiająca każdego użytkownika, który uzyskuje dostęp do obszaru roboczego.  Wybierz folder, aby sklonować tam folder **samouczków.**

### <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">Otwieranie sklonowanego notesu

1. W obszarze **Pliki użytkownika** otwórz folder, a następnie otwórz folder sklonowanych **samouczków.**

    ![Otwórz folder samouczków](./media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Notesy można wyświetlać w folderze **przykładów,** ale nie można uruchomić notesu.  Aby uruchomić notes, upewnij się, że sklonowana wersja notesu została otwarta w sekcji **Pliki użytkownika.**
    
1. Wybierz plik **samouczek-1st-experiment-sdk-train.ipynb** w folderze **tutorials/create-first-ml-experiment.**

1. Na górnym pasku wybierz wystąpienie obliczeniowe, które będzie używane do uruchamiania notesu. Te maszyny wirtualne są wstępnie skonfigurowane ze [wszystkim, czego potrzebujesz do uruchomienia usługi Azure Machine Learning.](concept-compute-instance.md#contents) 

1. Jeśli nie znaleziono żadnych maszyn wirtualnych, wybierz **+ Dodaj,** aby utworzyć maszynę wirtualną wystąpienia obliczeniowego. 

    1. Podczas tworzenia maszyny Wirtualnej podaj nazwę.  Nazwa musi zawierać od 2 do 16 znaków. Prawidłowe znaki to litery, cyfry i znak - i muszą być również unikatowe w całej subskrypcji platformy Azure.

    1.  Wybierz rozmiar maszyny wirtualnej z dostępnych opcji.

    1. Następnie wybierz pozycję **Utwórz**. Skonfigurowanie maszyny wirtualnej może potrwać około 5 minut.

1. Gdy maszyna wirtualna jest dostępna, zostanie wyświetlona na górnym pasku narzędzi.  Notes można teraz uruchomić za pomocą funkcji **Uruchom wszystko** na pasku narzędzi lub za pomocą **klawiszy Shift+Enter** w komórkach kodu notesu.

Jeśli masz niestandardowe widżety lub wolisz używać Jupyter/JupyterLab wybierz kroplówę **Jupyter** po prawej stronie, a następnie wybierz **Jupyter** lub **JupyterLab**. Zostanie otwarte nowe okno przeglądarki.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonałeś następujące zadania:

* Utworzono obszar roboczy usługi Azure Machine Learning.
* Utworzono i skonfigurowano serwer notesu w chmurze w obszarze roboczym.

W **drugiej części** samouczka można `tutorial-1st-experiment-sdk-train.ipynb` uruchomić kod w celu uczenia modelu uczenia maszynowego. 

> [!div class="nextstepaction"]
> [Samouczek: Trenuj swój pierwszy model](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Jeśli nie planujesz następujące część 2 tego samouczka lub innych samouczków, należy [zatrzymać maszyny Wirtualnej serwera notesu w chmurze,](tutorial-1st-experiment-sdk-train.md#clean-up-resources) gdy nie używasz go do zmniejszenia kosztów.


