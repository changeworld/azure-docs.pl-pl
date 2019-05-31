---
title: Konfigurowanie galerii udostępnionego obrazu w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować galerii udostępnionego obrazu w usłudze Azure DevTest Labs
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
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 51b394043f88789865edea5be6376ae536f88848
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420441"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Konfigurowanie galerii udostępnionego obrazu w usłudze Azure DevTest Labs
Obsługuje teraz DevTest Labs [galerii obrazów współdzielona](/virtual-machines/windows/shared-image-galleries.md) funkcji. Umożliwia użytkownikom laboratorium dostępu do obrazów w lokalizacji udostępnionej podczas tworzenia zasobów laboratorium. Pomaga również tworzenia struktury i organizacji wokół zarządzane niestandardowych obrazów maszyn wirtualnych. Funkcja galerii obrazów współdzielona obsługuje systemy:

- Zarządzane globalnej replikacji obrazów
- Przechowywanie wersji i grupowania obrazów w celu ułatwienia zarządzania
- Obrazy usługi wysokiej dostępności przy użyciu kont magazyn Strefowo nadmiarowy (ZRS) w regionach, które obsługują strefy dostępności. Magazyn ZRS zapewnia lepszą odporność na awarie strefowych.
- Udostępnianie w subskrypcjach, a nawet między dzierżawami, za pomocą kontroli dostępu opartej na rolach (RBAC).

Aby uzyskać więcej informacji, zobacz [dokumentacji galerii obrazów współdzielona](../virtual-machines/windows/shared-image-galleries.md). 
 
Jeśli masz dużą liczbę zarządzanych obrazów, które trzeba utrzymywać i chcesz udostępnić je w całej firmie, można użyć Galerii udostępnionego obrazu jako repozytorium, które można łatwo zaktualizować i udostępniać swoje obrazy. Jako właściciel laboratorium można dołączyć istniejącego galerii udostępnionego obrazu do środowiska laboratoryjnego. Po dołączeniu tej galerii użytkownicy laboratorium umożliwia tworzenie maszyn z tych najnowszych obrazów. Najważniejszą korzyścią z tej funkcji jest, że DevTest Labs teraz korzystać z zalet udostępniania obrazów między labs, między subskrypcjami i regionami. 

## <a name="considerations"></a>Zagadnienia do rozważenia
- Jedna Galeria udostępnionego obrazu można dołączać tylko do laboratorium naraz. Jeśli chcesz dołączyć inny galerii, musisz odłączyć istniejącą grupę, a następnie dołączyć inny. 
- DevTest Labs aktualnie nie obsługuje przekazywania obrazów w galerii, za pomocą laboratorium. 
- Podczas tworzenia maszyny wirtualnej przy użyciu obrazu z galerii obrazów udostępnionych, DevTest Labs zawsze używa najnowszej opublikowanej wersji tego obrazu.
- Mimo że usługa DevTest Labs ułatwia automatycznie podejmie próbę upewnij się, że w galerii obrazów udostępnionych replikuje obrazów do regionu, w której istnieje laboratorium, nie zawsze jest możliwe. Aby uniknąć użytkownikom tworzenie maszyn wirtualnych z obrazy te problemy, upewnij się, że obrazy są już replikowane do regionu w laboratorium."

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **wszystkich usług** w menu nawigacji po lewej stronie.
1. Wybierz z listy pozycję **DevTest Labs**.
1. Wybierz z listy labs, Twoje **laboratorium**.
1. Wybierz **konfiguracji i zasad** w **ustawienia** sekcji, w menu po lewej stronie.
1. Wybierz **galerie obrazów współdzielona** w obszarze **maszyny wirtualnej Określa** w menu po lewej stronie.
1. Dołącz istniejące galerii udostępnionego obrazu do środowiska laboratoryjnego, klikając **Dołącz** przycisk i wybierając galerii na liście rozwijanej.
1. Przejdź do galerii dołączonych i skonfiguruj galerii tak, aby **włączać lub wyłączać** udostępnionych obrazów w celu utworzenia maszyny Wirtualnej.
1. Użytkownicy laboratorium mogą następnie utwórz maszynę wirtualną przy użyciu obrazów włączone, klikając **+ Dodaj** oraz wyszukiwanie obrazów w **wybierz podstawowym** strony.

## <a name="use-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Dołącz galerii udostępnionego obrazu do środowiska laboratoryjnego
Jeśli używasz szablonu usługi Azure Resource Manager można dołączyć galerii udostępnionego obrazu do środowiska laboratoryjnego, należy dodać go w sekcji zasobów szablonu usługi Resource Manager, jak pokazano w poniższym przykładzie:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

Aby uzyskać kompletny przykład szablonu usługi Resource Manager Zobacz te przykłady szablonów usługi Resource Manager w publicznego repozytorium GitHub: [Konfigurowanie galerii udostępnionego obrazu podczas tworzenia laboratorium](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>Utwórz Maszynę wirtualną przy użyciu obrazu z galerii obrazów udostępnionych
Jeśli używasz szablonu usługi Azure Resource Manager, aby utworzyć maszynę wirtualną przy użyciu obrazu z galerii udostępnionego obrazu, użyj poniższego przykładu:

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

Aby dowiedzieć się więcej, zobacz te przykłady szablonów usługi Resource Manager w usłudze GitHub publicznych.
[Utwórz maszynę wirtualną przy użyciu obrazu z galerii obrazów udostępnionych](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage).

## <a name="use-api"></a>Korzystanie z interfejsu API

- Użyj interfejsu API w wersji 2018-10-15-preview.
- Aby dołączyć galerii, należy wysłać żądanie, jak pokazano w poniższym fragmencie kodu:
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- Aby wyświetlić wszystkie obrazy w galerii obrazów udostępnionych, możesz wyświetlić listę wszystkich obrazów udostępnione wraz z ich identyfikatorów zasobów przez

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- Utworzyć maszynę Wirtualną przy użyciu udostępnionych obrazów, możesz zrobić PUT na maszynach wirtualnych i we właściwościach maszyny wirtualnej, przekaż identyfikator udostępnianych obrazów, otrzymanych od poprzedniego wywołania. Do właściwości. SharedImageId


## <a name="next-steps"></a>Kolejne kroki
Na artefaktów, zobacz następujące artykuły:

- [Określ obowiązkowe artefaktów dla swojego laboratorium](devtest-lab-mandatory-artifacts.md)
- [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md)
- [Dodawanie repozytorium artefaktów do laboratorium](devtest-lab-artifact-author.md)
- [Diagnozowanie błędów artefaktów](devtest-lab-troubleshoot-artifact-failure.md)
