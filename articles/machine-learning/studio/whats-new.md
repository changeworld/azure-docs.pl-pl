---
title: What's new - Azure Machine Learning Studio | Dokumentacja firmy Microsoft
description: Nowe funkcje, które są dostępne w usłudze Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2018
ms.openlocfilehash: 90a9a81de488f2b3b256e3164c3bfde3019d0ad3
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53253021"
---
# <a name="whats-new-in-azure-machine-learning-studio"></a>What's New in Azure Machine Learning Studio?

## <a name="october-2018"></a>Październik 2018 r.

Aparat języka R w [wykonanie skryptu języka R](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script) modułu została dodana nowa wersja środowiska uruchomieniowego języka R — Microsoft R Open (MRO) 3.4.4. MRO 3.4.4 zależy od typu open-source CRAN R 3.4.4 i dlatego jest zgodny z pakietami, które współpracuje z danej wersji języka R.  Dowiedz się więcej o obsługiwane pakiety języka R w artykule "[pakiety języka R obsługiwanych przez usługi Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning#bkmk_List)".

## <a name="march-2017"></a>Marca 2017 r. 
Ta wersja aktualizacji Microsoft Azure Machine Learning oferuje następującej funkcji:

* Dedykowaną pojemność dla usługi Azure Machine Learning BES zadania

    Usługi Machine Learning Batch Pool przetwarzania używa [usługi Azure Batch](../../batch/batch-technical-overview.md) usługi w celu zapewnienia skalowania zarządzanych przez klienta usługa wykonywania wsadowego usługi Azure Machine Learning. Przetwarzanie puli usługi Batch pozwala tworzyć pule usługi Azure Batch, na których można przesłać zadania usługi batch oraz korzystać z ich wykonania w sposób przewidywalny.

    Aby uzyskać więcej informacji, zobacz [usługi Azure Batch dla zadań usługi Machine Learning](dedicated-capacity-for-bes-jobs.md).


## <a name="august-2016"></a>Sierpnia 2016 r. 
Ta wersja programu Microsoft Azure Machine Learning aktualizacje zapewniają następujące funkcje:
* Klasyczne usługi sieci Web można teraz zarządzać w nowym [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/) portalem zapewniającym w jednym miejscu, aby zarządzać wszystkimi aspektami usługi sieci Web.    
  * Który udostępnia usługę sieci web [statystyki użycia](manage-new-webservice.md).
  * Upraszcza testowanie wywołań usługi Azure Machine Learning zdalnego żądania przy użyciu przykładowych danych.
  * Zapewnia stronę testową usługę wykonywania wsadowego próbki historii przesyłania danych i zadań.
  * Umożliwia łatwiejsze zarządzanie w punkcie końcowym.

## <a name="july-2016"></a>Lipca 2016 r. 
Ta wersja programu Microsoft Azure Machine Learning aktualizacje zapewniają następujące funkcje:
* Usługi sieci Web są teraz zarządzane jako zasoby platformy Azure zarządzane za pośrednictwem [usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) interfejsów, co zapewnia następujące ulepszenia:
  * Istnieją nowe [interfejsów API REST](https://msdn.microsoft.com/library/azure/Dn950030.aspx) wdrażanie i zarządzanie nimi Menedżera zasobów na podstawie usług sieci Web.
  * Jest dostępny nowy [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/) portalem zapewniającym w jednym miejscu, aby zarządzać wszystkimi aspektami usługi sieci Web.
* Dołącza nowy model wdrażania usługi sieci web opartym na subskrypcji, w wielu regionach przy użyciu usługi Resource Manager, na podstawie interfejsów API, wykorzystując dostawcy zasobów usługi Resource Manager dla usług sieci Web.
* Wprowadzono nowe [planów cenowych](https://azure.microsoft.com/pricing/details/machine-learning/) i zaplanować możliwości zarządzania przy użyciu nowej jednostki Uzależnionej usługi Resource Manager dla rozliczeń.
  * Możesz teraz [wdrażanie usługi sieci web do wielu regionów](how-to-deploy-to-multiple-regions.md) bez konieczności tworzenia subskrypcji w każdym regionie.
* Udostępnia usługę sieci web [statystyki użycia](manage-new-webservice.md).
* Upraszcza testowanie wywołań usługi Azure Machine Learning zdalnego żądania przy użyciu przykładowych danych.
* Zapewnia stronę testową usługę wykonywania wsadowego próbki historii przesyłania danych i zadań.

Ponadto usługi Machine Learning Studio został zaktualizowany, aby umożliwić wdrażanie do nowego modelu usługi sieci Web lub w dalszym ciągu wdrażać klasycznego modelu usług sieci Web. 

