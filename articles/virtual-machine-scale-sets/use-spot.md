---
title: Tworzenie zestawu skalowania korzystającego z maszyn wirtualnych platformy Azure w punktach
description: Dowiedz się, jak tworzyć zestawy skalowania maszyny wirtualnej platformy Azure, które używają maszyn wirtualnych punktowych do oszczędzania na kosztach.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: a7bd22032a554c83a2ea2323ffdb3ae52dfe4faf
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545938"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>Maszyny wirtualne platformy Azure spot dla zestawów skalowania maszyn wirtualnych 

Korzystanie z platformy Azure Spot w zestawach skalowania umożliwia korzystanie z naszej niewykorzystanej pojemności przy znacznych oszczędnościach kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktury platformy Azure będzie eksmitować wystąpień spot. W związku z tym wystąpienia spot są idealne dla obciążeń, które mogą obsługiwać przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperów/testów, dużych obciążeń obliczeniowych i innych.

Ilość dostępnej pojemności może się różnić w zależności od rozmiaru, regionu, połówka dnia i innych. Podczas wdrażania wystąpień spot w zestawach skalowania platforma Azure przydzieli wystąpienie tylko wtedy, gdy dostępna jest pojemność, ale dla tych wystąpień nie ma umowy SLA. Zestaw skalowania punktowego jest wdrażany w jednej domenie błędów i nie oferuje gwarancji wysokiej dostępności.


## <a name="pricing"></a>Cennik

