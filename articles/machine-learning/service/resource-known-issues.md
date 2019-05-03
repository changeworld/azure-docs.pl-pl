---
title: Znane problemy i rozwiązywanie problemów
titleSuffix: Azure Machine Learning service
description: Pobierz listę znanych problemów, obejścia problemu i rozwiązywanie problemów dotyczących usługi Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/29/2019
ms.custom: seodec18
ms.openlocfilehash: 242b7cb45e9a73e78ae9f9b62e83311e110e07ee
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021106"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Znane problemy i rozwiązywania problemów z usługi Azure Machine Learning

Ten artykuł ułatwia znajdowanie i poprawić błędy lub błędów napotkanych podczas korzystania z usługi Azure Machine Learning.

## <a name="visual-interface-issues"></a>Kwestie dotyczące interfejsu Visual

Interfejs graficzny dla problemów z usługami uczenia maszynowego.

### <a name="long-compute-preparation-time"></a>Długi czas przygotowywania obliczeń.

Utwórz nowe obliczenie lub być wywoływany przez opuścić obliczeń wymaga czasu, może być kilka minut, a nawet dłużej. Zespół pracuje się do optymalizacji.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Nie można przebieg eksperymentu zawiera tylko zestaw danych 

Możesz chcieć uruchomić eksperyment zawiera tylko zestaw danych w celu wizualizacji zestawu danych. Jednak nie ma powinny być uruchamiane już dziś eksperymentu zestaw danych zawiera tylko. Czy firma Microsoft aktywnie naprawienie tego problemu.
 
Przed poprawki może nawiązać zestawu danych każdy moduł przekształcania danych (Select Columns in Dataset, edytować metadane itp podziału danych) i uruchomienie eksperymentu. Następnie możesz utworzyć wizualizację zestawu danych. 

