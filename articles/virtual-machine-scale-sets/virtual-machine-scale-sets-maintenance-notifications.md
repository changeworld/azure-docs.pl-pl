---
title: Powiadomienia o konserwacji dla skalowania maszyn wirtualnych ustawia na platformie Azure | Dokumentacja firmy Microsoft
description: Wyświetl powiadomienia o konserwacji i uruchamianie konserwacji samoobsługowej dla zestawów skalowania maszyn wirtualnych na platformie Azure.
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
ms.openlocfilehash: 31d4829c6adaf4bd5392ef393dcaefbeb7dc6255
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60618459"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Planowana konserwacja powiadomienia dotyczące zestawów skalowania maszyn wirtualnych


Platforma Azure przeprowadza okresowe aktualizacje w celu poprawy niezawodności, wydajności i bezpieczeństwa infrastruktury hosta dla maszyn wirtualnych (VM). Aktualizacje mogą obejmować poprawek środowiska macierzystego lub uaktualniania i likwidowaniu sprzętu. Większość aktualizacji nie wpływają na obsługiwanych maszynach wirtualnych. Jednak aktualizacje mają wpływ na maszyny wirtualne w tych scenariuszach:

- Jeśli konserwacja nie wymaga ponownego uruchomienia systemu, platforma Azure używa migracji w miejscu wstrzymać maszyny Wirtualnej, gdy host jest aktualizowana. Operacje konserwacji, które nie wymagają ponownego uruchomienia komputera są domena błędów zastosowane przez domenę błędów. Postęp jest zatrzymana, jeśli wszystkie sygnały kondycji ostrzeżenia są odbierane.

- Jeśli przeprowadzenia konserwacji wymagane jest ponowne uruchomienie komputera, pojawi się powiadomienie, który pokazuje, kiedy jest planowana konserwacja. W takich przypadkach są podane przedział czasu, w którym możesz uruchomić konserwację samodzielnie podczas działa najlepiej dla Ciebie.


Planowana konserwacja, która wymaga ponownego uruchomienia zaplanowano etapami. Każdy etap ma inny zakres (regionów):

- Falą rozpoczyna się od powiadomienie dla klientów. Domyślnie powiadomienia są wysyłane do właściciela subskrypcji i współwłaścicieli. Można dodać adresatów oraz opcje obsługi komunikatów, takie jak wiadomości e-mail, wiadomości SMS i elementy webhook do powiadomienia za pomocą platformy Azure [alertów dziennika aktywności](../azure-monitor/platform/activity-logs-overview.md).  
- Z zastrzeżeniem *samoobsługi okna* ma zostać udostępnione. W tym oknie możesz znaleźć, której maszyny wirtualne znajdują się w fazie badania. Możesz proaktywnie uruchomić konserwację, zgodnie z potrzebami planowania.
- Po oknie samoobsługi *zaplanowanego okna obsługi* rozpoczyna się. W pewnym momencie podczas tego okna platforma Azure planuje i stosuje wymaganej obsługi technicznej dla maszyny wirtualnej. 

Cel, że dwa okna jest zapewnienie wystarczająco dużo czasu, aby uruchomić konserwacji i ponowne uruchomienie maszyny Wirtualnej codzienne, gdy Azure nastąpi automatyczne uruchomienie konserwacji.

Zapytanie dla okna obsługi dla maszyn wirtualnych w zestawie skalowania maszyn wirtualnych i Rozpocznij konserwacji samoobsługowej, można użyć witryny Azure portal, programu PowerShell, interfejsu API REST i wiersza polecenia platformy Azure.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>W oknie samoobsługi należy rozpocząć konserwacji?  

Poniższe wskazówki może pomóc zdecydować, czy można uruchomić obsługi w czasie, który wybierzesz.

> [!NOTE] 
> Konserwacji samoobsługowej może nie być dostępne dla wszystkich maszyn wirtualnych. Aby ustalić, czy aktywnego ponownego wdrażania są dostępne dla maszyny Wirtualnej, poszukaj **teraz uruchomić** stanu konserwacji. Obecnie konserwacji samoobsługowej nie jest dostępna dla usług Azure Cloud Services (internetowy proces roboczy) i usługi Azure Service Fabric.


Konserwacji samoobsługowej nie jest zalecane w przypadku wdrożeń korzystających z *zestawy dostępności*. Zestawy dostępności są konfiguracje o wysokiej dostępności, w których tylko jedna aktualizacja dotyczy domeny w dowolnym momencie. Dla zestawów dostępności:

