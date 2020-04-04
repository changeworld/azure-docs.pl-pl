---
title: Przenoszenie zasobów do nowej subskrypcji lub grupy zasobów
description: Usługa Azure Resource Manager umożliwia przenoszenie zasobów do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: ffb5f8be81d3628084d127db404ab994d4d5b938
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631505"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji

W tym artykule pokazano, jak przenieść zasoby platformy Azure do innej subskrypcji platformy Azure lub innej grupy zasobów w ramach tej samej subskrypcji. Do przenoszenia zasobów możesz użyć witryny Azure Portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

Zarówno grupa źródłowa, jak i grupa docelowa są zablokowane podczas operacji przenoszenia. Operacje zapisu i usuwania na grupach zasobów są blokowane do momentu zakończenia przenoszenia. Ta blokada oznacza, że nie można dodawać, aktualizować ani usuwać zasobów w grupach zasobów. Nie oznacza to, że zasoby są zamrożone. Jeśli na przykład przeniesiesz program SQL Server i jego bazę danych do nowej grupy zasobów, nie dojdzie do przestoju aplikacji korzystającej z tej bazy danych. Nadal będzie możliwe odczytywanie i zapisywanie danych w bazie danych. Blokada może trwać maksymalnie cztery godziny, ale większość ruchów kończy się w znacznie krótszym czasie.

Przeniesienie zasobu powoduje jedynie przeniesienie go do nowej grupy zasobów lub subskrypcji. Operacja nie może zmienić lokalizacji zasobu.

## <a name="checklist-before-moving-resources"></a>Sporządzenie listy kontrolnej przed przeniesieniem zasobów

Przed przeniesieniem zasobu należy wykonać kilka ważnych czynności. Dzięki sprawdzeniu tych warunków można uniknąć błędów.

1. Zasoby, które chcesz przenieść, muszą obsługiwać operację przenoszenia. Aby uzyskać listę zasobów, które obsługują przenoszenie zasobów, zobacz [Przenoszenie obsługi operacji dla zasobów](move-support-resources.md).

1. Niektóre usługi mają określone ograniczenia lub wymagania podczas przenoszenia zasobów. Jeśli przenosisz dowolną z następujących usług, sprawdź te wskazówki przed przeprowadzką.

   * [Wskazówki dotyczące przenoszenia usług aplikacji](./move-limitations/app-service-move-limitations.md)
   * [Wskazówki dotyczące przenoszenia usług DevOps platformy Azure](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Wskazówki dotyczące przenoszenia modelu wdrażania klasycznego](./move-limitations/classic-model-move-limitations.md) — klasyczne obliczenia, klasyczna pamięć masowa, klasyczne sieci wirtualne i usługi w chmurze
   * [Wskazówki dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md)
   * [Wskazówki dotyczące przenoszenia usług odzyskiwania](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Maszyny wirtualne przenoszą wskazówki](./move-limitations/virtual-machines-move-limitations.md)