Poniżej ilustracji przedstawiono, jak: ![visulize danych](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problemy z instalacją zestawu SDK

**Komunikat o błędzie: Nie można odinstalować "PyYAML"**

Środowisko Azure Machine Learning zestawu SDK dla języka Python: PyYAML jest projektem zainstalowanych distutils. W związku z tym firma Microsoft nie można dokładnie określić pliki, które należą do niej w przypadku częściowej dezinstalacji. Aby kontynuować instalację zestawu SDK podczas ignorowanie tego błędu, należy użyć:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemy z utworzeniem obliczeniowego usługi Azure Machine Learning

Brak rzadkich prawdopodobieństwo, że niektórych użytkowników, którzy utworzyli swój obszar roboczy usługi Azure Machine Learning w witrynie Azure portal przed wydaniem Ogólnodostępnej mogą nie można utworzyć obliczeniowego usługi Azure Machine Learning, w tym obszarze roboczym. Możesz zgłosić żądanie pomocy technicznej, korzystająca z usługi lub Utwórz nowy obszar roboczy za pośrednictwem portalu lub zestawu SDK, aby odblokować samodzielnie natychmiast.

## <a name="image-building-failure"></a>Błąd tworzenia obrazu

Obraz tworzenia niepowodzenia podczas wdrażania usługi sieci web. Obejście polega na dodawanie "pynacl == 1.2.1" jako zależność pip Conda pliku konfiguracji obrazu.

## <a name="deployment-failure"></a>Wdrożenie zakończyło się niepowodzeniem

Jeśli zauważysz `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, zmiana jednostki SKU dla maszyn wirtualnych używanych we wdrożeniu na taki, który ma więcej pamięci.

## <a name="fpgas"></a>Układy FPGA

Nie można wdrażać modele na układów FPGA dopiero po przeprowadzeniu mają wymagane i zostało zatwierdzone dla limitu przydziału FPGA. Aby zażądać dostępu, wypełnij formularz żądania limitu przydziału: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

Uczenie maszynowe zautomatyzowany przepływ tensor aktualnie nie obsługuje wersji przepływu tensor 1.13. Zainstalowanie tej wersji spowoduje, że zależności pakietów przestanie działać. Pracujemy nad rozwiązaniem tego problemu w przyszłej wersji. 


## <a name="databricks"></a>Databricks

Problemy z usługi Databricks i Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Błąd podczas instalowania pakietów

Azure Machine Learning SDK nie można zainstalować w usłudze Azure Databricks podczas instalowania dodatkowych pakietów. Niektóre pakiety, takich jak `psutil`, mogą powodować konflikty. Aby uniknąć błędów instalacji, należy zainstalować pakiety zamrażanie wersji biblioteki. Ten problem jest związany, Databricks, a nie zestaw SDK usługi Azure Machine Learning. Ten problem z innymi bibliotekami, może wystąpić zbyt. Przykład:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Alternatywnie można użyć skryptów init, jeśli możesz zachować problemów z instalacją biblioteki języka Python. To podejście nie jest oficjalnie obsługiwany. Aby uzyskać więcej informacji, zobacz [skrypty należące do klastra init](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Anuluj maszynie automatycznych uczenia przebiegu

Korzystając z automatycznych usługi machine learning możliwości w usłudze Azure Databricks, Anuluj uruchomienie i uruchom nowy eksperyment, uruchamianie, ponowne uruchomienie klastra usługi Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iteracji dla zautomatyzowanych machine learning

Automatyczne maszynie uczenia ustawienia, jeśli masz więcej niż 10 iteracji, ustaw `show_output` do `False` momentu przesłania przebiegu.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Element widget do uczenia maszynowego Azure Machine Learning SDK/automatyczne

Element widget zestawu SDK usługi Azure Machine Learning nie jest obsługiwane w notesu usługi Databricks, ponieważ notesów nie można przeanalizować elementy widget HTML. Widżet można wyświetlić w portalu przy użyciu kodu w języku Python w komórce notesu usługi Azure Databricks:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Błąd importowania: Nie modułu o nazwie "pandas.core.indexes"

Jeśli zostanie wyświetlony ten błąd zastosowania zautomatyzowany usługi machine learning:

1. Uruchom następujące polecenie, aby zainstalować dwa pakiety w klastrze usługi Azure Databricks: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Odłączyć, a następnie ponownie dołączyć klastra do Notatnika. 

Jeśli to nie rozwiąże problemu, spróbuj ponownie uruchomić klaster.

## <a name="azure-portal"></a>Azure Portal

Jeśli przejdziesz bezpośrednio, aby wyświetlić obszar roboczy z Udostępnij link z zestawu SDK lub w portalu, nie można wyświetlić strony z normalnej Przegląd informacji o subskrypcji w rozszerzeniu. Ponadto nie można przełączyć się do innego obszaru roboczego. Jeśli potrzebujesz wyświetlić inny obszar roboczy, obejście polega na przejść bezpośrednio do [witryny Azure portal](https://portal.azure.com) i wyszukaj nazwę obszaru roboczego.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Czasami może być przydatne Jeśli podasz informacje diagnostyczne podczas pytania o pomoc. Aby wyświetlić niektóre dzienniki, odwiedź stronę [witryny Azure portal](https://portal.azure.com) i przejdź do swojego obszaru roboczego i wybierz **obszar roboczy > eksperymentu > Uruchom > dzienniki**.

## <a name="resource-quotas"></a>Limity przydziałów zasobów

Dowiedz się więcej o [limity przydziałów zasobów](how-to-manage-quotas.md) można napotkać podczas pracy z usługą Azure Machine Learning.

## <a name="authentication-errors"></a>Błędy uwierzytelniania

Jeśli operacja zarządzania w celu obliczeń z zadania zdalne, zostanie wyświetlony jeden z następujących błędów:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Na przykład zostanie wyświetlony błąd, jeśli zostanie podjęta próba Utwórz lub Dołącz obliczeniowego elementu docelowego z potoku uczenia Maszynowego, który jest przesyłany w celu wykonania zdalnego.
