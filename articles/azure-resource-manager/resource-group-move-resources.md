---
title: Przenoszenie zasobów platformy Azure do nowej subskrypcji lub grupy zasobów | Dokumentacja firmy Microsoft
description: Umożliwia przenoszenie zasobów do nowej grupy zasobów lub subskrypcji usługi Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 01ec8facf2771de9ec01b9470521340a59ee4d0d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721381"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji

W tym artykule pokazano, jak przenieść zasoby platformy Azure do innej subskrypcji platformy Azure lub innej grupy zasobów w ramach tej samej subskrypcji. Instrukcję przenoszenia zasobów, można użyć witryny Azure portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

Grupy źródłowej i docelowej grupy są zablokowane podczas operacji przenoszenia. Pisanie i operacje usuwania są zablokowane na temat grup zasobów, dopiero po zakończeniu przenoszenia. Ta blokada oznacza, że nie Dodawanie, aktualizowanie lub usuwanie zasobów w grupach zasobów, ale nie oznacza, że zasoby są zablokowane. Na przykład jeśli zostanie przeniesiony do nowej grupy zasobów programu SQL Server i jego bazy danych, aplikacji korzystającej z bazy danych środowisk bez przestojów. Nadal może odczytywać i zapisywać w bazie danych.

Przenoszenie zasobu tylko przenosi je do nowej grupy zasobów lub subskrypcji. Go nie zmienia lokalizację zasobu.

## <a name="checklist-before-moving-resources"></a>Listę kontrolną przed przenoszeniem zasobów

Przed przeniesieniem zasobu należy wykonać kilka ważnych czynności. Dzięki sprawdzeniu tych warunków można uniknąć błędów.

1. Zasoby, które chcesz przenieść musi obsługiwać operacji przenoszenia. Aby uzyskać listę zasobów obsługiwanych przenoszenia, zobacz [przenoszenie obsługi operacji dla zasobów](move-support-resources.md).

1. Niektóre usługi mają pewne ograniczenia i wymagania, podczas przenoszenia zasobów. Jeśli masz, przenosząc dowolne z następujących usług, sprawdź Niniejsze wytyczne przed przeniesieniem.

   * [App Services przenieść wskazówki](./move-limitations/app-service-move-limitations.md)
   * [Usługi Azure DevOps Przenieś wskazówki](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Wskazówki dotyczące przenoszenia modelu wdrożenia klasycznego](./move-limitations/classic-model-move-limitations.md) -Classic Compute, klasycznego magazynu, klasycznych sieci wirtualnych i usług w chmurze
   * [Usługi Recovery Services przenieść wskazówki](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Maszyny wirtualne przenieść wskazówki](./move-limitations/virtual-machines-move-limitations.md)
   * [Sieci wirtualne przenieść wskazówki](./move-limitations/virtual-network-move-limitations.md)

1. Subskrypcje źródłowe i docelowe muszą być aktywne. Jeśli masz problem z kontem, które zostało wyłączone, umożliwiając [utworzyć żądanie pomocy technicznej platformy Azure](../azure-supportability/how-to-create-azure-support-request.md). Wybierz **zarządzania subskrypcjami** jako typ problemu.

