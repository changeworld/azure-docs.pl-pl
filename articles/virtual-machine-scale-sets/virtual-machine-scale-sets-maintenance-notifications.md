---
title: Powiadomienia o konserwacji dla zestawów skalowania maszyn wirtualnych na platformie Azure
description: Wyświetlanie powiadomień o konserwacji i rozpoczęcie samoobsługowej konserwacji zestawów skalowania maszyn wirtualnych na platformie Azure.
author: shants123
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: shants
ms.openlocfilehash: 53ebb7c4710c5455ef90701dc7e94f1b846a874a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062698"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Powiadomienia dotyczące planowanej konserwacji zestawów skalowania maszyn wirtualnych


Platforma Azure okresowo wykonuje aktualizacje w celu zwiększenia niezawodności, wydajności i zabezpieczeń infrastruktury hosta dla maszyn wirtualnych (maszyn wirtualnych). Aktualizacje mogą obejmować instalowanie poprawek środowiska hostingowego lub uaktualnianie i likwidację sprzętu. Większość aktualizacji nie wpływa na hostowane maszyny wirtualne. Jednak aktualizacje wpływają na maszyny wirtualne w następujących scenariuszach:

- Jeśli konserwacja nie wymaga ponownego uruchomienia, platforma Azure używa migracji w miejscu, aby wstrzymać maszynę wirtualną podczas aktualizacji hosta. Operacje konserwacji, które nie wymagają ponownego uruchomienia, są stosowane domeny błędów przez domenę błędów. Postęp jest zatrzymywany w przypadku odbioru jakichkolwiek sygnałów ostrzegawczych.

- Jeśli konserwacja wymaga ponownego uruchomienia, otrzymasz powiadomienie o planowanej konserwacji. W takich przypadkach otrzymasz przedział czasu, który jest zazwyczaj 35 dni, gdzie można rozpocząć konserwację samodzielnie, gdy działa dla Ciebie.


Planowana konserwacja, która wymaga ponownego uruchomienia jest zaplanowana w fale. Każda fala ma inny zakres (regiony):

- Fala rozpoczyna się od powiadomienia do klientów. Domyślnie powiadomienie jest wysyłane do właściciela subskrypcji i współwłaścicieli. Do powiadomień można dodawać adresatów i opcje obsługi wiadomości, takie jak wiadomości e-mail, SMS i elementów webhook, korzystając z [alertów dziennika aktywności](../azure-monitor/platform/platform-logs-overview.md)platformy Azure.  
- Dzięki powiadomieniu zostanie udostępnione *okno samoobsługowe.* W tym oknie, które jest zazwyczaj 35 dni, można znaleźć, które z maszyn wirtualnych są uwzględnione w fali. Możesz aktywnie rozpocząć konserwację zgodnie z własnymi potrzebami planowania.
- Po oknie samoobsługowym rozpoczyna *się okno zaplanowanej konserwacji.* W pewnym momencie w tym oknie platformy Azure planuje i stosuje wymaganą konserwację do maszyny Wirtualnej. 

Celem posiadania dwóch okien jest zapewnienie wystarczającej ilości czasu na rozpoczęcie konserwacji i ponowne uruchomienie maszyny Wirtualnej, wiedząc, kiedy platforma Azure automatycznie rozpocznie konserwację.

Za pomocą witryny Azure portal, programu PowerShell, interfejsu API REST i interfejsu wiersza polecenia platformy Azure można przeszukiwać okna obsługi okien dla maszyn wirtualnych zestawu maszyn wirtualnych i rozpocząć samoobsługową konserwację.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Czy należy rozpocząć konserwację podczas okna samoobsługowego?  

Poniższe wskazówki mogą pomóc w podjęciu decyzji, czy rozpocząć konserwację w wybranego czasie.

> [!NOTE] 
> Samoobsługowa konserwacja może nie być dostępna dla wszystkich maszyn wirtualnych. Aby ustalić, czy proaktywne ponowne wdrożenie jest dostępne dla maszyny Wirtualnej, **poszukaj startu teraz** w stanie konserwacji. Obecnie samoobsługowa konserwacja nie jest dostępna dla usług w chmurze Azure (rola sieci Web/proces roboczy) i sieci szkieletowej usług Azure.


