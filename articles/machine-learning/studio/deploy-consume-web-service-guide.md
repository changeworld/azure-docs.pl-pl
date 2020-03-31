---
title: Wdrażanie i zużycie
titleSuffix: ML Studio (classic) - Azure
description: Za pomocą usługi Azure Machine Learning Studio (klasycznej) można wdrożyć przepływy pracy uczenia maszynowego i modele jako usługi sieci web. Te usługi sieci web mogą następnie służyć do wywoływania modeli uczenia maszynowego z aplikacji przez Internet do prognozowania w czasie rzeczywistym lub w trybie wsadowym.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: ff6ae0de0bbd8c47b81fa5066a97eb0b3e0cf6bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204396"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Usługi sieci Web usługi Azure Machine Learning Studio (klasyczne): wdrażanie i zużycie

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Za pomocą usługi Azure Machine Learning Studio (klasycznej) można wdrożyć przepływy pracy uczenia maszynowego i modele jako usługi sieci web. Te usługi sieci web mogą następnie służyć do wywoływania modeli uczenia maszynowego z aplikacji przez Internet do prognozowania w czasie rzeczywistym lub w trybie wsadowym. Ponieważ usługi sieci web są restful, można wywołać je z różnych języków programowania i platform, takich jak .NET i Java, i z aplikacji, takich jak Excel.

W następnych sekcjach znajdują się łącza do instruktajnów, kodu i dokumentacji ułatwiającej rozpoczęcie pracy.

## <a name="deploy-a-web-service"></a>Wdrażanie usługi internetowej

### <a name="with-azure-machine-learning-studio-classic"></a>Z usługą Azure Machine Learning Studio (klasyczna)

Portal Studio (klasyczny) i portal usług azure machine learning web services ułatwiają wdrażanie usługi sieci web i zarządzanie nią bez pisania kodu.

Poniższe łącza zawierają ogólne informacje dotyczące wdrażania nowej usługi sieci web:

* Aby uzyskać omówienie sposobu wdrażania nowej usługi sieci web opartej na usłudze Azure Resource Manager, zobacz [Wdrażanie nowej usługi sieci web.](deploy-a-machine-learning-web-service.md)
* Aby zapoznać się z instrukcją dotyczącą wdrażania usługi sieci web, zobacz [Wdrażanie usługi sieci Web usługi Azure Machine Learning.](deploy-a-machine-learning-web-service.md)
* Aby uzyskać pełny instruktaż dotyczący tworzenia i wdrażania usługi sieci web, zacznij od [samouczka 1: Przewidywanie ryzyka kredytowego](tutorial-part1-credit-risk.md).
* Aby uzyskać szczegółowe przykłady wdrażania usługi sieci web, zobacz:

  * [Samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md)
  * [Jak wdrożyć usługę sieci web w wielu regionach](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Z interfejsami API dostawcy zasobów usług sieci web (interfejsy API usługi Azure Resource Manager)

Dostawca zasobów usługi Azure Machine Learning Studio (klasyczny) dla usług sieci web umożliwia wdrażanie i zarządzanie usługami sieci web przy użyciu wywołań interfejsu API REST. Aby uzyskać więcej informacji, zobacz odwołanie do [usługi sieci Web uczenia maszynowego (REST).](/rest/api/machinelearning/index)

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Z poleceniami cmdlet programu PowerShell

Dostawca zasobów usługi Azure Machine Learning Studio (klasyczny) dla usług sieci web umożliwia wdrażanie i zarządzanie usługami sieci web przy użyciu poleceń cmdlet programu PowerShell.

Aby użyć poleceń cmdlet, należy najpierw zalogować się do konta platformy Azure z poziomu środowiska programu PowerShell przy użyciu polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Jeśli nie znasz sposobu wywoływania poleceń programu PowerShell opartych na Menedżerze zasobów, zobacz Korzystanie z [programu Azure PowerShell z usługą Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Aby wyeksportować eksperyment predykcyjny, użyj [tego przykładowego kodu](https://github.com/ritwik20/AzureML-WebServices). Po utworzeniu pliku exe z kodu można wpisać:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Uruchomienie aplikacji tworzy szablon JSON usługi sieci web. Aby użyć szablonu do wdrożenia usługi sieci web, należy dodać następujące informacje:

* Nazwa i klucz konta magazynu

    Nazwę i klucz konta magazynu można uzyskać z [witryny Azure portal](https://portal.azure.com/).
* Identyfikator planu zobowiązań

    Identyfikator planu można uzyskać z portalu [usług Azure Machine Learning Web Services,](https://services.azureml.net) logując się i klikając nazwę planu.

Dodaj je do szablonu JSON jako elementy *podrzędne* węzła Właściwości na tym samym poziomie co węzeł *MachineLearningWorkspace.*

Oto przykład:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Zobacz następujące artykuły i przykładowy kod, aby uzyskać dodatkowe informacje:

* Odwołanie do [poleceń cmdlet usługi Azure Machine Learning Studio (klasycznych)](https://docs.microsoft.com/powershell/module/az.machinelearning) w usłudze MSDN
* Przykładowy [przewodnik po](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) usłudze GitHub

## <a name="consume-the-web-services"></a>Korzystanie z usług internetowych

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Z interfejsu użytkownika usług azure machine learning web services (testowanie)

Usługę sieci web można przetestować w portalu usług Azure Machine Learning Web Services. Obejmuje to testowanie interfejsów usługi żądania-odpowiedzi (RRS) i usługi wykonywania wsadowego (BES).

* [Wdrażanie nowej usługi sieci Web](deploy-a-machine-learning-web-service.md)
* [Wdrażanie usługi sieci Web Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Z programu Excel

Można pobrać szablon programu Excel, który korzysta z usługi sieci Web:

* [Korzystanie z usługi sieci web usługi Azure Machine Learning z programu Excel](consuming-from-excel.md)
* [Dodatek programu Excel dla usług azure machine learning web services](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Od klienta opartego na rest

Usługi azure machine learning web są restful interfejsów API. Można korzystać z tych interfejsów API z różnych platform, takich jak .NET, Python, R, Java, itp. Strona **Korzystanie z** usługi sieci web w portalu usług azure machine learning web [zawiera](https://services.azureml.net) przykładowy kod, który może ułatwić rozpoczęcie pracy. Aby uzyskać więcej informacji, zobacz [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Jak korzystać z usługi internetowej Azure Machine Learning).
