---
title: Grupy miejsc docelowych zbliżeniowych platformy Azure dla aplikacji SAP | Dokumenty firmy Microsoft
description: Zawiera opis scenariuszy wdrażania SAP z grupami miejsc docelowych zbliżeniowych platformy Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01ce1599f86082aef3ff53d298cc53896074af66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277596"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Grupy miejsc docelowych zbliżeniowych platformy Azure dla optymalnego opóźnienia sieci w aplikacjach SAP
Aplikacje SAP oparte na architekturze SAP NetWeaver lub SAP S/4HANA są wrażliwe na opóźnienia sieci między warstwą aplikacji SAP a warstwą bazy danych SAP. Ta czułość jest wynikiem większości logiki biznesowej uruchomionej w warstwie aplikacji. Ponieważ warstwa aplikacji SAP uruchamia logikę biznesową, wydaje zapytania do warstwy bazy danych z wysoką częstotliwością, z szybkością tysięcy lub dziesiątek tysięcy na sekundę. W większości przypadków charakter tych zapytań jest prosty. Często można je uruchomić w warstwie bazy danych w 500 mikrosekund lub mniej.

Czas spędzony w sieci, aby wysłać takie zapytanie z warstwy aplikacji do warstwy bazy danych i otrzymać wynik zestaw z powrotem ma duży wpływ na czas potrzebny do uruchomienia procesów biznesowych. Ta wrażliwość na opóźnienie sieci jest powodem, dla którego należy osiągnąć optymalne opóźnienie sieci w projektach wdrażania SAP. Zobacz [SAP Note #1100926 — często zadawane pytania: Wydajność sieci,](https://launchpad.support.sap.com/#/notes/1100926/E) aby uzyskać wskazówki dotyczące klasyfikowania opóźnienia sieci.

W wielu regionach platformy Azure wzrosła liczba centrów danych. Wzrost ten został również wywołany przez wprowadzenie stref dostępności. W tym samym czasie klienci, szczególnie dla wysokiej klasy systemów SAP, używają bardziej specjalnych jednostek SKU maszyn wirtualnych z rodziny M lub dużych wystąpień HANA. Te typy maszyn wirtualnych platformy Azure nie są dostępne we wszystkich centrach danych w określonym regionie platformy Azure. Z powodu tych dwóch tendencji klienci doświadczyli opóźnienia sieci, które nie jest w optymalnym zakresie. W niektórych przypadkach to opóźnienie powoduje nieoptymalne działanie ich systemów SAP.

Aby zapobiec tym problemom, platforma Azure oferuje [grupy miejsc docelowych zbliżeniowych.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) Ta nowa funkcja została już wykorzystana do wdrażania różnych systemów SAP. Aby uzyskać ograniczenia dotyczące grup miejsc docelowych zbliżeniowych, zobacz artykuł, o którym mowa na początku tego akapitu. W tym artykule opisano scenariusze SAP, w których grupy miejsc docelowych zbliżeniowych platformy Azure mogą lub powinny być używane.

## <a name="what-are-proximity-placement-groups"></a>Co to są grupy miejsc docelowych zbliżeniowych? 
Grupa umieszczania zbliżeniowego platformy Azure jest konstrukcją logiczną. Po zdefiniowaniu jest powiązany z regionem platformy Azure i grupą zasobów platformy Azure. Po wdrożeniu maszyn wirtualnych do grupy miejsc docelowych zbliżeniowych odwołuje się:

- Pierwsza maszyna wirtualna platformy Azure wdrożona w centrum danych. Pierwszą maszynę wirtualną można potraktować jako "maszynę wirtualną zakresu", która jest wdrażana w centrum danych na podstawie algorytmów alokacji platformy Azure, które są ostatecznie łączone z definicjami użytkowników dla określonej strefy dostępności.
- Wszystkie kolejne maszyny wirtualne wdrożone, które odwołują się do grupy miejsc docelowych zbliżeniowych, aby umieścić wszystkie następnie wdrożone maszyny wirtualne platformy Azure w tym samym centrum danych co pierwsza maszyna wirtualna.

> [!NOTE]
> Jeśli nie ma wdrożonego sprzętu hosta, który mógłby uruchomić określony typ maszyny Wirtualnej w centrum danych, w którym została umieszczona pierwsza maszyna wirtualna, wdrożenie żądanego typu maszyny Wirtualnej nie powiedzie się. Zostanie wyświetlony komunikat o błędzie.

Pojedyncza [grupa zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) może mieć przypisane do niej wiele grup miejsc docelowych zbliżeniowych. Ale grupa miejsc docelowych zbliżeniowych można przypisać tylko do jednej grupy zasobów platformy Azure.

Korzystając z grup miejsc docelowych zbliżeniowych, należy pamiętać o następujących kwestiach:

- Jeśli dążysz do optymalnej wydajności systemu SAP i ograniczysz się do jednego centrum danych platformy Azure dla systemu przy użyciu grup miejsc docelowych zbliżeniowych, możesz nie być w stanie połączyć wszystkich typów rodzin maszyn wirtualnych w grupie miejsc docelowych. Te ograniczenia występują, ponieważ sprzęt hosta, który jest potrzebny do uruchomienia określonego typu maszyny Wirtualnej może nie być obecny w centrum danych, do którego została wdrożona "maszyna wirtualna o określonym zakresie" grupy umieszczania.
- Podczas cyklu życia takiego systemu SAP można zostać zmuszonym do przeniesienia systemu do innego centrum danych. Ten ruch może być wymagane, jeśli zdecydujesz, skalowawczy w poziomie warstwy DBMS HANA należy, na przykład, przenieść z czterech węzłów do 16 węzłów i nie ma wystarczającej pojemności, aby uzyskać dodatkowe 12 maszyn wirtualnych typu używanego w centrum danych.
- Z powodu likwidacji sprzętu firma Microsoft może tworzyć pojemności dla typu maszyny Wirtualnej używanego w innym centrum danych, a nie w centrum produkcyjnym, a nie dla tego, który był początkowo używany. W tym scenariuszu może być konieczne przeniesienie wszystkich maszyn wirtualnych grupy miejsc docelowych zbliżeniami do innego centrum danych.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Grupy miejsc docelowych zbliżeniowych z systemami SAP, które używają tylko maszyn wirtualnych platformy Azure
Większość wdrożeń systemu SAP NetWeaver i S/4HANA na platformie Azure nie używa [dużych wystąpień HANA.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) W przypadku wdrożeń, które nie używają dużych wystąpień HANA, ważne jest zapewnienie optymalnej wydajności między warstwą aplikacji SAP a warstwą DBMS. Aby to zrobić, zdefiniuj grupę miejsc docelowych zbliżeniowych platformy Azure tylko dla systemu.

W większości wdrożeń klientów klienci twórz jedną [grupę zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) dla systemów SAP. W takim przypadku istnieje relacja jeden do jednego między, na przykład, produkcyjną grupą zasobów systemu ERP a jej grupą umieszczania zbliżeniowego. W innych przypadkach klienci organizują swoje grupy zasobów poziomo i zbierają wszystkie systemy produkcyjne w jednej grupie zasobów. W takim przypadku należy mieć relację jeden do wielu między grupą zasobów dla produkcyjnych systemów SAP i kilkoma grupami miejsc docelowych zbliżeniowych dla produkcyjnego SAP ERP, SAP BW i tak dalej.

Unikaj łączenia kilku systemów produkcyjnych lub nieprodukcyjnych SAP w jednej grupie miejsc docelowych zbliżeniowych. Gdy niewielka liczba systemów SAP lub sap i niektóre otaczające aplikacje muszą mieć komunikację sieciową o małym opóźnieniu, można rozważyć przeniesienie tych systemów do jednej grupy miejsc docelowych zbliżeniowych. Należy unikać pakietów systemów, ponieważ im więcej systemów grupujesz w grupie miejsc docelowych zbliżeniowych, tym większe szanse:

- Aby wymagać typu maszyny Wirtualnej, którego nie można uruchomić w określonym centrum danych, do którego została określona grupa miejsc docelowych w pobliżu.
- Te zasoby maszyn wirtualnych spoza sieci główną, takich jak maszyny wirtualne z serii M, mogą ostatecznie zostać niespełnione, gdy potrzebujesz więcej, ponieważ z czasem dodajesz oprogramowanie do grupy miejsc docelowych zbliżeniami.

Oto jak wygląda idealna konfiguracja, jak opisano:

![Grupy miejsc docelowych zbliżeniowych tylko z maszynami wirtualnymi platformy Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

W takim przypadku pojedyncze systemy SAP są zgrupowane w jednej grupie zasobów, z jedną grupą miejsc docelowych zbliżeniowo. Nie ma zależności od tego, czy używasz konfiguracji skalowania w poziomie HANA w poziomie, czy skalowania w górę systemu DBMS.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Grupy miejsc docelowych zbliżeniowych i duże wystąpienia HANA
Jeśli niektóre systemy SAP opierają się na [dużych wystąpieniach HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) dla warstwy aplikacji, można doświadczyć znacznych ulepszeń opóźnienia sieci między jednostką dużych wystąpień HANA i maszynami wirtualnymi platformy Azure podczas korzystania z jednostek dużych wystąpień HANA, które są wdrażane w [wierszach lub stempłach wersji 4.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) Jednym z ulepszeń jest to, że jednostki dużych wystąpień HANA, w miarę ich wdrażania, wdrażają z grupą miejsc docelowych zbliżeniowych. Tej grupy miejsc docelowych zbliżeniowych można użyć do wdrożenia maszyn wirtualnych warstwy aplikacji. W rezultacie te maszyny wirtualne zostaną wdrożone w tym samym centrum danych, w którym znajduje się jednostka dużych wystąpień HANA.

Aby ustalić, czy jednostka dużych wystąpień HANA jest wdrażana w sygnaturze lub wierszu wersji 4, zapoznaj się z artykułem [Kontrola dużych wystąpień platformy Azure HANA za pośrednictwem witryny Azure portal.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit) W przeglądzie atrybutów jednostki dużych wystąpień HANA można również określić nazwę grupy miejsc docelowych zbliżeniowych, ponieważ została ona utworzona podczas wdrażania jednostki dużych wystąpień HANA. Nazwa wyświetlana w przeglądzie atrybutów to nazwa grupy miejsc docelowych zbliżeniowych, w której należy wdrożyć maszyny wirtualne warstwy aplikacji.

W porównaniu do systemów SAP, które używają tylko maszyn wirtualnych platformy Azure, podczas korzystania z dużych wystąpień HANA, masz mniejszą elastyczność w podejmowaniu decyzji, ile [grup zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) do użycia. Wszystkie jednostki dużych wystąpień [HANA dzierżawy dużych wystąpień HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) są pogrupowane w jedną grupę zasobów, zgodnie z [opisem w tym artykule](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). Jeśli nie wdrożysz w różnych dzierżaw, aby oddzielić, na przykład systemy produkcyjne i nieprodukcyjne lub inne systemy, wszystkie jednostki dużych wystąpień HANA zostaną wdrożone w jednej dzierżawie dużych wystąpień HANA. Ta dzierżawca ma relację jeden-do-jednego z grupą zasobów. Ale dla każdej z pojedynczych jednostek zostanie zdefiniowana oddzielna grupa miejsc docelowych zbliżeniowych.

W rezultacie relacje między grupami zasobów platformy Azure i grupami miejsc docelowych w pobliżu dla pojedynczej dzierżawy będą wyświetlane w tym miejscu:

![Grupy miejsc docelowych zbliżeniowych i duże wystąpienia HANA](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Przykład wdrożenia z grupami miejsc docelowych zbliżeniowymi
Poniżej przedstawiono niektóre polecenia programu PowerShell, których można użyć do wdrażania maszyn wirtualnych za pomocą grup miejsc docelowych zbliżeniowych platformy Azure.

Pierwszym krokiem po zalogowaniu się do [usługi Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)jest sprawdzenie, czy jesteś w subskrypcji platformy Azure, który ma być używany do wdrożenia:

<pre><code>
Get-AzureRmContext
</code></pre>

Jeśli chcesz zmienić subskrypcję na inną, możesz to zrobić, uruchamiając to polecenie:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Utwórz nową grupę zasobów platformy Azure, uruchamiając to polecenie:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Utwórz nową grupę miejsc docelowych w pobliżu, uruchamiając to polecenie:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Wdrażanie pierwszej maszyny Wirtualnej w grupie miejsc docelowych zbliżeniowych za pomocą polecenia takiego jak to:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Poprzednie polecenie wdraża maszynę wirtualną z systemem Windows. Po pomyślnym zakończeniu tego wdrożenia maszyny Wirtualnej zakres centrum danych grupy miejsc docelowych zbliżeniowych jest definiowany w regionie platformy Azure. Wszystkie kolejne wdrożenia maszyn wirtualnych, które odwołują się do grupy miejsc docelowych zbliżeniowych, jak pokazano w poprzednim poleceniu, zostaną wdrożone w tym samym centrum danych platformy Azure, o ile typ maszyny Wirtualnej może być hostowany na sprzęcie umieszczonym w tym centrum danych i pojemności dla tego typu maszyny Wirtualnej. Dostępne.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Łączenie zestawów dostępności i stref dostępności z grupami miejsc docelowych zbliżeniowymi
Jedną z wad korzystania ze stref dostępności dla wdrożeń systemu SAP jest to, że nie można wdrożyć warstwy aplikacji SAP przy użyciu zestawów dostępności w określonej strefie. Chcesz, aby warstwa aplikacji SAP była wdrażana w tych samych strefach co warstwa DBMS. Odwoływanie się do strefy dostępności i zestawu dostępności podczas wdrażania pojedynczej maszyny Wirtualnej nie jest obsługiwane. Tak wcześniej, trzeba było wdrożyć warstwę aplikacji, odwołując się do strefy. Utraciłeś możliwość upewnienia się, że maszyny wirtualne warstwy aplikacji zostały rozłożone na różne domeny aktualizacji i awarii.

Korzystając z grup miejsc docelowych zbliżeniowych, można pominąć to ograniczenie. Oto sekwencja wdrażania:

- Utwórz grupę miejsc docelowych zbliżeniowych.
- Wdrażanie maszyny wirtualnej kotwicy, zwykle serwera DBMS, odwołując się do strefy dostępności.
- Utwórz zestaw dostępności, który odwołuje się do grupy zbliżeniowej platformy Azure. (Zobacz polecenie w dalszej części tego artykułu).
- Wdrażanie maszyn wirtualnych warstwy aplikacji przez odwoływanie się do zestawu dostępności i grupy miejsc docelowych zbliżeniowych.

Zamiast wdrażać pierwszą maszynę wirtualną, jak pokazano w poprzedniej sekcji, odwołujesz się do strefy dostępności i grupy miejsc docelowych zbliżeniowych podczas wdrażania maszyny Wirtualnej:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Pomyślne wdrożenie tej maszyny wirtualnej będzie hostować wystąpienie bazy danych systemu SAP w jednej strefie dostępności. Zakres grupy miejsc docelowych zbliżeniowych jest ograniczony do jednego z centrów danych, które reprezentują zdefiniowaną strefę dostępności.

Załóżmy, że wdrożysz maszyny wirtualne usług centralnych w taki sam sposób, jak maszyny wirtualne DBMS, odwołując się do tej samej strefy lub stref i tych samych grup miejsc docelowych zbliżeniowych. W następnym kroku należy utworzyć zestawy dostępności, które mają być używane dla warstwy aplikacji systemu SAP.

Należy zdefiniować i utworzyć grupę miejsc docelowych zbliżeniowych. Polecenie tworzenia zestawu dostępności wymaga dodatkowego odwołania do identyfikatora grupy miejsc docelowych zbliżeniowych (a nie nazwy). Identyfikator grupy miejsc docelowych zbliżeniowych można uzyskać za pomocą tego polecenia:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Podczas tworzenia zestawu dostępności należy wziąć pod uwagę dodatkowe parametry podczas korzystania z dysków zarządzanych (domyślnie, chyba że określono inaczej) i grup miejsc docelowych zbliżeniowych:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

W idealnym przypadku należy użyć trzech domen błędów. Ale liczba obsługiwanych domen błędów może się różnić w zależności od regionu. W takim przypadku maksymalna liczba domen błędów możliwe dla określonych regionów jest dwa. Aby wdrożyć maszyny wirtualne warstwy aplikacji, należy dodać odwołanie do nazwy zestawu dostępności i nazwy grupy miejsc docelowych zbliżeniowych, jak pokazano poniżej:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Wynikiem tego wdrożenia jest:
- Warstwa DBMS i usługi centralne dla systemu SAP, który znajduje się w określonej strefie dostępności lub strefach dostępności.
- Warstwa aplikacji SAP, która znajduje się za pośrednictwem zestawów dostępności w tych samych centrach danych platformy Azure, co maszyna wirtualna dbms lub maszyny wirtualne.

> [!NOTE]
> Ponieważ można wdrożyć jedną maszynę wirtualną DBMS w jednej strefie, a drugą maszynę wirtualną DBMS w innej strefie, aby utworzyć konfigurację wysokiej dostępności, dla każdej ze stref potrzebna jest inna grupa miejsc docelowych zbliżeniowych. To samo dotyczy każdego zestawu dostępności, którego używasz.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Przenoszenie istniejącego systemu do grup miejsc docelowych zbliżeniowych
Jeśli masz już wdrożone systemy SAP, możesz zoptymalizować opóźnienie sieci niektórych krytycznych systemów i zlokalizować warstwę aplikacji i warstwę DBMS w tym samym centrum danych. Aby przenieść maszyny wirtualne z pełną dostępnością platformy Azure ustawioną na istniejącą grupę miejsc docelowych zbliżeniami, która jest już o zakresie, musisz zamknąć wszystkie maszyny wirtualne zestawu dostępności i przypisać zestaw dostępności do istniejącej grupy miejsc docelowych zbliżeniowych za pośrednictwem witryny Azure portal; Program PowerShell lub CLI. Jeśli chcesz przenieść maszynę wirtualną, która nie jest częścią zestawu dostępności do istniejącej grupy miejsc docelowych w pobliżu, wystarczy zamknąć maszynę wirtualną i przypisać ją do istniejącej grupy miejsc docelowych zbliżeniowych. 


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z dokumentacją:

- [Obciążenia SAP na platformie Azure: lista kontrolna planowania i wdrażania](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Wersja zapoznawcza: wdrażanie maszyn wirtualnych w grupach miejsc docelowych zbliżeniowych przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Podgląd: wdrażanie maszyn wirtualnych w grupach miejsc docelowych zbliżeniowych przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Zagadnienia dotyczące wdrażania usługi DBMS maszyn wirtualnych platformy Azure dla obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