- Pozwól Azure wyzwolić konserwacji. Konserwacji, która wymaga ponownego uruchomienia konserwacja odbywa się domena aktualizacji według domeny aktualizacji. Domeny aktualizacji nie otrzymywać obsłudze sekwencyjnie. Ma 30-minutowe pauzę między domenami aktualizacji.
- Jeśli do tymczasowej utraty niektórych pojemności (liczba domen aktualizacji/1) jest istotna, możesz łatwo kompensuje utraty, przydzielając dodatkowe wystąpienia w okresie konserwacji.
- Konserwacji, która nie wymaga ponownego uruchomienia systemu aktualizacje są stosowane na poziomie domeny błędów. 
    
**Nie** Użyj konserwacji samoobsługowej w następujących scenariuszach: 

- Jeśli wyłączysz maszyn wirtualnych, ręcznie, używając usługi DevTest Labs, za pomocą automatycznego zamykania lub postępując zgodnie z harmonogramem. Konserwacji samoobsługowej, w następujących scenariuszach może być przywrócenie stanu konserwacji i spowodować przestój dodatkowe.
- Na maszynach wirtualnych krótkotrwałe czy wiesz, zostaną usunięte przed upływem fala konserwacji. 
- W przypadku obciążeń o dużych stanie przechowywanych w (efemeryczne) dysk lokalny, który ma zostać zachowana po aktualizacji. 
- Jeżeli zmienisz rozmiar maszyny Wirtualnej często. W tym scenariuszu może być przywrócenie stanu konserwacji. 
- Jeśli przyjęły zaplanowanych zdarzeń, umożliwiające proaktywne trybu failover lub łagodne zamykanie obciążenia 15 minut przed rozpoczęciem konserwacji zamknięcia.

**Czy** Użyj konserwacji samoobsługowej, jeśli planuje się uruchamianie nieprzerwaną podczas fazy zaplanowanej konserwacji maszyny Wirtualnej, a żaden z poprzednich counterindications. 

Najlepiej użyć konserwacji samoobsługowej w następujących przypadkach:

- Musisz przekazać okna obsługi dokładnie w taki sposób, aby zarządzanie lub klienta. 
- Musisz wykonaj konserwację w określonym dniu. 
- Wymagane jest sterowanie sekwencję konserwacji, na przykład w aplikacji wielowarstwowej, aby zagwarantować bezpieczne odzyskiwanie.
- Potrzebujesz więcej niż 30 minut czasu odzyskiwania maszyny Wirtualnej między dwiema domenami aktualizacji. Aby kontrolować czas między domenami aktualizacji, możesz wyzwolić konserwacji w domenie aktualizacji co maszyny wirtualne w danym momencie.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Zestawy skalowania maszyn wirtualnych widok, do których zastosowano konserwacji w portalu

Podczas planowanej konserwacji jest zaplanowane, można wyświetlić listę zestawów skalowania maszyn wirtualnych, których dotyczy wave zbliżającej się konserwacji za pomocą witryny Azure portal. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz **wszystkich usług**, a następnie wybierz pozycję **zestawy skalowania maszyn wirtualnych**.
3. W obszarze **zestawy skalowania maszyn wirtualnych**, wybierz opcję **Upravit sloupce** aby otworzyć listę dostępnych kolumn.
4. W **dostępnych kolumn** zaznacz **konserwacji samoobsługowej**, a następnie przenieś go do **wybrane kolumny** listy. Wybierz przycisk **Zastosuj**.  

    Zapewnienie **konserwacji samoobsługowej** elementu łatwiej znaleźć, można zmienić opcji listy rozwijanej w **dostępnych kolumn** sekcja **wszystkich** do  **Właściwości**.

**Konserwacji samoobsługowej** kolumny będzie teraz widoczna na liście zestawów skalowania maszyn wirtualnych. Każdy zestaw skalowania maszyn wirtualnych może mieć jedną z następujących wartości dla kolumny konserwacji samoobsługowej:

| Wartość | Opis |
|-------|-------------|
| Yes | Co najmniej jedną maszynę Wirtualną w zestawie skalowania maszyn wirtualnych jest oknem samoobsługi. Możesz uruchomić konserwację w dowolnym momencie, w tym oknie samoobsługi. | 
| Nie | Bez maszyn wirtualnych w oknie samoobsługi w skali maszyny wirtualnej objęte ustawiono. | 
| - | Skalowania maszyn wirtualnych zestawy nie są częścią planowanej konserwacji.| 

