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
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659880"
---
>[!IMPORTANT]
>Można użyć zasobów utworzonych jako wymagania wstępne dla innych samouczków usługi Azure Machine Learning i artykułów instruktażowych.

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie zamierzasz używać niczego, co zostało utworzone, usuń całą grupę zasobów, aby nie ponosić żadnych opłat.

1. W witrynie Azure portal wybierz **grup zasobów** po lewej stronie okna.
 
   ![Usuwanie grupy zasobów w witrynie Azure Portal](./media/aml-ui-cleanup/delete-resources.png)

1. Na liście wybierz utworzoną grupę zasobów.

1. Wybierz pozycję **Usuń grupę zasobów**.

Usunięcie grupy zasobów powoduje również usunięcie wszystkich zasobów utworzonych w projektancie. 

### <a name="delete-individual-assets"></a>Usuwanie poszczególnych zasobów

W projektancie, w którym utworzono eksperyment, usuń poszczególne zasoby, zaznaczając je, a następnie wybierając przycisk **Usuń.**

Obiekt docelowy obliczeń, który został utworzony w tym miejscu *automatycznie skaluje się automatycznie* do zera węzłów, gdy nie jest używany. Ta akcja jest podejmowana w celu zminimalizowania opłat.Jeśli chcesz usunąć obiekt docelowy obliczeń, wykonaj następujące kroki:

![Usuwanie zasobów](./media/aml-ui-cleanup/delete-asset.png)

Można wyrejestrować zestawy danych z obszaru roboczego, zaznaczając każdy zestaw danych i wybierając pozycję **Wyrejestruj**.

![Wyrejestruj zestaw danych](./media/aml-ui-cleanup/unregister-dataset1225.png)

Aby usunąć zestaw danych, przejdź do konta magazynu przy użyciu witryny Azure portal lub Eksploratora usługi Azure Storage i ręcznie usuń te zasoby.


