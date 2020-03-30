---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: f6bd574c83d309ce6d6f54fdb1c7d23cb713420d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73182257"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Oznaczanie maszyny wirtualnej za pomocą szablonów
Najpierw przyjrzyjmy się tagowaniu za pomocą szablonów. [Ten szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) umieszcza tagi w następujących zasobach: Compute (Virtual Machine), Storage (Storage Account) i Network (publiczny adres IP, sieć wirtualna i interfejs sieciowy). Ten szablon jest przeznaczony dla maszyny Wirtualnej systemu Windows, ale można go dostosować do maszyn wirtualnych z systemem Linux.

Kliknij przycisk Wdrażanie na **platformie Azure** w [łączu szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Spowoduje to przejście do [witryny Azure portal,](https://portal.azure.com/) gdzie można wdrożyć ten szablon.

![Proste wdrażanie za pomocą tagów](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Ten szablon zawiera następujące tagi: *Dział*, *Aplikacja*i *Utworzone przez*. Możesz dodawać/edytować te znaczniki bezpośrednio w szablonie, jeśli chcesz mieć różne nazwy tagów.

![Tagi platformy Azure w szablonie](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Jak widać, znaczniki są definiowane jako pary klucz/wartość, oddzielone dwukropkiem (:). Znaczniki muszą być zdefiniowane w tym formacie:

        "tags": {
            "Key1" : "Value1",
            "Key2" : "Value2"
        }

Zapisz plik szablonu po zakończeniu edycji za pomocą wybranych tagów.

Następnie w sekcji **Edytuj parametry** możesz wypełnić wartości tagów.

![Edytowanie tagów w witrynie Azure portal](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Kliknij **przycisk Utwórz,** aby wdrożyć ten szablon z wartościami znaczników.

## <a name="tagging-through-the-portal"></a>Oznaczanie za pośrednictwem portalu
Po utworzeniu zasobów za pomocą znaczników można wyświetlać, dodawać i usuwać znaczniki w portalu.

Wybierz ikonę znaczników, aby wyświetlić znaczniki:

![Ikona tagów w witrynie Azure portal](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Dodaj nowy tag za pośrednictwem portalu, definiując własną parę Klucz/Wartość i zapisując go.

![Dodawanie nowego znacznika w witrynie Azure portal](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Nowy tag powinien teraz pojawić się na liście tagów zasobu.

![Nowy tag zapisany w witrynie Azure portal](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

