---
title: What's new in Azure Machine Learning | Dokumentacja firmy Microsoft
description: Nowe funkcje, które są dostępne w usłudze Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 61bea7fde96b239a50ec25a702a73ecfb62ce717
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836076"
---
# <a name="whats-new-in-azure-machine-learning"></a>Co nowego w usłudze Azure Machine Learning

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>Wersja 2017 marca aktualizacje Microsoft Azure Machine Learning zapewnia następującej funkcji:



* Dedykowany pojemności dla usługi Azure Machine Learning BES zadań

    Maszyny przetwarzania używa puli partii Learning [partii zadań Azure](../../batch/batch-technical-overview.md) usługą skalowalnego zarządzany przez klienta dla usługi Azure Machine Learning partii wykonywania. Przetwarzanie wsadowe puli służy do tworzenia pul partii zadań Azure, na których przesyłania zadań wsadowych i je w sposób przewidywalne.

    Aby uzyskać więcej informacji, zobacz [usługi partia zadań Azure Machine Learning zadań](dedicated-capacity-for-bes-jobs.md).


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Wersja sierpnia 2016 Microsoft Azure Machine Learning aktualizacje zapewniają następujące funkcje:
* Teraz można zarządzać klasycznym usługi sieci Web w nowej [usługi sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/) portal, który zawiera w jednym miejscu, aby zarządzać wszystkimi aspektami usługi sieci Web.    
  * Zapewniające usługi sieci web [statystyk użycia](manage-new-webservice.md).
  * Upraszcza testowania wywołań Azure Machine Learning zdalnego żądania przy użyciu przykładowych danych.
  * Udostępnia nową stroną testową usługi wykonywania wsadowego z historią przesyłania przykładowych danych i zadania.
  * Umożliwia łatwiejsze zarządzanie punktu końcowego.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Wersja lipca 2016 aktualizacje Microsoft Azure Machine Learning zapewniają następujące funkcje:
* Usługi sieci Web są teraz zarządzane jako zasobów platformy Azure zarządzanych za pomocą [usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) interfejsów, co zapewnia następujące udoskonalenia:
  * Istnieją nowe [interfejsów API REST](https://msdn.microsoft.com/library/azure/Dn950030.aspx) wdrażania i zarządzania nimi Menedżera zasobów na podstawie usług sieci Web.
  * Jest dostępna nowa [usługi sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/) portal, który zawiera w jednym miejscu, aby zarządzać wszystkimi aspektami usługi sieci Web.
* Umowa zawiera nowe interfejsy API wykorzystaniu dostawcy zasobów Menedżera zasobów dla usług sieci Web na podstawie modelu wdrażania usług sieci web subskrypcji, w przypadku korzystania z Menedżera zasobów.
* Wprowadzono nowe [planów cenowych](https://azure.microsoft.com/pricing/details/machine-learning/) i planowanie możliwości zarządzania przy użyciu nowego planu odzyskiwania Menedżera zasobów dla rozliczeń.
  * Możesz teraz [wdrażanie usługi sieci web na wiele regionów](how-to-deploy-to-multiple-regions.md) bez konieczności tworzenia subskrypcji w każdym regionie.
* Usługa sieci web [statystyk użycia](manage-new-webservice.md).
* Upraszcza testowania wywołań Azure Machine Learning zdalnego żądania przy użyciu przykładowych danych.
* Udostępnia nową stroną testową usługi wykonywania wsadowego z historią przesyłania przykładowych danych i zadania.

Ponadto Machine Learning Studio została zaktualizowana, aby umożliwić Wdrażanie nowego modelu usług sieci Web, lub Kontynuuj wdrażanie klasycznego modelu usług sieci Web. 

