---
title: Opublikuj ogłoszenie w laboratorium w laboratorium w usłudze Azure DevTest Labs | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać anons do laboratorium w laboratorium usługi Azure DevTest Labs
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
ms.openlocfilehash: 84120b07de3a03a049493eb973b6dc46f8668387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976446"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Publikowanie ogłoszenia w laboratorium w laboratorium usługi Azure DevTest Labs

Jako administrator laboratorium możesz opublikować niestandardowe ogłoszenie w istniejącym laboratorium, aby powiadomić użytkowników o ostatnich zmianach lub dodatkach do laboratorium. Na przykład możesz poinformować użytkowników o:

- Nowe rozmiary maszyn wirtualnych, które są dostępne
- Obrazy, które są obecnie bezużyteczne
- Aktualizacje zasad laboratoryjnych

Po opublikowaniu ogłoszenie jest wyświetlane na stronie Przegląd laboratorium, a użytkownik może go wybrać, aby uzyskać więcej informacji.

Funkcja anonsu ma być używana do powiadomień tymczasowych.  Możesz łatwo wyłączyć anons, gdy nie jest już potrzebne.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Kroki publikowania ogłoszenia w istniejącym laboratorium

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.** (Twoje laboratorium może być już wyświetlane na pulpicie nawigacyjnym w obszarze **Wszystkie zasoby).**
1. Z listy laboratoriów wybierz laboratorium, w którym chcesz opublikować anons.
1. W obszarze **Przegląd** laboratorium wybierz pozycję **Konfiguracja i zasady**.

    ![Przycisk Konfiguracja i zasady](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Po lewej stronie w obszarze **USTAWIENIA**wybierz pozycję **Ogłoszenie w laboratorium**.

    ![Przycisk anonsu laboratorium](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Aby utworzyć wiadomość dla użytkowników w tym laboratorium, ustaw **włączone tak** **.**

1. Można wprowadzić **datę wygaśnięcia,** aby określić datę i godzinę, po której anons nie jest już wyświetlany użytkownikom. Jeśli nie wprowadzisz daty wygaśnięcia, anons pozostanie do momentu jego wyłączenia.

   > [!NOTE]
   > Po wygaśnięciu anonsu nie jest już wyświetlany użytkownikom, ale nadal istnieje w **laburzystrzarze.** Możesz wprowadzić do niego zmiany i ponownie włączyć go, aby ponownie uaktywnić go.
   >
   >

1. Wprowadź **tytuł anonsu** i **tekst anonsu**.

   Tytuł może mieć maksymalnie 100 znaków i jest wyświetlany użytkownikowi na stronie Przegląd laboratorium. Jeśli użytkownik wybierze tytuł, zostanie wyświetlony tekst anonsu.

   Tekst anonsu akceptuje markdown. Po wprowadzeniu tekstu anonsu można wyświetlić wiadomość w obszarze Podgląd u dołu ekranu.

    ![Ekran anonsu laboratorium, aby utworzyć wiadomość.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Wybierz **pozycję Zapisz,** gdy ogłoszenie będzie gotowe do opublikowania.

Jeśli nie chcesz już wyświetlać tego anonsu użytkownikom laboratorium, wróć do strony **z ogłoszeniem laboratorium** i ustaw **włączono** **pozycję Nie**. Jeśli określono datę wygaśnięcia, anons zostanie automatycznie wyłączony w tym dniu i godzinie.

## <a name="steps-for-users-to-view-an-announcement"></a>Kroki, które użytkownicy mogą wyświetlać anons

1. Z [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)wybierz laboratorium.

1. Jeśli laboratorium ma ogłoszenie opublikowane dla niego, informacja jest wyświetlana w górnej części strony Przegląd laboratorium. To powiadomienie o informacjach jest tytułem anonsu, który został określony podczas tworzenia anonsu.

    ![Ogłoszenie o laboratorium na stronie Przegląd](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Użytkownik może wybrać wiadomość, aby wyświetlić całe ogłoszenie.

    ![Więcej informacji na temat ogłoszenia w laboratorium](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

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

Szablon usługi Azure Resource Manager można wdrożyć przy użyciu jednego z następujących sposobów:

- [Portal Azure](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Interfejs wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)
- [INTERFEJS API ODPOCZYNKU](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>Następne kroki
* Jeśli zmienisz lub ustawisz zasady laboratoryjne, możesz opublikować anons, aby poinformować użytkowników. [Ustaw zasady i harmonogramy](devtest-lab-set-lab-policy.md) zawiera informacje na temat stosowania ograniczeń i konwencji w całej subskrypcji przy użyciu niestandardowych zasad.
* Zapoznaj się z [galerią szablonów Szybki start programu Quick Start programu DevTest Labs w usłudze Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