1. Subskrypcje źródłowe i docelowe muszą być aktywne. Jeśli masz problemy z włączeniem konta, które zostało wyłączone, [utwórz żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Wybierz **pozycję Zarządzanie subskrypcjami** dla typu problemu.

1. Subskrypcje źródłowe i docelowe muszą istnieć w obrębie tej samej [dzierżawy usługi Azure Active Directory.](../../active-directory/develop/quickstart-create-new-tenant.md) Aby sprawdzić, czy obie subskrypcje mają ten sam identyfikator dzierżawy, należy użyć usługi Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

   W przypadku programu Azure PowerShell użyj:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Jeśli identyfikatory dzierżawy subskrypcji źródłowych i docelowych nie są takie same, użyj następujących metod, aby uzgodnić identyfikatory dzierżawy:

   * [Transfer ownership of an Azure subscription to another account](../../billing/billing-subscription-transfer.md) (Przenoszenie własności subskrypcji platformy Azure na inne konto)
   * [Jak skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Subskrypcja docelowa musi być zarejestrowana dla dostawcy przenoszonego zasobu. Jeśli nie, zostanie wyświetlony błąd informujący, że **subskrypcja nie jest zarejestrowana dla typu zasobu**. Ten błąd może wystąpić podczas przenoszenia zasobu do nowej subskrypcji, ale ta subskrypcja nigdy nie była używana z tym typem zasobu.

   W przypadku programu PowerShell użyj następujących poleceń, aby uzyskać status rejestracji:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Aby zarejestrować dostawcę zasobów, użyj:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   W przypadku interfejsu wiersza polecenia platformy Azure użyj następujących poleceń, aby uzyskać stan rejestracji:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Aby zarejestrować dostawcę zasobów, użyj:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Konto przenoszące zasoby musi mieć co najmniej następujące uprawnienia:

   * **Zasoby firmy Microsoft/subskrypcje/zasobyGroups/moveResources/action** w źródłowej grupie zasobów.
   * **Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/zapis** w docelowej grupie zasobów.

1. Przed przeniesieniem zasobów sprawdź przydziały subskrypcji dla subskrypcji, do której przenosisz zasoby. Jeśli przeniesienie zasobów oznacza, że subskrypcja przekroczy swoje limity, należy przejrzeć, czy można zażądać zwiększenia przydziału. Aby uzyskać listę limitów i jak zażądać zwiększenia, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).

1. **W przypadku przenoszenia między subskrypcjami zasób i jego zasoby zależne muszą znajdować się w tej samej grupie zasobów i muszą zostać przeniesione razem.** Na przykład maszyna wirtualna z dyskami zarządzanymi wymagałaby przeniesienia maszyny Wirtualnej i dysków zarządzanych wraz z innymi zasobami zależnymi.

   Jeśli przenosisz zasób do nowej subskrypcji, sprawdź, czy zasób ma zasoby zależne i czy znajdują się w tej samej grupie zasobów. Jeśli zasoby nie należy do tej samej grupy zasobów, sprawdź, czy zasoby można skonsolidować w tę samą grupę zasobów. Jeśli tak, przenieś wszystkie te zasoby do tej samej grupy zasobów przy użyciu operacji przenoszenia między grupami zasobów.

   Aby uzyskać więcej informacji, zobacz [Scenariusz przechodzenia między subskrypcjami](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Scenariusz przenoszenia między subskrypcjami

Przenoszenie zasobów z jednej subskrypcji do drugiej jest procesem trzyetapowym:

![scenariusz przenoszenia między subskrypcjami](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

W celach ilustracyjnych mamy tylko jeden zasób zależny.

* Krok 1: Jeśli zasoby zależne są rozdzielane między różne grupy zasobów, najpierw przenieś je do jednej grupy zasobów.
* Krok 2: Przenieś zasobów i zasobów zależnych razem z subskrypcji źródłowej do subskrypcji docelowej.
* Krok 3: Opcjonalnie redystrybuuj zasoby zależne do różnych grup zasobów w ramach subskrypcji docelowej.

## <a name="validate-move"></a>Sprawdzanie poprawności przenoszenia

[Operacja przenoszenia sprawdzania poprawności](/rest/api/resources/resources/validatemoveresources) umożliwia przetestowanie scenariusza przenoszenia bez przenoszenia zasobów. Ta operacja służy do sprawdzania, czy przeniesienie zakończy się pomyślnie. Sprawdzanie poprawności jest wywoływana automatycznie podczas wysyłania żądania przeniesienia. Tej operacji należy używać tylko wtedy, gdy trzeba wstępnie określić wyniki. Aby uruchomić tę operację, potrzebujesz:

* nazwa źródłowej grupy zasobów
* Identyfikator zasobu docelowej grupy zasobów
* identyfikator zasobu każdego zasobu do przeniesienia
* [token dostępu](/rest/api/azure/#acquire-an-access-token) dla Twojego konta

Wyślij następujące żądanie:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Z treścią żądania:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Jeśli żądanie zostanie poprawnie sformatowane, operacja zwraca:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Kod stanu 202 wskazuje, że żądanie sprawdzania poprawności zostało zaakceptowane, ale nie zostało jeszcze określone, czy operacja przenoszenia zakończy się pomyślnie. Wartość `location` zawiera adres URL używany do sprawdzania stanu długotrwałej operacji.  

Aby sprawdzić stan, wyślij następujące żądanie:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Gdy operacja jest nadal uruchomiona, nadal otrzymujesz kod stanu 202. Odczekaj liczbę sekund `retry-after` wskazaną w wartości przed ponowną próbą. Jeśli operacja przenoszenia zostanie pomyślnie zweryfikowana, zostanie wyświetlony kod stanu 204. Jeśli sprawdzanie poprawności przenoszenia nie powiedzie się, zostanie wyświetlony komunikat o błędzie, taki jak:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Używanie portalu

Aby przenieść zasoby, wybierz grupę zasobów z tymi zasobami, a następnie wybierz przycisk **Przenieś.**

![przenoszenie zasobów](./media/move-resource-group-and-subscription/select-move.png)

Wybierz, czy zasoby są przesuniane do nowej grupy zasobów, czy do nowej subskrypcji.

Wybierz zasoby do przeniesienia i docelową grupę zasobów. Potwierdź, że musisz zaktualizować skrypty dla tych zasobów i wybierz **przycisk OK**. Jeśli w poprzednim kroku wybrano ikonę edycji subskrypcji, należy również wybrać subskrypcję docelową.

![wybierz miejsce docelowe](./media/move-resource-group-and-subscription/select-destination.png)

W **obszarze Powiadomienia**zobaczysz, że operacja przenoszenia jest uruchomiona.

![pokaż stan przenoszenia](./media/move-resource-group-and-subscription/show-status.png)

Po jego zakończeniu zostaniesz powiadomiony o wyniku.

![pokaż wynik przenoszenia](./media/move-resource-group-and-subscription/show-result.png)

Jeśli pojawi się błąd, zobacz [Rozwiązywanie problemów z przenoszeniem zasobów platformy Azure do nowej grupy zasobów lub subskrypcji](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, należy użyć polecenia [Move-AzResource.](/powershell/module/az.resources/move-azresource) W poniższym przykładzie pokazano, jak przenieść kilka zasobów do nowej grupy zasobów.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Aby przejść do nowej subskrypcji, należy `DestinationSubscriptionId` dołączyć wartość parametru.

Jeśli pojawi się błąd, zobacz [Rozwiązywanie problemów z przenoszeniem zasobów platformy Azure do nowej grupy zasobów lub subskrypcji](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj polecenia [przenoszenia zasobów az.](/cli/azure/resource?view=azure-cli-latest#az-resource-move) Podaj identyfikatory zasobów do przeniesienia. W poniższym przykładzie pokazano, jak przenieść kilka zasobów do nowej grupy zasobów. W `--ids` parametrze podaj oddzieloną spację listę identyfikatorów zasobów do przeniesienia.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Aby przejść do nowej subskrypcji, podaj `--destination-subscription-id` parametr.

Jeśli pojawi się błąd, zobacz [Rozwiązywanie problemów z przenoszeniem zasobów platformy Azure do nowej grupy zasobów lub subskrypcji](troubleshoot-move.md).

## <a name="use-rest-api"></a>Korzystanie z interfejsu API REST

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj operacji [Przenieś zasoby.](/rest/api/resources/Resources/MoveResources)

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

W treści żądania należy określić docelową grupę zasobów i zasoby do przeniesienia.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Jeśli pojawi się błąd, zobacz [Rozwiązywanie problemów z przenoszeniem zasobów platformy Azure do nowej grupy zasobów lub subskrypcji](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pytanie: Moja operacja przenoszenia zasobów, która zwykle trwa kilka minut, działa prawie godzinę. Czy coś jest nie tak?**

Przenoszenie zasobu jest operacją złożoną, która ma różne fazy. Może obejmować więcej niż tylko dostawcę zasobów zasobu, który próbujesz przenieść. Ze względu na zależności między dostawcami zasobów usługa Azure Resource Manager umożliwia ukończenie operacji przez 4 godziny. Ten okres daje dostawcom zasobów szansę na odzyskanie z przejściowych problemów. Jeśli żądanie przeniesienia znajduje się w ciągu 4 godzin, operacja nadal próbuje zakończyć i może nadal zakończyć się pomyślnie. Źródłowe i docelowe grupy zasobów są zablokowane w tym czasie, aby uniknąć problemów ze spójnością.

**Pytanie: Dlaczego moja grupa zasobów jest zablokowana na 4 godziny podczas przenoszenia zasobów?**

Okno 4-godzinne to maksymalny czas dozwolony dla przenoszenia zasobów. Aby zapobiec modyfikacjom przenoszonych zasobów, zarówno źródłowe, jak i docelowe grupy zasobów są zablokowane na czas trwania przenoszenia zasobu.

Istnieją dwie fazy żądania przeniesienia. W pierwszej fazie zasób jest przenoszony. W drugiej fazie powiadomienia są wysyłane do innych dostawców zasobów, które są zależne od przenoszonego zasobu. Grupa zasobów może być zablokowana dla całego okna 4-godzinnego, gdy dostawca zasobów ulegnie awarii w obu fazach. W dozwolonym czasie Menedżer zasobów ponawia ponowny krok nie powiodło się.

Jeśli nie można przenieść zasobu w ciągu 4 godzin, Menedżer zasobów odblokowuje obie grupy zasobów. Zasoby, które zostały pomyślnie przeniesione, należą do docelowej grupy zasobów. Zasoby, których nie udało się przenieść, pozostają w grupie zasobów źródłowych.

**Pytanie: Jakie są implikacje zablokowania grup zasobów źródłowych i docelowych podczas przenoszenia zasobu?**

Blokada uniemożliwia usunięcie albo grupy zasobów, tworzenie nowego zasobu w każdej grupie zasobów lub usunięcie dowolnego z zasobów zaangażowanych w przenoszenie.

Na poniższej ilustracji przedstawiono komunikat o błędzie z witryny Azure portal, gdy użytkownik próbuje usunąć grupę zasobów, która jest częścią trwającego przenoszenia.

![Przenieś komunikat o błędzie próbujący usunąć](./media/move-resource-group-and-subscription/move-error-delete.png)

**Pytanie: Co oznacza kod błędu "MissingMoveDependentResources"?**

Podczas przenoszenia zasobu jego zasoby zależne muszą istnieć w docelowej grupie zasobów lub subskrypcji lub zostać uwzględnione w żądaniu przeniesienia. Otrzymasz MissingMoveDependentResources kod błędu, gdy zasób zależny nie spełnia tego wymagania. Komunikat o błędzie zawiera szczegółowe informacje o zasobie zależnym, który musi zostać uwzględniony w żądaniu przeniesienia.

Na przykład przeniesienie maszyny wirtualnej może wymagać przeniesienia siedmiu typów zasobów z trzema różnymi dostawcami zasobów. Te dostawców zasobów i typy są:

* Microsoft.Compute

  * wirtualnematyny
  * Dysków
* Microsoft.Network
  * siećPowierzchnie
  * publicYSadresy
  * networkSecurityGroups
  * wirtualne sieci
* Microsoft.Storage
  * konta przechowywania

Innym typowym przykładem jest przeniesienie sieci wirtualnej. Może być potrzebne przeniesienie kilku innych zasobów skojarzonych z tą siecią wirtualną. Żądanie przeniesienia może wymagać przenoszenia publicznych adresów IP, tabel tras, bram sieci wirtualnej, grup zabezpieczeń sieci i innych.

**Pytanie: Dlaczego nie mogę przenieść niektórych zasobów na platformie Azure?**

Obecnie nie wszystkie zasoby w pomocy technicznej platformy Azure są przesuwają się. Aby uzyskać listę zasobów obsługujących przenoszenie, zobacz [Przenoszenie obsługi operacji dla zasobów](move-support-resources.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę zasobów, które obsługują przenoszenie zasobów, zobacz [Przenoszenie obsługi operacji dla zasobów](move-support-resources.md).
