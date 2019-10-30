---
title: 'Samouczek: Tworzenie pierwszego eksperymentu z ML: Konfiguracja'
titleSuffix: Azure Machine Learning
description: W ramach tego samouczka nastąpi rozpoczęcie pracy z zestawem SDK środowiska Azure Machine Learning Python uruchomionym w notesach Jupyter.  W części 1 utworzysz obszar roboczy, w którym będziesz zarządzać eksperymentami i modelami ML.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: fc26b224a2af6ab4f1f6bf5551381d4739831351
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053871"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Samouczek: wprowadzenie do tworzenia pierwszego eksperymentu z użyciem zestawu SDK języka Python

W tym samouczku przedstawiono kompleksowe kroki umożliwiające rozpoczęcie pracy z zestawem SDK języka Python Azure Machine Learning w notesach Jupyter. Ten samouczek jest **częścią jednej z serii samouczków z dwiema częściami**oraz obejmuje instalację i konfigurację środowiska Python oraz tworzenie obszaru roboczego do zarządzania eksperymentami i modelami uczenia maszynowego. [**Częściowo dwie**](tutorial-1st-experiment-sdk-train.md) kompilacje na ten temat, aby szkolić wiele modeli uczenia maszynowego i wprowadzić proces zarządzania modelami przy użyciu zarówno Azure Portal, jak i zestawu SDK.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz [obszar roboczy usługi Azure Machine Learning](concept-workspace.md) do użycia w następnym samouczku.
> * Sklonuj Notes samouczków do folderu w obszarze roboczym.
> * Utwórz chmurową maszynę wirtualną Jupyter Notes Azure Machine Learning z zainstalowanym i wstępnie skonfigurowanym zestawem SDK języka Python.

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy Azure Machine Learning to podstawowe zasoby w chmurze, za pomocą których można eksperymentować, uczeniować i wdrażać modele uczenia maszynowego. Łączy ona Twoją subskrypcję i grupę zasobów platformy Azure z łatwym w użyciu obiektem w usłudze. 

Aby zarządzać zasobami platformy Azure, można utworzyć obszar roboczy za pośrednictwem Azure Portal konsoli internetowej. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Zanotuj swój **obszar roboczy** i **subskrypcję**. Będą one potrzebne do utworzenia eksperymentu w odpowiednim miejscu. 


## <a name="azure"></a>Klonowanie folderu notesu

W tym przykładzie używany jest serwer notesu w chmurze w obszarze roboczym na potrzeby instalacji i wstępnie skonfigurowanego środowiska. Jeśli wolisz sterować środowiskiem, pakietami i zależnościami, użyj [własnego środowiska](how-to-configure-environment.md#local) .

Wykonaj następujące czynności konfiguracji i uruchamiania na stronie docelowej obszaru roboczego (wersja zapoznawcza) — skonsolidowany interfejs, który obejmuje narzędzia uczenia maszynowego do wykonywania scenariuszy analizy danych dla lekarzy danych wszystkich poziomów umiejętności.

1. Zaloguj się do [strony docelowej obszaru roboczego](https://ml.azure.com/).

1. Wybierz swoją subskrypcję i utworzony obszar roboczy.

1. Wybierz **Notesy i pliki** po lewej stronie.

1. Otwórz folder **Samples** .

1. Wybierz pozycję **"..."** po prawej stronie folderu **samouczki** , a następnie wybierz pozycję **Klonuj**.

    ![Klonuj folder](media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. Dla każdego użytkownika, który uzyskuje dostęp do obszaru roboczego, jest wyświetlany folder.  Wybierz folder, w którym ma zostać sklonowany folder **samouczka** .

## <a name="a-nameopenselect-a-vm-to-run-the-notebook"></a><a name="open">wybrać maszynę wirtualną do uruchomienia notesu

1. W obszarze **pliki użytkownika** Otwórz folder, a następnie otwórz folder sklonowane **samouczki** .

    ![Otwórz folder samouczków](media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Notesy można wyświetlać w folderze **Samples** , ale nie można w tym miejscu uruchamiać notesu.  W celu uruchomienia notesu upewnij się, że otwarto sklonowaną wersję notesu w sekcji **pliki użytkownika** .
    
1. Wybierz plik **samouczek — pierwszy eksperyment-zestaw SDK-uczenie. ipynb** w folderze **samouczków** .

1. Na górnym pasku wybierz maszynę wirtualną notesu do użycia w celu uruchomienia notesu. Te maszyny wirtualne są wstępnie skonfigurowane z wszystko, czego potrzebujesz do uruchamiania Azure Machine Learning. Można wybrać maszynę wirtualną utworzoną przez dowolnego użytkownika obszaru roboczego. 

1. Jeśli nie znaleziono żadnych maszyn wirtualnych, wybierz pozycję **+ Nowa maszyna wirtualna** , aby utworzyć maszynę wirtualną.

    ![Tworzenie maszyny wirtualnej](media/tutorial-1st-experiment-sdk-setup/no-vm.png)

    1. Podczas tworzenia maszyny wirtualnej Podaj nazwę.  Nazwa musi składać się z od 2 do 16 znaków. Prawidłowe znaki to litery, cyfry i znaki, a także muszą być unikatowe w ramach subskrypcji platformy Azure.

    1. Następnie wybierz przycisk **Utwórz**. Skonfigurowanie maszyny wirtualnej może potrwać około 5 minut.

1. Gdy maszyna wirtualna będzie dostępna, zostanie wyświetlona na górnym pasku narzędzi.  Można teraz uruchomić Notes przy użyciu opcji **Uruchom wszystko** na pasku narzędzi lub naciskając **klawisze SHIFT + ENTER** w komórkach kodu notesu.


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące zadania:

* Utworzono obszar roboczy Azure Machine Learning.
* Utworzono i skonfigurowano serwer notesu w chmurze w obszarze roboczym.

W **drugiej części** samouczka uruchomiono kod w `tutorial-1st-experiment-sdk-train.ipynb` do uczenia modelu uczenia maszynowego. 

> [!div class="nextstepaction"]
> [Samouczek: uczenie swojego pierwszego modelu](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Jeśli nie planujesz wykonywania następujących czynności w ramach tego samouczka lub innych samouczków, [Zatrzymaj maszynę wirtualną serwera notesu chmury](tutorial-1st-experiment-sdk-train.md#clean-up-resources) , gdy nie używasz jej do obniżenia kosztów.


