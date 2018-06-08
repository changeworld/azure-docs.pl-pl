---
title: 'Krok 6: Dostęp do usługi Machine Learning Web | Dokumentacja firmy Microsoft'
description: 'Krok 6 opracowanie wskazówki rozwiązanie predykcyjne: dostęp do usługi sieci Web Azure Machine Learning active.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: be63a04d0fe71039b19eb6bc0678a319f622a811
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836708"
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Przewodnik, krok 6. Dostęp do usługi sieci Web Azure Machine Learning

Jest to ostatni krok wskazówki, [tworzenie rozwiązania analizy predykcyjnej w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Tworzenie obszaru roboczego usługi Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Przekazywanie istniejących danych](walkthrough-2-upload-data.md)
3. [Tworzenie nowego eksperymentu](walkthrough-3-create-new-experiment.md)
4. [Nauczanie i ocena modeli](walkthrough-4-train-and-evaluate-models.md)
5. [Wdrażanie usługi sieci Web](walkthrough-5-publish-web-service.md)
6. **Dostęp do usługi sieci Web**

- - -
Usługi sieci web, która używa modelu prognozowania ryzyko kredytowe wdrożyliśmy w poprzednim kroku, w tym przewodniku. Teraz użytkownicy mogą wysyłać dane do niego i otrzymywania wyników. 

Usługa sieci Web jest usługą sieci web platformy Azure, która umożliwia odbieranie i zwrócić dane przy użyciu interfejsów API REST w jeden z dwóch sposobów:  

* **Żądanie/odpowiedź** — użytkownik wysyła co najmniej jeden wiersz danych środki do usługi przy użyciu protokołu HTTP, a usługa odpowiada z jednego lub więcej zestawów wyników.
* **Wykonywanie wsadowe** — użytkownik przechowuje jednego lub większej liczby wierszy danych środki na platformie Azure blob, a następnie wysyła lokalizacji obiektu blob do usługi. Usługa wyników wszystkich wierszy danych w blob danych wejściowych, przechowuje wyniki w innym obiekcie blob i zwraca adres URL tego kontenera.  

Jest najszybszym i Najprostszym sposobem uzyskania dostępu do usługi sieci web klasycznego za pośrednictwem [aplikacji sieci Web usługi Azure ML żądanie-odpowiedź](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) lub [szablonu aplikacji sieci Web Azure ML partii wykonywania usługi](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Te szablony aplikacji sieci web można utworzyć aplikacji sieci web niestandardowego, który zna usługi sieci web dane wejściowe i co zwróci. Wszystko, co należy zrobić to zapewniają dostęp do danych i usługi sieci web, a reszta szablonu.

Aby uzyskać więcej informacji na temat używania szablonów aplikacji sieci web, zobacz [korzystać z usługi Azure Machine Learning w sieci Web przy użyciu szablonu aplikacji sieci web](consume-web-service-with-web-app-template.md).

Można również zaprojektować aplikacji niestandardowej do uzyskania dostępu do usługi sieci web przy użyciu kodu starter dostarczany w R, C# i języki programowania Python.

Można znaleźć szczegółowe informacje w [jak korzystać z usługi sieci Web Azure Machine Learning](consume-web-services.md).

