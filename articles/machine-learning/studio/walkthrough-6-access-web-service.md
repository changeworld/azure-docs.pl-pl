---
title: 'Krok 6: Dostęp do usługi sieci web - Azure Machine Learning Studio | Dokumentacja firmy Microsoft'
description: 'Krok 6 programowanie przewodnik rozwiązania do analizy predykcyjnej: Dostęp do usługi sieci web usługi active Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: b84d3be06d1d560776a3fcc86668e20bf58d3b0b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467428"
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-studio-web-service"></a>Przewodnik, krok 6 Dostęp do usługi sieci web Azure Machine Learning Studio

Jest to ostatni krok tego przewodnika, [tworzenia rozwiązania analizy predykcyjnej w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Tworzenie obszaru roboczego usługi Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Przekazywanie istniejących danych](walkthrough-2-upload-data.md)
3. [Tworzenie nowego eksperymentu](walkthrough-3-create-new-experiment.md)
4. [Nauczanie i ocena modeli](walkthrough-4-train-and-evaluate-models.md)
5. [Wdrażanie usługi sieci Web](walkthrough-5-publish-web-service.md)
6. **Dostęp do usługi sieci Web**

- - -
W poprzednim kroku, w tym przewodniku wdrożyliśmy usługę internetową, która używa nasz model prognozowania ryzyko kredytowe. Teraz użytkownicy będą mogli wysyłać dane i otrzymywania wyników. 

Usługi sieci Web to usługa sieci web platformy Azure, która może odbierać i zwracać dane przy użyciu interfejsów API REST w jeden z dwóch sposobów:  

* **Żądanie/odpowiedź** — użytkownik wysyła co najmniej jeden wiersz danych środków do usługi przy użyciu protokołu HTTP, a usługa odpowiada za pomocą co najmniej jeden zestaw wyników.
* **Wykonywanie wsadowe** — użytkownik przechowuje jeden lub więcej wierszy danych środków na platformie Azure, obiektów blob, a następnie wysyła lokalizacji obiektu blob do usługi. Usługa ocenia wszystkie wiersze danych w danych wejściowych obiektów blob, wyniki są zapisywane w inny obiekt blob i zwraca adres URL tego kontenera.  

Jest to najszybszy i najłatwiejszy sposób dostęp do klasycznej usługi sieci web, za pośrednictwem [usługi Azure ML odpowiedź na żądanie usługi Web App](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) lub [szablonu aplikacji sieci Web Azure ML partii wykonywania usługi](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Te szablony aplikacji sieci web można tworzyć niestandardową aplikację internetową znający usługę sieci web dane wejściowe i co zwróci. To wszystko, co należy zrobić, zapewniają dostęp do danych i usługi sieci web, a szablon zajmie się resztą.

Aby uzyskać więcej informacji na temat korzystania z szablonów aplikacji internetowych, zobacz [korzystanie z usługi Azure Machine Learning w sieci Web za pomocą szablonu aplikacji sieci web](consume-web-service-with-web-app-template.md).

Możesz również tworzyć niestandardowe aplikacje do uzyskania dostępu do usługi sieci web przy użyciu kodu startowego dostarczany w języku R, C#, i języki programowania w języku Python.

Można znaleźć szczegółowe informacje w [jak korzystanie z usługi Azure Machine Learning w sieci Web](consume-web-services.md).

