---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66123159"
---
>[!IMPORTANT]
>Jako warunki wstępne innych samouczków usługi Azure Machine Learning i artykuły z poradami, można użyć zasobów, które zostały utworzone.


### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie planujesz cokolwiek, który został utworzony, należy usunąć całą grupę zasobów, więc nie powodują naliczania żadnych opłat:

1. W witrynie Azure portal wybierz **grup zasobów** w lewej części okna.
 
   ![Usuwanie grupy zasobów w witrynie Azure Portal](./media/aml-ui-cleanup/delete-resources.png)

1. Na liście wybierz grupę zasobów, który został utworzony.

1. Po prawej stronie okna wybierz przycisk wielokropka (**...** ).

1. Wybierz pozycję **Usuń grupę zasobów**.

Usunięcie grupy zasobów spowoduje również usunięcie wszystkich zasobów utworzonych w interfejs graficzny.  

### <a name="delete-only-the-compute-target"></a>Usuń tylko docelową obliczeń

Docelowy obliczeń, który zostanie utworzony w tym miejscu *automatycznego skalowania* zero węzłów, gdy nie są używane. To, aby zminimalizować koszty. Jeśli chcesz usunąć obliczeniowego elementu docelowego, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), Otwórz obszar roboczy.

    ![Usuń obliczeniowego elementu docelowego](./media/aml-ui-cleanup/delete-compute-target.png)

1. W **obliczenia** sekcji obszaru roboczego wybierz zasób.

1. Wybierz pozycję **Usuń**.

### <a name="delete-individual-assets"></a>Usuwać poszczególne zasoby

W interfejs graficzny, w której utworzono eksperymentu, Usuń poszczególne zasoby, wybierając je, a następnie wybierając **Usuń** przycisku.

![Usuń eksperymentów](./media/aml-ui-cleanup/delete-experiment.png)
