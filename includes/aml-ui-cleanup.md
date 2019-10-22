---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/16/2019
ms.openlocfilehash: 0071b0df2c2e173eced1722372f88b1de2708afa
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692321"
---
>[!IMPORTANT]
>Możesz użyć zasobów utworzonych jako wymagania wstępne dla innych samouczków usług Azure Machine Learning i artykułów z artykułami.

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie planujesz korzystać z utworzonych elementów, Usuń całą grupę zasobów, aby nie naliczane były opłaty:

1. W Azure Portal wybierz pozycję **grupy zasobów** po lewej stronie okna.
 
   ![Usuwanie grupy zasobów w witrynie Azure Portal](./media/aml-ui-cleanup/delete-resources.png)

1. Z listy wybierz utworzoną grupę zasobów.

1. Po prawej stronie okna wybierz przycisk wielokropka ( **...** ).

1. Wybierz pozycję **Usuń grupę zasobów**.

Usunięcie grupy zasobów spowoduje również usunięcie wszystkich zasobów utworzonych w interfejsie wizualizacji.  

### <a name="delete-only-the-compute-target"></a>Usuń tylko element docelowy obliczeń

Obiekt docelowy obliczeń, który został utworzony w tym miejscu, *automatycznie przeskaluje* się do zerowych węzłów, gdy nie jest używany. Pozwala to zminimalizować opłaty. Jeśli chcesz usunąć element docelowy obliczeń, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)Otwórz obszar roboczy.

    ![Usuń element docelowy obliczeń](./media/aml-ui-cleanup/delete-compute-target.png)

1. W sekcji **obliczenia** obszaru roboczego wybierz zasób.

1. Wybierz pozycję **Usuń**.

### <a name="delete-individual-assets"></a>Usuwanie pojedynczych zasobów

W interfejsie wizualizacji, w którym został utworzony eksperyment, Usuń pojedyncze zasoby, zaznaczając je, a następnie wybierając przycisk **Usuń** . Zestawy danych można wyrejestrować z obszaru roboczego, zaznaczając każdy z nich i wybierając pozycję **Wyrejestruj**.

![Usuń zasoby](./media/aml-ui-cleanup/delete-asset.png)
