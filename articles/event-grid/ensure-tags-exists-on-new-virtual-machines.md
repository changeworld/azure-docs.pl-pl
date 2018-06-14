---
title: Integracja usługi Automatyzacja Azure z siatki zdarzeń | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można automatycznie dodać tag podczas tworzenia nowej maszyny Wirtualnej i wysyłania powiadomień do Teams firmy Microsoft.
keywords: automatyzacji elementu runbook, zespołów, zdarzenie siatki, maszyny wirtualnej, maszyna wirtualna
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
ms.openlocfilehash: 9a4d6ecf19fc96a9c7b92cf246effbf3948fb478
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2017
ms.locfileid: "26349074"
---
# <a name="integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Integracja usługi Automatyzacja Azure z siatki zdarzeń i zespoły firmy Microsoft

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importuj przykładowego elementu runbook zdarzeń siatki.
> * Utwórz element webhook opcjonalne Teams firmy Microsoft.
> * Tworzenie elementu webhook dla elementu runbook.
> * Utwórz subskrypcję zdarzeń siatki.
> * Utwórz maszynę Wirtualną, która wyzwala elementu runbook.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka [konto usługi Automatyzacja Azure](../automation/automation-offering-get-started.md) musi posiadać element runbook, który zostanie wywołany z subskrypcji Azure zdarzeń siatki.

## <a name="import-an-event-grid-sample-runbook"></a>Importuj przykładowego elementu runbook siatki zdarzeń
1. Wybierz konto automatyzacji, a następnie wybierz **elementów Runbook** strony.

   ![Wybierz elementy runbook](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Wybierz **galerii przeglądania** przycisku.

3. Wyszukaj **siatki zdarzeń**i wybierz **integrowania usługi Automatyzacja Azure z siatki zdarzeń**. 

    ![Importowanie elementu runbook z galerii](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Wybierz **importu** i nadaj mu nazwę **VMWrite czujki**.

5. Po zaimportował, wybierz **Edytuj** Aby wyświetlić źródło elementu runbook. Wybierz przycisk **Publikuj**.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Utwórz element webhook opcjonalne Teams firmy Microsoft
1. Teams firmy Microsoft, wybierz **więcej opcji** dalej, aby nazwa kanału, a następnie wybierz **łączniki**.

    ![Połączenia Teams firmy Microsoft](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Przewiń listę łączników do **przychodzącego elementu Webhook**i wybierz **Dodaj**.

3. Wprowadź **AzureAutomationIntegration** dla nazwy, a następnie wybierz **Utwórz**.

4. Kopiuj do Schowka elementu webhook i zapisz go. Adres URL elementu webhook jest używany do wysyłania informacji do Teams firmy Microsoft.

5. Wybierz **gotowe** można zapisać elementu webhook.

## <a name="create-a-webhook-for-the-runbook"></a>Tworzenie elementu webhook dla elementu runbook
1. Otwórz element runbook VMWrite czujki.

2. Wybierz **elementów Webhook**i wybierz **Dodawanie elementu Webhook** przycisku.

3. Wprowadź **WatchVMEventGrid** dla nazwy. Skopiuj adres URL do Schowka i zapisz go.

    ![Konfigurowanie nazwy elementu webhook](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Wybierz **Skonfiguruj parametry i parametry uruchomieniowe**, a następnie wprowadź adres URL elementu webhook Teams firmy Microsoft dla **CHANNELURL**. Pozostaw **WEBHOOKDATA** puste.

    ![Konfiguruj parametry elementu webhook](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Wybierz **OK** można utworzyć elementu webhook elementu runbook automatyzacji.


## <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji zdarzeń siatki
1. Na **konto automatyzacji** strony Przegląd, wybierz opcję **siatki zdarzeń**.

    ![Wybierz zdarzenie siatki](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Wybierz **+ subskrypcji zdarzeń** przycisku.

3. Konfigurowanie subskrypcji z następującymi informacjami:

    *   Wprowadź **AzureAutomation** dla nazwy.
    *   W **Typ tematu**, wybierz pozycję **subskrypcji platformy Azure**.
    *   Wyczyść **zasubskrybować wszystkie typy zdarzeń** pole wyboru.
    *   W **typów zdarzeń**, wybierz pozycję **Powodzenie zapisu zasobów**.
    *   W **punktu końcowego subskrybenta**, wprowadź adres URL elementu webhook VMWrite obserwowanie elementu runbook.
    *   W **prefiksu filtru**, wprowadź subskrypcji i grupy zasobów, której chcesz wyszukać nowe maszyny wirtualne utworzone. Powinna wyglądać:`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Wybierz **Utwórz** można zapisać subskrypcji zdarzeń siatki.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Utwórz maszynę Wirtualną, która wyzwala elementu runbook
1. Utwórz nową maszynę Wirtualną w grupie zasobów określone w przypadku subskrypcji siatki prefiksu filtru.

2. Powinna być wywoływana VMWrite obserwowanie elementu runbook i nowy znacznik dodane do maszyny Wirtualnej.

    ![Tag maszyny Wirtualnej](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Nowy komunikat jest wysyłany do kanału Teams firmy Microsoft.

    ![Powiadomienie Teams firmy Microsoft](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Następne kroki
Ten samouczek służy do konfigurowania integracji między siatki zdarzeń i automatyzacji. W tym samouczku omówiono:

> [!div class="checklist"]
> * Importuj przykładowego elementu runbook zdarzeń siatki.
> * Utwórz element webhook opcjonalne Teams firmy Microsoft.
> * Tworzenie elementu webhook dla elementu runbook.
> * Utwórz subskrypcję zdarzeń siatki.
> * Utwórz maszynę Wirtualną, która wyzwala elementu runbook.

> [!div class="nextstepaction"]
> [Tworzenie i trasy zdarzeń niestandardowych zdarzeń siatki](../event-grid/custom-event-quickstart.md)
