---
title: Tworzenie, wyświetlanie i zarządzanie nimi metryk alertów klasycznych przy użyciu usługi Azure Monitor
description: Dowiedz się, jak użyć witryny Azure portal, interfejsu wiersza polecenia lub programu Powershell, aby tworzyć, wyświetlać i zarządzać klasyczne reguły alertów metryk.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.openlocfilehash: 4a225dbc8e84d65a6ea25f63627599e5bb7d2ced
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66129759"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Tworzenie, wyświetlanie i zarządzanie metryki alertów klasycznych przy użyciu usługi Azure Monitor

Klasyczne alertów dotyczących metryk w usłudze Azure Monitor zapewnia sposób otrzymywania powiadomień, gdy jeden metryki przekroczą próg. Klasyczne alertów dotyczących metryk jest starszych funkcji, która służy do wyzwalania alertu metryki-wymiarowej tylko. Brak istniejących nowszej funkcję o nazwie alertów dotyczących metryk, który zawiera udoskonalone funkcje za pośrednictwem klasycznego alertów dotyczących metryk. Dowiedz się więcej na temat nowych funkcji alertów dotyczących metryk w programie [Przegląd alertów metryk](../../azure-monitor/platform/alerts-metric-overview.md). W tym artykule opisujemy sposób tworzyć, wyświetlać i zarządzać klasycznego reguł alertów dotyczących metryk za pośrednictwem witryny Azure portal, programu Powershell i wiersza polecenia platformy Azure.

## <a name="with-azure-portal"></a>Za pomocą witryny Azure portal

1. W [portal](https://portal.azure.com/), Znajdź zasób, który chcesz monitorować, a następnie wybierz ją.

2. W **monitorowanie** zaznacz **alerty (klasyczne)** . Tekst i ikona może się nieco różnić dla różnych zasobów. Jeśli nie możesz znaleźć **alerty (klasyczne)** w tym miejscu może okazać się w **alerty** lub **reguł alertów**.

    ![Monitorowanie](media/alerts-classic-portal/AlertRulesButton.png)

3. Wybierz **Dodaj alert dotyczący metryki (wersja klasyczna)** polecenia, a następnie wypełnij pola.

    ![Dodaj Alert](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Nazwa** regułę alertu. Następnie wybierz **opis**, który pojawia się również w wiadomości e-mail z powiadomieniem.

5. Wybierz **metryki** , którą chcesz monitorować. Następnie wybierz **warunek** i **próg** wartość metryki. Również wybrać **okres** czasu, przez jaki reguła metryki muszą być spełnione przed wyzwala alert. Na przykład jeśli używasz okresu "w ciągu ostatnich 5 minut", a alert szuka procesora CPU przekracza 80%, alert wyzwala po Procesora stale powyżej 80% przez 5 minut. Po pierwszym wyzwalacza ma miejsce ponownie wyzwala podczas Procesora pozostaje poniżej 80% przez 5 minut. Pomiar metryki użycia Procesora odbywa się co minutę.

6. Wybierz **wiadomości E-mail właścicielom...**  chcącym administratorów i współadministratorów, aby otrzymywać powiadomienia e-mail, gdy zostanie wyzwolony alert.

7. Jeśli chcesz wysyłać powiadomienia na adresy e-mail dodatkowych, gdy zostanie wyzwolony alert, dodaj je w **email(s) dodatkowego administratora** pola. Wiele wiadomości e-mail należy rozdzielić średnikami w następującym formacie: *e-mail\@contoso.com;email2\@contoso.com*

8. Umieść w prawidłowym identyfikatorem URI w **elementu Webhook** pola, jeśli ma ona wywoływana, gdy zostanie wyzwolony alert.

9. Jeśli używasz usługi Azure Automation, możesz wybrać element runbook mógł działać, gdy zostanie wyzwolony alert.

10. Wybierz pozycję **OK**, aby utworzyć alert.

W ciągu kilku minut ten alert jest aktywny i wyzwala w sposób opisany wcześniej.

Po utworzeniu alertu, można go zaznaczyć i wykonaj jedną z następujących czynności:

* Wyświetl wykres pokazujący próg metryki i rzeczywiste wartości z poprzedniego dnia.
* Edytuj lub usuń go.
* **Wyłącz** lub **Włącz** jej, jeśli chcesz tymczasowo zatrzymać lub wznowić odbieranie powiadomień dla tego alertu.

## <a name="with-azure-cli"></a>Z interfejsem wiersza polecenia platformy Azure

Przedstawione w poprzednich sekcjach opisano, jak tworzyć, wyświetlać i zarządzać reguł alertów dotyczących metryk za pomocą witryny Azure portal. W tej sekcji opisano sposób zrobić to samo za pomocą wielu platform [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). To najszybszy sposób rozpocząć korzystanie z wiersza polecenia platformy Azure za pośrednictwem [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Pobierz wszystkie klasyczne reguł alertów dotyczących metryk w grupie zasobów

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Szczegółowe informacje o określonym klasycznego reguła alertu metryki

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Utwórz regułę klasycznego alertu metryki

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Usuń regułę klasycznego alertu metryki

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>Z programem PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tej sekcji pokazano, jak za pomocą programu PowerShell, poleceń tworzyć, wyświetlać i zarządzać klasycznego alertów dotyczących metryk. Przykłady w artykule pokazano, jak można użyć poleceń cmdlet usługi Azure Monitor dla klasycznego alertów dotyczących metryk.

1. Jeśli jeszcze ich, konfigurowanie programu PowerShell do uruchamiania na komputerze. Aby uzyskać więcej informacji, zobacz [jak instalowanie i konfigurowanie programu PowerShell](/powershell/azure/overview). Możesz również przejrzeć całą listę Azure Monitor poleceń cmdlet programu PowerShell w [poleceń cmdlet usługi Azure Monitor (Insights)](https://docs.microsoft.com/powershell/module/az.applicationinsights).

2. Najpierw zaloguj się do subskrypcji platformy Azure.

    ```powershell
    Connect-AzAccount
    ```

3. Zostanie wyświetlony ekran logowania. Po zalogowaniu się na Twoim koncie, identyfikator dzierżawy, a domyślny identyfikator subskrypcji są wyświetlane. Wszystkie polecenia cmdlet platformy Azure działa w ramach subskrypcji domyślnej. Aby wyświetlić listę subskrypcji masz dostęp, użyj następującego polecenia:

    ```powershell
    Get-AzSubscription
    ```

4. Aby zmienić kontekst pracy do innej subskrypcji, użyj następującego polecenia:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Możesz pobrać wszystkie klasyczne reguły alertów metryk dla grupy zasobów:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Możesz wyświetlić szczegóły klasycznej reguły alertu metryki

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Możesz pobrać wszystkich reguł alertów dla zasobu docelowego. Na przykład wszystkich reguł alertów jest ustawiony na maszynie Wirtualnej.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Nie jest już można tworzyć reguły alertów klasycznych za pomocą programu PowerShell. Aby utworzyć regułę alertu, należy użyć nowego ["Add-AzMetricAlertRule"](/powershell/module/az.monitor/add-azmetricalertrule) polecenia.

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie klasycznego alertu metryki za pomocą szablonu usługi Resource Manager](../../azure-monitor/platform/alerts-enable-template.md).
- [Masz klasycznego alertu metryki powiadomić systemu poza platformą Azure za pomocą elementu webhook](../../azure-monitor/platform/alerts-webhooks.md).
