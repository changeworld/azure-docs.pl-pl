---
title: Obsługa powiadomienia o konserwacji dla zestawów skalowania maszyn wirtualnych na platformie Azure | Dokumentacja firmy Microsoft
description: Wyświetl powiadomienia o konserwacji dla zestawów skalowania maszyn wirtualnych na platformie Azure i Rozpocznij konserwacji samoobsługowej.
services: virtual-machine-scale-sets
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: shants
ms.openlocfilehash: 4ce984686c2bb320d5d32771d31b81cdec1153af
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38314406"
---
# <a name="handling-planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Obsługa Planned maintenance notifications zaplanowanej dla zestawów skalowania maszyn wirtualnych

Platforma Azure jest co pewien czas aktualizowana w celu poprawy niezawodności, wydajności i zabezpieczeń infrastruktury hostowania maszyn wirtualnych. Aktualizacje są zmiany, takie jak stosowanie poprawek środowiska macierzystego lub uaktualniania i likwidowaniu sprzętu. Większość tych aktualizacji jest przeprowadzana bez żadnego wpływu na obsługiwanych maszynach wirtualnych. Jednakże istnieją przypadki, w których aktualizacje mają wpływ:

- Jeśli konserwacja nie wymaga ponownego uruchomienia systemu, platforma Azure używa migracji w miejscu wstrzymać maszyny Wirtualnej, gdy host jest aktualizowana. Te operacje konserwacji bez rebootful są domena błędów zastosowane przez domenę błędów, a postęp zostanie zatrzymany, jeśli wszystkie sygnały kondycji ostrzeżenia są odbierane.

- Jeśli przeprowadzenia konserwacji wymagane jest ponowne uruchomienie komputera, otrzymasz powiadomienie o podczas planowanej konserwacji. W takich przypadkach są podane przedział czasu, w którym możesz uruchomić konserwację samodzielnie, kiedy pracuje dla Ciebie.


Planowana konserwacja, która wymaga ponownego uruchomienia, zaplanowano etapami. Każdy etap ma inny zakres (regiony).

- Falą rozpoczyna się od powiadomienie dla klientów. Domyślnie powiadomienia są wysyłane do właściciela subskrypcji i współwłaścicieli. Możesz dodać więcej adresatów i opcje obsługi komunikatów, takie jak wiadomości e-mail, wiadomości SMS i elementy Webhook, do powiadomienia za pomocą platformy Azure [alertów dziennika aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).  
- W momencie zgłoszenia *samoobsługi okna* ma zostać udostępnione. W tym oknie możesz znaleźć, w której maszyny wirtualne znajdują się w tej fazie badania i aktywnie Uruchom konserwację zgodnie z potrzebami planowania.
- Po oknie samoobsługi *zaplanowanego okna obsługi* rozpoczyna się. W pewnym momencie podczas tego okna platforma Azure planuje i dotyczy wymagane konserwacji maszyny wirtualnej. 

Cel, że dwa okna jest zapewnienie wystarczająco dużo czasu, aby uruchomić konserwacji i ponowne uruchomienie maszyny wirtualnej codzienne, gdy Azure nastąpi automatyczne uruchomienie konserwacji.


Możesz użyć witryny Azure portal, programu PowerShell, interfejsu API REST i interfejsu wiersza polecenia do wykonywania zapytań dla okna obsługi skalowania maszyn wirtualnych maszyn wirtualnych w zestawie i uruchomić konserwacji samoobsługowej.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>W oknie samoobsługi należy rozpocząć konserwacji?  

Poniższe wskazówki powinny pomóc w zdecyduj, czy chcesz używać tej funkcji i uruchomić własny czas konserwacji.

> [!NOTE] 
> Konserwacji samoobsługowej może nie być dostępne dla wszystkich maszyn wirtualnych. Aby ustalić, czy aktywnego ponownego wdrożenia jest dostępna dla maszyny Wirtualnej, poszukaj **teraz uruchomić** stanu konserwacji. Konserwacji samoobsługowej nie jest obecnie dostępna w przypadku usług Cloud Services (internetowy proces roboczy) i usługi Service Fabric.


Konserwacji samoobsługowej nie jest zalecane w przypadku wdrożeń za pomocą **zestawy dostępności** ponieważ te konfiguracje o wysokiej dostępności, której dotyczy to tylko jedna domena aktualizacji w dowolnym momencie. 

