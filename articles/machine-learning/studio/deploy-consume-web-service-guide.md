---
title: Wdrażanie i korzystanie
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio można użyć do wdrożenia, machine learning w przepływy pracy i modeli jako usług sieci web. Usługi sieci web można następnie wywołać maszyny uczenia modeli z aplikacji przez internet w celu wykonania prognoz w czasie rzeczywistym lub w trybie wsadowym.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 0a29d763ab54ee716e514df23576e9c3b294d792
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359663"
---
# <a name="azure-machine-learning-studio-web-services-deployment-and-consumption"></a>Usługi Azure Machine Learning Studio, usług sieci Web: Wdrażanie i korzystanie

Azure Machine Learning Studio można użyć do wdrożenia, machine learning w przepływy pracy i modeli jako usług sieci web. Usługi sieci web można następnie wywołać maszyny uczenia modeli z aplikacji przez Internet w celu wykonania prognoz w czasie rzeczywistym lub w trybie wsadowym. Ponieważ usługi sieci web są zgodne ze specyfikacją REST, można je wywoływać z różnych języków programowania i platform, takich jak .NET, Java i z aplikacji, takich jak program Excel.

W kolejnych sekcjach zawierają łącza do przewodników, kod i dokumentację ułatwiającą rozpoczęcie pracy.

## <a name="deploy-a-web-service"></a>Wdrażanie usługi internetowej

### <a name="with-azure-machine-learning-studio"></a>Za pomocą usługi Azure Machine Learning Studio

Portal programu Studio i portalu usług sieci Web Microsoft Azure Machine Learning ułatwiające wdrażanie i zarządzanie usługą sieci web bez konieczności pisania kodu.

Poniższe linki zawierają ogólne informacje o sposobie wdrażania nowej usługi sieci web:

* Aby uzyskać ogólne informacje o sposobie wdrażania nowej usługi sieci web, który jest oparty na usłudze Resource Manager, zobacz [Wdrażanie nowej usługi sieci web](publish-a-machine-learning-web-service.md).
* Aby uzyskać wskazówki dotyczące wdrażania usługi sieci web, zobacz [wdrażanie usługi sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Pełny przewodnik o tym, jak utworzyć i wdrożyć usługi sieci web, rozpoczynać się [samouczek 1: Prognozowanie ryzyka kredytowego](tutorial-part1-credit-risk.md).
* Aby uzyskać szczegółowe przykłady, które wdrażanie usługi sieci web zobacz:

  * [Samouczek 3. Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md)
  * [Jak wdrożyć usługę sieci web do wielu regionów](/azure/machine-learning/studio/publish-a-machine-learning-web-service#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Za pomocą dostawcy zasobów usługi sieci web API (interfejsów API usługi Azure Resource Manager)

Dostawca zasobów usługi Azure Machine Learning Studio, usług sieci web umożliwia wdrażanie i zarządzanie usługami sieci web przy użyciu wywołań interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Machine Learning w sieci Web usługi (REST)](/rest/api/machinelearning/index) odwołania.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Za pomocą poleceń cmdlet programu PowerShell

Dostawca zasobów usługi Azure Machine Learning Studio, usług sieci web umożliwia wdrażanie i zarządzanie usługami sieci web za pomocą poleceń cmdlet programu PowerShell.

Aby użyć polecenia cmdlet, należy najpierw zalogować do konta platformy Azure w środowisku programu PowerShell przy użyciu [Connect AzAccount](/powershell/module/az.accounts/connect-azaccount) polecenia cmdlet. Jeśli znasz sposób wywoływania poleceń programu PowerShell, które są oparte na Menedżera zasobów, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md).

Aby wyeksportować eksperymentu predykcyjnego, należy użyć [ten przykładowy kod](https://github.com/ritwik20/AzureML-WebServices). Po utworzeniu pliku .exe, od kodu, należy wpisać:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Uruchomiona jest aplikacja tworzy szablon JSON usługi sieci web. Aby użyć szablonu, aby wdrożyć usługę sieci web, należy dodać następujące informacje:

* Nazwa konta magazynu i klucza

    Możesz pobrać nazwę konta magazynu i klucza z [witryny Azure portal](https://portal.azure.com/).
* Identyfikator planu zobowiązania

    Możesz uzyskać identyfikator planu z [usług sieci Web Azure Machine Learning](https://services.azureml.net) portalu, rejestrując się i klikając nazwę planu.

Dodaj je do szablonu JSON jako elementy podrzędne *właściwości* węzła w tym samym poziomie co *MachineLearningWorkspace* węzła.

Oto przykład:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Zobacz następujące artykuły i przykładowy kod, aby uzyskać więcej informacji:

* [Polecenia cmdlet usługi Azure Machine Learning Studio](https://docs.microsoft.com/powershell/module/az.machinelearning) odwołania w witrynie MSDN
* Przykładowe [wskazówki](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) w witrynie GitHub

## <a name="consume-the-web-services"></a>Korzystanie z usług sieci web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Z usługi sieci Web Azure Machine Learning interfejsu użytkownika (testowanie)

Można testować usługi sieci web z poziomu portalu usług sieci Web Azure Machine Learning. W tym testowanie usługi odpowiedź na żądanie (RRS) i interfejsy usługę wykonywania wsadowego (BES).

* [Wdrażanie nowej usługi sieci Web](publish-a-machine-learning-web-service.md)
* [Wdrażanie usługi sieci Web Azure Machine Learning](publish-a-machine-learning-web-service.md)
* [Samouczek 3. Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Z poziomu programu Excel

Można pobrać szablonu programu Excel, który wykorzystuje usługę sieci web:

* [Korzystanie z usługi sieci web Azure Machine Learning z poziomu programu Excel](consuming-from-excel.md)
* [Dodatek programu Excel dla usług sieci Web Azure Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>W kliencie oparty na protokole REST

Usługi sieci Web Azure Machine Learning to interfejsy API REST. Mogą wykorzystywać te interfejsy API z różnych platform, takich jak .NET, Python, R, Java itp. **Zużywania** stronę usługi sieci web na [portalu usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net) zawiera przykładowy kod, który może pomóc Ci rozpocząć pracę. Aby uzyskać więcej informacji, zobacz [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Jak korzystać z usługi internetowej Azure Machine Learning).
