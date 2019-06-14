---
title: Opublikuj announcment do laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać zawiadomienia do laboratorium Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 2fe31271fa84bc4170bd431a4aadbcafc0df9086
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311528"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Publikowanie anonsu do laboratorium w usłudze Azure DevTest Labs

Będąc administratorem laboratorium możesz zadać niestandardowe ogłoszenie na istniejącego laboratorium w celu powiadomienia użytkowników o najnowsze zmiany i dodatki w laboratorium. Na przykład możesz chcieć poinformować użytkowników o:

- Nowe rozmiary maszyn wirtualnych, które są dostępne
- Obrazy, które są aktualnie bezużyteczne
- Aktualizacje zasad laboratorium

Gdy opublikowana, anons jest wyświetlana na stronie Przegląd laboratorium, a użytkownik może wybrać ją Aby uzyskać więcej informacji.

Funkcja ogłoszenie jest przeznaczona do użycia na potrzeby powiadomień tymczasowych.  Po nie jest już potrzebny, można łatwo Wyłącz powiadomienia.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Kroki umożliwiające publikowanie anonsu w laboratorium istniejące

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby zaznacz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy. (Laboratorium może być już wyświetlane na pulpicie nawigacyjnym w obszarze **wszystkie zasoby**).
1. Zaznacz na liście laboratoriów laboratorium, w której chcesz publikować anonsu.
1. W laboratorium **Przegląd** wybierz opcję **konfiguracji i zasad**.

    ![Przycisk konfiguracji i zasad](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Po lewej stronie w obszarze **ustawienia**, wybierz opcję **anonsu w laboratorium**.

    ![Przycisk anonsu w laboratorium](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Aby utworzyć wiadomość dla użytkowników, w tym środowisku laboratoryjnym, ustaw **włączone** do **tak**.

1. Możesz wprowadzić **datę wygaśnięcia** określić datę i godzinę, po którym anons nie jest już widocznym dla użytkowników. Jeśli nie zostanie wprowadzony datę wygaśnięcia, anons pozostaje, dopóki nie zostanie wyłączone.

   > [!NOTE]
   > Po wygaśnięciu anonsu ona już nie jest wyświetlana dla użytkowników, ale nadal istnieje w **anonsu w laboratorium** okienka. Można wprowadzić zmiany, a następnie ponownie włącz go, aby ją uaktywnić ponownie.
   >
   >

1. Wprowadź **Tytuł anonsu** i **tekst anonsu**.

   Tytuł może zawierać maksymalnie 100 znaków i jest wyświetlany na stronie Przegląd laboratorium. Jeśli użytkownik wybierze tytuł, jest wyświetlany tekst anonsu.

   Tekst anonsu akceptuje języka znaczników markdown. Podczas wprowadzania tekstu anons można wyświetlić komunikat w obszarze podglądu w dolnej części ekranu.

    ![Ekran ogłoszenie laboratorium do tworzenia komunikatu.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Wybierz **Zapisz** po anonsu jest gotowy do opublikowania.

Nie jest już do wyświetlenia tego anonsu użytkownikom laboratorium, należy powrócić do **anonsu w laboratorium** strony, a następnie ustaw **włączone** do **nie**. Jeśli określono datę wygaśnięcia anonsu jest wyłączona automatycznie na, daty i godziny.

## <a name="steps-for-users-to-view-an-announcement"></a>Kroki dla użytkowników wyświetlić anonsu

1. Z [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), wybierz laboratorium.

1. Jeśli laboratorium ma anonsu opublikowane dla niego, informacje o uznaniu informacji jest wyświetlany w górnej części strony Przegląd laboratorium. To ogłoszenie jest tytuł anonsu, który został określony podczas tworzenia anonsu.

    ![Ogłoszenie laboratorium na stronie przeglądu](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Użytkownik może wybrać komunikat, aby wyświetlić całą anonsu.

    ![Więcej informacji na anonsu w laboratorium](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Anons można określić jako część szablonu usługi Azure Resource Manager, jak pokazano w poniższym przykładzie:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

Szablon usługi Azure Resource Manager można wdrożyć przy użyciu jednej z następujących sposobów:

- [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Interfejs wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [Interfejs API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

## <a name="next-steps"></a>Kolejne kroki
* Jeśli zmienisz lub ustawianie zasad laboratorium warto publikowanie anonsu, aby informować użytkowników. [Ustawianie zasad i harmonogramów](devtest-lab-set-lab-policy.md) dostarcza informacji na temat stosowania ograniczeń i konwencje w ramach subskrypcji za pomocą zasad niestandardowych.
* Zapoznaj się z [galerii szablonów DevTest Labs Azure Resource Manager QuickStart](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
