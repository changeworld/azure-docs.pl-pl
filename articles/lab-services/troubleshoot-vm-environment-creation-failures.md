---
title: Rozwiązywanie problemów z awariami maszyn wirtualnych i środowiska usługi Azure DevTest Labs
description: Dowiedz się, jak rozwiązywać problemy z błędami tworzenia maszyn wirtualnych i środowiska w laboratoriach devtest azure.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 155a359608cf6d846578306545f5ce0b4003949c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166342"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Rozwiązywanie problemów z błędami maszyny wirtualnej i tworzenia środowiska w laboratoriach usługi Azure DevTest Labs
DevTest Labs daje ostrzeżenia, jeśli nazwa komputera jest nieprawidłowa lub jeśli masz zamiar naruszyć zasady laboratorium. Czasami zobaczysz czerwony `X` obok laboratorium maszyny Wirtualnej lub stanu środowiska, który informuje, że coś poszło nie tak.  Ten artykuł zawiera kilka sztuczek, których można użyć, aby znaleźć podstawowy problem i, miejmy nadzieję, uniknąć problemu w przyszłości.

## <a name="portal-notifications"></a>Powiadomienia portalu
Jeśli korzystasz z witryny Azure portal, pierwszym miejscem, na które należy zwrócić uwagę, jest **panel powiadomień**.  Panel powiadomień, dostępny na głównym pasku poleceń, klikając **ikonę dzwonka,** powie, czy maszyna wirtualna laboratorium lub tworzenie środowiska zakończyło się pomyślnie, czy nie.  Jeśli wystąpił błąd, zostanie wyświetlony komunikat o błędzie skojarzony z błędem tworzenia. Szczegóły często podają dalsze informacje, które pomogą Ci rozwiązać problem. W poniższym przykładzie tworzenie maszyny wirtualnej nie powiodło się z powodu wyczerpania rdzeni. Szczegółowy komunikat informuje, jak rozwiązać problem i zażądać zwiększenia przydziału podstawowego.

![Powiadomienie portalu platformy Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>Maszyna wirtualna w stanie korupcji
Jeśli stan maszyny wirtualnej w laboratorium jest **uszkodzony,** podstawowa maszyna wirtualna mogła zostać usunięta ze strony **Maszyny wirtualnej,** do której użytkownik może przejść ze strony **Maszyny wirtualne** (nie ze strony Laboratoria DevTest). Oczyść laboratorium w DevTest Labs, usuwając maszynę wirtualną z laboratorium. Następnie ponownie utworzyć maszynę wirtualną w laboratorium. 

![Maszyna wirtualna w stanie uszkodzonym](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Dzienniki aktywności
Spójrz na dzienniki aktywności, jeśli badasz błąd jakiś czas po próbie utworzenia maszyny Wirtualnej lub środowiska. W tej sekcji pokazano, jak znaleźć dzienniki dla maszyn wirtualnych i środowisk.

## <a name="activity-logs-for-virtual-machines"></a>Dzienniki aktywności dla maszyn wirtualnych

1. Na stronie głównej laboratorium wybierz maszynę wirtualną, aby uruchomić stronę **maszyny wirtualnej.**
2. Na stronie **Maszyna wirtualna** w sekcji **MONITOROWANIE** w menu po lewej stronie wybierz pozycję **Dziennik aktywności,** aby wyświetlić wszystkie dzienniki skojarzone z maszyną wirtualną.
3. W elementach dziennika aktywności wybierz operację, która nie powiodła się. Zazwyczaj operacja nie powiodła `Write Virtualmachines`się jest wywoływana .
4. W prawym okienku przełącz się na kartę JSON. Szczegóły są widoczne w widoku JSON dziennika.

    ![Dziennik aktywności maszyny Wirtualnej](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Przejrzyj dziennik JSON, `statusMessage` aż znajdziesz właściwość. Zawiera główny komunikat o błędzie i dalsze szczegółowe informacje, jeśli ma to zastosowanie. Następujący JSON jest przykładem dla podstawowego cytowany błąd przekroczony widział wcześniej w tym artykule.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Dziennik aktywności dla środowiska

Aby wyświetlić dziennik aktywności dla tworzenia środowiska, wykonaj następujące kroki:

1. Na stronie głównej laboratorium wybierz **pozycję Konfiguracja i zasady** w menu po lewej stronie.
2. na stronie **Konfiguracja i zasady** wybierz **pozycję Dzienniki aktywności** w menu.
3. Poszukaj błędu na liście działań w dzienniku i wybierz go.
4. W prawym okienku przełącz się na kartę JSON i poszukaj **statusuMessage**.

    ![Dziennik aktywności środowiska](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Dzienniki wdrażania szablonów Menedżera zasobów
Jeśli środowisko lub maszyna wirtualna została utworzona za pomocą automatyzacji, istnieje jedno ostatnie miejsce, aby szukać informacji o błędzie. To jest dziennik wdrażania szablonu usługi Azure Resource Manager. Gdy zasób laboratorium jest tworzony za pomocą automatyzacji, często odbywa się to za pomocą wdrożenia szablonu usługi Azure Resource Manager. Zobacz[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) przykładowe szablony usługi Azure Resource Manager, które tworzą zasoby DevTest Labs.

Aby wyświetlić dzienniki wdrażania szablonów laboratorium, wykonaj następujące kroki:

1. Uruchom stronę dla grupy zasobów, w której istnieje laboratorium.
2. Wybierz **wdrożeń** w menu po lewej stronie w obszarze **Ustawienia**.
3. Poszukaj wdrożeń o stanie nie powiodło się i wybierz je.
4. Na stronie **Wdrażanie** wybierz łącze **Szczegóły operacji** dla operacji, która nie powiodła się.
5. Zobaczysz szczegóły dotyczące operacji, która nie powiodła się w oknie **szczegóły operacji.**

## <a name="next-steps"></a>Następne kroki
Zobacz [Rozwiązywanie problemów z awariami artefaktów](devtest-lab-troubleshoot-artifact-failure.md)