- Pozwól Azure wyzwolić konserwacji. Konserwacji, która wymaga ponownego uruchomienia należy pamiętać, że konserwacja będzie odbywać się domena aktualizacji według domeny aktualizacji, że domenach aktualizacji nie otrzymywać konserwację sekwencyjnie, oraz czy 30-minutowe pauzę między domenami aktualizacji.
- Jeśli do tymczasowej utraty niektórych pojemności (liczba domen aktualizacji/1) ma znaczenie, jego można łatwo skompensować przydzielając dodatkowe wystąpienia w okresie konserwacji.
- Konserwacji, który nie jest wymagane ponowne uruchomienie, aktualizacje są stosowane na poziomie domeny błędów. 
    
**Nie** Użyj konserwacji samoobsługowej w następujących scenariuszach: 

- Jeśli wyłączysz maszyn wirtualnych, albo ręcznie, za pomocą usługi DevTest Labs przy użyciu automatycznego zamykania lub zgodnie z harmonogramem, może cofnąć stanu konserwacji i w związku z tym spowodować przestój dodatkowe.
- Na maszynach wirtualnych krótkotrwałe czy wiesz, zostaną usunięte przed upływem fala konserwacji. 
- W przypadku obciążeń o dużych stanie przechowywanych w (efemeryczne) dysk lokalny, który jest pożądane, aby zachować przy aktualizacji. 
- W przypadkach, w którym możesz zmienić rozmiar maszyny Wirtualnej często ponieważ można przywrócić stanu konserwacji. 
- Jeśli przyjęły zaplanowanych zdarzeń, umożliwiające proaktywne trybu failover lub łagodne zamykanie obciążenie, 15 minut przed rozpoczęciem konserwacji zamknięcia.

**Użyj** konserwacji samoobsługowej, jeśli zamierzasz uruchomić nieprzerwaną podczas fazy zaplanowanej konserwacji maszyny Wirtualnej i żadna oznaczeń zaradcze wymienione powyżej nie ma zastosowania. 

Najlepiej użyć konserwacji samoobsługowej w następujących przypadkach:

- Musisz przekazać dokładnie konserwacyjne do zarządzania lub odbiorcy końcowego. 
- Trzeba wykonać konserwacji przed upływem określonego terminu. 
- Wymagane jest sterowanie sekwencję konserwacji, na przykład wielowarstwową aplikację w celu zagwarantowania bezpiecznej odzyskiwania.
- Potrzebujesz więcej niż 30 minut czasu odzyskiwania maszyny Wirtualnej między dwiema domenami aktualizacji (domenami aktualizacji). Aby kontrolować czas między domenami aktualizacji, możesz wyzwolić konserwacji w sieci maszyn wirtualnych z jednej domeny aktualizacji (UD) w danym momencie.

 
## <a name="view-virtual-machine-scale-sets-impacted-by-maintenance-in-the-portal"></a>Wyświetlanie zestawów skalowania maszyn wirtualnych, wpływ na zachowanie w portalu

Podczas planowanej konserwacji jest zaplanowane, można wyświetlić listę zestawów skalowania maszyn wirtualnych, które mają wpływ wave zbliżającej się konserwacji przy użyciu witryny Azure portal. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** w okienku nawigacji po lewej stronie i wybierz polecenie **zestawy skalowania maszyn wirtualnych**.
3. Na **zestawy skalowania maszyn wirtualnych** wybierz **Upravit sloupce** u góry opcję, aby otworzyć listę dostępnych kolumn.
4. W **sekcji dostępnych kolumn**, wybierz opcję **konserwacji samoobsługowej** element i użyj przycisków strzałek, aby przenieść go do **wybrane kolumny** listy. Przełącz się na liście rozwijanej **dostępnych kolumn** sekcja **wszystkich** do **właściwości** się **konserwacji samoobsługowej** Element łatwiejsze do znalezienia. Po utworzeniu **konserwacji samoobsługowej** pozycja **wybrane kolumny** zaznacz **Zastosuj** znajdujący się u dołu strony. 

Po wykonaniu powyższych kroków **konserwacji samoobsługowej** kolumny będą wyświetlane na liście zestawów skalowania maszyn wirtualnych. Każdy zestaw skalowania maszyn wirtualnych może mieć jedną z następujących wartości dla kolumny konserwacji samoobsługowej:

