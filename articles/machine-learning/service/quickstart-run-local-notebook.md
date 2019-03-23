---
title: Uruchamianie notesu na własnym serwerze notesu Szybki Start
titleSuffix: Azure Machine Learning service
description: Rozpocznij pracę z usługą Azure Machine Learning. Możesz wypróbować obszaru roboczego, należy użyć własnego serwera lokalnego notesu.  Twój obszar roboczy jest podstawowe bloku w chmurze, którego używasz do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego.
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 8bb601ac5c1ed8e9ca6d2027fe1b774a938e1f0a
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58362411"
---
# <a name="quickstart-use-your-own-notebook-server-to-get-started-with-azure-machine-learning"></a>Szybki start: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu serwera notesu

W tym artykule używasz do wykonywania kodu, który jest zalogowany serwer notesu [obszaru roboczego usługi Azure Machine Learning](concept-azure-machine-learning-architecture.md). Ten obszar roboczy to podstawowy blok w chmurze umożliwiający eksperymentowanie z modelami uczenia maszynowego, ich trenowanie oraz wdrażanie za pomocą usługi Machine Learning.

Ten przewodnik Szybki Start korzysta z własnego środowiska Python i serwer notesu Jupyter. W przewodniku Szybki Start z żadnej instalacji, zobacz [Szybki Start: Rozpoczynanie pracy z usługą Azure Machine Learning przy użyciu serwer opartych na chmurze notesu](quickstart-run-cloud-notebook.md) 

Zobacz wersję wideo tego przewodnika Szybki start:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

W ramach tego przewodnika Szybki start wykonasz następujące czynności:

* Pisanie kodu, który rejestruje wartości w obszarze roboczym.
* Wyświetlanie zarejestrowanych wartości w obszarze roboczym.

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

## <a name="prerequisites"></a>Wymagania wstępne

* Serwer notesu Python 3.6 przy użyciu usługi Azure Machine Learning zainstalowany zestaw SDK
* Obszar roboczy usługi Azure Machine Learning
* Plik konfiguracji obszaru roboczego (**aml_config/config.json** ).

Pobierz wszystkie te wstępnie wymagane składniki z [Utwórz obszar roboczy usługi Azure Machine Learning](setup-create-workspace.md#portal).


## <a name="use-the-workspace"></a>Korzystanie z obszaru roboczego

Utwórz skrypt lub uruchomić Notes w tym samym katalogu co plik konfiguracji obszaru roboczego. Uruchom ten kod, który używa podstawowych interfejsów API zestawu SDK do śledzenia przebiegów eksperymentu.

1. Utwórz eksperyment w obszarze roboczym.
1. Zarejestruj pojedynczą wartość w eksperymencie.
1. Zarejestruj listę wartości w eksperymencie.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Wyświetlanie zarejestrowanych wyników

Po zakończeniu przebiegu możesz wyświetlić przebieg eksperymentu w witrynie Azure Portal. Aby wyświetlić adres URL prowadzący do wyników dla ostatniego przebiegu, użyj następującego kodu:

```python
print(run.get_portal_url())
```

Użyj linku, aby wyświetlić zarejestrowane wartości w witrynie Azure Portal w przeglądarce.

![Zarejestrowane wartości w witrynie Azure Portal](./media/quickstart-run-local-notebook/logged-values.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

>[!IMPORTANT]
>Utworzonych tutaj zasobów możesz użyć jako wstępnie wymaganych składników w innych samouczkach usługi Machine Learning i artykułach z instrukcjami.

Jeśli nie planujesz korzystać z zasobów, które zostały utworzone w tym artykule, usuń je, aby uniknąć ponoszenia opłat.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono zasoby niezbędne do eksperymentowania i wdrażania modeli. Uruchomiono kod w notesie i zbadano historię uruchamiania tego kodu w obszarze roboczym w chmurze.

> [!div class="nextstepaction"]
> [Samouczek: trenowanie modelu klasyfikacji obrazów](tutorial-train-models-with-aml.md)

Możesz też zapoznać się z [bardziej zaawansowane przykłady w witrynie GitHub](https://aka.ms/aml-notebooks) lub wyświetlić [Podręcznik użytkownika zestawu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
