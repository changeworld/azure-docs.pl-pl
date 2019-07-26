---
title: 'Szybki start: Uruchamianie notesu w chmurze'
titleSuffix: Azure Machine Learning service
description: W tym samouczku pokazano, jak rozpocząć pracę z usługą Azure Machine Learning i korzystać z zarządzanego serwera notesu w chmurze.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 1124bb17abb9340b442d8d6075551ffe0dc681f7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68371056"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Szybki start: Aby rozpocząć pracę z usługą Azure Machine Learning, Użyj serwera notesu opartego na chmurze

Ten przewodnik Szybki Start uczy się, jak rozpocząć korzystanie z usługi Azure Machine Learning przy użyciu zarządzanego serwera notesu w chmurze. Instalacja nie jest wymagana. Jeśli chcesz zamiast tego zainstalować zestaw SDK w środowisku języka Python, zobacz [szybki start: Użyj własnego serwera notesu, aby rozpocząć pracę z](quickstart-run-local-notebook.md)Azure Machine Learning.

W tym przewodniku szybki start pokazano, jak używać [obszaru roboczego usługi Azure Machine Learning](concept-azure-machine-learning-architecture.md) do śledzenia eksperymentów uczenia maszynowego (ml). W tym celu należy utworzyć [maszynę wirtualną z notesem (wersja zapoznawcza)](how-to-configure-environment.md#notebookvm): bezpieczna, oparta na chmurze stacja robocza platformy Azure, która udostępnia serwer Jupyter Notes, JupyterLab i w pełni przygotowane środowisko ml. Następnie uruchamiasz na tej maszynie wirtualnej Notes w języku Python, który rejestruje wartości w obszarze roboczym.

Aby to zrobić, wykonaj następujące czynności:

* Utwórz obszar roboczy.
* Utwórz maszynę wirtualną notesu w obszarze roboczym.
* Otwórz interfejs sieci Web Jupyter.
* Otwórz Notes zawierający kod umożliwiający oszacowanie liczby pi i rejestrowanie błędów w każdej iteracji.
* Uruchom Notes.
* Wyświetl zarejestrowane wartości błędów w obszarze roboczym. Poniższy przykład pokazuje, jak obszar roboczy ułatwia śledzenie informacji generowanych w skrypcie.

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Jeśli masz obszar roboczy usługi Azure Machine Learning, przejdź do [następnej sekcji](#create-notebook). W przeciwnym razie utwórz je teraz.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-notebook"></a>Tworzenie maszyny wirtualnej notesu

 W obszarze roboczym Utwórz zasób w chmurze, aby rozpocząć korzystanie z notesów Jupyter. Ten zasób jest platformą opartą na chmurze, która jest wstępnie skonfigurowana z wszystko, czego potrzebujesz do uruchomienia usługi Azure Machine Learning.

1. Otwórz obszar roboczy w [Azure Portal](https://portal.azure.com/). Jeśli nie masz pewności, jak zlokalizować obszar roboczy w portalu, zobacz How to View The [Workspace](how-to-manage-workspace.md#view).

1. Na stronie obszaru roboczego wybierz pozycję **maszyny wirtualne Notes** po lewej stronie.

1. Wybierz pozycję **+ Nowy** , aby utworzyć maszynę wirtualną notesu.  

     ![Wybierz nową maszynę wirtualną](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Podaj nazwę dla maszyny wirtualnej. Następnie wybierz przycisk **Utwórz**.

    > [!NOTE]
    > Nazwa maszyny wirtualnej notesu musi mieć długość od 2 do 16 znaków. Litery, cyfry i łączniki są prawidłowymi znakami. Nazwa musi być unikatowa w ramach subskrypcji platformy Azure.

    ![Utwórz nową maszynę wirtualną](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Odczekaj około 4 do 5 minut, aż stan zmieni się na **uruchomiony**.


## <a name="open-the-jupyter-web-interface"></a>Otwórz interfejs sieci Web Jupyter

Po uruchomieniu maszyny wirtualnej Użyj sekcji **maszyny wirtualne notesu** , aby otworzyć interfejs sieci Web Jupyter.

1. Wybierz pozycję **Jupyter** w kolumnie **URI** dla maszyny wirtualnej.  

    ![Uruchom serwer notesu Jupyter](./media/quickstart-run-cloud-notebook/start-server.png)

    Link uruchamia serwer notesu i otwiera stronę sieci Web notesu Jupyter na nowej karcie przeglądarki.  Ten link będzie działał tylko dla osoby, która tworzy maszynę wirtualną.  Każdy użytkownik obszaru roboczego musi utworzyć własną maszynę wirtualną.

1. Na stronie sieci Web notesu Jupyter Nazwa głównego folderu to Twoja nazwa użytkownika. Wybierz ten folder.

    > [!TIP]
    > Ten folder znajduje się w [kontenerze magazynu](concept-workspace.md#resources) w obszarze roboczym, a nie na maszynie wirtualnej notesu.  Możesz usunąć maszynę wirtualną notesu i nadal zachować całą swoją służbę.  Po utworzeniu nowej maszyny wirtualnej notesu zostanie ona załadowana w tym samym folderze.  Jeśli udostępnisz obszar roboczy innym osobom, zobaczysz Twój folder i zobaczysz ich. 

1. Nazwa folderu przykłady zawiera numer wersji (na przykład**przykłady-1.0.33.1**). Wybierz folder Samples.

1. Wybierz folder **szybkiego startu** .

## <a name="run-the-notebook"></a>Uruchamianie notesu

Uruchom Notes, który szacuje pi i rejestruje błąd w obszarze roboczym.

1. Wybierz plik **01.run experiment.ipynb**, aby otworzyć notes.

1. Jeśli zobaczysz Alert "nie znaleziono jądra", wybierz jądro **Python 3,6-Azure** (około połowy na liście) i ustaw jądro.

1. Wybierz pierwszą komórkę kodu, a następnie wybierz pozycję **Uruchom**.

    > [!NOTE]
    > Komórki kodu mają nawiasy klamrowe. Jeśli nawiasy są puste ( __[]__ ), kod nie został uruchomiony. Gdy kod jest uruchomiony, zostanie wyświetlona gwiazdka ( __[*]__ ). Po zakończeniu wykonywania kodu zostanie wyświetlona liczba **[1]** .  Liczba wskazuje kolejność, w której uruchomiono komórki.
    >
    > Użyj **klawiszy SHIFT + ENTER** jako skrótu, aby uruchomić komórkę.

    ![Uruchom pierwszą komórkę kodu](media/quickstart-run-cloud-notebook/cell1.png)

1. Uruchom drugą komórkę kodu. Jeśli widzisz instrukcje dotyczące uwierzytelniania, skopiuj kod i postępuj zgodnie z linkiem, aby się zalogować. Po zalogowaniu się w przeglądarce zostanie zapamiętane to ustawienie.  

    ![Uwierzytelnij](media/quickstart-run-cloud-notebook/authenticate.png)

1. Po pomyślnym uruchomieniu komórki kodu zostanie wyświetlony numer komórki __[2]__ . Jeśli zajdzie potrzeba zalogowania się, zostanie wyświetlony komunikat o stanie pomyślne uwierzytelnienie.   Jeśli nie musisz się zalogować, nie zobaczysz żadnych danych wyjściowych dla tej komórki. Zobaczysz tylko liczbę, która pojawia się, aby pokazać, że komórka została pomyślnie uruchomiona.

    ![Komunikat o powodzeniu](media/quickstart-run-cloud-notebook/success.png)

1. Uruchom resztę komórek kodu. Po zakończeniu każdej komórki jest wyświetlany numer komórki. Tylko Ostatnia komórka wyświetla wszystkie inne dane wyjściowe.  

    W największej komórce `run.log` kodu pojawia się w wielu miejscach. Każda `run.log` z nich dodaje swoją wartość do obszaru roboczego.

## <a name="view-logged-values"></a>Wyświetlanie zarejestrowanych wartości

1. Dane wyjściowe z `run` komórki zawierają łącze z powrotem do Azure Portal, w którym można przeglądać wyniki eksperymentu w obszarze roboczym.

    ![Wyświetlanie eksperymentów](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Wybierz **link do witryny Azure Portal** , aby wyświetlić informacje o przebiegu w obszarze roboczym. Ten link powoduje otwarcie obszaru roboczego w witrynie Azure Portal.

1. Wykresy zarejestrowanych wartości, które zobaczysz, zostały automatycznie utworzone w obszarze roboczym. Zawsze w przypadku zarejestrowania wielu wartości z tym samym parametrem nazwa wykres jest generowany automatycznie. Oto przykład:

   ![Wyświetlanie historii](./media/quickstart-run-cloud-notebook/web-results.png)

Ponieważ kod przybliżony pi używa losowych wartości, Twoje wykresy mogą wyglądać inaczej.  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

### <a name="stop-the-notebook-vm"></a>Zatrzymaj maszynę wirtualną notesu

Zatrzymaj maszynę wirtualną Notes, gdy nie używasz jej do obniżenia kosztów.  

1. W obszarze roboczym wybierz pozycję **maszyny wirtualne Notes**.

   ![Zatrzymaj serwer maszyn wirtualnych](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Z listy wybierz maszynę wirtualną.

1. Wybierz pozycję **Zatrzymaj**.

1. Gdy wszystko będzie gotowe do korzystania z serwera, wybierz pozycję **Uruchom**.

### <a name="delete-everything"></a>Usuń wszystko

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Po wykonaniu tych zadań przejdź do strony sieci Web Jupyter Notebook. W folderze **szybkiego startu** Otwórz i uruchom w notesie **02. deploy-Web-Service. ipynb** , aby dowiedzieć się, jak wdrożyć usługę sieci Web.

Jeśli chcesz zainstalować inne pakiety języka Python w środowisku Jupyter, użyj tego kodu w notesie:

```
!source activate py36 && pip install <packagename>
```

Na Jupyter Notebook stronie sieci Web Przejrzyj inne notesy w folderze Samples (przykłady), aby dowiedzieć się więcej na temat usługi Azure Machine Learning.

Aby poznać szczegółowo środowisko przepływu pracy, wykonaj czynności opisane w samouczku dotyczącym trenowania i wdrażania modelu w usłudze Machine Learning:  

> [!div class="nextstepaction"]
> [Samouczek: trenowanie modelu klasyfikacji obrazów](tutorial-train-models-with-aml.md)