Ceny dla wystąpień spot jest zmienna, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz ceny dla [systemów Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) i [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


W przypadku zmiennych cen możesz ustawić cenę maksymalną w dolarach amerykańskich (USD), używając do 5 miejsc po przecinku. Na przykład wartość `0.98765`będzie maksymalna cena $0.98765 USD za godzinę. Jeśli ustawisz maksymalną `-1`cenę, aby być , wystąpienie nie zostanie eksmitowany na podstawie ceny. Ceną za wystąpienie będzie aktualna cena spotu lub cena dla standardowego wystąpienia, która kiedykolwiek jest mniejsza, o ile dostępna jest pojemność i kwota.

## <a name="eviction-policy"></a>Zasady eksmisji

Podczas tworzenia zestawów skalowania punktowego można ustawić zasady eksmisji na *Deallocate* (domyślnie) lub *Usuń*. 

*Zasada Alokacji transakcji* przenosi eksmitowane wystąpienia do stanu zatrzymanego, co pozwala na ponowne wdrożenie eksmitowanych wystąpień. Nie ma jednak gwarancji, że alokacja zakończy się pomyślnie. Zdelegalizowane maszyny wirtualne będą wliczane do przydziału wystąpienia ustawionego skali i zostanie naliczona opłata za dyski bazowe. 

Jeśli chcesz, aby twoje wystąpienia w skali spot zostały usunięte po ich eksmisji, możesz ustawić zasady eksmisji, aby *usunąć*. Z zasadą eksmisji ustawioną na usunięcie, można utworzyć nowe maszyny wirtualne, zwiększając właściwość liczby wystąpień zestawu skalowania. Eksmitowane maszyny wirtualne są usuwane razem z ich dyskami bazowymi i dlatego nie zostanie naliczona opłata za magazyn. Można również użyć funkcji automatycznego skalowania zestawów skalowania, aby automatycznie spróbować skompensować eksmitowane maszyny wirtualne, jednak nie ma żadnej gwarancji, że alokacja zakończy się pomyślnie. Zaleca się używanie funkcji automatycznej skali w zestawach skalowania punktowego tylko po ustawieniu zasad eksmisji do usunięcia, aby uniknąć kosztów dysków i osiągnięcia limitów przydziału. 

Użytkownicy mogą wyrazić zgodę na otrzymywanie powiadomień w maszynie wirtualnej za pośrednictwem [usługi Azure Scheduled Events.](../virtual-machines/linux/scheduled-events.md) Spowoduje to powiadomienie, jeśli maszyny wirtualne są eksmitowane i będziesz miał 30 sekund, aby zakończyć wszystkie zadania i wykonać zadania zamknięcia przed eksmisją. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Wdrażanie maszyn wirtualnych punktowych w zestawach skalowania

Aby wdrożyć maszyny wirtualne punktowe w zestawach skalowania, można ustawić nową flagę *Priorytet* na *Punkt .* Wszystkie maszyny wirtualne w zestawie skalowania zostaną ustawione na Spot. Aby utworzyć zestaw skalowania z maszynami wirtualnymi punktowymi, należy użyć jednej z następujących metod:
- [Azure Portal](#portal)
- [Interfejs wiersza polecenia platformy Azure](#azure-cli)
- [Azure PowerShell](#powershell)
- [Szablony usługi Azure Resource Manager](#resource-manager-templates)

## <a name="portal"></a>Portal

Proces tworzenia zestawu skalowania, który używa maszyn wirtualnych punktowych, jest taki sam, jak opisano w [artykule Wprowadzenie.](quick-create-portal.md) Podczas wdrażania zestawu skalowania można ustawić flagę Spot i zasadę eksmisji: ![Tworzenie zestawu skalowania z maszynami wirtualnymi punktowymi](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Proces tworzenia zestawu skalowania z maszynami wirtualnymi punktowymi jest taki sam, jak opisano w [artykule wprowadzenie](quick-create-cli.md). Wystarczy dodać '--Priorytet Spot' `--max-price`i dodać . W tym przykładzie `-1` `--max-price` używamy, więc wystąpienie nie zostanie eksmitowany na podstawie ceny.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Proces tworzenia zestawu skalowania z maszynami wirtualnymi punktowymi jest taki sam, jak opisano w [artykule wprowadzenie](quick-create-powershell.md).
Wystarczy dodać '-Priority Spot' `-max-price` i dostarczyć a do [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Proces tworzenia zestawu skalowania, który używa maszyn wirtualnych punktowych, jest taki sam, jak opisano w artykule wprowadzenie dla [systemu Linux](quick-create-template-linux.md) lub [Windows](quick-create-template-windows.md). 

W przypadku wdrożeń szablonów punktowych użyj`"apiVersion": "2019-03-01"` lub później. Dodaj `priority`, `evictionPolicy` `billingProfile` i właściwości `"virtualMachineProfile":` do sekcji w szablonie: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

Aby usunąć wystąpienie po jego eksmisji, zmień parametr na `evictionPolicy` . `Delete`

## <a name="faq"></a>Często zadawane pytania

**P.** Po utworzeniu, jest spot wystąpienie takie same jak standardowe wystąpienie?

**Odp.:** Tak, z wyjątkiem nie ma umowy SLA dla maszyn wirtualnych punktowych i mogą one zostać eksmitowane w dowolnym momencie.


**P.** Co zrobić, gdy dostajesz eksmitowany, ale nadal potrzebujesz pojemności?

**Odp.:** Jeśli potrzebujesz pojemności od razu, zaleca się używanie standardowych maszyn wirtualnych zamiast maszyn wirtualnych typu spot.


**P.** W jaki sposób zarządza się przydziałem dla spot?

**Odp.:** Wystąpienia punktowe i wystąpienia standardowe będą miały oddzielne pule przydziałów. Przydział punktowy będzie współużytkowany między maszynami wirtualnymi i wystąpieniami zestawu skalowania. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**P.** Czy mogę poprosić o dodatkowy przydział na spot?

**Odp.:** Tak, można przesłać wniosek o zwiększenie przydziału dla maszyn wirtualnych punktowych za pośrednictwem [standardowego procesu żądania przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**P.** Czy można przekonwertować istniejące zestawy skalowania na zestawy skalowania punktowe?

**Odp.:** Nie, ustawienie `Spot` flagi jest obsługiwane tylko w czasie tworzenia.


**P.** Jeśli używam `low` zestawów skalowania o niskim priorytecie, czy muszę zacząć używać `Spot` zamiast tego?

**Odp.:** Na razie, `low` `Spot` zarówno i będzie działać, ale `Spot`należy rozpocząć przejście do korzystania .


**P.** Czy mogę utworzyć zestaw skalowania z zarówno zwykłymi maszynami wirtualnymi, jak i maszynami wirtualnymi punktowymi?

**Odp.:** Nie, zestaw skalowania nie może obsługiwać więcej niż jednego typu priorytetu.


**P.**  Czy mogę używać skalowania automatycznego z zestawami skalowania punktowych?

**Odp.:** Tak, można ustawić reguły skalowania automatycznego w zestawie skalowania punktowego. Jeśli maszyny wirtualne są eksmitowane, skalowanie automatyczne można spróbować utworzyć nowe maszyny wirtualne punktowe. Pamiętaj jednak, że nie masz gwarancji tej pojemności. 


**P.**  Czy skalowanie automatyczne działa z zasadami eksmisji (cofnięto alokację i usunięcie)?

**Odp.:** Zaleca się ustawienie zasad eksmisji do usunięcia podczas korzystania ze skalowania automatycznego. Dzieje się tak, ponieważ wystąpienia z alokacją są liczone względem liczby zdolności produkcyjnych w zestawie skalowania. Podczas korzystania z skalowania automatycznego, prawdopodobnie trafi liczbę wystąpień docelowych szybko ze względu na cofnięte, eksmitowane wystąpienia. 


**P.** Jakie kanały obsługują maszyny wirtualne spot?

**Odp.:** Zobacz poniższą tabelę dostępności maszyn wirtualnych w miejscu.

<a name="channel"></a>

| Kanały platformy Azure               | Dostępność maszyn wirtualnych w miejscu platformy Azure       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | Tak                               |
| Płatność zgodnie z rzeczywistym użyciem                | Tak                               |
| Dostawca usług w chmurze (CSP) | [Skontaktuj się ze swoim partnerem](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Korzyści                     | Niedostępne                     |
| Sponsorowane                    | Niedostępne                     |
| Bezpłatna wersja próbna                   | Niedostępne                     |


**P.** Gdzie mogę zadać pytania?

**Odp.:** Możesz opublikować i oznaczyć swoje pytanie na `azure-spot` Q&[A](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Następne kroki

Sprawdź [stronę cennik zestawu skalowania maszyny wirtualnej, aby](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) uzyskać szczegółowe informacje o cenach.
