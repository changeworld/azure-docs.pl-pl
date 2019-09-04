---
title: Tworzenie obszarów roboczych i zarządzanie nimi
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
ms.openlocfilehash: f103bde99eb5b21d06988f11ac6f17fcf50ceb31
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278273"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Tworzenie i zarządzanie obszarami roboczymi usługi Azure Machine Learning

W tym artykule będzie tworzenie, wyświetlanie i usuwanie [ **obszary robocze usługi Azure Machine Learning** ](concept-workspace.md) w witrynie Azure portal, aby uzyskać [usługi Azure Machine Learning](overview-what-is-azure-ml.md).  Portal jest najprostszym sposobem na rozpoczęcie pracy z obszarami roboczymi, ale w zależności od zmian lub wymagań dotyczących usługi Automation można także tworzyć i usuwać obszary robocze [przy użyciu interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md), [z kodem języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) lub [za pośrednictwem rozszerzenia vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Aby utworzyć obszar roboczy, musisz mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>Pobierz plik konfiguracji

1. Jeśli zostanie utworzona [maszyna wirtualna notesu](tutorial-1st-experiment-sdk-setup.md#azure), Pomiń ten krok.

1. Jeśli planujesz używać kodu w środowisku lokalnym, który odwołuje się do tego obszaru roboczego, wybierz pozycję **Pobierz plik config. JSON** z sekcji **Przegląd** w obszarze roboczym.  

   ![Pobierz plik config. JSON](./media/how-to-manage-workspace/configure.png)
   
   Umieść plik w strukturze katalogów za pomocą skryptów języka Python lub notesów Jupyter. Może znajdować się w tym samym katalogu, podkatalogu o nazwie *. Azure*lub w katalogu nadrzędnym. Podczas tworzenia maszyny wirtualnej notesu ten plik jest dodawany do poprawnego katalogu na maszynie wirtualnej.


## <a name="view"></a>Wyświetlanie obszaru roboczego

1. W lewym górnym rogu portalu, wybierz **wszystkich usług**.

1. W polu Filtr **wszystkich usług** wpisz **Usługa Machine Learning**.  

1. Wybierz **obszary robocze usługi Machine Learning**.

   ![Wyszukaj obszar roboczy usługi Azure Machine Learning](media/how-to-manage-workspace/all-services.png)

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
> [Samouczek: Modele uczenia](tutorial-train-models-with-aml.md)
