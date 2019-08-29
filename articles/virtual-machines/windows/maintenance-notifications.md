---
title: Obsługa powiadomień dotyczących konserwacji maszyn wirtualnych z systemem Windows na platformie Azure | Microsoft Docs
description: Wyświetl powiadomienia o konserwacji dla maszyn wirtualnych z systemem Windows uruchomionych na platformie Azure i uruchom konserwację samoobsługową.
services: virtual-machines-windows
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/30/2019
ms.author: shants
ms.openlocfilehash: c27d6d65629fb926442377c03ab688b8819aad20
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079189"
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Obsługa powiadomień dotyczących planowanej konserwacji dla maszyn wirtualnych z systemem Windows

Platforma Azure jest co pewien czas aktualizowana w celu poprawy niezawodności, wydajności i zabezpieczeń infrastruktury hostowania maszyn wirtualnych. Aktualizacje są zmianami, takimi jak poprawianie środowiska hostingu lub uaktualnianie i likwidowanie sprzętu. Większość tych aktualizacji jest przeprowadzana bez żadnego wpływu na hostowane maszyny wirtualne. Istnieją jednak przypadki, w których aktualizacje mają wpływ:

- Jeśli konserwacja nie wymaga ponownego uruchomienia, platforma Azure używa migracji w miejscu w celu wstrzymania maszyny wirtualnej podczas aktualizowania hosta. Te operacje konserwacji bez ponownego uruchomienia są stosowane w domenie błędów przez domenę błędów, a postęp jest zatrzymany w przypadku odebrania wszelkich ostrzeżeń dotyczących kondycji. 

- Jeśli konserwacja wymaga ponownego uruchomienia, otrzymasz powiadomienie o zaplanowaniu konserwacji. W takich przypadkach użytkownik otrzymuje przedział czasu, który zwykle 35 dni, w którym można samodzielnie rozpocząć konserwację, gdy będzie ona działać.


Planowana konserwacja wymagająca ponownego uruchomienia komputera jest zaplanowana na fale. Każda fala ma inny zakres (regiony).

- Fala rozpoczyna się od powiadomienia do klientów. Domyślnie powiadomienia są wysyłane do właściciela subskrypcji i współwłaścicieli. Do powiadomień przy użyciu [alertów dziennika aktywności](../../azure-monitor/platform/activity-logs-overview.md)platformy Azure można dodawać więcej adresatów i opcji obsługi komunikatów, takich jak wiadomości e-mail, wiadomości SMS i elementy webhook.  
- W momencie powiadomienia jest udostępniane *okno* samoobsługowe. W tym oknie, które jest zwykle 35 dni, można dowiedzieć się, które maszyny wirtualne znajdują się w tej Wave, i aktywnie rozpocząć konserwację zgodnie z własnymi potrzebami w zakresie planowania.
- Po włączeniu okna samoobsługowego zostanie rozpoczęte *zaplanowane okno obsługi* . W pewnym momencie w tym oknie usługa Azure planuje i stosuje wymaganą konserwację na maszynie wirtualnej. 

Celem dwóch okien jest udostępnienie wystarczającej ilości czasu na rozpoczęcie konserwacji i ponowne uruchomienie maszyny wirtualnej, wiedząc, że platforma Azure automatycznie rozpocznie konserwację.


Za pomocą Azure Portal, programu PowerShell, interfejsu API REST i wiersza polecenia można wysyłać zapytania dotyczące okien obsługi dla maszyn wirtualnych i uruchamiać konserwację samoobsługową.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Czy należy uruchomić konserwację w trakcie samoobsługowego okna?  

Poniższe wskazówki powinny ułatwić podjęcie decyzji o tym, czy korzystać z tej funkcji, i zacząć konserwację w swoim czasie.

> [!NOTE] 
> Konserwacja samoobsługowa może być niedostępna dla wszystkich maszyn wirtualnych. Aby określić, czy aktywne ponowne wdrażanie jest dostępne dla maszyny wirtualnej, Wyszukaj pozycję **Rozpocznij teraz** w stanie konserwacji. Samoobsługowe konserwacje nie są obecnie dostępne dla Cloud Services (roli sieć Web/proces roboczy) i Service Fabric.


