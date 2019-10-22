---
title: Tworzenie obszarów roboczych usługi Azure ML w portalu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć, wyświetlać i usuwać obszary robocze Azure Machine Learning w Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 511c737e160c0f0753e570314c9b29346972cb04
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71269264"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Tworzenie Azure Machine Learning obszarów roboczych i zarządzanie nimi w Azure Portal

W tym artykule opisano tworzenie, wyświetlanie i usuwanie [**Azure Machine Learning obszarów roboczych**](concept-workspace.md) w Azure Portal dla [Azure Machine Learning](overview-what-is-azure-ml.md).  Portal jest najprostszym sposobem na rozpoczęcie pracy z obszarami roboczymi, ale w zależności od zmian lub wymagań dotyczących usługi Automation można także tworzyć i usuwać obszary robocze [przy użyciu interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md), [z kodem języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) lub [za pośrednictwem rozszerzenia vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Do utworzenia obszaru roboczego potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>Pobierz plik konfiguracji

1. Jeśli zostanie utworzona [maszyna wirtualna notesu](tutorial-1st-experiment-sdk-setup.md#azure), Pomiń ten krok.

1. Jeśli planujesz używać kodu w środowisku lokalnym, który odwołuje się do tego obszaru roboczego, wybierz pozycję **Pobierz plik config. JSON** z sekcji **Przegląd** w obszarze roboczym.  

   ![Pobierz plik config. JSON](./media/how-to-manage-workspace/configure.png)
   
   Umieść plik w strukturze katalogów za pomocą skryptów języka Python lub notesów Jupyter. Może znajdować się w tym samym katalogu, podkatalogu o nazwie *. Azure*lub w katalogu nadrzędnym. Podczas tworzenia maszyny wirtualnej notesu ten plik jest dodawany do poprawnego katalogu na maszynie wirtualnej.


## <a name="view"></a>Wyświetlanie obszaru roboczego

1. W lewym górnym rogu portalu wybierz pozycję **wszystkie usługi**.

1. W polu Filtr **wszystkich usług** wpisz **Usługa Machine Learning**.  

1. Wybierz **obszary robocze usługi Machine Learning**.

   ![Wyszukaj Azure Machine Learning obszar roboczy](media/how-to-manage-workspace/all-services.png)

1. Przejrzyj listę znalezionych obszarów roboczych. Można filtrować na podstawie subskrypcji, grup zasobów i lokalizacji.  

1. Wybierz obszar roboczy, aby wyświetlić jego właściwości.
   ![Workspace właściwości ](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Usuwanie obszaru roboczego

Użyj przycisku Usuń w górnej części obszaru roboczego, który chcesz usunąć.

  ![Przycisk Usuń](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z samouczkiem o pełnej długości, aby dowiedzieć się, jak używać obszaru roboczego do kompilowania, uczenia i wdrażania modeli przy użyciu Azure Machine Learning.

> [!div class="nextstepaction"]
> [Samouczek: uczenie modeli](tutorial-train-models-with-aml.md)
