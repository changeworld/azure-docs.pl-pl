---
title: Rozwiązywanie problemów z niepowodzeniami tworzenia maszyny Wirtualnej i środowiska usługi Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z maszyny wirtualnej (VM) i błędy tworzenia środowiska w usłudze Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.openlocfilehash: 7baa5e4c113e6c21c6123ac7c8399533a7dfb358
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410301"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Rozwiązywanie problemów dotyczących maszyny wirtualnej (VM) i błędy tworzenia środowiska w usłudze Azure DevTest Labs
DevTest Labs umożliwia ostrzeżenia, jeśli nazwa komputera jest nieprawidłowa lub jeśli masz zamiar narusza zasady laboratorium. Czasami, zobaczysz czerwony `X` obok laboratorium stan maszyny Wirtualnej lub środowiska, informujące o tym, że wystąpił problem.  Ten artykuł zawiera kilka sztuczek, które można znaleźć podstawowego problemu i mamy nadzieję, że w przyszłości uniknąć tego problemu.

## <a name="portal-notifications"></a>Powiadomienia z portalu
Jeśli używasz witryny Azure portal jest pierwsze miejsce, Przyjrzyj się **panelu powiadomienia**.  Powiadomienia panelu dostępne na pasku poleceń głównego, klikając **ikonę dzwonka**, poinformuje, tworzenia maszyny Wirtualnej lub w środowisku laboratorium zakończyło się pomyślnie lub nie.  Wystąpił błąd, zobaczysz komunikat o błędzie skojarzony z błędem tworzenia. Szczegóły często podać informacje pomocne w rozwiązaniu tego problemu. W poniższym przykładzie tworzenia maszyny wirtualnej nie powiodło się ze względu na ilość rdzeni. Szczegółowy komunikat informuje, jak rozwiązać ten problem i zażądać zwiększenia limitu przydziału rdzeni.

![Powiadomienie witryny Azure portal](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)


## <a name="activity-logs"></a>Dzienniki aktywności
Spójrz na dziennikach aktywności, jeśli analizujesz błąd jakiś czas, po próbie utworzenia maszyny Wirtualnej lub środowiska. W tej sekcji pokazano, jak znaleźć dzienników dla środowisk i maszyn wirtualnych.

## <a name="activity-logs-for-virtual-machines"></a>Dzienniki aktywności dla maszyn wirtualnych

1. Na stronie głównej dla swojego laboratorium, wybierz maszynę Wirtualną można uruchomić **maszyny wirtualnej** strony.
2. Na **maszyny wirtualnej** stronie **monitorowanie** części menu po lewej stronie wybierz **dziennika aktywności** na przeglądanie dzienników wszystkich skojarzonych z maszyną Wirtualną.
3. W elementach dziennika aktywności należy wybrać operację, która nie powiodła się. Zazwyczaj nosi nazwę operacji zakończonej niepowodzeniem `Write Virtualmachines`.
4. W okienku po prawej stronie przejdź do karty JSON. Możesz wyświetlić szczegóły w widoku JSON dziennika.

    ![Dziennik aktywności dla maszyny Wirtualnej](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Przejrzyj dziennik JSON do momentu znalezienia `statusMessage` właściwości. Oferuje on komunikat o błędzie głównego i uzyskać więcej szczegółowych informacji, jeśli ma to zastosowanie. Następujące dane JSON, występuje błąd przykład core cytowane przekroczyła widoczne we wcześniejszej części tego artykułu.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Dziennik aktywności dla środowiska

Aby wyświetlić dziennik aktywności, aby utworzyć środowiska, wykonaj następujące kroki:

1. Na stronie głównej dla swojego laboratorium wybierz **konfiguracji i zasad** w menu po lewej stronie.
2. na **konfiguracji i zasad** wybierz opcję **dzienników aktywności** w menu.
3. Błąd na liście działań w dzienniku Znajdź i zaznacz go.
4. W okienku po prawej stronie, przejdź do karty JSON i poszukaj **komunikat stanu**.

    ![Dziennik aktywności środowiska](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Dzienniki wdrożenia szablonu usługi Resource Manager
Jeśli Twoje środowisko lub maszyna wirtualna została utworzona za pomocą automatyzacji, ma jeden ostatnie miejsce do wyszukiwania informacji o błędzie. To dziennik wdrażania szablonu usługi Azure Resource Manager. Po utworzeniu zasobu laboratorium za pomocą automatyzacji go często odbywa się za pośrednictwem wdrażania szablonu usługi Azure Resource Manager. Zobacz[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) dla przykładowych szablonów usługi Azure Resource Manager, które tworzą zasoby usługi DevTest Labs.

Aby wyświetlić dzienniki wdrożenia szablonu laboratorium, wykonaj następujące kroki:

1. Uruchom stronę grupy zasobów, w której istnieje laboratorium.
2. Wybierz **wdrożeń** w menu po lewej stronie w obszarze **ustawienia**.
3. Wyszukaj wdrożenia za pomocą stan niepowodzenia i wybierz ją.
4. Na **wdrożenia** wybierz opcję **szczegóły operacji** link dla operacji, która nie powiodła się.
5. Zobacz szczegółowe informacje o operacji, która zakończyła się niepowodzeniem w **szczegóły operacji** okna.

## <a name="next-steps"></a>Kolejne kroki
Zobacz [Rozwiązywanie problemów z błędami artefaktu](devtest-lab-troubleshoot-artifact-failure.md)