---
title: Tworzenie obszarów roboczych usługi Azure ML w portalu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć, wyświetlać i usuwać obszary robocze Azure Machine Learning w Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 1985c596b9f4b9b78b0055bfe1eab9888c30e201
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489765"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Tworzenie Azure Machine Learning obszarów roboczych i zarządzanie nimi w Azure Portal
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule opisano tworzenie, wyświetlanie i usuwanie [**Azure Machine Learning obszarów roboczych**](concept-workspace.md) w Azure Portal dla [Azure Machine Learning](overview-what-is-azure-ml.md).  Portal jest najprostszym sposobem na rozpoczęcie pracy z obszarami roboczymi, ale w zależności od zmian lub wymagań dotyczących usługi Automation można także tworzyć i usuwać obszary robocze [przy użyciu interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md), [z kodem języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) lub [za pośrednictwem rozszerzenia vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Do utworzenia obszaru roboczego potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń dla subskrypcji platformy Azure. 

1. W lewym górnym rogu Azure Portal wybierz pozycję **+ Utwórz zasób**.

      ![Tworzenie nowego zasobu](../../../includes/media/aml-create-in-portal/portal-create-resource.png)

1. Użyj paska wyszukiwania, aby znaleźć **obszar roboczy usługi Machine Learning**.

1. Wybierz **obszar roboczy usługi Machine Learning**.

1. W okienku **obszaru roboczego usługi Machine Learning** wybierz pozycję **Utwórz** , aby rozpocząć.

1. Podaj następujące informacje, aby skonfigurować nowy obszar roboczy:

   Pole|Opis 
   ---|---
   Nazwa obszaru roboczego |Wprowadź unikatową nazwę identyfikującą obszar roboczy. W tym przykładzie używamy **dokumentów-WS**. Nazwy muszą być unikatowe w ramach grupy zasobów. Użyj nazwy, która jest łatwa do odzyskania i odróżniania od obszarów roboczych utworzonych przez inne osoby.  
   Subskrypcja |Wybierz subskrypcję platformy Azure, której chcesz użyć.
   Grupa zasobów | Użyj istniejącej grupy zasobów w subskrypcji lub wprowadź nazwę, aby utworzyć nową grupę zasobów. Grupa zasobów zawiera powiązane zasoby dla rozwiązania platformy Azure. W tym przykładzie używane są **dokumenty-AML**. 
   Lokalizacja | Wybierz lokalizację znajdującą się najbliżej użytkowników i zasoby danych, aby utworzyć obszar roboczy.
   Wersja obszaru roboczego | Wybierz pozycję **podstawowa** lub **Enterprise**.  Ta wersja obszaru roboczego określa funkcje, do których będziesz mieć dostęp i Cennik. Dowiedz się więcej o [ofertach Basic i Enterprise Edition](overview-what-is-azure-ml.md#sku). 

    ![Konfigurowanie obszaru roboczego](media/how-to-manage-workspace/select-edition.png)

1. Po zakończeniu konfigurowania obszaru roboczego wybierz pozycję **Utwórz**. 

   > [!Warning] 
   > Tworzenie obszaru roboczego w chmurze może potrwać kilka minut.

   Po zakończeniu procesu zostanie wyświetlony komunikat o powodzeniu wdrożenia. 
 
 1. Aby wyświetlić nowy obszar roboczy, wybierz pozycję **Przejdź do zasobu**.

### <a name="download-a-configuration-file"></a>Pobierz plik konfiguracji

1. Jeśli zostanie utworzona [maszyna wirtualna notesu](tutorial-1st-experiment-sdk-setup.md#azure), Pomiń ten krok.

1. Jeśli planujesz używać kodu w środowisku lokalnym, który odwołuje się do tego obszaru roboczego, wybierz pozycję **Pobierz plik config. JSON** z sekcji **Przegląd** w obszarze roboczym.  

   ![Pobierz plik config. JSON](./media/how-to-manage-workspace/configure.png)
   
   Umieść plik w strukturze katalogów za pomocą skryptów języka Python lub notesów Jupyter. Może znajdować się w tym samym katalogu, podkatalogu o nazwie *. Azure*lub w katalogu nadrzędnym. Podczas tworzenia maszyny wirtualnej notesu ten plik jest dodawany do poprawnego katalogu na maszynie wirtualnej.

## <a name="upgrade"></a>Uaktualnianie do wersji Enterprise

Możesz uaktualnić obszar roboczy z wersji podstawowa do wersji Enterprise, aby korzystać z rozszerzonych funkcji, takich jak środowisko o małym kodzie i ulepszone funkcje zabezpieczeń.

1. Zaloguj się w [portalu Azure](https://portal.azure.com).

1. Wybierz obszar roboczy, który chcesz uaktualnić.

1. Wybierz pozycję **Uaktualnij** u góry lub w komunikacie uaktualnienia.

    ![Uaktualnianie obszaru roboczego](media/how-to-manage-workspace/upgrade.png)

1. Wybierz pozycję **Potwierdź aktualizację**.


> [!IMPORTANT]
> Nie można obniżyć poziomu obszaru roboczego Enterprise Edition do obszaru roboczego wersja podstawowa. 

## <a name="view"></a>Znajdowanie obszaru roboczego

1. W lewym górnym rogu portalu wybierz pozycję **wszystkie usługi**.

1. W polu Filtr **wszystkich usług** wpisz **Uczenie maszynowe**.  

1. Wybierz **Azure Machine Learning**.

   ![Wyszukaj Azure Machine Learning obszar roboczy](media/how-to-manage-workspace/all-services.png)

1. Przejrzyj listę znalezionych obszarów roboczych. Można filtrować na podstawie subskrypcji, grup zasobów i lokalizacji.  

1. Wybierz obszar roboczy, aby wyświetlić jego właściwości.
   ![](media/how-to-manage-workspace/allservices_view_workspace_full.PNG) właściwości obszaru roboczego

## <a name="delete-a-workspace"></a>Usuwanie obszaru roboczego

Użyj przycisku Usuń w górnej części obszaru roboczego, który chcesz usunąć.

  ![Przycisk Usuń](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z samouczkiem o pełnej długości, aby dowiedzieć się, jak używać obszaru roboczego do kompilowania, uczenia i wdrażania modeli przy użyciu Azure Machine Learning.

> [!div class="nextstepaction"]
> [Samouczek: uczenie modeli](tutorial-train-models-with-aml.md)
