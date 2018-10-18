---
title: 'Szybki start: tworzenie obszaru roboczego usługi Machine Learning w witrynie Azure Portal — Azure Machine Learning'
description: Obszar roboczy usługi Azure Machine Learning można utworzyć w witrynie Azure Portal. Ten obszar roboczy to podstawowy blok w chmurze umożliwiający eksperymentowanie z modelami uczenia maszynowego, ich szkolenie oraz wdrażanie za pomocą usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: b6f0201a36a676e7647b9f5e60bc2df3415b9594
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831334"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning-service"></a>Szybki start: rozpoczynanie pracy z usługą Azure Machine Learning w witrynie Azure Portal

W tym przewodniku Szybki start utworzysz obszar roboczy usługi Azure Machine Learning w witrynie Azure Portal. Ten obszar roboczy to podstawowy blok w chmurze umożliwiający eksperymentowanie z modelami uczenia maszynowego, ich szkolenie oraz wdrażanie za pomocą usługi Azure Machine Learning. 

W tym samouczku zostaną wykonane następujące czynności:

* Tworzenie obszaru roboczego w subskrypcji platformy Azure.
* Wypróbowanie jego działania za pomocą języka Python w notesie platformy Azure i rejestrowanie wartości z wielu iteracji.
* Wyświetlanie zarejestrowanych wartości w obszarze roboczym.

Dla Twojej wygody następujące zasoby platformy Azure są automatycznie dodawane do obszaru roboczego, jeśli są dostępne w regionie: [rejestr kontenerów](https://azure.microsoft.com/services/container-registry/), [magazyn](https://azure.microsoft.com/services/storage/), [usługa Application Insights](https://azure.microsoft.com/services/application-insights/) i [magazyn kluczy](https://azure.microsoft.com/services/key-vault/).

Utworzone zasoby mogą być używane jako wstępnie wymagane składniki w innych samouczkach usługi Azure Machine Learning i artykułach z instrukcjami. Podobnie jak dla innych usług platformy Azure, w przypadku usługi Azure Machine Learning obowiązują limity dotyczące niektórych zasobów (na przykład rozmiar klastra usługi BatchAI). Przeczytaj [ten](how-to-manage-quotas.md) artykuł, aby poznać domyślne limity i sposoby żądania ich zwiększenia.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Na stronie obszaru roboczego kliknij pozycję `Explore your Azure Machine Learning service workspace`

 ![eksplorowanie obszaru roboczego](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>Korzystanie z obszaru roboczego

Teraz zobaczysz, jak obszar roboczy ułatwia zarządzanie skryptami uczenia maszynowego. W tej sekcji omówiono następujące zagadnienia:

* Otwieranie notesu w usłudze Azure Notebooks.
* Uruchamianie kodu, który tworzy niektóre rejestrowane wartości.
* Wyświetlanie zarejestrowanych wartości w obszarze roboczym.

Jest to przykład przedstawiający, jak obszar roboczy może pomóc w śledzeniu informacji wygenerowanych przez skrypt. 

### <a name="open-a-notebook"></a>Otwieranie notesu 

Usługa Azure Notebooks udostępnia bezpłatną platformę w chmurze do przechowywania notesów Jupyter. Jest ona wstępnie skonfigurowana do obsługi wszystkich elementów potrzebnych, aby uruchomić usługę Azure Machine Learning.  

Kliknij przycisk `Open Azure Notebooks`, aby spróbować wykonać pierwszy eksperyment.

 ![Uruchamianie notesu platformy Azure](./media/quickstart-get-started/explore_ws.png)

Po zalogowaniu zostanie otwarta nowa karta z wyświetlonym monitem `Clone Library`.  Kliknij pozycję `Clone`.


### <a name="run-the-notebook"></a>Uruchamianie notesu

Oprócz dwóch notesów zobaczysz jeszcze plik `config.json`.  Ten plik konfiguracji zawiera informacje o utworzonym właśnie obszarze roboczym.  

Kliknij pozycję `01.run-experiment.ipynb`, aby otworzyć notes.

Komórki można uruchamiać pojedynczo, naciskając klawisze `Shift`+`Enter`.  Można też uruchomić cały notes, wybierając z menu pozycje `Cells` > `Run All`.  Wyświetlenie znaku [*] obok komórki oznacza uruchomienie.  Po zakończeniu działania kodu dla tej komórki pojawia się cyfra.

Może zostać wyświetlony monit o zalogowanie się.  Skopiuj kod z komunikatu, a następnie kliknij link i wklej kod w nowym oknie.  Pamiętaj, aby nie kopiować spacji znajdującej się przed ani za kodem.  Zaloguj się przy użyciu konta użytego w witrynie Azure Portal.

 ![logowanie](./media/quickstart-get-started/login.png)

W notesie kod w drugim wierszu odczytuje dane z pliku `config.json` w celu nawiązania połączenia z obszarem roboczym.
```
ws = Workspace.from_config()
```

Trzecia komórka kodu uruchamia eksperyment o nazwie „my-first-experiment”.  Ta nazwa będzie używana do wyszukiwania informacji o przebiegu w obszarze roboczym.

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

W ostatniej komórce notesu znajdują się wartości zapisywane w pliku dziennika.

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

Te wartości można wyświetlić w obszarze roboczym po zakończeniu działania kodu.

## <a name="view-logged-values"></a>Wyświetlanie zarejestrowanych wartości

Po zakończeniu działania wszystkich komórek notesu wróć do strony portalu.  

Kliknij przycisk `View Experiments`.

![wyświetlanie eksperymentów](./media/quickstart-get-started/view_exp.png)

Zamknij wyskakujące okienko `Reports`.

Kliknij pozycję `my-first-experiment`.

Przejrzyj informacje o wykonanym właśnie przebiegu.  Przewiń stronę w dół, znajdź tabelę przebiegów i kliknij link w postaci numeru przebiegu.

 ![link historii przebiegu](./media/quickstart-get-started/report.png)

Zostaną wyświetlone wykresy utworzone automatycznie na podstawie zarejestrowanych wartości:

   ![wyświetlanie historii](./media/quickstart-get-started/plots.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Jeśli chcesz, możesz także usunąć pojedynczy obszar roboczy, zachowując grupę zasobów. W tym celu wyświetl właściwości obszaru roboczego i wybierz przycisk Usuń.

## <a name="next-steps"></a>Następne kroki

Utworzono zasoby niezbędne do rozpoczęcia eksperymentowania z modelami i ich wdrażania. Uruchomiono także fragmenty kodu w notesie i zbadano historię uruchamiania dotyczącą tego kodu w obszarze roboczym w chmurze.

Aby poznać szczegółowo środowisko przepływu pracy, wykonaj czynności opisane w samouczku dotyczącym szkolenia i wdrażania modelu w usłudze Azure Machine Learning.  

> [!div class="nextstepaction"]
> [Samouczek: szkolenie modelu klasyfikacji obrazów](tutorial-train-models-with-aml.md)
