---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ccc2b574ea054a1b0ecf32a1e59691050fb66fcf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108308"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Tagowanie maszyny wirtualnej za pomocą szablonów
Najpierw Przyjrzyjmy się tagowanie za pomocą szablonów. [Ten szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) umieszcza tagi w następujących zasobach: Obliczeń (maszyna wirtualna), magazynu (konta magazynu) i sieci, (publiczny adres IP, sieci wirtualnej i interfejsu sieciowego). Ten szablon jest dla maszyny Wirtualnej z systemem Windows, ale mogą być dostosowane do maszyn wirtualnych systemu Linux.

Kliknij przycisk **Wdróż na platformie Azure** przycisk [link szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Spowoduje to przejście do [witryny Azure portal](https://portal.azure.com/) którym można wdrożyć tego szablonu.

![Proste wdrożenie przy użyciu tagów](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Ten szablon zawiera następujące tagi: *Dział*, *aplikacji*, i *utworzone przez*. Możesz można Dodawanie/edytowanie tych znaczników bezpośrednio w szablonie Jeśli różnych nazwach.

![Usługa Azure tagów w szablonie](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Jak widać, znaczniki są definiowane jako pary klucz/wartość, oddzielone dwukropkiem (:). Tagi muszą być zdefiniowane w następującym formacie:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Zapisz plik szablonu, po zakończeniu edycji za pomocą tagów wybranych przez użytkownika.

Następnie w **Edytuj parametry** sekcji, możesz podać wartości dla swoich tagów.

![Edytowanie tagów w witrynie Azure portal](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Kliknij przycisk **Utwórz** do wdrożenia tego szablonu za pomocą wartości tagu.

## <a name="tagging-through-the-portal"></a>Znakowanie portalu
Po utworzeniu zasobów przy użyciu tagów można wyświetlać, dodawać i usuwać tagi w portalu.

Wybierz ikonę tagów, aby wyświetlić tagi:

![Ikona tagów w witrynie Azure portal](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Dodaj nowy tag za pośrednictwem portalu, definiując własne pary klucz/wartość i zapisz go.

![Dodawanie nowego tagu w witrynie Azure portal](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Nowy tag powinien zostać wyświetlony na liście tagi dla zasobu.

![Nowy znacznik zapisane w witrynie Azure portal](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

