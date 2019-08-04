---
title: 'Samouczek: Zainstaluj środowisko i obszar roboczy.'
titleSuffix: Azure Machine Learning service
description: W tej serii samouczków ukończono kompleksowe kroki umożliwiające rozpoczęcie pracy z zestawem SDK języka Python Azure Machine Learning w notesach Jupyter.  Część jednej z nich obejmuje tworzenie środowiska serwera notesu w chmurze oraz tworzenie obszaru roboczego do zarządzania eksperymentami i modelami uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: baeb175c57573e557d298ff3197394ab619434ff
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772648"
---
# <a name="tutorial-setup-environment-and-workspace"></a>Samouczek: Zainstaluj środowisko i obszar roboczy

W tym samouczku przedstawiono kompleksowe kroki umożliwiające rozpoczęcie pracy z zestawem SDK języka Python Azure Machine Learning w notesach Jupyter. Ten samouczek jest **częścią jednej z serii samouczków z dwiema częściami**oraz obejmuje instalację i konfigurację środowiska Python oraz tworzenie obszaru roboczego do zarządzania eksperymentami i modelami uczenia maszynowego. [**Częściowo dwie**](tutorial-1st-experiment-sdk-train.md) kompilacje na ten temat, aby szkolić wiele modeli uczenia maszynowego i wprowadzić proces zarządzania modelami przy użyciu zarówno Azure Portal, jak i zestawu SDK.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz obszar roboczy uczenia maszynowego do użycia w następnym samouczku.
> * Tworzenie maszyny wirtualnej notesu, opartej na chmurze serwera Notatnika Jupyter Azure Machine Learning z wstępnie zainstalowanym i wstępnie skonfigurowanym zestawem SDK języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

Jedynym wymaganiem wstępnym dla tego samouczka jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy to podstawowe zasoby w chmurze, za pomocą których można eksperymentować, uczeniować i wdrażać modele uczenia maszynowego. Wiąże się to z łatwym w użyciu zestawem SDK i grupą zasobów platformy Azure. Jeśli masz już obszar roboczy usługi Azure Machine Learning, przejdź do [następnej sekcji](#azure). W przeciwnym razie utwórz je teraz.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Tworzenie serwera notesu w chmurze

W tym przykładzie używany jest serwer notesu w chmurze w obszarze roboczym na potrzeby instalacji i wstępnie skonfigurowanego środowiska. Jeśli wolisz sterować środowiskiem, pakietami i zależnościami, użyj [własnego środowiska](how-to-configure-environment.md#local) .

W obszarze roboczym utworzysz zasób w chmurze, aby rozpocząć korzystanie z notesów Jupyter. Ten zasób to oparta na chmurze maszyna wirtualna z systemem Linux, wstępnie skonfigurowana z wszystko, czego potrzebujesz do uruchomienia usługi Azure Machine Learning.

1. Otwórz obszar roboczy w [Azure Portal](https://portal.azure.com/).  Jeśli nie masz pewności, jak zlokalizować obszar roboczy w portalu, zobacz jak [znaleźć obszar roboczy](how-to-manage-workspace.md#view).

1. Na stronie obszaru roboczego w Azure Portal wybierz pozycję **maszyny wirtualne Notes** po lewej stronie.

1. Wybierz pozycję **+ Nowy** , aby utworzyć maszynę wirtualną notesu.

     ![Wybierz nową maszynę wirtualną](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Podaj nazwę dla maszyny wirtualnej. Następnie wybierz przycisk **Utwórz**.

    > [!NOTE]
    > Nazwa maszyny wirtualnej notesu musi zawierać od 2 do 16 znaków. Prawidłowe znaki to litery, cyfry i znaki.  Nazwa musi również być unikatowa w ramach subskrypcji platformy Azure.

    ![Utwórz nową maszynę wirtualną](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Poczekaj, aż stan zmieni się na **uruchomiony**.

### <a name="launch-jupyter-web-interface"></a>Uruchom interfejs sieci Web Jupyter

Po uruchomieniu maszyny wirtualnej Użyj sekcji **maszyny wirtualne notesu** , aby uruchomić interfejs sieci Web Jupyter.

1. Wybierz pozycję **Jupyter** w kolumnie **URI** dla maszyny wirtualnej.

    ![Uruchom serwer notesu Jupyter](./media/quickstart-run-cloud-notebook/start-server.png)

    Link uruchamia serwer notesu i otwiera stronę sieci Web notesu Jupyter na nowej karcie przeglądarki.  Ten link będzie działał tylko dla osoby, która tworzy maszynę wirtualną. Każdy użytkownik obszaru roboczego musi utworzyć własną maszynę wirtualną.

1. Na stronie internetowej notesu Jupyter znajduje się Górna nazwa folderu.  Wybierz ten folder.

    > [!TIP]
    > Ten folder znajduje się w [kontenerze magazynu](concept-workspace.md#resources) w obszarze roboczym, a nie na maszynie wirtualnej notesu.  Możesz usunąć maszynę wirtualną notesu i nadal zachować całą swoją służbę.  Po utworzeniu nowej maszyny wirtualnej notesu zostanie ona załadowana w tym samym folderze. Jeśli udostępnisz obszar roboczy innym osobom, zobaczysz Twój folder i zobaczysz ich.

1. Otwórz podkatalog, a następnie otwórz `tutorials/tutorial-1st-experiment-sdk-train.ipynb` w celu uruchomienia **części dwóch** samouczka. `samples-*`

## <a name="end"></a>Czyszczenie zasobów

Nie wykonuj tej sekcji, jeśli planujesz przejście do **części 2** samouczka.

### <a name="stop-the-notebook-vm"></a>Zatrzymaj maszynę wirtualną notesu

Jeśli używasz serwera notesu w chmurze, Zatrzymaj maszynę wirtualną, gdy nie używasz jej do obniżenia kosztów.

1. W obszarze roboczym wybierz pozycję **maszyny wirtualne Notes**.

   ![Zatrzymaj serwer maszyn wirtualnych](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Z listy wybierz maszynę wirtualną.

1. Wybierz pozycję **Zatrzymaj**.

1. Gdy wszystko będzie gotowe do korzystania z serwera, wybierz pozycję **Uruchom**.

### <a name="delete-everything"></a>Usuń wszystko

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zostały wykonane następujące zadania:

* Utworzono obszar roboczy usługi Azure Machine Learning.
* Utworzono i skonfigurowano serwer notesu w chmurze w obszarze roboczym.

Przejdź do **części 2** tego samouczka, aby nauczyć prosty model uczenia maszynowego.

> [!div class="nextstepaction"]
> [Samouczek: Uczenie swojego pierwszego modelu](tutorial-1st-experiment-sdk-train.md)
