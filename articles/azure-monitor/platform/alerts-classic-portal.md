---
title: Tworzenie, wyświetlanie i zarządzanie klasycznymi alertami metryk za pomocą usługi Azure Monitor
description: Dowiedz się, jak używać witryny Azure portal, interfejsu wiersza polecenia lub programu Powershell do tworzenia, wyświetlania i zarządzania klasycznymi regułami alertów metryk.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/18/2018
ms.openlocfilehash: b770b9bd34c8267889db536ec81332de32cb8776
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668319"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Tworzenie, wyświetlanie i zarządzanie klasycznymi alertami metryk przy użyciu usługi Azure Monitor

Klasyczne alerty metryki w usłudze Azure Monitor umożliwiają uzyskanie powiadomienia, gdy jedna z metryk przekroczy próg. Alerty metryki klasycznej to starsza funkcja, która umożliwia alerty tylko w przypadku metryk niewymiarowych. Istnieje istniejąca nowsza funkcja o nazwie alerty metryki, która ma ulepszoną funkcjonalność za nikim klasycznym alertów metryk. Więcej informacji o nowych funkcjach alertów metryk można dowiedzieć się więcej w [przeglądzie alertów metryk.](../../azure-monitor/platform/alerts-metric-overview.md) W tym artykule opisano, jak tworzyć, wyświetlać i zarządzać klasycznymi regułami alertów metryk za pośrednictwem witryny Azure portal, interfejsu wiersza polecenia platformy Azure i programu Powershell.

## <a name="with-azure-portal"></a>Dzięki witrynie Azure portal

1. W [portalu](https://portal.azure.com/)znajdź zasób, który chcesz monitorować, a następnie wybierz go.

2. W sekcji **MONITOROWANIE** wybierz **pozycję Alerty (klasyczny)**. Tekst i ikona mogą się nieznacznie różnić w zależności od zasobów. Jeśli nie znajdziesz **alertów (klasycznych)** tutaj, możesz je znaleźć w alertach lub **regułach alertów.** **Alert Rules**

    ![Monitorowanie](media/alerts-classic-portal/AlertRulesButton.png)

3. Wybierz polecenie **Dodaj alert metryki (klasyczny),** a następnie wypełnij pola.

    ![Dodaj alert](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Nazwij** regułę alertu. Następnie wybierz **opis**, który pojawia się również w wiadomościach e-mail z powiadomieniami.

5. Wybierz **metrykę,** którą chcesz monitorować. Następnie wybierz **Condition** i **Threshold** wartość dla metryki. Należy również wybrać **okres** czasu, który reguła metryki musi być spełniony przed wyzwalania alertu. Na przykład jeśli używasz okresu "W ciągu ostatnich 5 minut", a alert szuka procesora CPU powyżej 80%, alert wyzwala, gdy procesor był stale powyżej 80% przez 5 minut. Po wystąpieniu pierwszego wyzwalacza wyzwala się ponownie, gdy procesor pozostanie poniżej 80% przez 5 minut. Pomiar metryki procesora cpu odbywa się co minutę.

6. Wybierz **właścicieli wiadomości e-mail...** jeśli chcesz, aby administratorzy i współadministratorzy otrzymywali powiadomienia e-mail po uruchomieniu alertu.

7. Jeśli chcesz wysyłać powiadomienia na dodatkowe adresy e-mail po uruchomieniu alertu, dodaj je w polu **Wiadomości e-mail dodatkowego administratora.** Oddziel wiele wiadomości e-mail średnikami w następującym formacie: *e-mail\@\@contoso.com;email2 contoso.com*

8. Umieść prawidłowy identyfikator URI w polu **Webhook,** jeśli chcesz, aby był wywoływany po uruchomieniu alertu.

9. Jeśli używasz usługi Azure Automation, można wybrać system runbook do uruchomienia po uruchomieniu alertu.

10. Wybierz pozycję **OK**, aby utworzyć alert.

W ciągu kilku minut alert jest aktywny i wyzwala, jak opisano wcześniej.

Po utworzeniu alertu można go wybrać i wykonać jedno z następujących zadań:

* Wyświetl wykres przedstawiający próg metryki i rzeczywiste wartości z poprzedniego dnia.
* Edytuj lub usuń go.
* **Wyłącz** lub **włącz** go, jeśli chcesz tymczasowo zatrzymać lub wznowić otrzymywanie powiadomień dla tego alertu.

## <a name="with-azure-cli"></a>Z interfejsem wiersza polecenia platformy Azure

W poprzednich sekcjach opisano sposób tworzenia, wyświetlania i zarządzania regułami alertów metryk przy użyciu witryny Azure Portal. W tej sekcji opisano, jak zrobić to samo przy użyciu wieloplatformowego [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) Najszybszym sposobem rozpoczęcia korzystania z interfejsu wiersza polecenia platformy Azure jest [usługa Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Pobierz wszystkie klasyczne reguły alertów metryk w grupie zasobów

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Zobacz szczegóły określonej klasycznej reguły alertów metryki

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Tworzenie klasycznej reguły alertu metryki

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Usuwanie klasycznej reguły alertu metryki

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>Z programem PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tej sekcji pokazano, jak używać poleceń programu PowerShell do tworzenia, wyświetlania i zarządzania klasycznymi alertami metryk. Przykłady w tym artykule ilustrują, jak można używać poleceń cmdlet usługi Azure Monitor dla alertów metryk klasycznych.

1. Jeśli jeszcze tego nie zrobiłeś, skonfiguruj program PowerShell do uruchamiania na komputerze. Aby uzyskać więcej informacji, zobacz [Jak zainstalować i skonfigurować program PowerShell](/powershell/azure/overview). Można również przejrzeć całą listę poleceń cmdlet programu Azure Monitor PowerShell w poleceniach [cmdlet usługi Azure Monitor (Insights).](https://docs.microsoft.com/powershell/module/az.applicationinsights)

2. Najpierw zaloguj się do subskrypcji platformy Azure.

    ```powershell
    Connect-AzAccount
    ```

3. Zostanie wyświetlony ekran logowania. Po zalogowaniu się na swoje konto, TenantID i domyślny identyfikator subskrypcji są wyświetlane. Wszystkie polecenia cmdlet platformy Azure działają w kontekście domyślnej subskrypcji. Aby wyświetlić listę subskrypcji, do których masz dostęp, użyj następującego polecenia:

    ```powershell
    Get-AzSubscription
    ```

4. Aby zmienić kontekst pracy na inną subskrypcję, użyj następującego polecenia:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. W grupie zasobów można pobrać wszystkie klasyczne reguły alertów metryki:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Możesz wyświetlić szczegóły klasycznej reguły alertów metryk

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Można pobrać wszystkie reguły alertów ustawione dla zasobu docelowego. Na przykład wszystkie reguły alertów ustawione na maszynie Wirtualnej.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Klasyczne reguły alertów nie mogą być już tworzone za pośrednictwem programu PowerShell. Aby utworzyć regułę alertu, należy użyć nowego polecenia ["Add-AzMetricAlertRule".](/powershell/module/az.monitor/add-azmetricalertrule)

## <a name="next-steps"></a>Następne kroki

- [Utwórz klasyczny alert metryki z szablonem Menedżera zasobów](../../azure-monitor/platform/alerts-enable-template.md).
- [Mieć klasyczny alert metryki powiadamiać system nie platformy Azure za pomocą elementu webhook](../../azure-monitor/platform/alerts-webhooks.md).
