---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 2daaf9bbdf90029f0aad4333ab94e2d1d1d3d7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129172"
---
## <a name="limitations"></a>Ograniczenia

- Zestawy skalowania maszyny wirtualnej nie są obecnie obsługiwane na dedykowanych hostach.

## <a name="benefits"></a>Korzyści 

Rezerwowanie całego hosta zapewnia następujące korzyści:

-   Izolacja sprzętowa na poziomie serwera fizycznego. Żadne inne maszyny wirtualne nie zostaną umieszczone na hostach. Dedykowane hosty są wdrażane w tych samych centrach danych i współużytkują tę samą sieć i podstawową infrastrukturę pamięci masowej, co inne hosty nieizolowane.
-   Kontrola nad zdarzeniami konserwacji zainicjowane przez platformę Azure. Podczas gdy większość zdarzeń konserwacji mają niewielki lub żaden wpływ na maszyny wirtualne, istnieją pewne poufne obciążenia, w których każda sekunda pauzy może mieć wpływ. Dzięki dedykowanym hostom możesz zapisać się do okna konserwacji, aby zmniejszyć wpływ na usługę.
-   Dzięki korzyści hybrydowej platformy Azure możesz przenieść własne licencje dla systemu Windows i SQL na platformę Azure. Korzystanie z korzyści hybrydowych zapewnia dodatkowe korzyści. Aby uzyskać więcej informacji, zobacz [Korzyści hybrydowe platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Grupy, hosty i maszyny wirtualne  

![Widok nowych zasobów dla dedykowanych hostów.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**Grupa hostów** jest zasobem reprezentującym kolekcję dedykowanych hostów. Tworzysz grupę hostów w regionie i strefie dostępności i dodajesz do niej hosty.

**Host** to zasób mapowany na serwer fizyczny w centrum danych platformy Azure. Serwer fizyczny jest przydzielany podczas tworzenia hosta. Host jest tworzony w grupie hosta. Host ma jednostkę SKU opisującą, które rozmiary maszyn wirtualnych mogą być tworzone. Każdy host może obsługiwać wiele maszyn wirtualnych o różnych rozmiarach, o ile pochodzą one z tej samej serii rozmiarów.

Podczas tworzenia maszyny Wirtualnej na platformie Azure, można wybrać dedykowany host do użycia dla maszyny Wirtualnej. Masz pełną kontrolę, które maszyny wirtualne są umieszczane na hostach.


## <a name="high-availability-considerations"></a>Kwestie wysokiej dostępności 

Aby uzyskać wysoką dostępność, należy wdrożyć wiele maszyn wirtualnych rozłożonych na wiele hostów (co najmniej 2). Za pomocą hostów dedykowanych platformy Azure masz kilka opcji do aprowizowania infrastruktury do kształtowania granic izolacji błędów.

### <a name="use-availability-zones-for-fault-isolation"></a>Używanie stref dostępności do izolacji błędów

Strefy dostępności są unikatowymi lokalizacjami fizycznymi w regionie platformy Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Grupa hostów jest tworzona w jednej strefie dostępności. Po utworzeniu wszystkie hosty zostaną umieszczone w tej strefie. Aby osiągnąć wysoką dostępność w różnych strefach, musisz utworzyć wiele grup hostów (po jednej na strefę) i odpowiednio rozłożyć hosty.

Jeśli grupa hostów zostanie przypisana do strefy dostępności, wszystkie maszyny wirtualne utworzone na tym hoście muszą zostać utworzone w tej samej strefie.

### <a name="use-fault-domains-for-fault-isolation"></a>Używanie domen błędów do izolacji błędów

Hosta można utworzyć w określonej domenie błędów. Podobnie jak maszyna wirtualna w zestawie skalowania lub zestaw dostępności, hosty w różnych domenach błędów zostaną umieszczone na różnych stojakach fizycznych w centrum danych. Podczas tworzenia grupy hostów, należy określić liczbę domen błędów. Podczas tworzenia hostów w grupie hostów przypisywana jest domena błędów dla każdego hosta. Maszyny wirtualne nie wymagają przypisania domeny błędów.

Domeny błędów nie są takie same jak kolokacji. Posiadanie tej samej domeny błędów dla dwóch hostów nie oznacza, że znajdują się one w pobliżu siebie.

Domeny błędów są ograniczone do grupy hostów. Nie należy zakładać anty-koligacji między dwiema grupami hostów (chyba że znajdują się one w różnych strefach dostępności).

Maszyny wirtualne wdrożone na hostach z różnymi domenami błędów będą miały swoje podstawowe usługi dysków zarządzanych na wielu sygnaturach magazynu, aby zwiększyć ochronę przed izolacją przed usterkami.

### <a name="using-availability-zones-and-fault-domains"></a>Korzystanie ze stref dostępności i domen usterek

Można użyć obu funkcji razem, aby osiągnąć jeszcze większą izolację błędów. W takim przypadku określisz strefę dostępności i liczbę domen błędów dla każdej grupy hostów, przypiszesz domenę błędów do każdego hosta w grupie i przypiszesz strefę dostępności do każdej z maszyn wirtualnych

Znaleziony w tym [miejscu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) szablon przykładowy Menedżera zasobów używa stref i domen błędów do rozmieszczenia hostów w celu uzyskania maksymalnej odporności w regionie.

## <a name="maintenance-control"></a>Sterowanie konserwacją

Infrastruktura obsługująca maszyny wirtualne może od czasu do czasu być aktualizowana w celu zwiększenia niezawodności, wydajności, zabezpieczeń i uruchomienia nowych funkcji. Platforma Azure próbuje zminimalizować wpływ konserwacji platformy, gdy tylko jest to możliwe, ale klienci z *obciążeniami poufnymi konserwacji* nie tolerują nawet kilku sekund, że maszyna wirtualna musi zostać zamrożona lub odłączona w celu konserwacji.

**Kontrola konserwacji** zapewnia klientom możliwość pominięcia regularnych aktualizacji platformy zaplanowanych na dedykowanych hostach, a następnie zastosowania jej w wybranym przez siebie czasie w 35-dniowym oknie rolowanym.

> [!NOTE]
>  Kontrola konserwacji jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz **Aktualizacje sterowania aktualizacjemi przy użyciu interfejsu [wiersza polecenia](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) lub [programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)**.

## <a name="capacity-considerations"></a>Zagadnienia dotyczące pojemności

Po zainicjowaniu obsługi administracyjnej dedykowanego hosta platforma Azure przypisuje go do serwera fizycznego. Gwarantuje to dostępność pojemności, gdy trzeba aprowizować maszynę wirtualną. Platforma Azure używa całej pojemności w regionie (lub strefie), aby wybrać serwer fizyczny dla hosta. Oznacza to również, że klienci mogą oczekiwać, że będą mogli zwiększyć swój dedykowany rozmiar hosta bez obawy o wyczerpanie miejsca w klastrze.

## <a name="quotas"></a>Przydziały

Istnieje domyślny limit przydziału 3000 procesorów wirtualnych dla dedykowanych hostów dla dedykowanych hostów dla regionu. Ale liczba hostów, które można wdrożyć jest również ograniczona przez przydział dla rodziny rozmiar maszyn wirtualnych używane dla hosta. Na przykład subskrypcja **płatności zgodnie z rzeczywistymi przyjamiarami** może mieć przydział 10 procesorów wirtualnych dostępnych dla serii o rozmiarze Dsv3 w regionie Wschodnie stany USA. W takim przypadku należy zażądać zwiększenia przydziału do co najmniej 64 procesorów wirtualnych przed wdrożeniem dedykowanego hosta. Wybierz przycisk **Poproś o zwiększenie** w prawym górnym rogu, aby w razie potrzeby złożyć żądanie.

![Zrzut ekranu przedstawiający stronę użycia i przydziałów w portalu](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Aby uzyskać więcej informacji, zobacz [Przydziały vCPU maszyn wirtualnych](/azure/virtual-machines/windows/quotas).

Bezpłatne subskrypcje wersji próbnej i MSDN nie mają przydziału dla dedykowanych hostów platformy Azure.

## <a name="pricing"></a>Cennik

Użytkownicy są obciążani opłatami za dedykowany host, niezależnie od tego, ile maszyn wirtualnych jest wdrażanych. W miesięcznym zestawieniu zobaczysz nowy typ zasobu rozliczanego hostów. Maszyny wirtualne na dedykowanym hoście będą nadal wyświetlane w instrukcji, ale będą nosić cenę 0.

Cena hosta jest ustawiana na podstawie rodziny maszyn wirtualnych, typu (rozmiar sprzętu) i regionu. Cena hosta jest względna do największego rozmiaru maszyny Wirtualnej obsługiwanego na hoście.

Licencjonowanie oprogramowania, magazynowanie i użycie sieci są rozliczane oddzielnie od hosta i maszyn wirtualnych. Nie ma żadnych zmian w tych elementów podlegania rozliczania.

Aby uzyskać więcej informacji, zobacz [Cennik hosta dedykowanego platformy Azure](https://aka.ms/ADHPricing).

Można również zaoszczędzić na kosztach dzięki [zarezerwowane wystąpienie hostów dedykowanych platformy Azure.](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
 
## <a name="sizes-and-hardware-generations"></a>Rozmiary i generacje sprzętu

Jednostka SKU jest zdefiniowana dla hosta i reprezentuje serię rozmiar maszyny Wirtualnej i typ. Można mieszać wiele maszyn wirtualnych o różnych rozmiarach w ramach jednego hosta, o ile są one tej samej serii rozmiarów. 

*Typ* jest generowanie sprzętu. Różne typy sprzętu dla tej samej serii maszyn wirtualnych będą pochodzić od różnych dostawców procesorów i mają różne generacje procesorów i liczbę rdzeni. 

Rozmiary i typy sprzętu różnią się w zależności od regionu. Więcej informacji można znaleźć na [stronie z cennikiem hosta.](https://aka.ms/ADHPricing)


## <a name="host-life-cycle"></a>Cykl życia gospodarza


Platforma Azure monitoruje stan kondycji hostów i zarządza nim. Następujące stany zostaną zwrócone podczas kwerendy hosta:

| Stan zdrowia   | Opis       |
|----------|----------------|
| Dostępny host     | Nie są znane żadne problemy z hostem.   |
| Gospodarz w trakcie dochodzenia  | Mamy pewne problemy z gospodarzem, który analizujemy. Jest to stan przejściowy wymagany dla platformy Azure, aby spróbować zidentyfikować zakres i główną przyczynę zidentyfikowanego problemu. Może to mieć wpływ na maszyny wirtualne uruchomione na hoście. |
| Alokacja oczekująca na hosta   | Platforma Azure nie może przywrócić hosta do stanu w dobrej kondycji i poprosić o ponowne wdrożenie maszyn wirtualnych z tego hosta. Jeśli `autoReplaceOnFailure` jest włączona, maszyny wirtualne są *usługi uzdrowiony* do zdrowego sprzętu. W przeciwnym razie maszyna wirtualna może być uruchomiona na hoście, który ma zakończyć się niepowodzeniem.|
| Przydzielone przez hosta  | Wszystkie maszyny wirtualne zostały usunięte z hosta. Nie są już naliczane opłaty za ten host, ponieważ sprzęt został wyjęty z obrotu.   |

