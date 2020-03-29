---
title: Jak odlewnia w chmurze integruje się z platformą Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak odlewnia w chmurze może korzystać z usług platformy Azure w celu poprawy środowiska przedsiębiorstwa
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 04ef72f7ec70b370305395ae8de8180f4594b43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277345"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integracja usługi Cloud Foundry z platformą Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) to platforma PaaS działająca na platformie IaaS dostawców chmury. Oferuje spójne środowisko wdrażania aplikacji przez dostawców chmury. Może również integrować się z różnymi usługami platformy Azure, z wysokiej klasy wysokiej klasy przedsiębiorstwem, skalowalnością i oszczędnościami kosztów.
Istnieje [6 podsystemów Cloud Foundry,](https://docs.cloudfoundry.org/concepts/architecture/)które można elastycznie skalować w trybie online, w tym: Routing, Uwierzytelnianie, Zarządzanie cyklem życia aplikacji, Zarządzanie usługami, Wiadomości i Monitorowanie. Dla każdego z podsystemów można skonfigurować odlewnia w chmurze do korzystania z usługi platformy Azure korespondenta. 

![Odnaleźnia chmury w architekturze integracji platformy Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Wysoka dostępność i skalowalność
### <a name="managed-disk"></a>Dysk zarządzany
Bosh używa usługi Azure CPI (Cloud Provider Interface) do tworzenia i usuwania dysków procedur. Domyślnie używane są dyski niezarządzane. Wymaga od klienta ręcznego tworzenia kont magazynu, a następnie konfigurowania kont w plikach manifestu CF. Wynika to z ograniczenia liczby dysków na konto magazynu.
Teraz [dysk zarządzany](https://azure.microsoft.com/services/managed-disks/) jest dostępny, oferuje zarządzane bezpieczne i niezawodne przechowywanie dysków dla maszyn wirtualnych. Klient nie musi już zajmować się kontem magazynu dla skali i ha. Platforma Azure automatycznie rozmieszcza dyski. Niezależnie od tego, czy jest to nowe, czy istniejące wdrożenie, cpi platformy Azure będzie obsługiwać tworzenie lub migrację dysku zarządzanego podczas wdrażania modułu CF. Jest obsługiwany przez PCF 1.11. Można również zapoznać się z open source Cloud Foundry [Managed Disk wskazówki](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) dla odwołania. 
### <a name="availability-zone-"></a>Strefa dostępności *
Jako platforma aplikacji natywna dla chmury, Cloud Foundry została zaprojektowana z [czterema poziomami wysokiej dostępności.](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html) Podczas gdy pierwsze trzy poziomy awarii oprogramowania mogą być obsługiwane przez sam system CF, odporność na uszkodzenia platformy jest dostarczana przez dostawców chmury. Kluczowe składniki cf powinny być chronione za pomocą rozwiązania platformy platformy platformy dostawcy chmury. Obejmuje to GoRouters, Diego Brains, bazę danych CF i płytki usługowe. Domyślnie [zestaw dostępności platformy Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) jest używany do odporności na uszkodzenia między klastrami w centrum danych.
Dobrą nowością jest, [Strefa dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview ) jest już wydana, dzięki temu, że tolerancja usterek zostanie przesunięcana na wyższy poziom, z nadmiarowością o małych opóźnieniach w centrach danych.
Strefa dostępności platformy Azure osiąga wysokiej dostępności, umieszczając zestaw maszyn wirtualnych w ponad 2 centrach danych, każdy zestaw maszyn wirtualnych jest nadmiarowy dla innych zestawów. Jeśli jedna strefa jest w dół, inne zestawy są nadal żywe, odizolowane od katastrofy.
> [!NOTE] 
> Strefa dostępności platformy Azure nie jest jeszcze oferowana we wszystkich regionach, sprawdź najnowsze [ogłoszenie o liście obsługiwanych regionów](https://docs.microsoft.com/azure/availability-zones/az-overview). W przypadku odnajdowania chmury open source sprawdź [strefę dostępności platformy Azure, aby uzyskać wskazówki dotyczące odnaleźnia chmury typu open source.](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone)

## <a name="2-network-routing"></a>2. Routing sieciowy
Domyślnie podstawowy moduł równoważenia obciążenia platformy Azure jest używany dla przychodzących żądań interfejsu API/aplikacji CF, przesyłając je do gorouters. Komponenty CF, takie jak Diego Brain, MySQL, ERT mogą również używać modułu równoważącego ruch dla HA. Platforma Azure udostępnia również zestaw w pełni zarządzanych rozwiązań równoważenia obciążenia. Jeśli szukasz zakończenia TLS ("Odciążanie SSL") lub przetwarzania warstwy aplikacji żądania HTTP/HTTPS, należy wziąć pod uwagę bramę aplikacji. Aby uzyskać wysoką dostępność i skalowalność równoważenia obciążenia w warstwie 4, należy wziąć pod uwagę standardowy moduł równoważenia obciążenia.
### <a name="azure-application-gateway-"></a>Brama aplikacji platformy Azure *
[Usługa Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) oferuje różne funkcje równoważenia obciążenia warstwy 7, w tym odciążanie SSL, kompleksowy ssl do końca, zapora aplikacji sieci Web, koligacja sesji oparta na plikach cookie i inne. Bramę aplikacji można [skonfigurować w odnaleźniu w chmurze open source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). W przypadku pcf sprawdź [informacje o wersji PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) dla testu POC.

### <a name="azure-standard-load-balancer-"></a>Standardowy moduł równoważenia obciążenia platformy Azure *
Azure Load Balancer jest modułem równoważenia obciążenia warstwy 4. Służy do dystrybucji ruchu między wystąpieniami usług w zestawie z równoważenia obciążenia. Wersja standardowa zapewnia [zaawansowane funkcje](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) na górze wersji podstawowej. Na przykład 1. Limit maksymalnej puli wewnętrznej bazy danych jest podnoszony ze 100 do 1000 maszyn wirtualnych.  2. Punkty końcowe obsługują teraz wiele zestawów dostępności zamiast pojedynczego zestawu dostępności.  3. Dodatkowe funkcje, takie jak porty wysokiej źródła informacji, bogatsze dane monitorowania i tak dalej. Jeśli przenosisz się do strefy dostępności platformy Azure, wymagany jest standardowy moduł równoważenia obciążenia. W przypadku nowego wdrożenia zalecamy rozpoczęcie pracy z modułem równoważenia obciążenia standardowego platformy Azure. 

## <a name="3-authentication"></a>3. Uwierzytelnianie 
[Cloud Foundry User Account and Authentication](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) to centralna usługa zarządzania tożsamościami dla cf i jej różnych składników. [Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) to usługa zarządzania katalogami i tożsamościami firmy Microsoft wielodostępnych, opartych na chmurze. Domyślnie UAA jest używany do uwierzytelniania Odlewniczej chmury. Jako opcja zaawansowana UAA obsługuje również usługę Azure AD jako magazyn użytkowników zewnętrznych. Użytkownicy usługi Azure AD mogą uzyskiwać dostęp do odnajdywania w chmurze przy użyciu ich tożsamości LDAP bez konta odnajdywania w chmurze. Wykonaj następujące kroki, aby [skonfigurować usługę Azure AD dla UAA w PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Przechowywanie danych dla systemu runtime odlewniczego chmury
Cloud Foundry oferuje dużą rozszerzalność do korzystania z usługi Azure blobstore lub Azure MySQL/PostgreSQL dla magazynu systemu środowiska wykonawczego aplikacji.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Usługa Azure Blobstore dla magazynu obiektów blob kontrolera chmury odnalezczania chmury w chmurze
Magazyn obiektów blob kontrolera chmury jest magazynem danych o znaczeniu krytycznym dla pakietów kompilacji, kropelek, pakietów i pul zasobów. Domyślnie serwer NFS jest używany dla magazynu obiektów blobstore kontrolera chmury. Aby uniknąć pojedynczego punktu awarii, należy użyć usługi Azure Blob Storage jako magazynu zewnętrznego. Zapoznaj się z [dokumentacją Cloud Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) dla tła i [opcjami w Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL jako Chmura Odlewnia Elastyczna Baza Danych Czasu Pracy *
Cf Elastic Runtime wymaga dwóch głównych baz danych systemu:
#### <a name="ccdb"></a>Baza CCDB 
Baza danych kontrolera w chmurze.  Kontroler chmury zapewnia punkty końcowe interfejsu API REST dla klientów, aby uzyskać dostęp do systemu. CCDB przechowuje tabele dla organizacji, przestrzeni, usług, ról użytkowników i innych dla kontrolera chmury.
#### <a name="uaadb"></a>UAADB 
Baza danych dla konta użytkownika i uwierzytelniania. Przechowuje dane związane z uwierzytelnianiem użytkownika, na przykład zaszyfrowane nazwy użytkowników i hasła.

Domyślnie można użyć lokalnej bazy danych systemu (MySQL). Aby uzyskać wysokiej jakości i skalować, skorzystaj z zarządzanych przez platformę Azure usług MySQL lub PostgreSQL. Oto [instrukcja włączania platformy Azure MySQL/PostgreSQL dla CCDB, UAADB i innych baz danych systemu z Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Broker usług otwartych
Broker usług platformy Azure oferuje spójny interfejs do zarządzania dostępem aplikacji do usług platformy Azure. Nowy [projekt Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure) zapewnia jeden i prosty sposób dostarczania usług do aplikacji w odlewni w chmurze, openshift i kubernetes. Zobacz [kafelek Azure Open Service Broker dla PCF,](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) aby uzyskać instrukcje dotyczące wdrażania na PCF.

## <a name="6-metrics-and-logging"></a>6. Metryki i rejestrowanie
Dysza usługi Azure Log Analytics jest składnikiem odnajdywacza w chmurze, który przekazuje metryki z [loggregatora odnajdywacza chmury firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) do [dzienników usługi Azure Monitor.](https://azure.microsoft.com/services/log-analytics/) Dzięki dyszy można zbierać, wyświetlać i analizować metryki kondycji i wydajności systemu CF w wielu wdrożeniach.
Kliknij [tutaj,](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) aby dowiedzieć się, jak wdrożyć dyszę usługi Azure Log Analytics zarówno w środowisku Open Source, jak i Pivotal Cloud Foundry, a następnie uzyskać dostęp do danych z konsoli dzienników usługi Azure Monitor. 
> [!NOTE]
> Z PCF 2.0 metryki kondycji BOSH dla maszyn wirtualnych są domyślnie przekazywane do loggregatora Firehose i są zintegrowane z konsolą dzienników usługi Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Oszczędność kosztów
### <a name="cost-saving-for-devtest-environments"></a>Oszczędność kosztów w środowiskach deweloperskich/testowych
#### <a name="b-series-"></a>Seria B: *
Podczas gdy seria VM f i D były powszechnie zalecane dla środowiska produkcyjnego Pivotal Cloud Foundry, nowa [seria B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) "burstable" przynosi nowe opcje. Maszyny wirtualne serii B serii B są idealne dla obciążeń, które nie wymagają ciągłej pełnej wydajności procesora, takich jak serwery sieci web, małe bazy danych oraz środowiska programistyczne i testowe. Te obciążenia zazwyczaj mają wymagania dotyczące wydajności serii. Jest $0.012/hour (B1) w porównaniu do $0.05/hour (F1), zobacz pełną listę [rozmiarów maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) i [cen,](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) aby uzyskać szczegółowe informacje. 
#### <a name="managed-standard-disk"></a>Zarządzany dysk standardowy: 
Dyski premium były zalecane ze swojej niezawodnej wydajności w produkcji.  Dzięki [dyskowi zarządzanego](https://azure.microsoft.com/services/managed-disks/)standardowa pamięć masowa może również zapewnić podobną niezawodność i różną wydajność. W przypadku obciążenia, które nie jest zależne od wydajności, takiego jak środowisko deweloperne/testowe lub niekrytyczne, zarządzane dyski standardowe oferują alternatywną opcję o niższych kosztach.  
### <a name="cost-saving-in-general"></a>Oszczędność kosztów w ogóle 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Znaczne oszczędności kosztów maszyny Wirtualnej z rezerwacjami platformy Azure: 
Obecnie wszystkie maszyny wirtualne CF są rozliczane przy użyciu cen "na żądanie", nawet jeśli środowiska zazwyczaj pozostają w nieskończoność. Teraz możesz zarezerwować pojemność maszyny wirtualnej na okres 1 lub 3 lat i uzyskać zniżki w wysokości 45-65%. Rabaty są stosowane w systemie rozliczeniowym, bez zmian w środowisku. Aby uzyskać szczegółowe informacje, zobacz [Jak działają rezerwacje platformy Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/) 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Zarządzany dysk premium o mniejszych rozmiarach: 
Dyski zarządzane obsługują mniejsze rozmiary dysków, na przykład P4(32 GB) i P6(64 GB) zarówno dla dysków premium, jak i standardowych. Jeśli masz małe obciążenia, możesz zaoszczędzić koszty podczas migracji ze standardowych dysków premium do zarządzanych dysków premium.
#### <a name="use-azure-first-party-services"></a>Korzystanie z usług pierwszej firmy platformy Azure: 
Korzystanie z usługi pierwszej firmy platformy Azure obniży długoterminowe koszty administracyjne, oprócz wysokiej klasy i niezawodności wymienionych w powyższych sekcjach. 

Pivotal uruchomiła [ert small footprint](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) dla klientów PCF, komponenty są zlokalizowane w zaledwie 4 maszyny wirtualne, z systemem do 2500 wystąpień aplikacji. Wersja próbna jest teraz dostępna za pośrednictwem [usługi Azure Market place.](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)

## <a name="next-steps"></a>Następne kroki
Funkcje integracji platformy Azure są najpierw dostępne z [Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), zanim będą dostępne w Pivotal Cloud Foundry. Funkcje oznaczone * nadal nie są dostępne za pośrednictwem PCF. Integracja cloud foundry z usługą Azure Stack nie jest omówiona w tym dokumencie.
Aby uzyskać pomoc techniczną PCF w przypadku funkcji oznaczonych *, lub integracji Cloud Foundry z usługą Azure Stack, skontaktuj się z menedżerem konta Pivotal i Microsoft, aby uzyskać najnowszy stan. 

