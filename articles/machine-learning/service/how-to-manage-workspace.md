---
title: Tworzenie i zarządzanie nimi obszarów roboczych uczenia maszynowego Azure
description: Dowiedz się, jak tworzyć, wyświetlać i usuwać obszarów roboczych uczenia maszynowego Azure w witrynie Azure portal.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.openlocfilehash: 7d01a2e3ebd46315966c82a43a17ffc5b329b829
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954351"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Tworzenie i zarządzanie nimi obszarów roboczych uczenia maszynowego Azure

W tym artykule będzie tworzenie, wyświetlanie i usuwanie [ **obszarów roboczych uczenia maszynowego Azure** ](concept-azure-machine-learning-architecture.md#workspace) w witrynie Azure portal, aby uzyskać [usługi Azure Machine Learning](overview-what-is-azure-ml.md).  Można również tworzyć i usuwać obszary robocze [przy użyciu interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) lub [przy użyciu kodu w języku Python](http://aka.ms/aml-sdk).

Aby utworzyć obszar roboczy, musisz mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Wyświetlanie obszaru roboczego

1. W lewym górnym rogu portalu, wybierz **wszystkich usług**. 

1. W **wszystkich usług** polu filtrowania, należy wpisać **obszaru roboczego usługi Machine Learning**.  

   ![Wyszukaj obszaru roboczego usługi Azure Machine Learning](media/how-to-manage-workspace/allservices-search1.png)

1. W wynikach filtrowania, wybierz **obszaru roboczego usługi Machine Learning** Aby wyświetlić listę swoich obszarów roboczych. 

   ![Wyszukaj obszaru roboczego usługi Azure Machine Learning](media/how-to-manage-workspace/allservices-search.PNG)

1. Przejrzyj listę obszarów roboczych, można odnaleźć. Można filtrować na podstawie subskrypcji, grupy zasobów i lokalizacji.  

   ![Lista obszaru roboczego usługi Azure Machine Learning](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. Wybierz obszar roboczy został utworzony, aby wyświetlić jego właściwości.

   ![PNG](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Usuwanie obszaru roboczego

Przycisk Usuń w górnej części obszaru roboczego, który chcesz usunąć.

  ![PNG](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z samouczka kompleksowo przedstawiono sposób tworzenia, uczenia i wdrażania modeli przy użyciu usługi Azure Machine Learning za pomocą obszaru roboczego.

> [!div class="nextstepaction"]
> [Samouczek: Trenowanie modeli](tutorial-train-models-with-aml.md)
