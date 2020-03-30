---
title: Dodawanie maszyny wirtualnej przy użyciu udostępnionego obrazu w laboratorium devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać maszynę wirtualną (VM) przy użyciu obrazu z dołączonej galerii obrazów udostępnionych w laboratorium devtest usługi Azure
services: devtest-lab,virtual-machines
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
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68775593"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Dodawanie maszyny wirtualnej przy użyciu obrazu z dołączonej galerii obrazów udostępnionych
Usługa Azure DevTest Labs umożliwia dołączenie galerii obrazów udostępnionych do laboratorium, a następnie użycie obrazów w galerii jako baz dla maszyn wirtualnych utworzonych w laboratorium. Aby dowiedzieć się, jak dołączyć galerię obrazów udostępnionych do laboratorium, zobacz [Konfigurowanie galerii obrazów udostępnionych](configure-shared-image-gallery.md). W tym artykule pokazano, jak dodać maszynę wirtualną do laboratorium przy użyciu obrazu z dołączonej galerii obrazów udostępnionych jako podstawy. 

## <a name="azure-portal"></a>Portal Azure
W tej sekcji dowiesz się, jak za pomocą witryny Azure portal, aby dodać maszynę wirtualną do laboratorium na podstawie obrazu z dołączonej galerii obrazów udostępnionych. Ta sekcja nie zawiera szczegółowych instrukcji krok po kroku do tworzenia maszyny Wirtualnej przy użyciu witryny Azure portal. Aby uzyskać te szczegóły, zobacz [Tworzenie maszyny Wirtualnej — Azure portal](devtest-lab-add-vm.md). Wyróżnia tylko kroki, w których wybierasz obraz z dołączonej galerii obrazów udostępnionych i wybierasz wersję obrazu, którego chcesz użyć. 

Podczas dodawania maszyny Wirtualnej do laboratorium można wybrać obraz z dołączonej galerii obrazów udostępnionych jako obraz podstawowy: 

![Wybieranie udostępnionego obrazu dla bazy](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Następnie na karcie **Ustawienia zaawansowane** na stronie Tworzenie **zasobów laboratorium** można wybrać wersję obrazu, którego obraz bazowy ma być używany:

![Wybierz wersję obrazu](./media/add-vm-use-shared-image/select-version-shared-image.png)

Można przełączyć się do przy użyciu innej wersji obrazu po utworzeniu maszyny Wirtualnej. 

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Jeśli używasz szablonu usługi Azure Resource Manager do utworzenia maszyny wirtualnej przy użyciu udostępnionego obrazu galerii obrazów, określ wartość **sharedImageId** w sekcji **Właściwości.** Zobacz poniższy przykład: 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

W przypadku pełnego przykładu szablonu Menedżera zasobów zobacz [Tworzenie maszyny wirtualnej przy użyciu przykładowego obrazu galerii obrazów udostępnionych](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) w naszym repozytorium Usługi GitHub. 

## <a name="rest-api"></a>Interfejs API REST

1. Najpierw musisz uzyskać identyfikator obrazu w galerii obrazów udostępnionych. Jednym ze sposobów jest wyświetlenie listy wszystkich obrazów w załączonej galerii obrazów udostępnionych przy użyciu następującego polecenia GET. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Wywołaj metodę PUT na maszynach wirtualnych, przekazując identyfikator udostępnionego obrazu `properties.SharedImageId`otrzymanego z poprzedniego wywołania do .

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak dołączyć galerię obrazów udostępnionych do laboratorium i skonfigurować ją, zobacz [Konfigurowanie galerii obrazów udostępnionych](configure-shared-image-gallery.md).