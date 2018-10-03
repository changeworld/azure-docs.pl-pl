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
ms.openlocfilehash: d4910eb3dfacb46efe3f85aea3a441bdaaeb1392
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48236416"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Znane problemy i rozwiązywania problemów z usługi Azure Machine Learning
 
Ten artykuł ułatwia znajdowanie i poprawić błędy lub błędów napotkanych podczas korzystania z usługi Azure Machine Learning. 

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

## <a name="gather-diagnostics-information"></a>Zbierz informacje diagnostyczne
Czasami może być przydatne Jeśli podasz informacje diagnostyczne podczas pytania o pomoc. Oto, miejsca zamieszkania pliki dziennika:

## <a name="resource-quotas"></a>Limity przydziałów zasobów

Dowiedz się więcej o [limity przydziałów zasobów](how-to-manage-quotas.md) można napotkać podczas pracy z usługą Azure Machine Learning.

## <a name="get-more-support"></a>Uzyskaj więcej obsługę

Możesz przesyłać żądania pomocy technicznej i poproś pomoc techniczną, fora i inne. [Dowiedz się więcej...](support-for-aml-services.md)
