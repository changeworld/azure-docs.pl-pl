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
ms.openlocfilehash: 891615ea301348b83124823b10403964d394c224
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476056"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Samouczek: wprowadzenie do tworzenia pierwszego eksperymentu z użyciem zestawu SDK języka Python
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym samouczku przedstawiono kompleksowe kroki umożliwiające rozpoczęcie pracy z zestawem SDK języka Python Azure Machine Learning w notesach Jupyter. Ten samouczek jest **częścią jednej z serii samouczków z dwiema częściami**oraz obejmuje instalację i konfigurację środowiska Python oraz tworzenie obszaru roboczego do zarządzania eksperymentami i modelami uczenia maszynowego. [**Częściowo dwie**](tutorial-1st-experiment-sdk-train.md) kompilacje na ten temat umożliwiają uczenie wielu modeli uczenia maszynowego i wprowadzenie procesu zarządzania modelami przy użyciu zarówno Azure Machine Learning Studio, jak i zestawu SDK.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz [obszar roboczy usługi Azure Machine Learning](concept-workspace.md) do użycia w następnym samouczku.
> * Sklonuj Notes samouczków do folderu w obszarze roboczym.
> * Tworzenie wystąpienia obliczeniowego opartego na chmurze z zainstalowanym i wstępnie skonfigurowanym zestawem SDK języka Python Azure Machine Learning.


Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy Azure Machine Learning to podstawowe zasoby w chmurze, za pomocą których można eksperymentować, uczeniować i wdrażać modele uczenia maszynowego. Łączy ona Twoją subskrypcję i grupę zasobów platformy Azure z łatwym w użyciu obiektem w usłudze. 

Aby zarządzać zasobami platformy Azure, można utworzyć obszar roboczy za pośrednictwem Azure Portal konsoli internetowej. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Zanotuj swój **obszar roboczy** i **subskrypcję**. Będą one potrzebne do utworzenia eksperymentu w odpowiednim miejscu. 


## <a name="azure"></a>Klonowanie folderu notesu

W tym przykładzie używany jest serwer notesu w chmurze w obszarze roboczym na potrzeby instalacji i wstępnie skonfigurowanego środowiska. Jeśli wolisz sterować środowiskiem, pakietami i zależnościami, użyj [własnego środowiska](how-to-configure-environment.md#local) .

Należy wykonać następujące czynności w celu skonfigurowania i uruchomienia kroków w programie Azure Machine Learning Studio — skonsolidowany interfejs, który obejmuje narzędzia uczenia maszynowego do wykonywania scenariuszy analizy danych dla lekarzy danych wszystkich poziomów umiejętności.

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/).

1. Wybierz swoją subskrypcję i utworzony obszar roboczy.

1. Wybierz **notesy** po lewej stronie.

1. Otwórz folder **Samples** .

1. Otwórz folder **Python** .

1. Otwórz w folderze numer wersji.  Ta liczba reprezentuje bieżącą wersję zestawu Python SDK.

1. Wybierz pozycję **"..."** po prawej stronie folderu **samouczki** , a następnie wybierz pozycję **Klonuj**.

    ![Klonuj folder](media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. Zostanie wyświetlona lista folderów pokazująca każdego użytkownika, który uzyskuje dostęp do obszaru roboczego.  Wybierz folder, w którym ma zostać sklonowany folder **samouczków** .

## <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">otworzyć sklonowanego notesu

1. W obszarze **pliki użytkownika** Otwórz folder, a następnie otwórz folder sklonowane **samouczki** .

    ![Otwórz folder samouczków](media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Notesy można wyświetlać w folderze **Samples** , ale nie można w tym miejscu uruchamiać notesu.  W celu uruchomienia notesu upewnij się, że otwarto sklonowaną wersję notesu w sekcji **pliki użytkownika** .
    
1. Wybierz plik **samouczek — pierwszy eksperyment-zestaw SDK-uczenie. ipynb** w folderze **samouczków** .

1. Na górnym pasku wybierz wystąpienie obliczeniowe, które ma zostać użyte do uruchomienia notesu. Te maszyny wirtualne są wstępnie skonfigurowane z [wszystko, czego potrzebujesz do uruchamiania Azure Machine Learning](concept-compute-instance.md#contents). Można wybrać maszynę wirtualną utworzoną przez dowolnego użytkownika obszaru roboczego. 

1. Jeśli nie zostaną znalezione żadne maszyny wirtualne, wybierz pozycję **+ Dodaj obliczenia** , aby utworzyć maszynę wirtualną.

    1. Podczas tworzenia maszyny wirtualnej Podaj nazwę.  Nazwa musi składać się z od 2 do 16 znaków. Prawidłowe znaki to litery, cyfry i znaki, a także muszą być unikatowe w ramach subskrypcji platformy Azure.

    1. Następnie wybierz przycisk **Utwórz**. Skonfigurowanie maszyny wirtualnej może potrwać około 5 minut.

1. Gdy maszyna wirtualna będzie dostępna, zostanie wyświetlona na górnym pasku narzędzi.  Można teraz uruchomić Notes przy użyciu opcji **Uruchom wszystko** na pasku narzędzi lub naciskając **klawisze SHIFT + ENTER** w komórkach kodu notesu.

> [!NOTE]
> Wystąpienia obliczeniowe są dostępne tylko dla obszarów roboczych z regionem **Północno-środkowe stany USA** lub **Południowe Zjednoczone Królestwo**.
>Jeśli obszar roboczy znajduje się w innym regionie, możesz w zamian utworzyć [maszynę wirtualną w notesie](concept-compute-instance.md#notebookvm) i korzystać z niej.  Do uruchomienia notesu można użyć maszyny wirtualnej lub wystąpienia obliczeniowego.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące zadania:

* Utworzono obszar roboczy Azure Machine Learning.
* Utworzono i skonfigurowano serwer notesu w chmurze w obszarze roboczym.

W **drugiej części** samouczka uruchomiono kod w `tutorial-1st-experiment-sdk-train.ipynb` do uczenia modelu uczenia maszynowego. 

> [!div class="nextstepaction"]
> [Samouczek: uczenie swojego pierwszego modelu](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Jeśli nie planujesz wykonywania następujących czynności w ramach tego samouczka lub innych samouczków, [Zatrzymaj maszynę wirtualną serwera notesu chmury](tutorial-1st-experiment-sdk-train.md#clean-up-resources) , gdy nie używasz jej do obniżenia kosztów.


