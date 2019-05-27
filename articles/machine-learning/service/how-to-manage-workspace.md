---
title: Tworzenie i zarządzanie obszarami roboczymi
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak tworzyć, wyświetlać i usuwać obszary robocze usługi Azure Machine Learning, w witrynie Azure portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 7f0806a1d68cd2cede1ae51f0a50a8125c1e7c8b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "66016528"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Tworzenie i zarządzanie obszarami roboczymi usługi Azure Machine Learning

W tym artykule będzie tworzenie, wyświetlanie i usuwanie [ **obszary robocze usługi Azure Machine Learning** ](concept-workspace.md) w witrynie Azure portal, aby uzyskać [usługi Azure Machine Learning](overview-what-is-azure-ml.md).  Można również tworzyć i usuwać obszary robocze [przy użyciu interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md), [przy użyciu kodu w języku Python](https://aka.ms/aml-sdk) lub [za pośrednictwem rozszerzenia programu VS Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Aby utworzyć obszar roboczy, musisz mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>Wyświetlanie obszaru roboczego

1. W lewym górnym rogu portalu, wybierz **wszystkich usług**.

1. W **wszystkich usług** polu filtrowania, należy wpisać **usługi machine learning service**.  

1. Wybierz **obszary robocze usługi Machine Learning**.

   ![Wyszukaj w obszarze roboczym usługi Azure Machine Learning](media/how-to-manage-workspace/all-services.png)

1. Przejrzyj listę obszarów roboczych, można odnaleźć. Można filtrować na podstawie subskrypcji, grupy zasobów i lokalizacji.  

1. Wybierz obszar roboczy, aby wyświetlić jego właściwości.
   ![Właściwości obszaru roboczego](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Usuwanie obszaru roboczego

Przycisk Usuń w górnej części obszaru roboczego, który chcesz usunąć.

  ![Usuwanie przycisku](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z samouczka kompleksowo przedstawiono sposób tworzenia, uczenia i wdrażania modeli przy użyciu usługi Azure Machine Learning za pomocą obszaru roboczego.

> [!div class="nextstepaction"]
> [Samouczek: Szkolenie modeli](tutorial-train-models-with-aml.md)
