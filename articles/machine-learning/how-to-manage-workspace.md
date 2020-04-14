---
title: Tworzenie obszarów roboczych usługi Azure Machine Learning w portalu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć, wyświetlać i usuwać obszary robocze usługi Azure Machine Learning w witrynie Azure portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 4b4b64bcca57e1dc98cdba10626597532ae1461c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269736"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Tworzenie obszarów roboczych usługi Azure Machine Learning i zarządzanie nimi w witrynie Azure portal
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule utworzysz, wyświetlisz i usuniesz [**obszary robocze usługi Azure Machine Learning**](concept-workspace.md) w witrynie Azure portal dla usługi Azure Machine [Learning.](overview-what-is-azure-ml.md)  Portal jest najprostszym sposobem, aby rozpocząć pracę z obszarami roboczymi, ale w miarę zmiany potrzeb lub zwiększenia wymagań dotyczących automatyzacji można również tworzyć i usuwać obszary robocze [za pomocą interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md), z [kodem Pythona](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) lub [rozszerzeniem kodu VS.](tutorial-setup-vscode-extension.md)

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Aby utworzyć obszar roboczy, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu poświadczeń dla subskrypcji platformy Azure. 

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.

      ![Tworzenie nowego zasobu](./media/how-to-manage-workspace/create-workspace.gif)

1. Użyj paska wyszukiwania, aby znaleźć **uczenie maszynowe**.

1. Wybierz **opcję Uczenie maszynowe**.

1. W okienku **Uczenie maszynowe** wybierz pozycję **Utwórz,** aby rozpocząć.

1. Podaj następujące informacje, aby skonfigurować nowy obszar roboczy:

   Pole|Opis 
   ---|---
   Nazwa obszaru roboczego |Wprowadź unikatową nazwę identyfikującą obszar roboczy. W tym przykładzie używamy **docs-ws**. Nazwy muszą być unikatowe w całej grupie zasobów. Użyj nazwy, która jest łatwa do przywołania i odróżnienia od obszarów roboczych utworzonych przez inne osoby. Nazwa obszaru roboczego jest niewrażliwa na wielkości liter.
   Subskrypcja |Wybierz subskrypcję platformy Azure, której chcesz użyć.
   Grupa zasobów | Użyj grupy zasobów istniejącej w Twojej subskrypcji lub wprowadź nazwę, aby utworzyć nową grupę zasobów. Grupa zasobów przechowuje powiązane zasoby dla rozwiązania platformy Azure. W tym przykładzie używamy **docs-aml**. 
   Lokalizacja | Wybierz lokalizację najbliższą użytkownikom i zasoby danych, aby utworzyć obszar roboczy.
   Edycja obszaru roboczego | Wybierz **opcję Podstawowe** lub **Przedsiębiorstwo**.  Ta wersja obszaru roboczego określa funkcje, do których będziesz mieć dostęp i ceny. Dowiedz się więcej o [ofertach wersji Basic i Enterprise.](overview-what-is-azure-ml.md#sku) 

    ![Konfigurowanie obszaru roboczego](./media/how-to-manage-workspace/select-edition.png)

1. Po zakończeniu konfigurowania obszaru roboczego wybierz pozycję **Recenzja + Utwórz**.
2. Przejrzyj ustawienia i wkonuj dodatkowe zmiany lub poprawki. Po spełnieniu ustawień wybierz pozycję **Utwórz**.

   > [!Warning] 
   > Utworzenie obszaru roboczego w chmurze może potrwać kilka minut.

   Po zakończeniu procesu zostanie wyświetlony komunikat o powodzenie wdrożenia. 
 
 1. Aby wyświetlić nowy obszar roboczy, wybierz pozycję **Przejdź do zasobu**.

### <a name="download-a-configuration-file"></a>Pobieranie pliku konfiguracyjnego

1. Jeśli zostaniesz utworzyć [wystąpienie obliczeniowe,](tutorial-1st-experiment-sdk-setup.md#azure)pomiń ten krok.

1. Jeśli planujesz używać kodu w środowisku lokalnym, który odwołuje się do tego obszaru roboczego, wybierz **pobierz plik config.json** z sekcji **Przegląd** obszaru roboczego.  

   ![Pobierz config.json](./media/how-to-manage-workspace/configure.png)
   
   Umieść plik w strukturze katalogów za pomocą skryptów Pythona lub notesów Jupyter. Może znajdować się w tym samym katalogu, podkatalogu o nazwie *.azureml*lub w katalogu nadrzędnym. Podczas tworzenia wystąpienia obliczeniowego, ten plik jest dodawany do właściwego katalogu na maszynie wirtualnej dla Ciebie.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Uaktualnienie do wersji Enterprise

Możesz uaktualnić obszar roboczy z wersji Basic do Enterprise, aby korzystać z ulepszonych funkcji, takich jak funkcje niskiego kodu i ulepszone funkcje zabezpieczeń.

1. Zaloguj się do [studia usługi Azure Machine Learning](https://ml.azure.com).

1. Wybierz obszar roboczy, który chcesz uaktualnić.

1. Wybierz **pozycję Dowiedz się więcej** w prawym górnym rogu strony.

   [![Uaktualnianie obszaru](./media/how-to-manage-workspace/upgrade.png) roboczego](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Wybierz **pozycję Uaktualnij** w wyświetlonym oknie.


> [!IMPORTANT]
> Nie można obniżyć obszaru roboczego wersji Enterprise do obszaru roboczego wersji podstawowej. 

## <a name="find-a-workspace"></a><a name="view"></a>Znajdowanie obszaru roboczego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W górnym polu wyszukiwania wpisz machine **learning**.  

1. Wybierz **opcję Uczenie maszynowe**.

   ![Wyszukiwanie obszaru roboczego usługi Azure Machine Learning](./media/how-to-manage-workspace/find-workspaces.png)

1. Przejrzyj listę znalezionych obszarów roboczych. Można filtrować na podstawie subskrypcji, grup zasobów i lokalizacji.  

1. Wybierz obszar roboczy, aby wyświetlić jego właściwości.

## <a name="delete-a-workspace"></a>Usuwanie obszaru roboczego

W [witrynie Azure portal](https://portal.azure.com/)wybierz pozycję **Usuń** u góry obszaru roboczego, który chcesz usunąć.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Usuń obszar roboczy":::

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="resource-provider-errors"></a>Błędy dostawcy zasobów

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Przenoszenie obszaru roboczego

> [!WARNING]
> Przenoszenie obszaru roboczego usługi Azure Machine Learning do innej subskrypcji lub przenoszenie subskrypcji właścicielskiej do nowej dzierżawy nie jest obsługiwane. Może to spowodować błędy.

### <a name="deleting-the-azure-container-registry"></a>Usuwanie rejestru kontenerów platformy Azure

Obszar roboczy usługi Azure Machine Learning używa usługi Azure Container Registry (ACR) dla niektórych operacji. Automatycznie utworzy wystąpienie ACR, gdy po raz pierwszy będzie potrzebne.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z samouczkiem pełnej długości, aby dowiedzieć się, jak używać obszaru roboczego do tworzenia, uczenia i wdrażania modeli za pomocą usługi Azure Machine Learning.

> [!div class="nextstepaction"]
> [Samouczek: Modele pociągów](tutorial-train-models-with-aml.md)
