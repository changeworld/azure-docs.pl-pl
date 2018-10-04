---
title: Znane problemy i rozwiązywanie problemów dla usługi Azure Machine Learning
description: Pobierz listę znanych problemów, obejścia problemu i rozwiązywania problemów
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 02cee5a3e088c919ec94aee6f46ef6f428b9bb48
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249421"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Znane problemy i rozwiązywania problemów z usługi Azure Machine Learning
 
Ten artykuł ułatwia znajdowanie i poprawić błędy lub błędów napotkanych podczas korzystania z usługi Azure Machine Learning. 

## <a name="sdk-installation-issues"></a>Problemy z instalacją zestawu SDK

**Komunikat o błędzie: nie można odinstalować "PyYAML"** 

PyYAML jest projektem zainstalowanych distutils. W związku z tym firma Microsoft nie można dokładnie określić pliki, które należą do niej w przypadku częściowej dezinstalacji. Aby kontynuować instalację zestawu SDK podczas ignorowanie tego błędu, należy użyć:
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="image-building-failure"></a>Błąd tworzenia obrazu

Obraz tworzenia niepowodzenia podczas wdrażania usługi sieci web. Obejście polega na dodawanie "pynacl == 1.2.1" jako zależność pip Conda pliku konfiguracji obrazu.  

## <a name="pipelines"></a>Potoki
Wystąpi błąd podczas wywoływania PythonScriptStep wiele razy z rzędu bez wprowadzania zmian w skrypcie lub parametrów. Obejście polega na odbudować obiektu PipelineData.

## <a name="fpgas"></a>Układów FPGA
Nie można wdrażać modele na układów FPGA dopiero po przeprowadzeniu mają wymagane i zostało zatwierdzone dla limitu przydziału FPGA. Aby zażądać dostępu, wypełnij formularz żądania limitu przydziału: https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Usługa Databricks

Problemy z usługi Databricks i Azure Machine Learning.

1. Zalecenie klastra usługi Databricks:
   
   Tworzenie klastra usługi Azure Databricks jako v4.x za pomocą języka Python 3. Firma Microsoft zaleca klastra wysokiej współbieżności.
 
1. Po zainstalowaniu dodatkowych pakietów, błąd w usłudze Databricks instalacji zestawu SDK AML.

   Niektóre pakiety, takich jak `psutil upgrade libs`, mogą powodować konflikty. Aby uniknąć błędów instalacji, należy zainstalować pakiety zamrożenia wersji lib. Ten problem jest powiązane z usługi Databricks i nie dotyczą AML zestawu SDK. Przykład:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Czasami może być przydatne Jeśli podasz informacje diagnostyczne podczas pytania o pomoc. Oto, miejsca zamieszkania pliki dziennika:

## <a name="resource-quotas"></a>Limity przydziałów zasobów

Dowiedz się więcej o [limity przydziałów zasobów](how-to-manage-quotas.md) można napotkać podczas pracy z usługą Azure Machine Learning.

## <a name="get-more-support"></a>Uzyskaj więcej obsługę

Możesz przesyłać żądania pomocy technicznej i poproś pomoc techniczną, fora i inne. [Dowiedz się więcej...](support-for-aml-services.md)
