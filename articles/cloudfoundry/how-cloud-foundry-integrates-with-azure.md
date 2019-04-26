---
title: Jak rozwiązanie Cloud Foundry integruje się z platformą Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak użyć usług platformy Azure do poprawić funkcjonalność z perspektywy Enterprise Cloud Foundry
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 7cbffdd40e574c7e906a9388b70ca9d32fd84649
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60198979"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integracja usługi Cloud Foundry z platformą Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) to platforma PaaS uruchomione na platformie IaaS dostawcy chmury. Oferuje środowisko wdrażania aplikacji spójne w dostawcy usług w chmurze. Można również integrować z różnymi usługami platformy Azure przy użyciu klasy korporacyjnej o wysokiej dostępności, skalowalności i oszczędności kosztów.
Istnieją [6 podsystemów Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), które może być elastyczne skalowanie w trybie online, w tym: Routing, uwierzytelniania, zapewniają Zarządzanie cyklem życia aplikacji, zarządzanie usługą obsługi wiadomości i monitorowania. Dla każdej podsystemów można skonfigurować Cloud Foundry używać korespondenta usługi platformy Azure. 

![Cloud Foundry na architekturę Integracja z platformą Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Wysoka dostępność i skalowalność
### <a name="managed-disk"></a>Dysk zarządzany
Bosh używa CPI Azure (interfejs dostawcy chmury) do tworzenia dysku i usuwanie procedur. Domyślnie używane są dyski niezarządzane. Wymaga klienta, aby ręcznie utworzyć konta magazynu, a następnie skonfigurować konta w plikach manifestu CF. Jest to ze względu na ograniczenie liczby dysków na konto magazynu.
Teraz [dysku zarządzanego](https://azure.microsoft.com/services/managed-disks/) jest dostępna, oferuje magazyn na dyskach zarządzanych bezpieczny i niezawodny dla maszyn wirtualnych. Klient nie są już potrzebne do czynienia z kontem magazynu w celu zapewnienia skalowalności i wysokiej dostępności. Azure, które automatycznie rozmieszcza dysków. Czy jest nowym lub istniejącym wdrożeniu Azure CPI będzie obsługiwać tworzenia lub migracji dysku zarządzanego podczas wdrażania usługi CF. Możliwe jest rozwiązanie PCF 1.11. Możesz też zapoznać się z rozwiązania typu open-source Cloud Foundry [wskazówki dysku zarządzanego](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) dla odwołania. 
### <a name="availability-zone-"></a>Strefa dostępności *
Platforma aplikacji natywnych dla chmury, Cloud Foundry została zaprojektowana pod kątem [cztery wysoki poziom dostępności](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Podczas pierwszych trzech poziomów błędy oprogramowania może zostać obsłużony przez sam system CF, platforma odporności na uszkodzenia są udostępniane przez dostawców rozwiązań w chmurze. Najważniejsze składniki CF powinny być chronione przy użyciu dostawcy chmury platforma rozwiązania o wysokiej dostępności. W tym GoRouters, mózgów Diego, Kafelki CF bazy danych i usług. Domyślnie [zestawu dostępności platformy Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) służy do odporności na uszkodzenia między klastrami w centrum danych.
Jest dobre nowe, [strefy dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview ) wydaniu teraz Przywracanie odporności na uszkodzenia do następnego poziomu z nadmiarowością małe opóźnienia w centrach danych.
Strefy dostępności platformy Azure osiąga wysokiej dostępności, umieszczając zestaw maszyn wirtualnych do centrów danych 2 +, poszczególne zestawy maszyn wirtualnych są nadmiarowe do innych zestawów. Jeśli w jednej strefie nie działa, inne zestawy są nadal aktywne, w odizolowanych od po awarii.
> [!NOTE] 
> Strefy dostępności platformy Azure nie jest dostępna do wszystkich regionów jeszcze, Sprawdź najnowsze [ogłoszenie listę obsługiwanych regionów](https://docs.microsoft.com/azure/availability-zones/az-overview). Otwórz źródło Cloud Foundry znaleźć [strefy dostępności platformy Azure "open source" Cloud Foundry wskazówki dotyczące](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Routing w sieci
Domyślnie program Azure podstawowego modułu równoważenia obciążenia jest używana do obsługi przychodzących żądań aplikacji interfejsu API usługi CF, przekazując je dalej do Gorouters. CF składników, takich jak mózg Diego, MySQL, ERT umożliwia również moduł równoważenia obciążenia do równoważenia ruchu zapewnia wysoką dostępność. System Azure oferuje także zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia. Jeśli szukasz, kończenie żądań protokołu TLS ("odciążanie protokołu SSL") lub na przetwarzanie warstwy aplikacji żądania HTTP/HTTPS, należy wziąć pod uwagę Application Gateway. Aby uzyskać wysoką dostępność i skalowalność równoważenia obciążenia na warstwy 4 należy wziąć pod uwagę standardowego modułu równoważenia obciążenia.
### <a name="azure-application-gateway-"></a>Bramy aplikacji platformy Azure *
[Usługa Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) oferuje różnorodne możliwości, łącznie z protokołu SSL, odciążanie typu end to end SSL, zapory aplikacji sieci Web, koligacja sesji na podstawie pliku cookie i inne równoważenia obciążenia warstwy 7. Możesz [konfigurowania bramy aplikacji Otwórz źródło Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). Rozwiązanie PCF, można sprawdzić [wersji PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) testu weryfikacji Koncepcji.

### <a name="azure-standard-load-balancer-"></a>Usługa Azure standardowego modułu równoważenia obciążenia *
Usługa Azure Load Balancer to moduł równoważenia obciążenia warstwy 4. Służy do dystrybucji ruchu między wystąpieniami usług w zestawie z równoważeniem obciążenia. Wersja standardowa oferuje [zaawansowane funkcje](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) na podstawie wersji podstawowej. Na przykład 1. Maksymalny limit puli wewnętrznej bazy danych jest wywoływane od 100 do 1000 maszyn wirtualnych.  2. Punkty końcowe są teraz obsługiwane w wielu zestawom dostępności zamiast pojedynczym zestawie dostępności.  3. Dodatkowe funkcje, takie jak porty wysokiej dostępności, dokładniejsze dane monitorowania i tak dalej. Jeśli przenosisz do strefy dostępności platformy Azure, standardowego modułu równoważenia obciążenia jest wymagany. Dla nowego wdrożenia firma Microsoft zaleca uruchomienie przy użyciu usługi Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. Authentication 
[Cloud Foundry konta użytkownika i uwierzytelniania](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) to usługa zarządzania tożsamości centralnej CF i jego składnikami. [Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) to usługa firmy Microsoft wielodostępna, oparta na chmurze zarządzania katalogami i tożsamościami zarządzania. Domyślnie UAA jest używany do uwierzytelniania usługi Cloud Foundry. Jako opcję zaawansowaną UAA obsługuje usługi Azure AD jako użytkownik zewnętrzny magazyn. Użytkownicy usługi Azure AD mogą uzyskać dostęp do usługi Cloud Foundry przy użyciu ich tożsamości protokołu LDAP, bez konta usługi Cloud Foundry. Wykonaj następujące kroki, aby [Konfigurowanie programu Azure AD dla użytki PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Przechowywanie danych na potrzeby System plików środowiska uruchomieniowego usługi Cloud Foundry
Usługi cloud Foundry upublicznienie rozszerza doskonałe użycie elementu blobstore platformy Azure lub usługi Azure MySQL/PostgreSQL do przechowywania danych aplikacji środowiska uruchomieniowego systemu.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Usługa Azure elementu Blobstore dla elementu blobstore kontrolera Cloud Foundry chmury
Elementu blobstore kontrolera chmury jest magazynem danych krytycznych buildpacks, droplety, pakietów i pul zasobów. Domyślnie serwer NFS służy do elementu blobstore kontrolera w chmurze. Aby uniknąć pojedynczego punktu awarii, należy użyć usługi Azure Blob Storage jako magazynu zewnętrznego. Zapoznaj się z [dokumentacji usługi Cloud Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) w tle, a [opcje w rozwiązaniu Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL jako Cloud Foundry elastyczne uruchomić czasu bazę danych *
Elastyczne środowisko uruchomieniowe usługi CF wymaga dwóch głównych systemów baz danych:
#### <a name="ccdb"></a>CCDB 
Baza danych kontrolera w chmurze.  Kontroler cloud oferuje punkty końcowe interfejsu API REST, aby klienci mogli uzyskać dostęp do systemu. CCDB przechowuje tabele, organizacje, miejsca do magazynowania, usług ról użytkownika i bardziej kontrolera w chmurze.
#### <a name="uaadb"></a>UAADB 
Baza danych dla konta użytkownika i uwierzytelniania. Przechowuje uwierzytelnianie użytkownika związane z danymi, na przykład zaszyfrowane nazwy użytkownika i hasła.

Domyślnie system lokalny bazy danych (MySQL) może służyć do. Zapewnia wysoką dostępność i skala, używają usługi Azure MySQL zarządzanych lub postgresql w warstwie usług. Oto instrukcja [włączania usługi Azure MySQL/PostgreSQL CCDB, UAADB i innych systemowych baz danych przy użyciu Otwórz źródło Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Usługi Open Service Broker
Broker usług platformy Azure zapewnia spójny interfejs do zarządzania dostępem aplikacji do usług platformy Azure. Nowy [Open Service Broker for Azure projektu](https://github.com/Azure/open-service-broker-azure) zapewnia pojedynczy i prosty sposób dostarczanie usług do aplikacji usługi Cloud Foundry, OpenShift i Kubernetes. Zobacz [Azure Open Service Broker dla kafelka PCF](https://network.pivotal.io/products/azure-open-service-broker-pcf/) instrukcje wdrożenia na platformie PCF.

## <a name="6-metrics-and-logging"></a>6. Rejestrowanie i metryki
Dodatek usługi Azure Log Analytics Nozzle jest składnikiem usługi Cloud Foundry, która przesyła dalej metryki z [pilnym loggregator Cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) do [dzienniki usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/). Za pomocą dodatku nozzle usługi CF można zbierać, wyświetlać i analizować metryki kondycji i wydajności systemu CF na wiele wdrożeń.
Kliknij przycisk [tutaj](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) Aby dowiedzieć się, jak wdrożyć dodatek usługi Azure Log Analytics Nozzle typu Open Source i Pivotal Cloud Foundry środowiska, a następnie uzyskać dostęp do danych z platformy Azure Monitor dzienniki konsoli. 
> [!NOTE]
> 2.0 PCF BOSH metryk kondycji dla maszyn wirtualnych są przekazywane do pilnym Loggregator domyślnie i są zintegrowane w konsoli Dzienniki usługi Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Redukcja kosztów
### <a name="cost-saving-for-devtest-environments"></a>Oszczędność do środowiska deweloperskie i testowe
#### <a name="b-series-"></a>Seria B: *
Podczas serii F i D maszyny Wirtualnej były często zalecane w środowisku produkcyjnym rozwiązaniu Pivotal Cloud Foundry nowy "z możliwością zwiększania wydajności" [seria B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) udostępnia nowe opcje. Seria B z możliwością zwiększania wydajności maszyny wirtualne są idealne dla obciążeń, których nie potrzebujesz pełnej wydajności procesora CPU w sposób ciągły, takich jak serwery sieci web, małych baz danych i rozwoju i środowisk testowych. Te obciążenia mają zwykle wymagań dotyczących wydajności z możliwością zwiększania wydajności. $ 0,012/godz. (B1) w porównaniu do 0,05 USD za godzinę (F1), zobacz pełną listę [rozmiarów maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) i [ceny](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) Aby uzyskać szczegółowe informacje. 
#### <a name="managed-standard-disk"></a>Dysku zarządzanego w warstwie standardowa: 
Dyski w warstwie Premium zostały zalecane w przypadku niezawodność, wydajność w środowisku produkcyjnym.  Za pomocą [dysku zarządzanego](https://azure.microsoft.com/services/managed-disks/), magazynu w warstwie standardowa może również dostarczać podobne niezawodność, wydajność innego. W przypadku obciążeń, które nie są zależne od wydajności, takich jak tworzenie i testowanie lub niekrytyczne środowiska zarządzane dyski w warstwie standardowa oferują alternatywnych opcji przy niższych kosztach.  
### <a name="cost-saving-in-general"></a>Ogólnie rzecz biorąc redukcja kosztów 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Znaczące maszyny Wirtualnej koszt zapisywanie za pomocą platformy Azure rezerwacji: 
Obecnie wszystkie CF maszyny wirtualne są rozliczane przy użyciu cen "na żądanie", mimo że środowiska zazwyczaj panowania przez czas nieokreślony. Teraz możesz zarezerwować pojemności maszyn wirtualnych na okres 1 i 3-letnie i Uzyskaj rabat wynoszący 45 do 65%. W systemie rozliczeniowym, bez konieczności wprowadzania zmian do środowiska są stosowane rabaty za. Aby uzyskać więcej informacji, zobacz [platformy Azure, działa rezerwacje](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Zarządzane za pomocą mniejsze rozmiary dysku w warstwie Premium: 
Zarządzane dyski pomocy technicznej mniejsze rozmiary dysku, na przykład P4(32 GB) i P6(64 GB) dla warstwy premium, jak i dyski w warstwie standardowa. W przypadku obciążeń małymi można zapisać kosztów, podczas migracji z dysków magazynu premium standardowa do dysków zarządzanych w warstwie premium.
#### <a name="use-azure-first-party-services"></a>Użyj platformy Azure pierwsze usługi innej firmy: 
Korzystając z zalet platformy Azure usługa firmy Microsoft obniży długoterminowego podawania kosztów, oprócz wysokiej dostępności i niezawodności w powyższym sekcje. 

Uruchomiono Pivotal [małych ERT zużycie](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) klientom PCF składniki wspólnie znajdują się w tylko 4 maszyn wirtualnych uruchomionych maksymalnie 2500 wystąpień aplikacji. Wersja próbna jest teraz dostępna za pośrednictwem [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Następne kroki
Funkcje integracji platformy Azure będą dostępne z [Otwórz źródło Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), zanim będzie dostępna w rozwiązaniu Pivotal Cloud Foundry. Funkcje oznaczone atrybutem * nadal nie są dostępne za pośrednictwem PCF. Cloud Foundry integracji z usługą Azure Stack nie jest to omówione w tym dokumencie.
Dla obsługi PCF na funkcje oznaczone atrybutem *, lub Cloud Foundry integracji z usługą Azure Stack, skontaktuj się z menedżerem firm Pivotal i Microsoft najnowszy stan. 