1. Subskrypcje źródłowe i docelowe muszą istnieć w tej samej [dzierżawy usługi Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Aby sprawdzić, czy obie subskrypcje mają ten sam identyfikator dzierżawy, należy użyć programu Azure PowerShell lub wiersza polecenia platformy Azure.

   Dla programu Azure PowerShell użyj polecenia:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Jeśli identyfikatorów dzierżawy subskrypcje źródłowe i docelowe nie są takie same, należy użyć następujących metod, aby uzgodnić identyfikatorów dzierżawy:

   * [Transfer ownership of an Azure subscription to another account](../billing/billing-subscription-transfer.md) (Przenoszenie własności subskrypcji platformy Azure na inne konto)
   * [Jak skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Subskrypcja docelowa musi być zarejestrowana dla dostawcy przenoszonego zasobu. Jeśli nie, pojawi się komunikat o błędzie informujący, że **subskrypcja nie jest zarejestrowana dla typu zasobu**. Możesz zobaczyć ten błąd podczas przenoszenia zasobu do nowej subskrypcji, ale ta subskrypcja nie była nigdy używana z tym typem zasobu.

   W przypadku programu PowerShell Użyj następujących poleceń, można pobrać stanu rejestracji:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Aby zarejestrować dostawcę zasobów, należy użyć:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Wiersza polecenia platformy Azure Użyj następujących poleceń, aby uzyskać stan rejestracji:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Aby zarejestrować dostawcę zasobów, należy użyć:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Konto przenoszenia zasobów musi mieć co najmniej następujące uprawnienia:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** na źródłową grupę zasobów.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** w docelowej grupie zasobów.

1. Przed przeniesieniem zasoby, sprawdź limity przydziału subskrypcji dla subskrypcji, którą przenosisz zasoby. Jeśli przenoszenia zasobów oznacza, że subskrypcja przekroczy limit, należy przejrzeć, czy możesz poprosić o zwiększenie limitu przydziału. Aby uzyskać listę limitów i sposób poprosić o zwiększenie zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).

## <a name="validate-move"></a>Waliduj przeniesienie

[Zweryfikować operacji przeniesienia](/rest/api/resources/resources/validatemoveresources) umożliwia testowanie scenariusza przenoszenia bez faktycznego przenoszenia zasobów. Użyj tej operacji, aby sprawdzić, przeniesienie, zostanie wykonane pomyślnie. Sprawdzanie poprawności automatycznie jest wywoływana, gdy wysyłasz żądanie przeniesienia. Tylko wtedy, gdy trzeba wstępnie określić wyniki przy użyciu tej operacji. Aby wykonać tę operację, musisz mieć:

* Nazwa źródłowa grupa zasobów
* Identyfikator zasobu docelowa grupa zasobów
* Identyfikator zasobu poszczególnych zasobów, aby przenieść
* [token dostępu](/rest/api/azure/#acquire-an-access-token) dla swojego konta

Wyślij żądanie następujące:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Z treści żądania:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Jeśli żądanie jest prawidłowo sformatowany, zwraca wartość operacji:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Kod stanu 202 wskazuje żądanie weryfikacji został zaakceptowany, ale nie zostało jeszcze nieustalona, operację przenoszenia, zostanie wykonana pomyślnie. `location` Wartość zawiera adres URL, którego używasz, aby sprawdzić stan operacji długotrwałej.  

Aby sprawdzić stan, Wyślij następujące żądanie:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Podczas operacji jest nadal uruchomione, nadal jest wyświetlany kod stanu 202. Oczekiwania w sekundach `retry-after` wartość przed podjęciem ponownej próby. Jeśli sprawdzanie poprawności operacji przenoszenia przebiegnie pomyślnie, zostanie wyświetlony kod stanu 204. W przypadku niepowodzenia weryfikacji przeniesienia pojawi się komunikat o błędzie, takich jak:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Używanie portalu

Instrukcję przenoszenia zasobów, wybierz grupę zasobów za pomocą tych zasobów, a następnie wybierz **przenieść** przycisku.

![Przenoszenie zasobów](./media/resource-group-move-resources/select-move.png)

Wybierz, czy przenosisz zasoby do nowej grupy zasobów lub nowej subskrypcji.

Wybierz zasoby do przeniesienia i docelowa grupa zasobów. Potwierdzić, że należy zaktualizować skrypty dla tych zasobów, a następnie wybierz pozycję **OK**. Jeśli w poprzednim kroku wybrano opcję edycji Ikona subskrypcji, możesz również wybrać subskrypcja docelowa.

![Wybierz miejsce docelowe](./media/resource-group-move-resources/select-destination.png)

W **powiadomienia**, zobaczysz, że działa operacji przenoszenia.

![Pokaż stan przenoszenia](./media/resource-group-move-resources/show-status.png)

Po ukończeniu, otrzymasz powiadomienie o wyniku.

![Pokaż wynik przenoszenia](./media/resource-group-move-resources/show-result.png)

Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów dotyczących przenoszenia zasobów platformy Azure do nowej grupy zasobów lub subskrypcji](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj [AzResource przenoszenia](/powershell/module/az.resources/move-azresource) polecenia. Poniższy przykład pokazuje, jak przenieść kilka zasobów do nowej grupy zasobów.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Aby przejść do nowej subskrypcji, należy dołączyć wartość dla `DestinationSubscriptionId` parametru.

Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów dotyczących przenoszenia zasobów platformy Azure do nowej grupy zasobów lub subskrypcji](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj [przenoszenia zasobów az](/cli/azure/resource?view=azure-cli-latest#az-resource-move) polecenia. Podaj identyfikatory zasobów można przenieść zasobu. Poniższy przykład pokazuje, jak przenieść kilka zasobów do nowej grupy zasobów. W `--ids` parametru, podaj listę rozdzielonych spacjami identyfikatorów można przenieść zasobów.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Aby przejść do nowej subskrypcji, podać `--destination-subscription-id` parametru.

Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów dotyczących przenoszenia zasobów platformy Azure do nowej grupy zasobów lub subskrypcji](troubleshoot-move.md).

## <a name="use-rest-api"></a>Korzystanie z interfejsu API REST

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj [przenoszenia zasobów](/rest/api/resources/Resources/MoveResources) operacji.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

W treści żądania należy określić docelową grupę zasobów i zasobów, aby przenieść.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów dotyczących przenoszenia zasobów platformy Azure do nowej grupy zasobów lub subskrypcji](troubleshoot-move.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę zasobów obsługiwanych przenoszenia, zobacz [przenoszenie obsługi operacji dla zasobów](move-support-resources.md).