Samoobsługowe konserwacje nie są zalecane w przypadku wdrożeń korzystających z **zestawów dostępności** , ponieważ są to konfiguracje o wysokim poziomie dostępności, w których dotyczy tylko jednej domeny aktualizacji w danym momencie. 
- Pozwól, aby platforma Azure wyzwolił konserwację. Aby przeprowadzić konserwację wymagającą ponownego uruchomienia, należy pamiętać, że konserwacja zostanie zaimplementowana przez domenę aktualizacji, ponieważ domeny aktualizacji nie zawsze odbierają konserwację w sposób sekwencyjny oraz że istnieje 30-minutowe wstrzymanie między domenami aktualizacji. 
- Jeśli nieprzerwana utrata pewnej pojemności (1/aktualizacja liczby domen) jest istotna, można ją łatwo kompensować przez przydzielenie dodatkowych wystąpień w okresie konserwacji.
- W przypadku konserwacji, która nie wymaga ponownego uruchomienia, aktualizacje są stosowane na poziomie domeny błędów.
    
**Nie** należy używać samoobsługowej konserwacji w następujących scenariuszach: 
- W przypadku częstego wyłączania maszyn wirtualnych ręcznie przy użyciu usługi DevTest Labs przy użyciu automatycznego zamykania lub zgodnie z harmonogramem można przywrócić stan konserwacji i w związku z tym spowodować dodatkowy Przestój.
- Na krótkoterminowych maszynach wirtualnych, które znasz, zostaną usunięte przed końcem fazy obsługi. 
- W przypadku obciążeń o dużym stanie przechowywanym na dysku lokalnym (tymczasowych), które mają być utrzymywane po aktualizacji. 
- W przypadkach, w których często zmienia się rozmiar maszyny wirtualnej, może to spowodować przywrócenie stanu konserwacji.
- W przypadku zaakceptowania zaplanowanych zdarzeń, które umożliwiają aktywne przejście w tryb failover lub bezpieczne zamknięcie obciążenia, 15 minut przed rozpoczęciem zamykania konserwacji

**Użyj** samoobsługowej konserwacji, jeśli planujesz uruchomić maszynę wirtualną w trybie nieprzerwanym w fazie zaplanowanej konserwacji, a żadne z powyższych wskazań liczników nie są stosowane. 

Najlepszym rozwiązaniem jest użycie samoobsługowej konserwacji w następujących przypadkach:

- Należy skontaktować się z dokładnym oknem obsługi, aby zarządzać lub klientem końcowym. 
- Należy przeprowadzić konserwację według danego dnia. 
- Należy sterować sekwencją czynności konserwacyjnych, na przykład aplikacji wielowarstwowej w celu zagwarantowania bezpiecznego odzyskiwania.
- W przypadku dwóch domen aktualizacji należy uzyskać więcej niż 30 minut czasu odzyskiwania maszyny wirtualnej. Aby kontrolować czas między domenami aktualizacji, należy wyzwolić konserwację na maszynach wirtualnych w jednej domenie aktualizacji (UD) naraz.

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Sprawdzanie stanu konserwacji przy użyciu programu PowerShell

Możesz również użyć programu Azure PowerShell, aby zobaczyć, kiedy maszyny wirtualne są zaplanowane do konserwacji. Informacje o planowanej konserwacji są dostępne w poleceniu cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) podczas `-status` korzystania z parametru.
 