Konserwacja samoobsługowa nie jest zalecana w przypadku wdrożeń korzystających z *zestawów dostępności.* Zestawy dostępności to konfiguracje o wysokiej dostępności, w których dotyczy tylko jednej domeny aktualizacji w dowolnym momencie. W przypadku zestawów dostępności:

- Pozwól platformie Azure wyzwolić konserwację. W przypadku konserwacji, która wymaga ponownego uruchomienia, konserwacja jest wykonywana przez domenę aktualizacji. Domeny aktualizacji nie muszą otrzymywać konserwacji sekwencyjnie. Między domenami aktualizacji jest 30-minutowa przerwa.
- Jeśli tymczasowa utrata niektórych pojemności (liczba domen 1/update) jest problemem, można łatwo zrekompensować stratę, przydzielając dodatkowe wystąpienia w okresie konserwacji.
- W przypadku konserwacji, która nie wymaga ponownego uruchomienia, aktualizacje są stosowane na poziomie domeny błędów. 
    
**Nie używaj** samoobsługowej konserwacji w następujących scenariuszach: 

- Jeśli często zamykasz maszyny wirtualne, ręcznie, używając devtest labs, używając automatycznego zamykania lub zgodnie z harmonogramem. Samoobsługowa konserwacja w tych scenariuszach może przywrócić stan konserwacji i spowodować dodatkowe przestoje.
- Na krótkotrwałych maszyn wirtualnych, które wiesz, zostaną usunięte przed końcem fali konserwacji. 
- Dla obciążeń o dużym stanie przechowywanych na dysku lokalnym (efemerycznym), który chcesz zachować po aktualizacji. 
- Jeśli często rozmiar maszyny Wirtualnej jest 500000000000000000000 00 W tym scenariuszu może przywrócić stan konserwacji. 
- Jeśli przyjęto zaplanowane zdarzenia, które umożliwiają proaktywne tryb failover lub pełne wdzięku zamknięcie obciążenia na 15 minut przed rozpoczęciem zamykania systemu.

**Należy** używać samoobsługowej konserwacji, jeśli zamierzasz uruchomić maszynę wirtualną nieprzerwanie podczas fazy zaplanowanej konserwacji i nie ma zastosowania żadna z poprzednich przeciwwskazań. 

Najlepiej jest korzystać z samoobsługowej konserwacji w następujących przypadkach:

- Musisz poinformować kierownictwo lub klienta o dokładnym oknie konserwacji. 
- Należy zakończyć konserwację w określonym terminie. 
- Należy kontrolować sekwencję konserwacji, na przykład w aplikacji wielowarstwowej, aby zagwarantować bezpieczne odzyskiwanie.
- Potrzebujesz więcej niż 30 minut czasu odzyskiwania maszyny Wirtualnej między dwiema domenami aktualizacji. Aby kontrolować czas między domenami aktualizacji, należy wyzwolić konserwację na maszynach wirtualnych po jednej domenie aktualizacji naraz.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Wyświetlanie zestawów skalowania maszyny wirtualnej, na które ma wpływ konserwacja w portalu

Po zaplanowaniu planowanej fali konserwacji można wyświetlić listę zestawów skalowania maszyny wirtualnej, których dotyczy nadchodząca fala konserwacji przy użyciu witryny Azure portal. 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Zestawy skalowania maszyny wirtualnej**.
3. W **obszarze Zestawy skalowania maszyny wirtualnej**wybierz pozycję **Edytuj kolumny,** aby otworzyć listę dostępnych kolumn.
4. W sekcji **Dostępne kolumny** wybierz pozycję **Konserwacja samoobsługowa**, a następnie przenieś ją do listy **Wybrane kolumny.** Wybierz przycisk **Zastosuj**.  

    Aby ułatwić znajdowanie elementu **samoobsługi,** można zmienić opcję rozwijaną w sekcji **Dostępne kolumny** z **Opcji Wszystkie** na **Właściwości**.

Kolumna **Samoobsługowa konserwacja** jest teraz wyświetlana na liście zestawów skalowania maszyny wirtualnej. Każdy zestaw skalowania maszyny wirtualnej może mieć jedną z następujących wartości dla kolumny samoobsługowej konserwacji:

