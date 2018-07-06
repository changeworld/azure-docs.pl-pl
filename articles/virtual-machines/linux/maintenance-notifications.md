---
title: Obsługa powiadomienia o konserwacji maszyn wirtualnych systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Wyświetl powiadomienia o konserwacji dla maszyn wirtualnych systemu Linux działających na platformie Azure i uruchomić konserwacji samoobsługowej.
services: virtual-machines-linux
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/02/2018
ms.author: shants
ms.openlocfilehash: 12a3c4556de21bb0c0dd6b09458943fb03092532
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866131"
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Obsługa Planned maintenance notifications zaplanowanej dla maszyn wirtualnych systemu Linux

Platforma Azure przeprowadza okresowe aktualizacje w celu poprawy niezawodności, wydajności i bezpieczeństwa infrastruktury hosta dla maszyn wirtualnych. Aktualizacje są zmiany, takie jak stosowanie poprawek środowiska macierzystego lub uaktualniania i likwidowaniu sprzętu. Większość tych aktualizacji jest przeprowadzana bez żadnego wpływu na obsługiwanych maszynach wirtualnych. Jednakże istnieją przypadki, w których aktualizacje mają wpływ:

- Jeśli konserwacja nie wymaga ponownego uruchomienia systemu, platforma Azure używa migracji w miejscu wstrzymać maszyny Wirtualnej, gdy host jest aktualizowana. Te operacje konserwacji bez rebootful są domena błędów zastosowane przez domenę błędów, a postęp zostanie zatrzymany, jeśli wszystkie sygnały kondycji ostrzeżenia są odbierane.

- Jeśli przeprowadzenia konserwacji wymagane jest ponowne uruchomienie komputera, otrzymasz powiadomienie o podczas planowanej konserwacji. W takich przypadkach są podane przedział czasu, w którym możesz uruchomić konserwację samodzielnie, kiedy pracuje dla Ciebie.


Planowana konserwacja, która wymaga ponownego uruchomienia zaplanowano etapami. Każdy etap ma inny zakres (regiony).

- Falą rozpoczyna się od powiadomienie dla klientów. Domyślnie powiadomienia są wysyłane do właściciela subskrypcji i współwłaścicieli. Możesz dodać więcej adresatów i opcje obsługi komunikatów, takie jak wiadomości e-mail, wiadomości SMS i elementy webhook, do powiadomienia za pomocą platformy Azure [alertów dziennika aktywności](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).  
- W momencie zgłoszenia *samoobsługi okna* ma zostać udostępnione. W tym oknie możesz znaleźć, w której maszyny wirtualne znajdują się w tej fazie badania i aktywnie Uruchom konserwację zgodnie z potrzebami planowania.
- Po oknie samoobsługi *zaplanowanego okna obsługi* rozpoczyna się. W pewnym momencie podczas tego okna platforma Azure planuje i dotyczy wymagane konserwacji maszyny wirtualnej. 

Cel, że dwa okna jest zapewnienie wystarczająco dużo czasu, aby uruchomić konserwacji i ponowne uruchomienie maszyny wirtualnej codzienne, gdy Azure nastąpi automatyczne uruchomienie konserwacji.


Można znaleźć okna obsługi dla maszyn wirtualnych, a następnie uruchomić konserwacji samoobsługowej, można użyć witryny Azure portal, programu PowerShell, interfejsu API REST i interfejsu wiersza polecenia.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Należy rozpocząć konserwacji za pomocą przedziale samoobsługi?  

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
- Jeśli zdecydowali się zaplanowanych zdarzeń, umożliwiające proaktywne trybu failover lub łagodne zamykanie obciążenie, 15 minut przed rozpoczęciem konserwacji zamknięcia

**Użyj** konserwacji samoobsługowej, jeśli zamierzasz uruchomić nieprzerwaną podczas fazy zaplanowanej konserwacji maszyny Wirtualnej i żadna oznaczeń zaradcze wymienione powyżej nie ma zastosowania. 

Najlepiej użyć konserwacji samoobsługowej w następujących przypadkach:
- Musisz przekazać dokładnie konserwacyjne do zarządzania lub odbiorcy końcowego. 
- Trzeba wykonać konserwacji przed upływem określonego terminu. 
- Wymagane jest sterowanie sekwencję konserwacji, na przykład wielowarstwową aplikację w celu zagwarantowania bezpiecznej odzyskiwania.
- Potrzebujesz więcej niż 30 minut czasu odzyskiwania maszyny Wirtualnej między dwiema domenami aktualizacji (domenami aktualizacji). Aby kontrolować czas między domenami aktualizacji, możesz wyzwolić konserwacji w sieci maszyn wirtualnych z jednej domeny aktualizacji (UD) w danym momencie.



## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>Znajdowanie maszyn wirtualnych jest zaplanowana do konserwacji przy użyciu interfejsu wiersza polecenia