## <a name="notification-and-alerts-in-the-portal"></a>Powiadomienia i alerty w portalu

Azure komunikuje się harmonogram planowanej konserwacji, wysyłając wiadomość e-mail do grupy właściciel i współwłaściciele subskrypcji. Możesz dodać adresatów i kanały do komunikacji przez tworzenie alertów dziennika aktywności. Aby uzyskać więcej informacji, zobacz [monitorowania aktywności subskrypcji z dziennika aktywności platformy Azure](../azure-monitor/platform/activity-logs-overview.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz **Monitor**. 
3. W **monitorowanie — alerty (klasyczne)** okienku wybierz **+ Dodaj alert dziennika aktywności**.
4. Na **Dodaj alert dziennika aktywności** strony, wybierz lub wprowadź żądane informacje. W **kryteria**, upewnij się, że są ustawione następujące wartości:
   - **Kategoria zdarzenia**: Wybierz **kondycji usługi**.
   - **Usługi**: Wybierz **zestawy skalowania maszyn wirtualnych i maszyn wirtualnych**.
   - **Typ**: Wybierz **planowanej konserwacji**. 
    
Aby dowiedzieć się więcej o sposobie konfigurowania alertów dziennika aktywności, zobacz [alerty Tworzenie dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Uruchom konserwację na zestawie skalowania maszyn wirtualnych z portalu

Można wyświetlić więcej szczegółów dotyczących konserwacji w ramach przeglądu zestawów skalowania maszyn wirtualnych. Jeśli co najmniej jedną maszynę Wirtualną w zestawie skalowania maszyn wirtualnych jest uwzględniony w fali planowanej konserwacji, nowe powiadomienie wstążki jest dodawany w górnej części strony. Wybierz na Wstążce powiadomienie, aby przejść do **konserwacji** strony. 

Na **konserwacji** strony, możesz zobaczyć, które wystąpienie maszyny Wirtualnej jest zależna od planowanej konserwacji. Aby uruchomić konserwacji, zaznacz pole wyboru, które odpowiada dotyczy maszyny Wirtualnej. Następnie wybierz **Uruchom konserwację**.

Po rozpoczęciu konserwacji objęte maszyny wirtualne w zestawie skalowania maszyn wirtualnych poddawane konserwacji i są tymczasowo niedostępne. Jeśli zdarzyło Ci się przeoczyć okna samoobsługi, nadal widać w danym przedziale czasu, gdy zestaw skalowania maszyn wirtualnych będzie obsługiwany przez platformę Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Sprawdzanie stanu konserwacji przy użyciu programu PowerShell

Można użyć programu Azure PowerShell, aby zobaczyć, kiedy maszyn wirtualnych w zestawach skalowania maszyn wirtualnych jest zaplanowana do konserwacji. Informacje o planowanej konserwacji jest dostępna za pomocą [Get AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) polecenia cmdlet, gdy używasz `-InstanceView` parametru.
 
Informacje o konserwacji jest zwracany tylko wtedy, gdy planowanej konserwacji. Jeśli zaplanowano konserwację, który wpływa na wystąpieniu maszyny Wirtualnej, polecenie cmdlet nie zwraca żadnych informacji o konserwacji. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Następujące właściwości są zwracane w obszarze **MaintenanceRedeployStatus**: 

| Wartość | Opis |

|-------|---------------| | IsCustomerInitiatedMaintenanceAllowed | Wskazuje, czy możesz uruchomić konserwację na maszynie Wirtualnej, w tym momencie. | | PreMaintenanceWindowStartTime | Początek okna obsługi samoobsługi podczas można zainicjować obsługi na maszynie Wirtualnej. | | PreMaintenanceWindowEndTime | Koniec samoobsługi oknem obsługi po można zainicjować obsługi na maszynie Wirtualnej. | | MaintenanceWindowStartTime | Na początek zaplanowanej konserwacji w którym Azure powoduje zainicjowanie konserwacji na maszynie Wirtualnej. | | MaintenanceWindowEndTime | Koniec okna zaplanowanych konserwacji, w którym Azure powoduje zainicjowanie konserwacji na maszynie Wirtualnej. | | LastOperationResultCode | Wynik ostatniej próby umożliwiającej samodzielne zainicjowanie konserwacji na maszynie Wirtualnej. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Uruchom konserwację w ramach wystąpienia maszyny Wirtualnej przy użyciu programu PowerShell

Możesz uruchomić konserwację na maszynie Wirtualnej, jeśli **IsCustomerInitiatedMaintenanceAllowed** ustawiono **true**. Użyj [AzVmss zestaw](/powershell/module/az.compute/set-azvmss) polecenia cmdlet z `-PerformMaintenance` parametru.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Sprawdzanie stanu konserwacji przy użyciu interfejsu wiersza polecenia

Można wyświetlić informacje o planowanej konserwacji za pomocą [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Informacje o konserwacji jest zwracany tylko wtedy, gdy planowanej konserwacji. Jeśli zaplanowano nie obsługi, który wpływa na wystąpieniu maszyny Wirtualnej, polecenie nie zwraca żadnych informacji o konserwacji. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Następujące właściwości są zwracane w obszarze **MaintenanceRedeployStatus** dla każdego wystąpienia maszyny Wirtualnej: 

| Wartość | Opis |

|-------|---------------| | IsCustomerInitiatedMaintenanceAllowed | Wskazuje, czy możesz uruchomić konserwację na maszynie Wirtualnej, w tym momencie. | | PreMaintenanceWindowStartTime | Początek okna obsługi samoobsługi podczas można zainicjować obsługi na maszynie Wirtualnej. | | PreMaintenanceWindowEndTime | Koniec samoobsługi oknem obsługi po można zainicjować obsługi na maszynie Wirtualnej. | | MaintenanceWindowStartTime | Na początek zaplanowanej konserwacji w którym Azure powoduje zainicjowanie konserwacji na maszynie Wirtualnej. | | MaintenanceWindowEndTime | Koniec okna zaplanowanych konserwacji, w którym Azure powoduje zainicjowanie konserwacji na maszynie Wirtualnej. | | LastOperationResultCode | Wynik ostatniej próby umożliwiającej samodzielne zainicjowanie konserwacji na maszynie Wirtualnej. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Uruchom konserwację w ramach wystąpienia maszyny Wirtualnej przy użyciu interfejsu wiersza polecenia

Poniższe wywołanie powoduje zainicjowanie obsługi na wystąpieniu maszyny Wirtualnej, jeśli `IsCustomerInitiatedMaintenanceAllowed` ustawiono **true**:

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Często zadawane pytania

**Pyt.: Dlaczego należy teraz przeprowadzić ponowny rozruch maszyn wirtualnych?**

**Odp.:** Mimo że większość aktualizacji i uaktualnień, aby platforma Azure nie mają wpływu na dostępność maszyn wirtualnych, w niektórych przypadkach, nie możemy uniknąć ponownego uruchamiania maszyn wirtualnych hostowanych na platformie Azure. Zakumulowaliśmy kilka zmian, które wymagają ponownego uruchomienia naszych serwerów, które będą powodować ponowne uruchomienie maszyny Wirtualnej.

**Pyt.: Jeśli I Obserwuj zalecenia wysokiej dostępności przy użyciu dostępności zestawu, mogę bezpieczne?**

**Odp.:** Maszyny wirtualne wdrożone w dostępności zestawu lub w zestawach skalowania maszyn wirtualnych za pomocą domeny aktualizacji. Podczas przeprowadzania konserwacji platforma Azure honoruje ograniczenia domen aktualizacji i nie ponowny rozruch maszyn wirtualnych w innej domenie aktualizacji (w tym samym zestawie dostępności). Azure oczekuje również co najmniej 30 minut przed przejściem do następnej grupy maszyn wirtualnych. 

Aby uzyskać więcej informacji na temat wysokiej dostępności, zobacz [regiony i dostępność maszyn wirtualnych na platformie Azure](../virtual-machines/windows/regions-and-availability.md).

**Pyt.: Jak mogę otrzymywać powiadomienia o planowanej konserwacji?**

**Odp.:** Uruchamia planowanej konserwacji przez ustawienie harmonogramu na jednym lub wielu regionach platformy Azure. Wkrótce po wiadomość e-mail z powiadomieniem są wysyłane do właścicieli subskrypcji (jedną wiadomość e-mail na subskrypcję). Aby dodać kanały i adresatów dla tego powiadomienia, korzystanie z alertów dziennika aktywności. Jeśli wdrożysz maszynę Wirtualną w regionie, w którym jest już zaplanowana planowanej konserwacji nie otrzymasz powiadomienie. Zamiast tego sprawdzanie stanu konserwacji maszyny Wirtualnej.

**Pyt.: Nie widzę żadnego wskazania dotyczącego planowanej konserwacji w portalu, programu PowerShell lub interfejsu wiersza polecenia. Co jest nie tak?**

**Odp.:** Informacje dotyczące planowanej konserwacji jest dostępne podczas fali planowanej konserwacji tylko dla maszyn wirtualnych, których dotyczy planowanej konserwacji. Jeśli nie widzisz danych, fala konserwacji mogą być już zakończona (lub nie jest uruchomiona) lub maszyny Wirtualnej może być już hostowany na serwerze zaktualizowane.

**Pyt.: Czy istnieje sposób wiedzieć dokładnie w Jeśli Moja maszyna wirtualna będzie miała wpływ?**

**Odp.:** Gdy firma Microsoft Ustaw harmonogram, definiujemy przedział czasu w kilka dni. Dokładne sekwencjonowanie serwerów (i maszyn wirtualnych) w tym oknie (przedziale czasu) jest nieznane. Jeśli chcesz wiedzieć, dokładny czas maszyny wirtualne zostaną zaktualizowane, możesz użyć [zaplanowane zdarzenia](../virtual-machines/windows/scheduled-events.md). Użycie zaplanowanych zdarzeń, możesz zapytania z poziomu maszyny Wirtualnej i Otrzymuj powiadomienie 15 minut przed ponownym rozruchem maszyny Wirtualnej.

**Pyt.: Jak długo potrwa ponowne uruchomienie mojej maszyny Wirtualnej?**

**Odp.:**  W zależności od rozmiaru maszyny Wirtualnej ponowne uruchomienie komputera może potrwać kilka minut, w oknie konserwacji samoobsługowej. Podczas zainicjowane na platformie Azure jest uruchamiany ponownie w okna zaplanowanej konserwacji ponownego uruchamiania zwykle trwa około 25 minut. Jeśli korzystasz z usług Cloud Services (internetowy proces roboczy), zestawy skalowania maszyn wirtualnych, lub zestawów dostępności, otrzymują 30 minut między poszczególnymi grupami maszyn wirtualnych (domena aktualizacji) podczas okna zaplanowanej konserwacji. 

**Pyt.: Nie widzę żadnych informacji konserwacji na moich maszynach wirtualnych. Czym jest problem?**

**Odp.:** Istnieje kilka powodów dlaczego nie widać żadnych informacji konserwacji na maszynach wirtualnych:
   - W przypadku korzystania z subskrypcji, oznaczone jako *Microsoft Internal*.
   - Maszyny wirtualne nie są zaplanowane do obsługi. Może być czy fala konserwacji zakończyło się, zostało anulowane lub został zmodyfikowany, tak aby maszyny wirtualne nie są już dotyczą.
   - Nie masz **konserwacji** kolumną dodaną do widoku listy maszyn wirtualnych. Mimo że dodaliśmy tę kolumnę do widoku domyślnego, jeśli skonfigurujesz widoku w taki sposób, aby zobaczyć kolumn innych niż domyślne, należy ręcznie dodać **konserwacji** kolumny do widoku listy maszyn wirtualnych.

**Pyt.: Moja maszyna wirtualna jest zaplanowana do konserwacji po raz drugi. Dlaczego?**

**Odp.:** W kilku przypadkach maszyna wirtualna jest zaplanowana do konserwacji po już ukończone swoje konserwacji i ponownego wdrożenia:
   - Firma Microsoft ma anulowane fala konserwacji i ponownego uruchomienia go przy użyciu różnych ładunku. Może być wykryliśmy uszkodzoną ładunek, a po prostu należy wdrożyć dodatkowe ładunku.
   - Maszyna wirtualna działała *usługi naprawiane* do innego węzła ze względu na usterkę sprzętową.
   - Wybrano, aby zatrzymać (Cofnij ich przydział) i uruchom ponownie maszynę Wirtualną.
   - Masz **automatyczne zamknięcie** włączone dla maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak zarejestrować zdarzenia konserwacji z poziomu maszyny Wirtualnej przy użyciu [zaplanowane zdarzenia](../virtual-machines/windows/scheduled-events.md).
