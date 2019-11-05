---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/22/2019
ms.openlocfilehash: 5a66212122745d0f4426e48e9487e9d674cec53f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489959"
---
>[!IMPORTANT]
>Możesz użyć zasobów utworzonych jako wymagania wstępne dla innych samouczków Azure Machine Learning i artykułów z przewodnikiem.

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie planujesz korzystać z utworzonych elementów, Usuń całą grupę zasobów, aby nie naliczane były opłaty:

1. W Azure Portal wybierz pozycję **grupy zasobów** po lewej stronie okna.
 
   ![Usuwanie grupy zasobów w witrynie Azure Portal](./media/aml-ui-cleanup/delete-resources.png)

1. Z listy wybierz utworzoną grupę zasobów.

1. Po prawej stronie okna wybierz przycisk wielokropka ( **...** ).

1. Wybierz pozycję **Usuń grupę zasobów**.

Usunięcie grupy zasobów spowoduje również usunięcie wszystkich zasobów utworzonych w projektancie.  

### <a name="delete-only-the-compute-target"></a>Usuń tylko element docelowy obliczeń

Obiekt docelowy obliczeń, który został utworzony w tym miejscu, *automatycznie przeskaluje* się do zerowych węzłów, gdy nie jest używany. Pozwala to zminimalizować opłaty. Jeśli chcesz usunąć element docelowy obliczeń, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)Otwórz obszar roboczy.

    ![Usuń element docelowy obliczeń](./media/aml-ui-cleanup/delete-compute-target.png)

1. W sekcji **obliczenia** obszaru roboczego wybierz zasób.

1. Wybierz pozycję **Usuń**.

### <a name="delete-individual-assets"></a>Usuwanie pojedynczych zasobów

W projektancie, w którym został utworzony eksperyment, Usuń pojedyncze zasoby, zaznaczając je, a następnie wybierając przycisk **Usuń** .

![Usuń zasoby](./media/aml-ui-cleanup/delete-asset.png)

Zestawy danych można wyrejestrować z obszaru roboczego, zaznaczając każdy z nich i wybierając pozycję **Wyrejestruj**.

![Wyrejestruj zestaw danych](./media/aml-ui-cleanup/unregister-dataset.png)


