---
title: Tworzenie i zarządzanie obszarami roboczymi
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak tworzyć, wyświetlać i usuwać obszary robocze usługi Azure Machine Learning, w witrynie Azure portal.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 73a47929fa26ae0729943e17d0f6c9a054f4f6bb
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276710"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Tworzenie i zarządzanie obszarami roboczymi usługi Azure Machine Learning

W tym artykule będzie tworzenie, wyświetlanie i usuwanie [ **obszary robocze usługi Azure Machine Learning** ](concept-azure-machine-learning-architecture.md#workspace) w witrynie Azure portal, aby uzyskać [usługi Azure Machine Learning](overview-what-is-azure-ml.md).  Można również tworzyć i usuwać obszary robocze [przy użyciu interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md) lub [przy użyciu kodu w języku Python](https://aka.ms/aml-sdk).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego 

Aby utworzyć obszar roboczy, musisz mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem należy utworzyć bezpłatne konto. Spróbuj [bezpłatnej i płatnej wersji usługi Azure Machine Learning](http://aka.ms/AMLFree) już dziś.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Wyświetlanie obszaru roboczego

1. W lewym górnym rogu portalu, wybierz **wszystkich usług**. 

1. W **wszystkich usług** polu filtrowania, należy wpisać **obszaru roboczego usługi Machine Learning**.  

   ![Wyszukaj w obszarze roboczym usługi Azure Machine Learning](media/how-to-manage-workspace/allservices-search1.png)

1. W wynikach filtrowania, wybierz **obszaru roboczego usługi Machine Learning** Aby wyświetlić listę swoich obszarów roboczych. 

   ![Lista obszarów roboczych usługi Azure Machine Learning](media/how-to-manage-workspace/allservices-search.PNG)

1. Przejrzyj listę obszarów roboczych, można odnaleźć. Można filtrować na podstawie subskrypcji, grupy zasobów i lokalizacji.  

   ![Wyświetl obszary robocze](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. Wybierz obszar roboczy został utworzony, aby wyświetlić jego właściwości.

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
