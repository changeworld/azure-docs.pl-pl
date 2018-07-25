---
title: Integracja usługi Azure Automation z usługą Event Grid | Microsoft Docs
description: Dowiedz się, jak automatycznie dodać tag po utworzeniu nowej maszyny wirtualnej i wysłać powiadomienie do usługi Microsoft Teams.
keywords: automation, runbook, teams, event grid, virtual machine, VM
services: automation
documentationcenter: ''
author: eamonoreilly
manager: ''
editor: ''
ms.service: automation
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: eamono
ms.openlocfilehash: 6270f8bad893798f46d8db91e7b1140b6a125350
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049868"
---
# <a name="integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Integracja usługi Azure Automation z usługami Event Grid i Microsoft Teams

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie przykładowego elementu runbook usługi Event Grid.
> * Tworzenie opcjonalnego elementu webhook usługi Microsoft Teams.
> * Tworzenie elementu webhook dla elementu runbook.
> * Tworzy subskrypcję usługi Event Grid.
> * Tworzenie maszyny wirtualnej, która wyzwala element runbook.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebujesz [konta usługi Azure Automation](../automation/automation-offering-get-started.md), na którym będzie przechowywany element runbook wyzwolony z subskrypcji usługi Azure Event Grid.

* Na koncie usługi Automation musi zostać załadowany moduł `AzureRM.Tags`. Zobacz [How to import modules in Azure Automation (Jak importować moduły w usłudze Azure Automation) ](../automation/automation-update-azure-modules.md), aby dowiedzieć się, jak importować moduły do usługi Azure Automation.

## <a name="import-an-event-grid-sample-runbook"></a>Importowanie przykładowego elementu runbook usługi Event Grid

1. Wybierz konto usługi Automation, a następnie wybierz stronę **Elementy runbook**.

   ![Wybieranie elementów runbook](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Wybierz przycisk **Przeglądaj galerię**.

3. Wyszukaj pozycję **Event Grid**, a następnie wybierz pozycję **Integrowanie usługi Azure Automation z usługą Event Grid**.

    ![Importowanie elementu runbook z galerii](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Wybierz polecenie **Importuj** i wprowadź nazwę **Watch-VMWrite**.

5. Po zaimportowaniu elementu runbook wybierz polecenie **Edytuj**, aby wyświetlić jego źródło. Wybierz przycisk **Publikuj**.

> [!NOTE]
> Wiersz 74 w skrypcie należy zmienić na `Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose`. Parametr `-Tags` to teraz `-Tag`.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Tworzenie opcjonalnego elementu webhook usługi Microsoft Teams

1. W usłudze Microsoft Teams wybierz pozycję **Więcej opcji** obok nazwy kanału, a następnie wybierz pozycję **Łączniki**.

    ![Połączenia usługi Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Przewiń listę łączników do pozycji **Przychodzący element webhook** i wybierz pozycję **Dodaj**.

3. Wprowadź nazwę **AzureAutomationIntegration**, a następnie wybierz pozycję **Utwórz**.

4. Skopiuj element webhook do schowka, a następnie zapisz go. Adres URL elementu webhook jest używany do wysyłania informacji do usługi Microsoft Teams.

5. Wybierz pozycję **Gotowe**, aby zapisać element webhook.

## <a name="create-a-webhook-for-the-runbook"></a>Tworzenie elementu webhook dla elementu runbook

1. Otwórz element runbook Watch-VMWrite.

2. Wybierz pozycję **Elementy webhook**, a następnie **Dodaj element webhook**.

3. Wprowadź nazwę **WatchVMEventGrid**. Skopiuj adres URL do schowka, a następnie zapisz go.

    ![Konfigurowanie nazwy elementu webhook](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Wybierz pozycję **Skonfiguruj parametry i parametry uruchomieniowe**, a następnie wprowadź adres URL elementu webhook usługi Microsoft Teams w polu **CHANNELURL**. Pozostaw pole **WEBHOOKDATA** puste.

    ![Konfigurowanie parametrów elementu webhook](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Wybierz pozycję **OK**, aby utworzyć element webhook dla elementu runbook usługi Automation.


## <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji usługi Event Grid
1. Na stronie omówienia **konta usługi Automation** wybierz pozycję **Event Grid**.

    ![Wybieranie usługi Event Grid](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Wybierz przycisk **+ Subskrypcja zdarzeń**.

3. Skonfiguruj subskrypcję przy użyciu następujących informacji:

    *   Wprowadź nazwę **AzureAutomation**.
    *   W obszarze **Typ tematu** wybierz opcję **Subskrypcje platformy Azure**.
    *   Wyczyść pole wyboru **Subskrybuj wszystkie typy zdarzeń**.
    *   W obszarze **Typy zdarzeń** wybierz pozycję **Powodzenie zapisu zasobów**.
    *   W obszarze **Punkt końcowy subskrybenta** wprowadź adres URL elementu webhook dla elementu runbook Watch-VMWrite.
    *   W obszarze **Filtr prefiksu** wprowadź subskrypcję i grupę zasobów, w której chcesz szukać nowo utworzonych maszyn wirtualnych. Powinno to wyglądać następująco: `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Wybierz pozycję **Utwórz**, aby zapisać subskrypcję usługi Event Grid.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Tworzenie maszyny wirtualnej, która wyzwala element runbook
1. Utwórz nową maszynę wirtualną w grupie zasobów określonej w filtrze prefiksu subskrypcji usługi Event Grid.

2. Element runbook Watch-VMWrite powinien zostać wywołany, a nowy tag dodany do maszyny wirtualnej.

    ![Tag maszyny wirtualnej](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Nowy komunikat zostanie wysłany do kanału usługi Microsoft Teams.

    ![Powiadomienie usługi Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Następne kroki
W tym samouczku skonfigurowano integrację usług Event Grid i Automation. W tym samouczku omówiono:

> [!div class="checklist"]
> * Importowanie przykładowego elementu runbook usługi Event Grid.
> * Tworzenie opcjonalnego elementu webhook usługi Microsoft Teams.
> * Tworzenie elementu webhook dla elementu runbook.
> * Tworzy subskrypcję usługi Event Grid.
> * Tworzenie maszyny wirtualnej, która wyzwala element runbook.

> [!div class="nextstepaction"]
> [Tworzenie i kierowanie zdarzeń niestandardowych za pomocą usługi Event Grid](../event-grid/custom-event-quickstart.md)