Informacje o konserwacji są zwracane tylko wtedy, gdy jest planowana konserwacja. Jeśli żadna konserwacja nie zostanie zaplanowana na maszynę wirtualną, polecenie cmdlet nie zwróci żadnych informacji o konserwacji. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
Get-AzVM -ResourceGroupName rgName -Name vmName -Status
```

Następujące właściwości są zwracane w obszarze MaintenanceRedeployStatus: 

| Value | Opis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Wskazuje, czy w tej chwili można rozpocząć konserwację maszyny wirtualnej |
| PreMaintenanceWindowStartTime         | Początek okna samoobsługowego konserwacji, gdy można zainicjować konserwację na maszynie wirtualnej |
| PreMaintenanceWindowEndTime           | Koniec okna samoobsługowego konserwacji, gdy można zainicjować konserwację na maszynie wirtualnej |
| MaintenanceWindowStartTime            | Rozpoczęcie konserwacji zaplanowanej, w której platforma Azure inicjuje konserwację na maszynie wirtualnej |
| MaintenanceWindowEndTime              | Koniec okna zaplanowanej konserwacji, w którym platforma Azure inicjuje konserwację na maszynie wirtualnej |
| LastOperationResultCode               | Wynik ostatniej próby zainicjowania konserwacji na maszynie wirtualnej |



Możesz również uzyskać stan konserwacji dla wszystkich maszyn wirtualnych w grupie zasobów przy użyciu polecenia [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) i nie OKREŚLAJ maszyny wirtualnej.
 
```powershell
Get-AzVM -ResourceGroupName rgName -Status
```

Następująca funkcja programu PowerShell przyjmuje Identyfikator subskrypcji i drukuje listę maszyn wirtualnych, które są zaplanowane do konserwacji.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Rozpocznij konserwację na maszynie wirtualnej przy użyciu programu PowerShell

Przy użyciu informacji z funkcji w poprzedniej sekcji, następujące polecenie uruchamia konserwację na maszynie wirtualnej, jeśli **IsCustomerInitiatedMaintenanceAllowed** jest ustawiona na wartość true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klasyczne wdrożenia

Jeśli nadal masz starsze maszyny wirtualne wdrożone przy użyciu klasycznego modelu wdrażania, możesz użyć programu PowerShell do wykonywania zapytań dotyczących maszyn wirtualnych i inicjowania obsługi.

Aby uzyskać stan konserwacji maszyny wirtualnej, wpisz:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Aby rozpocząć konserwację klasycznej maszyny wirtualnej, wpisz:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>Często zadawane pytania


**Pyt.: Dlaczego należy teraz ponownie uruchomić maszyny wirtualne?**

**Odp.:** Chociaż większość aktualizacji i uaktualnień na platformie Azure nie ma wpływu na dostępność maszyny wirtualnej, istnieją przypadki, w których nie można uniknąć ponownego uruchamiania maszyn wirtualnych hostowanych na platformie Azure. Zebrano kilka zmian, które wymagają ponownego uruchomienia naszych serwerów, co spowoduje ponowne uruchomienie maszyn wirtualnych.

**Pyt.: Jeśli zgodnie z zaleceniami dotyczącymi wysokiej dostępności korzystasz z zestawu dostępności, czy jesteś bezpieczny?**

**Odp.:** Maszyny wirtualne wdrożone w zestawie dostępności lub zestawach skalowania maszyn wirtualnych obsługują pojęcie domen aktualizacji (UD). Podczas przeprowadzania konserwacji platforma Azure uznaje ograniczenie UD i nie będzie ponownie uruchamiać maszyn wirtualnych z różnych UD (w ramach tego samego zestawu dostępności).  Platforma Azure czeka także przez co najmniej 30 minut przed przejściem do następnej grupy maszyn wirtualnych. 

Aby uzyskać więcej informacji na temat wysokiej dostępności, zobacz [dostępność dla maszyn wirtualnych na platformie Azure](availability.MD).

**Pyt.: Jak mogę otrzymywać powiadomienia o planowanej konserwacji?**

**Odp.:** Planowana fala konserwacji jest uruchamiana przez ustawienie harmonogramu do co najmniej jednego regionu świadczenia usługi Azure. Wkrótce po otrzymaniu powiadomienia e-mail do właścicieli subskrypcji (jednej wiadomości e-mail na subskrypcję). Dodatkowe kanały i adresatów dla tego powiadomienia można skonfigurować przy użyciu alertów dziennika aktywności. W przypadku wdrożenia maszyny wirtualnej w regionie, w którym zaplanowano zaplanowaną konserwację, nie otrzymasz powiadomienia, ale konieczne jest sprawdzenie stanu konserwacji maszyny wirtualnej.

**Pyt.: Nie widzę żadnej wskazówki dotyczącej planowanej konserwacji w portalu, programie PowerShell lub interfejsie wiersza polecenia. Co jest nie tak?**

**Odp.:** Informacje związane z planowaną konserwacją są dostępne podczas planowanej fazy konserwacji tylko dla maszyn wirtualnych, na które mają mieć wpływ. Innymi słowy, jeśli nie widzisz danych, może to oznaczać, że fala konserwacji została już ukończona (lub nie została uruchomiona) albo że maszyna wirtualna jest już hostowana na zaktualizowanym serwerze.

**Pyt.: Czy istnieje sposób, aby dokładnie poznać, kiedy wpłynie na moją maszynę wirtualną?**

**Odp.:** Podczas ustawiania harmonogramu definiujemy przedział czasu kilka dni. Jednak dokładne sekwencjonowanie serwerów (i maszyn wirtualnych) w tym oknie jest nieznane. Klienci, którzy chcą wiedzieć, jak dokładny czas na ich maszynach wirtualnych mogą używać zaplanowanych [zdarzeń](scheduled-events.md) i zapytań z poziomu maszyny wirtualnej, a następnie otrzymywać 15-minutowy powiadomienie przed ponownym uruchomieniem maszyny wirtualnej.

**Pyt.: Jak długo zajmiemy się ponownym uruchomieniem maszyny wirtualnej?**

**Odp.:**  W zależności od rozmiaru maszyny wirtualnej ponowne uruchomienie usługi może potrwać kilka minut. Po zainicjowaniu ponownego uruchomienia platformy Azure w oknie zaplanowanej konserwacji ponowne uruchomienie zajmie zwykle około 25 minut. Należy pamiętać, że w przypadku korzystania z Cloud Services (roli sieć Web/proces roboczy), Virtual Machine Scale Sets lub zestawów dostępności otrzymasz 30 minut między każdą grupą maszyn wirtualnych (UD) w oknie zaplanowanej konserwacji. 

**Pyt.: Co to jest środowisko w przypadku Virtual Machine Scale Sets?**

**Odp.:** Planowana konserwacja jest teraz dostępna dla Virtual Machine Scale Sets. Aby uzyskać instrukcje dotyczące sposobu inicjowania samoobsługowej konserwacji, [zaplanowaną konserwację dla dokumentu VMSS](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) .

**Pyt.: Co to jest środowisko w przypadku Cloud Services (rola sieć Web/proces roboczy) i Service Fabric?**

**Odp.:** Mimo że planowana konserwacja ma wpływ na te platformy, klienci korzystający z tych platform są uznawani za bezpiecznych, o ile w danym momencie konserwacja będzie miała wpływ tylko na maszyny wirtualne w jednej domenie uaktualnienia. Samoobsługowe konserwacje nie są obecnie dostępne dla Cloud Services (roli sieć Web/proces roboczy) i Service Fabric.

**Pyt.: Nie widzę żadnych informacji o konserwacji na moich maszynach wirtualnych. Co się stało?**

**Odp.:** Istnieje kilka powodów, dla których nie widzisz żadnych informacji o konserwacji maszyn wirtualnych:
1.  Używasz subskrypcji oznaczonej jako wewnętrzne firmy Microsoft.
2.  Maszyny wirtualne nie są zaplanowane do konserwacji. Może się zdarzyć, że fala konserwacji zakończyła się, została anulowana lub zmodyfikowana, tak aby maszyny wirtualne nie miały już na nie wpływu.
3.  Do widoku listy maszyn wirtualnych nie została dodana kolumna **konserwacja** . Po dodaniu tej kolumny do widoku domyślnego klienci, którzy skonfigurowali wyświetlanie kolumn innych niż domyślne, muszą ręcznie dodać kolumnę **konserwacji** do widoku listy maszyn wirtualnych.

**Pyt.: Zaplanowano konserwację maszyny wirtualnej po raz drugi. Dlaczego?**

**Odp.:** Istnieje kilka przypadków użycia, w których zobaczysz, że maszyna wirtualna została zaplanowana do konserwacji po zakończeniu konserwacji i ponownego wdrożenia:
1.  Twoja fala została anulowana i uruchomiona ponownie z innym ładunkiem. Może być to, że wykryto błąd ładunku i po prostu musimy wdrożyć dodatkowy ładunek.
2.  Twoja maszyna wirtualna została zaakceptowana w innym węźle z powodu błędu sprzętowego
3.  Wybrano zatrzymanie (Cofnięcie alokacji) i ponowne uruchomienie maszyny wirtualnej
4.  Włączono **automatyczne zamykanie** dla maszyny wirtualnej


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak można rejestrować zdarzenia konserwacji z poziomu maszyny wirtualnej przy użyciu [Scheduled Events](scheduled-events.md).