| Wartość | Opis |
|-------|-------------|
| Yes | Co najmniej jedną maszynę wirtualną w zestawie skalowania maszyny wirtualnej jest w oknie samoobsługi. Możesz uruchomić konserwację w dowolnym momencie, w tym oknie samoobsługi. | 
| Nie | Brak ustawionych w oknie samoobsługi w skalowania wykorzystywanych maszyn wirtualnych maszyn wirtualnych. | 
| - | Twoje zestawy skalowania maszyn wirtualnych nie są częścią planowanej konserwacji.| 

## <a name="notification-and-alerts-in-the-portal"></a>Powiadomienia i alerty w portalu

Azure komunikuje się harmonogram planowanej konserwacji, wysyłając wiadomość e-mail do grupy właściciel i współwłaściciele subskrypcji. Możesz dodać dodatkowych adresatów oraz kanały do komunikacji przez tworzenie alertów dziennika aktywności platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie aktywności subskrypcji z dziennika aktywności platformy Azure] (.. / monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz **Monitor**. 
3. W **monitorowanie — alerty (klasyczne)** okienku kliknij **+ Dodaj alert dziennika aktywności**.
4. Uzupełnij informacje w **Dodaj alert dziennika aktywności** strony i upewnij się, ustaw następujące opcje **kryteria**:
   - **Kategoria zdarzenia**: kondycja usługi
   - **Usługi**: zestawy skalowania maszyn wirtualnych i maszyn wirtualnych
   - **Typ**: Planowana konserwacja 
    
Aby dowiedzieć się więcej na temat konfigurowania alertów dziennika aktywności, zobacz [Tworzenie alertów dziennika aktywności](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Uruchom konserwację na zestawie skalowania maszyn wirtualnych z portalu

Podczas przeglądania omówienie zestawów skalowania maszyn wirtualnych, można zobaczyć więcej konserwacji powiązane szczegóły. Jeśli co najmniej jedna maszyna wirtualna w zestawie skalowania maszyn wirtualnych jest uwzględniona w fali planowanej konserwacji, wstążki nowe powiadomienie zostanie dodana w górnej części strony. Możesz kliknąć na Wstążce powiadomienie, aby przejść do **konserwacji** strony, aby zobaczyć, które wystąpienie maszyny wirtualnej jest zależna od planowanej konserwacji. 

Z tego miejsca można uruchomić konserwacji, zaznaczając pole odpowiadające dotyczy maszyny wirtualnej i klikając **Uruchom konserwację** opcji.

Po rozpoczęciu konserwacji wykorzystywanych maszyn wirtualnych w zestawie skalowania maszyn wirtualnych będą poddawane konserwacji i jest tymczasowo niedostępny. Jeśli zdarzyło Ci się przeoczyć okna samoobsługi, nadal można wyświetlić okno, gdy zestaw skalowania maszyn wirtualnych będzie obsługiwany przez platformę Azure.
 
## <a name="check-maintenance-status-using-powershell"></a>Sprawdzanie stanu konserwacji przy użyciu programu PowerShell

Można użyć programu Azure Powershell, aby zobaczyć, kiedy maszyn wirtualnych w zestawach skalowania maszyn wirtualnych jest zaplanowana do konserwacji. Informacje o planowanej konserwacji jest dostępne z [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) polecenia cmdlet, gdy używasz `-InstanceView` parametru.
 
Informacje o konserwacji jest zwracany tylko wtedy, gdy planowanej konserwacji. Jeśli zaplanowano konserwację, która ma wpływ na wystąpieniu maszyny Wirtualnej, polecenie cmdlet nie zwraca żadnych informacji konserwacji. 

```powershell
Get-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

W obszarze MaintenanceRedeployStatus zwracane są następujące właściwości: 
| Wartość | Opis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Wskazuje, czy możesz uruchomić konserwację na maszynie Wirtualnej, w tym momencie ||
| PreMaintenanceWindowStartTime         | Początek okna obsługi samoobsługi podczas można zainicjować obsługi na maszynie Wirtualnej ||
| PreMaintenanceWindowEndTime           | Koniec samoobsługi oknem obsługi po można zainicjować obsługi na maszynie Wirtualnej ||
| MaintenanceWindowStartTime            | Na początek zaplanowanej konserwacji, w którym Azure powoduje zainicjowanie konserwacji na maszynie Wirtualnej ||
| MaintenanceWindowEndTime              | Koniec okna zaplanowanych konserwacji, w którym Azure powoduje zainicjowanie konserwacji na maszynie Wirtualnej ||
| LastOperationResultCode               | Wynik ostatniej próby umożliwiającej samodzielne zainicjowanie konserwacji na maszynie Wirtualnej ||



### <a name="start-maintenance-on-your-vm-instance-using-powershell"></a>Uruchom konserwację w ramach wystąpienia maszyny Wirtualnej przy użyciu programu PowerShell

Możesz uruchomić konserwację na maszynie Wirtualnej, jeśli **IsCustomerInitiatedMaintenanceAllowed** jest ustawiona na wartość true przy użyciu [polecenia Set-AzureRmVmss](/powershell/module/azurerm.compute/set-azurermvmss) polecenia cmdlet z `-PerformMaintenance` parametru.

```powershell
Set-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-using-cli"></a>Sprawdzanie stanu konserwacji przy użyciu interfejsu wiersza polecenia

Informacje o planowanej konserwacji są widoczne przy użyciu [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Informacje o konserwacji jest zwracany tylko wtedy, gdy planowanej konserwacji. Jeśli brak zaplanowanej konserwacji, który wpływa na wystąpieniu maszyny Wirtualnej, polecenie nie zwraca żadnych informacji o konserwacji. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Dla każdego wystąpienia maszyny Wirtualnej w obszarze MaintenanceRedeployStatus zwracane są następujące właściwości: 
| Wartość | Opis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Wskazuje, czy możesz uruchomić konserwację na maszynie Wirtualnej, w tym momencie ||
| PreMaintenanceWindowStartTime         | Początek okna obsługi samoobsługi podczas można zainicjować obsługi na maszynie Wirtualnej ||
| PreMaintenanceWindowEndTime           | Koniec samoobsługi oknem obsługi po można zainicjować obsługi na maszynie Wirtualnej ||
| MaintenanceWindowStartTime            | Na początek zaplanowanej konserwacji, w którym Azure powoduje zainicjowanie konserwacji na maszynie Wirtualnej ||
| MaintenanceWindowEndTime              | Koniec okna zaplanowanych konserwacji, w którym Azure powoduje zainicjowanie konserwacji na maszynie Wirtualnej ||
| LastOperationResultCode               | Wynik ostatniej próby umożliwiającej samodzielne zainicjowanie konserwacji na maszynie Wirtualnej ||


### <a name="start-maintenance-on-your-vm-instance-using-cli"></a>Uruchom konserwację w ramach wystąpienia maszyny Wirtualnej przy użyciu interfejsu wiersza polecenia

Poniższe wywołanie zainicjuje konserwację na wystąpieniu maszyny Wirtualnej, jeśli `IsCustomerInitiatedMaintenanceAllowed` jest ustawiona na wartość true.

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Często zadawane pytania


**Pyt.: Dlaczego należy teraz przeprowadzić ponowny rozruch maszyny wirtualne?**

**Odp.:** podczas gdy większość aktualizacji i uaktualnień do platformy Azure nie miało wpływu na dostępność maszyn wirtualnych, istnieją przypadki, w których nie możemy uniknąć, ponowne uruchamianie maszyn wirtualnych hostowanych na platformie Azure. Zakumulowaliśmy kilka zmian, które wymagają ponownego uruchomienia naszych serwerów, które będą powodować ponowny rozruch maszyn wirtualnych.

**Pyt.: Jeśli I postępuj zgodnie z zaleceniami, usługi wysokiej dostępności przy użyciu zestawu dostępności, mogę bezpieczne?**

**Odp.:** zestawu maszyn wirtualnych wdrożonych w dostępności lub zestawach skalowania maszyn wirtualnych obsługują pojęcie domen aktualizacji (UD). Podczas przeprowadzania konserwacji platforma Azure honoruje ograniczenia domen aktualizacji i nie zostanie wykonany ponowny rozruch maszyn wirtualnych z różnych UD (w tym samym zestawie dostępności).  Azure oczekuje również co najmniej 30 minut przed przejściem do następnej grupy maszyn wirtualnych. 

Aby uzyskać więcej informacji na temat wysokiej dostępności, zobacz [regiony i dostępność maszyn wirtualnych na platformie Azure](../virtual-machines/windows/regions-and-availability.md).

**P: jak otrzymywanie powiadomień o planowanej konserwacji?**

**Odp.:** planowanej konserwacji rozpoczyna się przez ustawienie harmonogramu na jednym lub wielu regionach platformy Azure. Wkrótce po wiadomość e-mail z powiadomieniem są wysyłane do właścicieli subskrypcji (jedną wiadomość e-mail na subskrypcję). Dodatkowe kanały i adresatów dla tego powiadomienia można konfigurować za pomocą alertów dziennika aktywności. W przypadku, gdy wdrożono maszynę wirtualną w regionie, gdzie jest już zaplanowana planowanej konserwacji, nie będą otrzymywać powiadomienia, ale raczej muszą sprawdzać stan konserwacji maszyny wirtualnej.

**P: nie widzę żadnego wskazania dotyczącego planowanej konserwacji w portalu, programu Powershell lub interfejsu wiersza polecenia. Co jest nie tak?**

**Odp.:** informacje dotyczące planowanej konserwacji są dostępne podczas fali planowanej konserwacji tylko dla maszyn wirtualnych, które będą mieć wpływ na jej. Innymi słowy Jeśli dane nie są wyświetlane, możliwe że fala konserwacji została już zakończona (lub Nierozpoczęte) lub maszyny wirtualnej jest już hostowana na zaktualizowanym serwerze.

**P: czy istnieje sposób, aby dowiedzieć się, kiedy Moja maszyna wirtualna ma wpływ na?**

**Odp.:** podczas ustawiania harmonogramu, definiujemy przedział czasu w kilka dni. Jednak dokładne sekwencjonowanie serwerów (i maszyn wirtualnych), w tym przedziale czasu jest nieznany. Klienci, którzy chcieliby wiedzieć dokładny czas dla maszyn wirtualnych, można użyć [zaplanowane zdarzenia](../virtual-machines/windows/scheduled-events.md) zapytania z poziomu maszyny wirtualnej i Otrzymuj powiadomienie 15 minut przed ponownym rozruchem maszyny Wirtualnej.

**Pytanie: jak długo potrwa ponowne uruchomienie mojej maszyny wirtualnej?**

**Odp.:** w zależności od rozmiaru maszyny Wirtualnej, ponowne uruchomienie komputera może potrwać kilka minut, w oknie konserwacji samoobsługowej. Podczas Azure zainicjował ponowne uruchomienia w okna zaplanowanej konserwacji podczas ponownego rozruchu zwykle zajmie około 25 minut. Należy pamiętać, że w przypadku, gdy używasz usługi Cloud Services (internetowy proces roboczy), zestawy skalowania maszyn wirtualnych lub zestawów dostępności, będziesz mieć możliwość wybrania 30 minut między poszczególnymi grupami z maszyn wirtualnych (UD) podczas okna zaplanowanej konserwacji. 

**P: nie widzę żadnych informacji konserwacji na moich maszynach wirtualnych. Czym jest problem?**

**Odp.:** istnieje kilka powodów dlaczego nie widzisz żadnych informacji konserwacji na maszynach wirtualnych:
   - Używane są oznaczone jako Microsoft wewnętrzne subskrypcji.
   - Maszyny wirtualne nie jest zaplanowana do konserwacji. Możliwe, że fala konserwacji została zakończona, anulowana lub zmodyfikować tak, aby maszyny wirtualne są nie wpływa już go.
   - Nie masz **konserwacji** kolumną dodaną do widoku listy maszyn wirtualnych. Dodaliśmy tę kolumnę do widoku domyślnego, klienci, którzy skonfigurowali, aby zobaczyć kolumn innych niż domyślne należy ręcznie dodać **konserwacji** kolumnę do widoku listy ich maszyn wirtualnych.

**Pytanie: Moja maszyna wirtualna jest zaplanowana do konserwacji po raz drugi. Dlaczego?**

**Odp.:** istnieje kilka przypadków użycia, w której można znaleźć maszyny Wirtualnej zaplanowana do konserwacji po zakończeniu konserwacji i ponownego wdrożenia:
   - Firma Microsoft ma anulowane fala konserwacji i ponownego uruchomienia go przy użyciu różnych ładunku. Możliwe, że Wykryliśmy, że ładunek błędowi i po prostu należy wdrożyć dodatkowe ładunku.
   - Maszyna wirtualna działała *usługi naprawiane* do innego węzła ze względu na usterkę sprzętową.
   - Wybrano, aby zatrzymać (Cofnij ich przydział) i uruchom ponownie maszynę Wirtualną.
   - Masz **automatyczne zamknięcie** włączone dla maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak możesz się zarejestrować zdarzenia konserwacji z w ramach maszyny Wirtualnej przy użyciu [Scheduled Events](../virtual-machines/windows/scheduled-events.md).
