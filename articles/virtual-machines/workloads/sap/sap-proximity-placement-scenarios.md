---
title: Grupy umieszczania bliskości platformy Azure w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP | Microsoft Docs
description: Opisuje scenariusze wdrażania SAP przy użyciu grup umieszczania usługi Azure zbliżeniowe
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f97cce2d1a8b2de5634215629ddb997dc8f7196a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235199"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Grupy umieszczania w pobliżu na platformie Azure w celu zapewnienia optymalnego opóźnienia sieci dla aplikacji SAP
Aplikacje SAP oparte na architekturze SAP NetWeaver lub SAP S/4HANA są wrażliwe na opóźnienie sieci między warstwą aplikacji SAP a warstwą bazy danych SAP. Powodem tej czułości tych architektur jest odblokowanie w przypadku, gdy większość logiki biznesowej jest wykonywana w warstwie aplikacji. W wyniku wykonania logiki biznesowej warstwa aplikacji SAP wystawia zapytania do warstwy bazy danych o wysokiej częstotliwości tysięcy i dziesiątki tysięcy na sekundę. W większości przypadków charakter tych zapytań jest prosty. Często można je wykonywać w warstwie bazy danych w mniej niż 500 mikrosekundach lub nawet mniej. Czas spędzony na sieci do wysłania takiego zapytania z warstwy aplikacji do warstwy bazy danych i otrzymania z powrotem zestawu wyników z warstwy bazy danych ma znaczny wpływ na czas potrzebny na wykonanie procesów biznesowych. Ta czułość na opóźnienie sieci to czas, jaki należy spełnić w projektach wdrożenia SAP w celu uzyskania optymalnego opóźnienia sieci. W [programie SAP Uwaga #1100926 — często zadawane pytania: Wydajność](https://launchpad.support.sap.com/#/notes/1100926/E)sieci, system SAP opublikował niektóre wskazówki dotyczące klasyfikowania opóźnień sieci.

Po jednej stronie w wielu regionach platformy Azure liczba centrów danych zwiększyła się także przez wprowadzenie Strefy dostępności. Z drugiej strony Klienci, szczególnie dla systemów SAP wysokiej klasy, używały bardziej specjalnych jednostek SKU maszyn wirtualnych z rodziny serii M lub dużych wystąpień HANA. Typy maszyn wirtualnych platformy Azure, które nie znajdują się we wszystkich centrach danych w określonym regionie platformy Azure. W wyniku tych dwóch Tendencies klienci napotykali przypadki, w których opóźnienie sieci nie było w optymalnym zakresie i w niektórych przypadkach spowodowało nieoptymalną wydajność systemów SAP.

Aby zapobiec temu problemowi, platforma Azure oferuje konstrukcję nazywaną [grupą umieszczania usługi Azure zbliżeniowe](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Ta nowa funkcja została już użyta do wdrożenia różnych systemów SAP. Zapoznaj się z artykułem dotyczącym ograniczeń dotyczących grup umieszczania sąsiedztwa. W tym artykule opisano różne scenariusze SAP, w których można używać grup umieszczania usługi Azure zbliżeniowe lub ich używać.

## <a name="what-are-proximity-placement-groups"></a>Co to są grupy umieszczania zbliżeniowe 
Grupa umieszczania bliskości platformy Azure to konstrukcja logiczna, która w fazie definiowania jest powiązana z regionem platformy Azure i grupą zasobów platformy Azure. Podczas wdrażania maszyn wirtualnych odwołuje się do grupy umieszczania sąsiedztwa:

- Pierwsza wdrożona maszyna wirtualna platformy Azure w celu rozliczenia w centrum danych. Pierwsza maszyna wirtualna może być widoczna jako zakotwiczenie maszyny wirtualnej, która jest wdrażana w centrum danych w oparciu o algorytmy alokacji platformy Azure ostatecznie połączone z definicjami użytkowników dla określonej strefy dostępności platformy Azure.
- Przez wszystkie kolejne maszyny wirtualne wdrożone jako odwołujące się do grupy umieszczania sąsiedztwa, aby umieścić wszystkie kolejne wdrożone maszyny wirtualne platformy Azure w tym samym centrum danych co pierwsza maszyna wirtualna została umieszczona w.

> [!NOTE]
> Jeśli nie ma wdrożonego sprzętu hosta, który może uruchamiać określony typ maszyny wirtualnej w tym samym centrum danych co pierwsza maszyna wirtualna została umieszczona w, wdrożenie żądanego typu maszyny wirtualnej nie powiedzie się i zakończy się komunikatem o błędzie. Mogą to być sytuacje, w których bardziej nietypowe maszyny wirtualne, takie jak maszyny wirtualne z procesorami GPU lub typu maszyny wirtualnej HPC, powinny wyśrodkować wokół przykładu maszyn wirtualnych serii M wdrożonych jako pierwszy typ maszyny wirtualnej

Jedna [Grupa zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) może mieć wiele grup umieszczania sąsiedztwa przypisanych do siebie samej. Jednak jedna grupa położenia sąsiedztwa może być przypisana tylko do jednej grupy zasobów platformy Azure.

Przy użyciu grup umieszczania sąsiedztwa należy pamiętać o:

- Po wprowadzeniu optymalnej wydajności systemu SAP i ograniczeniu do jednego centrum danych platformy Azure dla tego systemu za pomocą grup umieszczania w sąsiedztwie można połączyć wszystkie typy rodzin maszyn wirtualnych w ramach tej grupy umieszczania sąsiedztwa. Przyczyną jest to, że określony sprzęt hosta, który jest niezbędny do uruchamiania wyłącznie określonego typu maszyny wirtualnej, może nie być obecny w centrum danych "zakotwiczenie maszyny wirtualnej" grupy umieszczania zostało wdrożone
- W cyklu życia takiego systemu SAP można wymusić przeniesienie systemu do innego centrum danych. Takie przeniesienie może zostać wymuszone w przypadkach, w których podjęto decyzję, że warstwa DBMS platformy HANA w poziomie powinna być na przykład przeniesiona z czterech węzłów do 16 węzłów. Nie ma już wystarczającej pojemności, aby uzyskać dodatkowe 12 maszyn wirtualnych typu, które zostały już użyte w tym samym centrum danych.
- Ze względu na likwidowanie sprzętu firma Microsoft może tworzyć pojemności dla typów maszyn wirtualnych używanych w innym centrum danych, a nie w tym samym centrum. Takie wystąpienie może oznaczać, że można przenieść wszystkie maszyny wirtualne grupy umieszczania zbliżeniowego do innego centrum danych.


## <a name="azure-proximity-placement-groups-with-sap-systems-using-azure-vms-exclusively"></a>Grupy umieszczania zbliżeniowe platformy Azure z systemami SAP przy użyciu maszyn wirtualnych platformy Azure
Większość przypadków wdrożenia oprogramowania SAP NetWeaver i S/4HANA na platformie Azure nie korzysta z [dużych wystąpień Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). W przypadku wdrożeń takich systemów ważne jest zapewnienie optymalnej wydajności między warstwą aplikacji SAP i warstwą DBMS. W tym celu należy zdefiniować grupę umieszczania bliskości platformy Azure tylko dla tego systemu. W większości wdrożeń klientów klienci wybierali jedną [grupę zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) dla systemów SAP. W takim przypadku istnieje relacja 1:1 między, na przykład grupy zasobów systemowej usługi ERP i jej Grupa umieszczania sąsiedztwa. W niektórych innych przypadkach wdrożenia klienci zorganizują grupy zasobów w poziomie i zebranli wszystkie systemy produkcyjne w jednej grupie zasobów. W takim przypadku należy mieć 1 do wielu relacji między grupą zasobów dla produkcyjnych systemów SAP oraz kilka grup umieszczania sąsiedztwa w produkcyjnym oprogramowaniu SAP ERP, SAP BW itd. Należy unikać grupowania kilku lub nawet wszystkich systemów produkcyjnych lub nieprodukcyjnych oprogramowania SAP w pojedynczej grupie umieszczenia sąsiedztwa. W wyjątkach, w przypadku niewielkiej liczby systemów SAP lub systemu SAP, a niektóre aplikacje otaczające wymagają komunikacji sieciowej o małym opóźnieniu, można rozważyć przeniesienie tych systemów do jednej grupy umieszczania sąsiedztwa. Przyczyną takiego zalecenia jest to, że więcej systemów należy grupować w grupie umieszczania bliskości, im im większa szansa:

- Jeśli potrzebujesz typu maszyny wirtualnej, którego nie można uruchomić w konkretnym centrum danych, Grupa umieszczania bliskości została zakotwiczenie.
- Zasoby niektórych nieuniwersalnych maszyn wirtualnych, takich jak Seria M, prawdopodobnie nie zostały już spełnione, gdy zostanie poproszony o dodanie dodatkowego oprogramowania do istniejącej grupy umieszczania sąsiedztwa w czasie.

Idealne użycie w opisany sposób będzie wyglądać następująco:

![Grupy sąsiedztwa dla wszystkich maszyn wirtualnych platformy Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

W takim przypadku pojedyncze systemy SAP są pogrupowane w jednej grupie zasobów z każdą każdą grupą położenia zbliżeniowe. Nie ma zależności od tego, czy używane są konfiguracje skalowania w poziomie i w systemie DBMS.


## <a name="azure-proximity-placement-groups-and-hana-large-instances"></a>Grupy umieszczenia i wystąpienia usługi Azure zbliżeniowe
W przypadkach, w których niektóre systemy SAP korzystają z [dużych wystąpień platformy Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) jako warstwy aplikacji, poważnymi ulepszeniami opóźnień sieci między jednostką dużego wystąpienia usługi Hana i maszynami wirtualnymi platformy Azure można osiągnąć przy użyciu jednostek z dużymi wystąpieniami Hana, które zostały wdrożone w [wersji 4 wiersze lub sygnatury](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Jedna z ulepszeń polega na tym, że jednostki w dużych wystąpieniach platformy HANA, które zostały wdrożone, mają już wdrożoną grupę umieszczania w sąsiedztwie. Aby wdrożyć maszyny wirtualne warstwy aplikacji, można użyć tej grupy umieszczania w sąsiedztwie. W związku z tym te maszyny wirtualne zostaną wdrożone w tym samym centrum danych, które hostuje jednostkę dużej instancji platformy HANA.

Aby wykryć, czy jednostka dużego wystąpienia platformy HANA została wdrożona w ramach sygnatury lub wiersza poprawki 4, zapoznaj się z artykułem [Kontrola dużych wystąpień usługi Azure Hana przez Azure Portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit). W Omówienie atrybutów jednostki dużego wystąpienia usługi HANA można także sprawdzić nazwę grupy umieszczania sąsiedztwa, która została utworzona podczas wdrażania dla jednostki dużego wystąpienia platformy HANA. Nazwa wyświetlana w opisie atrybutów, to nazwa grupy położenia bliskości, należy użyć do wdrożenia maszyn wirtualnych warstwy aplikacji w programie.

W przeciwieństwie do systemów SAP korzystających tylko z usługi Azure Virtual Machines, decyzja o liczbie [grup zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) , które powinny być używane, jest odrzucana od użytkownika do stopnia w przypadku korzystania z dużych wystąpień Hana. Wszystkie jednostki usługi HANA duże wystąpienia w [dzierżawie dużej instancji Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) są pogrupowane w jednej grupie zasobów platformy Azure zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal). O ile nie zachodzi potrzeba wdrożenia w różnych dzierżawach w celu oddzielenia na przykład produkcji i nieprodukcyjnej lub niektórych systemów, wszystkie duże wystąpienia usługi HANA zostaną wdrożone w jednej dzierżawie dużej instancji HANA, która ponownie ma 1:1 relację z platformą Azure Grupa zasobów. Wszystkie pojedyncze jednostki będą mieć oddzielną grupę umieszczania sąsiedztwa. 

W efekcie grupowanie między grupami zasobów platformy Azure i grupami położenia bliskości dla jednej dzierżawy będzie wyglądać następująco:

![Grupy umieszczania zbliżeniowe dla wszystkich maszyn wirtualnych platformy Azure](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)


## <a name="short-example-of-deploying-with-azure-proximity-placement-groups"></a>Krótki przykład wdrażania przy użyciu grup rozłożeń usługi Azure zbliżeniowe
Aby dodemonstrować, jak można użyć grup umieszczania usługi Azure zbliżeniowe do wdrożenia maszyny wirtualnej, w tym celu należy zapoznać się z listą poleceń programu PowerShell, które demonstrują użycie w pierwszej kolejności przy użyciu grup rozłożeń usługi Azure zbliżeniowe

Pierwszym krokiem po zalogowaniu się przy użyciu [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) jest sprawdzenie, czy jesteś w odpowiedniej subskrypcji platformy Azure, której chcesz użyć do wdrożenia za pomocą polecenia:

<pre><code>
Get-AzureRmContext
</code></pre>

Jeśli musisz zmienić na inną subskrypcję, możesz to zrobić, wykonując następujące polecenie:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

W trzecim kroku chcesz utworzyć nową grupę zasobów platformy Azure za pomocą tego polecenia:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Nową grupę umieszczania zbliżeniowe można utworzyć teraz przy użyciu:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Teraz możesz rozpocząć wdrażanie pierwszej maszyny wirtualnej w tej grupie umieszczania sąsiedztwa przy użyciu polecenia, takiego jak:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Za pomocą powyższego polecenia zostanie wdrożona maszyna wirtualna z systemem Windows. Po pomyślnym wdrożeniu maszyny wirtualnej w regionie platformy Azure zostanie zdefiniowany zakres centrów w grupie umieszczania sąsiedztwa. Wszystkie kolejne wdrożenia maszyn wirtualnych odwołujące się do grupy umieszczania bliskości jak w ostatnim poleceniu zostaną wdrożone w tym samym centrum danych platformy Azure, o ile typ maszyny wirtualnej może być hostowany na sprzęcie umieszczonym w tym centrum danych i/lub wydajności dla tego typu maszyny wirtualnej.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Łączenie zestawów dostępności i Strefy dostępności z grupami umieszczania zbliżeniowe 
Przy użyciu Strefy dostępności na potrzeby wdrożeń systemu SAP jedną z wad jest fakt, że warstwa aplikacji SAP nie może być kontrolowana wdrożona przy użyciu zestawów dostępności w ramach określonej strefy. Ponieważ warstwa aplikacji SAP została wdrożona w tych samych strefach co warstwa systemu DBMS i odwołuje się do strefy dostępności i zestawu dostępności podczas wdrażania pojedynczej maszyny wirtualnej nie jest obsługiwana, wymuszono wdrożenie warstwy aplikacji przez odwołanie do strefy Dzięki temu utracisz możliwość upewnienia się, że maszyny wirtualne warstwy aplikacji są rozłożone w różnych domenach aktualizacji i awarii. Za pomocą grup umieszczania w sąsiedztwie można obejść to ograniczenie. Sekwencja wdrożeń będzie wyglądać następująco:

- Tworzenie grupy umieszczania w pobliżu
- Wdróż "zakotwiczenie maszyny wirtualnej", zazwyczaj serwer DBMS, odwołując się do określonej strefy dostępności platformy Azure
- Utwórz zestaw dostępności odwołujący się do grupy bliskości platformy Azure (patrz poniżej)
- Wdróż maszyny wirtualne warstwy aplikacji, odwołując się do zestawu dostępności i grupy umieszczania sąsiedztwa

Zamiast wdrażać pierwszą maszynę wirtualną, jak pokazano powyżej, należy odwołać się do strefy dostępności platformy Azure i grupy umieszczania sąsiedztwa podczas wdrażania maszyny wirtualnej:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Po pomyślnym wdrożeniu tej maszyny wirtualnej, która będzie hostować wystąpienie bazy danych mojego systemu SAP w jednej strefie dostępności platformy Azure, zakres grupy umieszczania sąsiedztwa jest ustalony w jednym z centrów, które reprezentują zdefiniowaną strefę dostępności .

Przyjęto założenie, że maszyny wirtualne usług centralnych są wdrażane w taki sam sposób, jak maszyny wirtualne DBMs, odwołując się do tych samych stref, jak w przypadku maszyn wirtualnych systemu DBMS i tych samych grup umieszczania sąsiedztwa. W następnym kroku należy utworzyć zestawy dostępności, które mają być używane dla warstwy aplikacji systemu SAP.
Należy zdefiniować i utworzyć grupę umieszczania sąsiedztwa. Polecenie do tworzenia zestawu dostępności wymaga dodatkowego odwołania do identyfikatora grupy położenia zbliżeniowe (nie nazwy). Identyfikator grupy położenia zbliżeniowe można uzyskać, korzystając z:



<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Podczas tworzenia zestawu dostępności należy wziąć pod uwagę dodatkowe parametry podczas korzystania z usługi Managed disks (domyślnie o ile nie określono inaczej) i grup umieszczania zbliżeniowe:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

W idealnym przypadku należy użyć trzech domen błędów. Jednak liczba obsługiwanych domen błędów może się różnić od regionu do regionu. W takim przypadku Maksymalna liczba domen błędów dla określonych regionów była większa. W przypadku wdrażania maszyn wirtualnych warstwy aplikacji należy dodać odwołanie do nazwy zestawu dostępności i nazwy grupy położenia zbliżeniowe, jak pokazano tutaj:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Wynik tej sekwencji będzie warstwą DBMS i centralnymi usługami systemu SAP, które znajdują się w określonych strefach dostępności i warstwie aplikacji SAP, które znajdują się za pomocą zestawów dostępności w tych samych centrach danych platformy Azure, które otrzymały maszyny wirtualne systemu DBMS szczebl.

> [!NOTE]
> Po wdrożeniu jednej maszyny wirtualnej DBMS w jednej strefie i drugiej maszynie wirtualnej systemu DBMS do innej strefy w celu utworzenia konfiguracji wysokiej dostępności należy wymagać różnych grup umieszczania sąsiedztwa dla każdej z tych stref. Ten sam wartość si dla zestawu dostępności, którego możesz użyć

## <a name="get-an-existing-system-into-azure-proximity-placement-groups"></a>Uzyskaj istniejący system do grup rozłożeń usługi Azure zbliżeniowe
Ponieważ wdrożono już systemy SAP, możesz chcieć zoptymalizować opóźnienie sieci niektórych krytycznych systemów i zlokalizować warstwę aplikacji i warstwę DBMS w tym samym centrum danych. Na etapie publicznej wersji zapoznawczej funkcji grupy umieszczania sąsiedztwa, usuwanie maszyn wirtualnych i nowe tworzenie maszyn wirtualnych jest niezbędne do przechodzenia do grup umieszczania w sąsiedztwie. Na tym etapie działania nie jest wystarczająco dobre, aby zamknąć maszyny wirtualne, aby można było przypisywać te maszyny wirtualne zamykające do grup umieszczania w sąsiedztwie.


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z dokumentacją:

- [Obciążenie SAP na liście kontrolnej planowania i wdrażania platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Przeglądania Wdrażanie maszyn wirtualnych w grupach umieszczania sąsiedztwa przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Przeglądania Wdrażanie maszyn wirtualnych w grupach umieszczania sąsiedztwa przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

