---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 66f5c72fcabb62e21f0110cb981b7271244c0648
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73800028"
---
>[!IMPORTANT]
>Możesz użyć zasobów utworzonych jako wymagania wstępne dla innych samouczków Azure Machine Learning i artykułów z przewodnikiem.

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie planujesz korzystać z utworzonych elementów, Usuń całą grupę zasobów, aby nie naliczane były opłaty:

1. W Azure Portal wybierz pozycję **grupy zasobów** po lewej stronie okna.
 
   ![Usuwanie grupy zasobów w witrynie Azure Portal](./media/aml-ui-cleanup/delete-resources.png)

1. Z listy wybierz utworzoną grupę zasobów.

1. Wybierz pozycję **Usuń grupę zasobów**.

Usunięcie grupy zasobów spowoduje również usunięcie wszystkich zasobów utworzonych w projektancie.  

### <a name="delete-individual-assets"></a>Usuwanie pojedynczych zasobów

W projektancie, w którym został utworzony eksperyment, Usuń pojedyncze zasoby, zaznaczając je, a następnie wybierając przycisk **Usuń** .

Obiekt docelowy obliczeń, który został utworzony w tym miejscu, *automatycznie przeskaluje* się do zerowych węzłów, gdy nie jest używany. Pozwala to zminimalizować opłaty. Jeśli chcesz usunąć element docelowy obliczeń, wykonaj następujące czynności:

![Usuń zasoby](./media/aml-ui-cleanup/delete-asset.png)

Zestawy danych można wyrejestrować z obszaru roboczego, zaznaczając każdy z nich i wybierając pozycję **Wyrejestruj**.

![Wyrejestruj zestaw danych](./media/aml-ui-cleanup/unregister-dataset.png)

Aby usunąć zestaw danych, przejdź do konta magazynu przy użyciu Azure Portal lub Eksplorator usługi Storage i ręcznie usuń te zasoby.


