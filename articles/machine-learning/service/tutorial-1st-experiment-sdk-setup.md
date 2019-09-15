---
title: 'Samouczek: Utwórz swój pierwszy eksperyment z ML: Konfiguracja'
titleSuffix: Azure Machine Learning
description: W tej serii samouczków ukończono kompleksowe kroki umożliwiające rozpoczęcie pracy z zestawem SDK języka Python Azure Machine Learning w notesach Jupyter.  Część jednej z nich obejmuje tworzenie środowiska serwera notesu w chmurze oraz tworzenie obszaru roboczego do zarządzania eksperymentami i modelami uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 08/28/2019
ms.openlocfilehash: 9bc5b9688a8cd568b47fe2dad88d6d007ceca0c4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004055"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Samouczek: Wprowadzenie do tworzenia pierwszego eksperymentu z użyciem zestawu SDK języka Python

W tym samouczku przedstawiono kompleksowe kroki umożliwiające rozpoczęcie pracy z zestawem SDK języka Python Azure Machine Learning w notesach Jupyter. Ten samouczek jest **częścią jednej z serii samouczków z dwiema częściami**oraz obejmuje instalację i konfigurację środowiska Python oraz tworzenie obszaru roboczego do zarządzania eksperymentami i modelami uczenia maszynowego. [**Częściowo dwie**](tutorial-1st-experiment-sdk-train.md) kompilacje na ten temat, aby szkolić wiele modeli uczenia maszynowego i wprowadzić proces zarządzania modelami przy użyciu zarówno Azure Portal, jak i zestawu SDK.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz [obszar roboczy usługi Azure Machine Learning](concept-workspace.md) do użycia w następnym samouczku.
> * Utwórz chmurową maszynę wirtualną Jupyter Notes Azure Machine Learning z zainstalowanym i wstępnie skonfigurowanym zestawem SDK języka Python.

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy Azure Machine Learning to podstawowe zasoby w chmurze, za pomocą których można eksperymentować, uczeniować i wdrażać modele uczenia maszynowego. Wiąże się to z łatwym w użyciu zestawem SDK i grupą zasobów platformy Azure. Jeśli masz już obszar roboczy Azure Machine Learning, przejdź do [następnej sekcji](#azure). W przeciwnym razie utwórz je teraz.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Tworzenie serwera notesu w chmurze

W tym przykładzie używany jest serwer notesu w chmurze w obszarze roboczym na potrzeby instalacji i wstępnie skonfigurowanego środowiska. Jeśli wolisz sterować środowiskiem, pakietami i zależnościami, użyj [własnego środowiska](how-to-configure-environment.md#local) .

W obszarze roboczym utworzysz zasób w chmurze, aby rozpocząć korzystanie z notesów Jupyter. Ten zasób to oparta na chmurze maszyna wirtualna z systemem Linux, wstępnie skonfigurowana z wszystko, czego potrzebujesz do uruchamiania Azure Machine Learning.

1. Otwórz obszar roboczy w [Azure Portal](https://portal.azure.com/).  Jeśli nie masz pewności, jak zlokalizować obszar roboczy w portalu, zobacz jak [znaleźć obszar roboczy](how-to-manage-workspace.md#view).

1. Na stronie obszaru roboczego w Azure Portal wybierz pozycję **maszyny wirtualne Notes** po lewej stronie.

1. Wybierz pozycję **+ Nowy** , aby utworzyć maszynę wirtualną notesu.

     ![Wybierz nową maszynę wirtualną](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. Podaj nazwę dla maszyny wirtualnej. 
   + Nazwa maszyny wirtualnej notesu musi zawierać od 2 do 16 znaków. Prawidłowe znaki to litery, cyfry i znaki.  
   + Nazwa musi również być unikatowa w ramach subskrypcji platformy Azure.

1. Następnie wybierz przycisk **Utwórz**. Skonfigurowanie maszyny wirtualnej może chwilę potrwać.

1. Poczekaj, aż stan zmieni się na **uruchomiony**.
   Po uruchomieniu maszyny wirtualnej Użyj sekcji **maszyny wirtualne notesu** , aby uruchomić interfejs sieci Web Jupyter.

1. Wybierz pozycję **Jupyter** w kolumnie **URI** dla maszyny wirtualnej.

    ![Uruchom serwer notesu Jupyter](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

   Link uruchamia serwer notesu i otwiera stronę sieci Web notesu Jupyter na nowej karcie przeglądarki.  Ten link będzie działał tylko dla osoby, która tworzy maszynę wirtualną. Każdy użytkownik obszaru roboczego musi utworzyć własną maszynę wirtualną.


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące zadania:

* Utworzono obszar roboczy Azure Machine Learning.
* Utworzono i skonfigurowano serwer notesu w chmurze w obszarze roboczym.

W **drugiej części** samouczka uruchomiono kod w `tutorial-1st-experiment-sdk-train.ipynb` celu uczenia modelu uczenia maszynowego. 

> [!div class="nextstepaction"]
> [Samouczek: Uczenie swojego pierwszego modelu](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Jeśli nie planujesz wykonywania następujących czynności w ramach tego samouczka lub innych samouczków, [Zatrzymaj maszynę wirtualną serwera notesu chmury](tutorial-1st-experiment-sdk-train.md#clean-up-resources) , gdy nie używasz jej do obniżenia kosztów.


