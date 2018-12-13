---
title: Znane problemy i rozwiązywanie problemów
titleSuffix: Azure Machine Learning service
description: Pobierz listę znanych problemów, obejścia problemu i rozwiązywanie problemów dotyczących usługi Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 45eb24bb8a49fb59ae44533e59b2760940ee5c1a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097709"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Znane problemy i rozwiązywania problemów z usługi Azure Machine Learning
 
Ten artykuł ułatwia znajdowanie i poprawić błędy lub błędów napotkanych podczas korzystania z usługi Azure Machine Learning. 

## <a name="sdk-installation-issues"></a>Problemy z instalacją zestawu SDK

**Komunikat o błędzie: nie można odinstalować "PyYAML"** 

Zestaw Azure maszyny Learning SDK dla języka Python: PyYAML jest projektem distutils zainstalowane. W związku z tym firma Microsoft nie można dokładnie określić pliki, które należą do niej w przypadku częściowej dezinstalacji. Aby kontynuować instalację zestawu SDK podczas ignorowanie tego błędu, należy użyć:
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemy z utworzeniem obliczeniowego usługi Azure Machine Learning
Brak rzadkich prawdopodobieństwo, że niektórych użytkowników, którzy utworzyli swój obszar roboczy usługi Azure Machine Learning w witrynie Azure portal przed wydaniem Ogólnodostępnej mogą nie można utworzyć obliczeniowego usługi Azure Machine Learning, w tym obszarze roboczym. Możesz zgłosić żądanie pomocy technicznej, korzystająca z usługi lub Utwórz nowy obszar roboczy za pośrednictwem portalu lub zestawu SDK, aby odblokować samodzielnie natychmiast. 

## <a name="image-building-failure"></a>Błąd tworzenia obrazu

Obraz tworzenia niepowodzenia podczas wdrażania usługi sieci web. Obejście polega na dodawanie "pynacl == 1.2.1" jako zależność pip Conda pliku konfiguracji obrazu.  

## <a name="fpgas"></a>Układów FPGA
Nie można wdrażać modele na układów FPGA dopiero po przeprowadzeniu mają wymagane i zostało zatwierdzone dla limitu przydziału FPGA. Aby zażądać dostępu, wypełnij formularz żądania limitu przydziału: https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Problemy z usługi Databricks i Azure Machine Learning.

1. Zalecenie klastra usługi Databricks:
   
   Tworzenie klastra usługi Azure Databricks jako v4.x za pomocą języka Python 3. Firma Microsoft zaleca klastra wysokiej współbieżności.
 
2. Po zainstalowaniu dodatkowych pakietów, błąd w usłudze Databricks instalacji zestawu SDK AML.

   Niektóre pakiety, takich jak `psutil`, mogą powodować konflikty. Aby uniknąć błędów instalacji, należy zainstalować pakiety zamrożenia wersji lib. Ten problem jest powiązane z usługi Databricks i nie dotyczą zestawu SDK usługi Azure ML — może ona twarzy zbyt z innymi bibliotekami. Przykład:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
   ```
   Alternatywnie można użyć skryptów init, jeśli możesz zachować problemów z instalacją przy użyciu bibliotek języka Python. To podejście nie jest oficjalnie obsługiwanych podejście. Możesz zapoznać się z [tego dokumentu](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

3. Korzystając z automatycznych uczenia maszynowego w usłudze Databricks, jeśli zostanie wyświetlony `Import error: numpy.core.multiarray failed to import`

   Obejście: importowanie biblioteki Python `numpy==1.14.5` do usługi Databricks za pomocą klastra Utwórz bibliotekę, aby [zainstalować i dołączyć](https://docs.databricks.com/user-guide/libraries.html#create-a-library).

## <a name="azure-portal"></a>Azure Portal
Jeśli przejdziesz bezpośrednio, aby wyświetlić obszar roboczy z Udostępnij link z zestawu SDK lub w portalu, nie można wyświetlić strony z normalnej Przegląd informacji o subskrypcji w rozszerzeniu. Ponadto nie można przełączyć się do innego obszaru roboczego. Jeśli zachodzi potrzeba wyświetlenia innego obszaru roboczego, obejście polega na przejść bezpośrednio do [witryny Azure portal](https://portal.azure.com) i wyszukaj nazwę obszaru roboczego.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Czasami może być przydatne Jeśli podasz informacje diagnostyczne podczas pytania o pomoc. Oto, miejsca zamieszkania pliki dziennika:

## <a name="resource-quotas"></a>Limity przydziałów zasobów

Dowiedz się więcej o [limity przydziałów zasobów](how-to-manage-quotas.md) można napotkać podczas pracy z usługą Azure Machine Learning.

## <a name="get-more-support"></a>Uzyskaj więcej obsługę

Możesz przesyłać żądania pomocy technicznej i poproś pomoc techniczną, fora i inne. [Dowiedz się więcej...](support-for-aml-services.md)
