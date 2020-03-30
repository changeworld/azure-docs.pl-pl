---
title: Konfigurowanie galerii obrazów udostępnionych w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować galerię obrazów udostępnionych w laboratorium devtest azure
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 9593d60f76802cd515ca85616bce028cf3aa0d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589321"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Konfigurowanie galerii obrazów udostępnionych w usłudze Azure DevTest Labs
DevTest Labs obsługuje teraz funkcję [Shared Image Gallery.](../virtual-machines/windows/shared-image-galleries.md) Umożliwia użytkownikom laboratorium dostęp do obrazów z udostępnionej lokalizacji podczas tworzenia zasobów laboratoryjnych. Pomaga również tworzyć strukturę i organizację wokół obrazów maszyn wirtualnych zarządzanych przez niestandardowe. Funkcja Shared Image Gallery obsługuje:

- Zarządzana globalna replikacja obrazów
- Przechowywanie wersji i grupowanie obrazów w celu łatwiejszego zarządzania
- Udostępnij obrazy na wysokim miejscu dzięki kontom Strefowego Nadmiarowego Magazynu (ZRS) w regionach obsługujących strefy dostępności. ZRS oferuje lepszą odporność na awarie strefowe.
- Udostępnianie między subskrypcjami, a nawet między dzierżawami przy użyciu kontroli dostępu opartej na rolach (RBAC).

Aby uzyskać więcej informacji, zobacz [dokumentację udostępnionej galerii obrazów](../virtual-machines/windows/shared-image-galleries.md). 
 
Jeśli masz dużą liczbę zarządzanych obrazów, które chcesz zachować i chcesz je udostępnić w całej firmie, możesz użyć udostępnionej galerii obrazów jako repozytorium, które ułatwia aktualizowanie i udostępnianie obrazów. Jako właściciel laboratorium możesz dołączyć istniejącą galerię obrazów udostępnionych do laboratorium. Po dołączeniu tej galerii użytkownicy laboratorium mogą tworzyć maszyny z tych najnowszych obrazów. Kluczową zaletą tej funkcji jest to, że DevTest Labs może teraz korzystać z udostępniania obrazów w laboratoriach, w ramach subskrypcji i w różnych regionach. 

> [!NOTE]
> Aby dowiedzieć się więcej o kosztach związanych z usługą Shared Image Gallery, zobacz [Galeria rozliczeń za udostępnione obrazy](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Zagadnienia do rozważenia
- W ciągu jednego czasu można dołączyć tylko jedną galerię obrazów udostępnionych do laboratorium. Jeśli chcesz dołączyć inną galerię, musisz odłączyć istniejącą i dołączyć inną. 
- DevTest Labs obsługuje obecnie udostępnione galerii obrazów uogólnionych obrazów tylko.
- DevTest Labs obecnie nie obsługuje przekazywania obrazów do galerii za pośrednictwem laboratorium. 
- Podczas tworzenia maszyny wirtualnej przy użyciu udostępnionego obrazu galerii obrazów DevTest Labs zawsze używa najnowszej opublikowanej wersji tego obrazu. Jeśli jednak obraz ma wiele wersji, użytkownik może wybrać utworzenie komputera z wcześniejszej wersji, przechodząc do karty Ustawienia zaawansowane podczas tworzenia maszyny wirtualnej.  
- Mimo że DevTest Labs automatycznie podejmuje najlepszą próbę zapewnienia udostępnionej galerii obrazów replikuje obrazy do regionu, w którym istnieje laboratorium, nie zawsze jest to możliwe. Aby uniknąć problemów użytkowników z tworzeniem maszyn wirtualnych z tych obrazów, upewnij się, że obrazy są już replikowane do regionu laboratorium."

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz **pozycję Wszystkie usługi** w lewym menu nawigacyjnym.
1. Wybierz z listy pozycję **DevTest Labs**.
1. Z listy laboratoriów wybierz **laboratorium**.
1. Wybierz **pozycję Konfiguracja i zasady** w sekcji **Ustawienia** w menu po lewej stronie.
1. Wybierz **pozycję Udostępnione galerie obrazów** w obszarze Bazy maszyn **wirtualnych** w menu po lewej stronie.

    ![Menu Udostępnione galerie obrazów](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Dołącz istniejącą galerię zdjęć udostępnionych do swojego laboratorium, klikając przycisk **Dołącz** i wybierając galerię na rozwijanej.

    ![Attach](./media/configure-shared-image-gallery/attach-options.png)
1. Przejdź do dołączonej galerii i skonfiguruj galerię, aby **włączyć lub wyłączyć** udostępnione obrazy do tworzenia maszyn wirtualnych. Wybierz galerię obrazów z listy, aby ją skonfigurować. 

    Domyślnie **zezwalaj na użycie wszystkich obrazów jako baz maszyn wirtualnych** na **Tak**. Oznacza to, że wszystkie obrazy dostępne w dołączonej galerii obrazów udostępnionych będą dostępne dla użytkownika laboratorium podczas tworzenia nowej maszyny Wirtualnej laboratorium. Jeśli dostęp do niektórych obrazów musi być ograniczony, zmień **Zezwalaj na wszystkie obrazy, które mają być używane jako bazy maszyn wirtualnych** na **Nie,** i wybierz obrazy, które mają być używane podczas tworzenia maszyn wirtualnych, a następnie wybierz przycisk **Zapisz.**

    ![Włączanie lub wyłączanie](./media/configure-shared-image-gallery/enable-disable.png)
1. Użytkownicy laboratorium mogą następnie utworzyć maszynę wirtualną przy użyciu włączonych obrazów, klikając **+Dodaj** i znajdując obraz na **stronie podstawowej.**

    ![Użytkownicy laboratorium](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Dołączanie udostępnionej galerii obrazów do laboratorium
Jeśli używasz szablonu usługi Azure Resource Manager do dołączania udostępnionej galerii obrazów do laboratorium, musisz dodać go w sekcji zasobów szablonu Menedżera zasobów, jak pokazano w poniższym przykładzie:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

W przykładzie pełnego szablonu Menedżera zasobów zobacz te przykłady szablonów Menedżera zasobów w naszym publicznym repozytorium Usługi GitHub: [Konfigurowanie udostępnionej galerii obrazów podczas tworzenia laboratorium](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-api"></a>Korzystanie z interfejsu API

### <a name="shared-image-galleries---create-or-update"></a>Udostępnione galerie obrazów — tworzenie lub aktualizowanie

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>Udostępnione obrazy galerie zdjęć - Lista 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły dotyczące tworzenia maszyny wirtualnej przy użyciu obrazu z dołączonej galerii obrazów udostępnionych: [Tworzenie maszyny Wirtualnej przy użyciu udostępnionego obrazu z galerii](add-vm-use-shared-image.md)