| Wartość | Opis |
|-------|-------------|
| Tak | Co najmniej jedna maszyna wirtualna w zestawie skalowania maszyny wirtualnej znajduje się w oknie samoobsługowym. Konserwację można rozpocząć w dowolnym momencie podczas tego okna samoobsługowego. | 
| Nie | Żadne maszyny wirtualne nie znajdują się w oknie samoobsługi w zestawie skalowania maszyny wirtualnej, którego dotyczy problem. | 
| - | Zestawy skalowania maszyn wirtualnych nie są częścią grupy czynności konserwacji planowanej.| 

## <a name="notification-and-alerts-in-the-portal"></a>Powiadomienia i alerty w portalu

Platforma Azure komunikuje harmonogram planowanej konserwacji, wysyłając wiadomość e-mail do właściciela subskrypcji i grupy współwłaścicieli. Adresatów i kanałów do tej komunikacji można dodać, tworząc alerty dziennika aktywności. Aby uzyskać więcej informacji, zobacz [Monitorowanie aktywności subskrypcji za pomocą dziennika aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **Monitor**. 
3. W okienku **Monitor — alerty (klasyczne)** wybierz pozycję **+Dodaj alert dziennika aktywności**.
4. Na stronie **Alert Dodaj dziennik aktywności** wybierz lub wprowadź żądane informacje. W **polu Kryteria**upewnij się, że ustawiono następujące wartości:
   - **Kategoria zdarzenia**: Wybierz **kondycję usługi**.
   - **Usługi**: Wybierz **zestawy skalowania maszyn wirtualnych i maszyny wirtualne**.
   - **Typ**: Wybierz **planowaną konserwację**. 
    
Aby dowiedzieć się więcej o konfigurowaniu alertów dziennika aktywności, zobacz [Tworzenie alertów dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Rozpocznij konserwację zestawu skalowania maszyny wirtualnej z portalu

Więcej szczegółów związanych z konserwacją można zobaczyć w omówienie zestawów skalowania maszyny wirtualnej. Jeśli co najmniej jedna maszyna wirtualna w zestawie skalowania maszyny wirtualnej jest uwzględniona w planowanej fali konserwacji, w górnej części strony zostanie dodana nowa wstążka powiadomień. Wybierz wstążkę powiadomień, aby przejść do strony **Konserwacja.** 

Na **konserwacji** strony można zobaczyć, które wystąpienie maszyny Wirtualnej ma wpływ planowanej konserwacji. Aby rozpocząć konserwację, zaznacz pole wyboru, które odpowiada dotkniętej maszynie wirtualnej. Następnie wybierz pozycję **Rozpocznij konserwację**.

Po uruchomieniu konserwacji, których dotyczy problem maszyny wirtualne w zestawie skalowania maszyny wirtualnej przechodzą konserwację i są tymczasowo niedostępne. Jeśli pominięto okno samoobsługi, nadal można zobaczyć okno czasu, kiedy zestaw skalowania maszyny wirtualnej będą obsługiwane przez platformę Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Sprawdzanie stanu konserwacji przy użyciu programu PowerShell

Za pomocą programu Azure PowerShell można zobaczyć, kiedy maszyny wirtualne w zestawach skalowania maszyny wirtualnej są zaplanowane do konserwacji. Informacje o planowanej konserwacji są dostępne przy użyciu polecenia cmdlet `-InstanceView` [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) podczas korzystania z parametru.
 
Informacje o konserwacji są zwracane tylko wtedy, gdy planowana jest konserwacja. Jeśli nie jest zaplanowana żadna konserwacja, która wpływa na wystąpienie maszyny Wirtualnej, polecenie cmdlet nie zwraca żadnych informacji o konserwacji. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Następujące właściwości są zwracane w obszarze **MaintenanceRedeployStatus:** 

| Wartość | Opis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed (Dozwolone) | Wskazuje, czy można rozpocząć konserwację na maszynie Wirtualnej w tej chwili. |
| Czas rozpoczęcia pracy przedwidokadowy         | Początek okna samoobsługowego konserwacji, kiedy można zainicjować konserwację maszyny Wirtualnej. |
| Czas wstępnej opiekiDostępniewaczas           | Koniec okna samoobsługowego konserwacji, gdy można zainicjować konserwację na maszynie Wirtualnej. |
| Czas rozpoczęcia konserwacji            | Początek zaplanowanej konserwacji, w której platforma Azure inicjuje konserwację na maszynie wirtualnej. |
| MaintenanceWindowEndTime (Czas konserwacji systemu Windows              | Koniec zaplanowanego okna konserwacji, w którym platforma Azure inicjuje konserwację na maszynie wirtualnej. |
| Kod LastOperationResultCode               | Wynik ostatniej próby zainicjowania konserwacji na maszynie Wirtualnej. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Rozpoczynanie konserwacji wystąpienia maszyny Wirtualnej przy użyciu programu PowerShell

Można rozpocząć konserwację na maszynie Wirtualnej, jeśli **IsCustomerInitiatedMaintenanceAllowed** jest ustawiona na **true**. Użyj polecenia cmdlet [Set-AzVmss](/powershell/module/az.compute/set-azvmss) z parametrem. `-PerformMaintenance`

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Sprawdź stan konserwacji przy użyciu interfejsu wiersza polecenia

Informacje o planowanej konserwacji można wyświetlić za pomocą [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Informacje o konserwacji są zwracane tylko wtedy, gdy planowana jest konserwacja. Jeśli nie zostanie zaplanowana żadna konserwacja, która wpływa na wystąpienie maszyny Wirtualnej, polecenie nie zwraca żadnych informacji o konserwacji. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Następujące właściwości są zwracane w obszarze **MaintenanceRedeployStatus** dla każdego wystąpienia maszyny Wirtualnej: 

| Wartość | Opis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed (Dozwolone) | Wskazuje, czy można rozpocząć konserwację na maszynie Wirtualnej w tej chwili. |
| Czas rozpoczęcia pracy przedwidokadowy         | Początek okna samoobsługowego konserwacji, kiedy można zainicjować konserwację maszyny Wirtualnej. |
| Czas wstępnej opiekiDostępniewaczas           | Koniec okna samoobsługowego konserwacji, gdy można zainicjować konserwację na maszynie Wirtualnej. |
| Czas rozpoczęcia konserwacji            | Początek zaplanowanej konserwacji, w której platforma Azure inicjuje konserwację na maszynie wirtualnej. |
| MaintenanceWindowEndTime (Czas konserwacji systemu Windows              | Koniec zaplanowanego okna konserwacji, w którym platforma Azure inicjuje konserwację na maszynie wirtualnej. |
| Kod LastOperationResultCode               | Wynik ostatniej próby zainicjowania konserwacji na maszynie Wirtualnej. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Rozpocznij konserwację wystąpienia maszyny Wirtualnej przy użyciu interfejsu wiersza polecenia

Następujące wywołanie inicjuje konserwację `IsCustomerInitiatedMaintenanceAllowed` wystąpienia maszyny Wirtualnej, jeśli jest ustawiona na **true:**

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Najczęściej zadawane pytania

**Pyt.: Dlaczego musisz teraz ponownie uruchomić moje maszyny wirtualne?**

**Odp.:** Mimo że większość aktualizacji i uaktualnień do platformy Azure nie wpływa na dostępność maszyn wirtualnych, w niektórych przypadkach nie możemy uniknąć ponownego uruchamiania maszyn wirtualnych hostowanych na platformie Azure. Zgromadziliśmy kilka zmian, które wymagają od nas ponownego uruchomienia naszych serwerów, co spowoduje ponowne uruchomienie maszyny Wirtualnej.

**Pyt.: Jeśli przestrzegam zaleceń dotyczących wysokiej dostępności przy użyciu zestawu dostępności, czy jestem bezpieczny?**

**Odp.:** Maszyny wirtualne wdrożone w zestawie dostępności lub w zestawach skalowania maszyny wirtualnej używają domen aktualizacji. Podczas wykonywania konserwacji platforma Azure honoruje ograniczenie domeny aktualizacji i nie uruchamia ponownie maszyn wirtualnych z innej domeny aktualizacji (w ramach tego samego zestawu dostępności). Platforma Azure również czeka na co najmniej 30 minut przed przejściem do następnej grupy maszyn wirtualnych. 

Aby uzyskać więcej informacji na temat wysokiej dostępności, zobacz [Regiony i dostępność maszyn wirtualnych na platformie Azure](../virtual-machines/windows/availability.md).

**P: Jak mogę zostać powiadomiony o planowanej konserwacji?**

**Odp.:** Planowana fala konserwacji rozpoczyna się od ustawienia harmonogramu na co najmniej jeden region platformy Azure. Wkrótce potem do właścicieli subskrypcji jest wysyłane powiadomienie e-mail (jedna wiadomość e-mail na subskrypcję). Kanały i adresaci dla tego powiadomienia można dodać za pomocą alertów dziennika aktywności. Jeśli wdrożysz maszynę wirtualną w regionie, w którym planowana konserwacja jest już zaplanowana, nie otrzymasz powiadomienia. Zamiast tego sprawdź stan konserwacji maszyny Wirtualnej.

**P: Nie widzę żadnych oznak planowanej konserwacji w portalu, programie PowerShell lub wierszu polecenia. Co jest nie tak?**

**Odp.:** Informacje związane z planowaną konserwacją są dostępne podczas planowanej fali konserwacji tylko dla maszyn wirtualnych, których dotyczy planowana konserwacja. Jeśli nie widzisz danych, fala konserwacji może być już zakończona (lub nie została uruchomiona) lub maszyna wirtualna może być już hostowana na zaktualizowanym serwerze.

**Pyt.: Czy istnieje sposób, aby wiedzieć dokładnie, kiedy moja maszyna wirtualna będzie dotyczyć?**

**Odp.:** Kiedy ustawiamy harmonogram, definiujemy przedział czasu kilku dni. Dokładne sekwencjonowanie serwerów (i maszyn wirtualnych) w tym oknie (przedziale czasu) jest nieznane. Jeśli chcesz poznać dokładny czas aktualizacji maszyn wirtualnych, możesz użyć [zaplanowanych zdarzeń.](../virtual-machines/windows/scheduled-events.md) Korzystając z zaplanowanych zdarzeń, można wysyłać zapytania z poziomu maszyny Wirtualnej i otrzymywać 15-minutowe powiadomienie przed ponownym uruchomieniem maszyny Wirtualnej.

**Pyt.: Jak długo zajmie Ci ponowne uruchomienie mojej maszyny Wirtualnej?**

**Odp.:**  W zależności od rozmiaru maszyny Wirtualnej ponowne uruchomienie może potrwać do kilku minut podczas okna samoobsługowej konserwacji. Podczas ponownego uruchamiania zainicjowanego przez platformę Azure w oknie zaplanowanej konserwacji ponowne uruchomienie zwykle trwa około 25 minut. Jeśli używasz usług w chmurze (rola sieci Web/procesu roboczego), zestawów skalowania maszyny wirtualnej lub zestawów dostępności, podczas zaplanowanej konserwacji między każdą grupą maszyn wirtualnych (domeną aktualizacji) jest 30 minut. 

**P: Nie widzę żadnych informacji o konserwacji na moich maszynach wirtualnych. Co poszło nie tak?**

**Odp.:** Istnieje kilka powodów, dla których możesz nie widzieć żadnych informacji o konserwacji na maszynach wirtualnych:
   - Używasz subskrypcji oznaczonej jako *Microsoft Internal*.
   - Maszyny wirtualne nie są zaplanowane do konserwacji. Może się okazać, że fala konserwacji została anulowana, została anulowana lub została zmodyfikowana, aby maszyny wirtualne nie były już dotknięte tym problemem.
   - Nie masz kolumny **Konserwacja** dodane do widoku listy maszyny Wirtualnej. Mimo że dodaliśmy tę kolumnę do widoku domyślnego, jeśli skonfigurujesz widok tak, aby były wyświetlane kolumny inne niż domyślne, należy ręcznie dodać kolumnę **Konserwacja** do widoku listy maszyny Wirtualnej.

**P: Moja maszyna wirtualna jest zaplanowana do konserwacji po raz drugi. Dlaczego?**

**Odp.:** W kilku przypadkach użycia maszyna wirtualna jest zaplanowana do konserwacji po zakończeniu konserwacji i przesuń ponownie:
   - Anulowaliśmy falę konserwacji i uruchomiliśmy ją ponownie z innym ładunkiem. Być może wykryliśmy uszkodzony ładunek i musimy po prostu wdrożyć dodatkowy ładunek.
   - Maszyna wirtualna została *wyleciona* do innego węzła z powodu usterki sprzętowej.
   - Wybrano, aby zatrzymać (cofnięto przydział) i ponownie uruchomić maszynę wirtualną.
   - Automatyczne **zamykanie** jest włączone dla maszyny Wirtualnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarejestrować się w celu rejestrowania zdarzeń konserwacji z poziomu maszyny Wirtualnej przy użyciu [zaplanowanych zdarzeń.](../virtual-machines/windows/scheduled-events.md)