Informacje o planowanej konserwacji są widoczne przy użyciu [maszyny wirtualnej platformy azure get-instance-view](/cli/azure/vm?view=azure-cli-latest#az_vm_get_instance_view).
 
Informacje o konserwacji jest zwracany tylko wtedy, gdy planowanej konserwacji. Jeśli zdefiniowano żadnej konserwacji zaplanowane ten wpływ na maszynę Wirtualną, polecenie nie zwraca żadnych informacji konserwacji. 

```azure-cli
az vm get-instance-view -g rgName -n vmName
```

Następujące wartości są zwracane w obszarze MaintenanceRedeployStatus: 

| Wartość | Opis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Wskazuje, czy możesz uruchomić konserwację na maszynie Wirtualnej, w tym momencie ||
| PreMaintenanceWindowStartTime         | Początek okna obsługi samoobsługi podczas można zainicjować obsługi na maszynie Wirtualnej ||
| PreMaintenanceWindowEndTime           | Koniec samoobsługi oknem obsługi po można zainicjować obsługi na maszynie Wirtualnej ||
| MaintenanceWindowStartTime            | Początek okna zaplanowanych konserwacji, w którym Azure powoduje zainicjowanie konserwacji na maszynie Wirtualnej ||
| MaintenanceWindowEndTime              | Koniec okna zaplanowanych konserwacji, w którym Azure powoduje zainicjowanie konserwacji na maszynie Wirtualnej ||
| LastOperationResultCode               | Wynik ostatniej próby umożliwiającej samodzielne zainicjowanie konserwacji na maszynie Wirtualnej ||




## <a name="start-maintenance-on-your-vm-using-cli"></a>Uruchom konserwację na maszynie Wirtualnej przy użyciu interfejsu wiersza polecenia

Poniższe wywołanie spowoduje zainicjowanie konserwacji na maszynie Wirtualnej, jeśli `IsCustomerInitiatedMaintenanceAllowed` jest ustawiona na wartość true.

```azure-cli
az vm perform-maintenance rgName vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="classic-deployments"></a>Klasyczne wdrożenia

Jeśli nadal masz starsze maszyny wirtualne, które zostały wdrożone za pomocą klasycznego modelu wdrażania, możesz korzystanie 1.0 interfejsu wiersza polecenia do wykonywania zapytań w przypadku maszyn wirtualnych i zainicjowanie konserwacji.

Upewnij się, że jesteś w trybie poprawne, aby pracować z klasycznej maszyny Wirtualnej, wpisując:

```
azure config mode asm
```

Aby wyświetlić stan konserwacji maszyny Wirtualnej o nazwie *myVM*, wpisz:

```
azure vm show myVM 
``` 

Aby uruchomić konserwację na klasycznej maszynie Wirtualnej o nazwie *myVM* w *Moja_usługa* usługi i *myDeployment* wdrożenia, należy wpisać:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```


## <a name="faq"></a>Często zadawane pytania


**Pyt.: Dlaczego należy teraz przeprowadzić ponowny rozruch maszyny wirtualne?**

**Odp.:** podczas gdy większość aktualizacji i uaktualnień do platformy Azure nie miało wpływu na dostępność maszyn wirtualnych, istnieją przypadki, w których nie możemy uniknąć, ponowne uruchamianie maszyn wirtualnych hostowanych na platformie Azure. Zakumulowaliśmy kilka zmian, które wymagają ponownego uruchomienia naszych serwerów, które będą powodować ponowny rozruch maszyn wirtualnych.

**Pyt.: Jeśli I postępuj zgodnie z zaleceniami, usługi wysokiej dostępności przy użyciu zestawu dostępności, mogę bezpieczne?**

**Odp.:** zestawu maszyn wirtualnych wdrożonych w dostępności lub zestawach skalowania maszyn wirtualnych obsługują pojęcie domen aktualizacji (UD). Podczas przeprowadzania konserwacji platforma Azure honoruje ograniczenia domen aktualizacji i nie zostanie wykonany ponowny rozruch maszyn wirtualnych z różnych UD (w tym samym zestawie dostępności).  Azure oczekuje również co najmniej 30 minut przed przejściem do następnej grupy maszyn wirtualnych. 

Aby uzyskać więcej informacji na temat wysokiej dostępności, zobacz [regiony i dostępność maszyn wirtualnych na platformie Azure](regions-and-availability.MD).

**P: jak otrzymywanie powiadomień o planowanej konserwacji?**

**Odp.:** planowanej konserwacji rozpoczyna się przez ustawienie harmonogramu na jednym lub wielu regionach platformy Azure. Wkrótce po wiadomość e-mail z powiadomieniem są wysyłane do właścicieli subskrypcji (jedną wiadomość e-mail na subskrypcję). Dodatkowe kanały i adresatów dla tego powiadomienia można konfigurować za pomocą alertów dziennika aktywności. W przypadku, gdy wdrożono maszynę wirtualną w regionie, gdzie jest już zaplanowana planowanej konserwacji, nie będą otrzymywać powiadomienia, ale raczej muszą sprawdzać stan konserwacji maszyny wirtualnej.

**P: nie widzę żadnego wskazania dotyczącego planowanej konserwacji w portalu, programu Powershell lub interfejsu wiersza polecenia. Co jest nie tak?**

**Odp.:** informacje dotyczące planowanej konserwacji są dostępne podczas fali planowanej konserwacji tylko dla maszyn wirtualnych, które będą mieć wpływ na jej. Innymi słowy Jeśli dane nie są wyświetlane, możliwe że fala konserwacji została już zakończona (lub Nierozpoczęte) lub maszyny wirtualnej jest już hostowana na zaktualizowanym serwerze.

**P: czy istnieje sposób, aby dowiedzieć się, kiedy Moja maszyna wirtualna ma wpływ na?**

**Odp.:** podczas ustawiania harmonogramu, definiujemy przedział czasu w kilka dni. Jednak dokładne sekwencjonowanie serwerów (i maszyn wirtualnych), w tym przedziale czasu jest nieznany. Klienci, którzy chcieliby wiedzieć dokładny czas dla maszyn wirtualnych, można użyć [zaplanowane zdarzenia](scheduled-events.md) zapytania z poziomu maszyny wirtualnej i Otrzymuj powiadomienie 15 minut przed ponownym rozruchem maszyny Wirtualnej.

**Pytanie: jak długo potrwa ponowne uruchomienie mojej maszyny wirtualnej?**

**Odp.:** w zależności od rozmiaru maszyny Wirtualnej, ponowne uruchomienie komputera może potrwać kilka minut, w oknie konserwacji samoobsługowej. Podczas Azure zainicjował ponowne uruchomienia w okna zaplanowanej konserwacji podczas ponownego rozruchu zwykle zajmie około 25 minut. Należy pamiętać, że w przypadku, gdy używasz usługi Cloud Services (internetowy proces roboczy), zestawy skalowania maszyn wirtualnych lub zestawów dostępności, będziesz mieć możliwość wybrania 30 minut między poszczególnymi grupami z maszyn wirtualnych (UD) podczas okna zaplanowanej konserwacji.

**Pyt.: co to jest środowisko w przypadku zestawów skalowania maszyn wirtualnych?**

**Odp.:** planowana konserwacja jest teraz dostępna dla zestawów skalowania maszyn wirtualnych. Aby uzyskać instrukcje dotyczące sposobu inicjowania konserwacji samoobsługowej, zobacz sekcję "Jak do -> Zarządzaj -> Planowana konserwacja" w dokumentacji zestawu skalowania maszyn wirtualnych.

**Pyt.: co to jest środowisko w przypadku usług Cloud Services (internetowy proces roboczy) i usługi Service Fabric?**

**Odp.:** podczas planowanej konserwacji wpływa te platformy, klienci korzystający z tych platform są uznawane za bezpieczne, biorąc pod uwagę że tylko maszyny wirtualne w jednej uaktualnienia domeny (UD) będzie mieć wpływ w danym momencie. Konserwacji samoobsługowej nie jest obecnie dostępna w przypadku usług Cloud Services (internetowy proces roboczy) i usługi Service Fabric.

**P: nie widzę żadnych informacji konserwacji na moich maszynach wirtualnych. Czym jest problem?**

**Odp.:** istnieje kilka powodów dlaczego nie widzisz żadnych informacji konserwacji na maszynach wirtualnych:
1.  Używane są oznaczone jako Microsoft wewnętrzne subskrypcji.
2.  Maszyny wirtualne nie jest zaplanowana do konserwacji. Możliwe, że fala konserwacji została zakończona, anulowana lub zmodyfikować tak, aby maszyny wirtualne są nie wpływa już go.
3.  Nie masz **konserwacji** kolumną dodaną do widoku listy maszyn wirtualnych. Dodaliśmy tę kolumnę do widoku domyślnego, klienci, którzy skonfigurowali, aby zobaczyć kolumn innych niż domyślne należy ręcznie dodać **konserwacji** kolumnę do widoku listy ich maszyn wirtualnych.

**Pytanie: Moja maszyna wirtualna jest zaplanowana do konserwacji po raz drugi. Dlaczego?**

**Odp.:** istnieje kilka przypadków użycia, w której można znaleźć maszyny Wirtualnej zaplanowana do konserwacji po zakończeniu konserwacji i ponownego wdrożenia:
1.  Firma Microsoft ma anulowane fala konserwacji i ponownego uruchomienia go przy użyciu różnych ładunku. Możliwe, że Wykryliśmy, że ładunek błędowi i po prostu należy wdrożyć dodatkowe ładunku.
2.  Maszyna wirtualna działała *usługi naprawiane* do innego węzła ze względu na usterkę sprzętową.
3.  Wybrano, aby zatrzymać (Cofnij ich przydział) i uruchom ponownie maszynę Wirtualną.
4.  Masz **automatyczne zamknięcie** włączone dla maszyny Wirtualnej.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak możesz się zarejestrować zdarzenia konserwacji z w ramach maszyny Wirtualnej przy użyciu [Scheduled Events](scheduled-events.md).
