---
title: Jak chmury Foundry integruje się z programem Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak utlize może Foundry chmury Azure usług do udoskonalenie Enterprice
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
ms.openlocfilehash: 1f4afbe1849210c55c392d014449224f2fe97b04
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655468"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integracja Foundry chmury platformy Azure

[Chmury Foundry](https://docs.cloudfoundry.org/) to platforma PaaS uruchomiony na górze dostawców chmury IaaS platformy. Oferuje środowisko wdrażania aplikacji spójne przez dostawców chmury. Ponadto on również integrować z różnymi usługami platformy Azure z klasy enterprise wysokiej dostępności, skalowalności i oszczędności.
Brak [6 podsystemami Foundry chmury](https://docs.cloudfoundry.org/concepts/architecture/), które mogą być elastycznie skali w trybie online, łącznie z: routingu, uwierzytelnianie, Zarządzanie cyklem życia aplikacji, zarządzanie usługą obsługi wiadomości i monitorowania. Dla każdego z podsystemów można skonfigurować Foundry chmury mogą korzystać z korespondenta usługi Azure. 

![Foundry chmury w oparciu o architekturę integracja Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Wysoką dostępność i skalowalność
### <a name="managed-disk"></a>Dysk zarządzany
Bosh wykorzystuje CPI Azure (interfejs dostawcy chmury) dla dysku, tworzenie i usuwanie procedury. Domyślnie są używane dyski niezarządzane. Wymaga to klienta, aby ręcznie utworzyć konta magazynu, a następnie skonfigurować konta w plikach manifestu CF. Jest to spowodowane ograniczenia dotyczące liczby dysków na konto magazynu.
Teraz [zarządzane dysku](https://azure.microsoft.com/services/managed-disks/) jest dostępny, oferuje dysków zarządzanych w bezpieczny i niezawodny magazyn dla maszyny wirtualnej. Odbiorcy nie muszą już radzenia sobie z kontem magazynu dla skalowalności i wysokiej dostępności. Azure automatycznie rozmieszcza dysków. Czy usługa jest nowym lub istniejącym wdrożeniu, Azure CPI obsłuży tworzenia lub migracji dysków zarządzanych podczas wdrażania CF. Możliwe jest PCF 1.11. Można również zapoznać się Foundry chmury open source [dysku zarządzanych wskazówek](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) odwołania. 
### <a name="availability-zone-"></a>Dostępność strefy *
Jako platforma chmury natywnych aplikacji, chmury Foundry zaprojektowano z [cztery poziom wysokiej dostępności](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Gdy pierwsze trzy poziomy błędy oprogramowania są obsługiwane przez sam system CF, platformy odporność na uszkodzenia są udostępniane przez dostawców chmury. Najważniejsze składniki CF powinny być chronione przy użyciu dostawcy chmury platformy rozwiązania wysokiej dostępności. W tym GoRouters, mózgów Diego, CF Kafelki bazy danych i usług. Domyślnie [zestawu dostępności Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) służy odporności na uszkodzenia między klastrami w centrum danych.
Jest dobra nowe, [strefy dostępności Azure](https://docs.microsoft.com/azure/availability-zones/az-overview ) wydaniu teraz przechodzenie odporność na uszkodzenia do następnego poziomu przy użyciu nadmiarowość małe opóźnienia między centrami danych.
Strefy dostępności Azure realizuje HA przez umieszczenie zestaw maszyn wirtualnych w centrach danych 2 +, każdy zestaw maszyn wirtualnych są nadmiarowe do innych zestawów. Jeśli jednej strefie nie działa, innych zestawów są nadal aktywne, odizolowanych od po awarii.
> [!NOTE] 
> Azure dostępność strefy nie jest oferowany na wszystkie regiony jeszcze, Sprawdź najnowsze [anonsu do listy obsługiwanych regionów](https://docs.microsoft.com/azure/availability-zones/az-overview). Otwórz Foundry chmury źródła można sprawdzić [strefy dostępności Azure wskazówki Foundry chmury typu open source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Routing w sieci
Domyślnie usługi równoważenia obciążenia podstawowe Azure jest używana dla przychodzących żądań aplikacji interfejsu API CF, przekazując je dalej do Gorouters. CF składników, takich jak inteligencji Diego, MySQL, Wstaw umożliwia również usługi równoważenia obciążenia równoważenie ruchu dla wysokiej dostępności. Ponadto platforma Azure oferuje zestaw pełni zarządzana rozwiązań do równoważenia obciążenia. Jeśli szukasz dla zakończenia połączenia TLS ("odciążanie protokołu SSL") lub na przetwarzanie warstwy aplikacji żądań HTTP i HTTPS, należy wziąć pod uwagę Application Gateway. Wysokiej dostępności i skalowalności obciążenia równoważenia warstwy 4 należy wziąć pod uwagę modułu równoważenia obciążenia standardowego.
### <a name="azure-application-gateway-"></a>Brama aplikacji w Azure *
[Brama aplikacji w Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) oferuje różne obciążenia warstwy 7 równoważenia możliwości, takie jak SSL Odciążanie kompleksowe SSL, Zapora aplikacji sieci Web, koligacji na podstawie plików cookie sesji i inne. Możesz [brama aplikacji w otwartych Foundry chmurze źródło](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). PCF, można sprawdzić [wersji PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) dla testu weryfikacji Koncepcji.

### <a name="azure-standard-load-balancer-"></a>Moduł równoważenia obciążenia standardowego Azure *
Moduł równoważenia obciążenia Azure jest usługą równoważenia obciążenia warstwy 4. Służy do dystrybucji ruchu między wystąpieniami usług w zestawie o zrównoważonym obciążeniu. Zawiera standardową wersję [zaawansowane funkcje](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) na podstawowe wersji. Na przykład 1. Maksymalny limit puli wewnętrznej bazy danych jest uruchamiany od 100 do 1000 maszyn wirtualnych.  2. Punkty końcowe teraz obsługuje wiele zestawów dostępności zamiast zestawu dostępności jednego.  3. Dodatkowe funkcje, takie jak porty wysokiej dostępności, bardziej zaawansowane funkcje monitorowania danych itp. Jeśli przenosisz do strefy dostępności Azure usługi równoważenia obciążenia standardowego jest wymagana. W przypadku nowego wdrożenia zalecamy rozpoczynać się od standardowego równoważenia obciążenia Azure. 

## <a name="3-authentication"></a>3. Authentication 
[Konto użytkownika Foundry i uwierzytelniania w chmurze](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) jest usługą zarządzania centralnej tożsamości CF i jego różnych składników. [Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest wielodostępnej, oparte na chmurze katalogami i tożsamościami zarządzania usługi Microsoft. Domyślnie UAA jest używany do uwierzytelniania Foundry chmury. Jako opcję zaawansowaną UAA obsługuje usługi Azure AD jako użytkownik zewnętrzny magazynu. Azure AD użytkownicy mogą uzyskiwać dostęp Foundry chmury przy użyciu tożsamości LDAP, bez konta Foundry chmury. Wykonaj następujące kroki, aby [Konfigurowanie usługi Azure AD dla UAA w PCF](http://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Magazyn danych dla chmury Foundry środowiska wykonawczego systemu
Foundry chmury zapewnia dużą elastyczność w za pomocą elementu blobstore Azure lub usługi Azure MySQL/PostgreSQL aplikacji środowiska wykonawczego systemu magazynu.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure elementu Blobstore dla elementu blobstore kontrolerem chmury Foundry chmury
Elementu blobstore kontrolerem chmury jest magazynem danych o kluczowym znaczeniu buildpacks, droplety, pakietów i pul zasobów. Domyślnie serwer systemu plików NFS jest używany do elementu blobstore kontrolerem chmury. Aby uniknąć pojedynczego punktu awarii, należy użyć magazynu obiektów Blob Azure jako magazynu zewnętrznego. Zapoznaj się z [dokumentacji Foundry chmury](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) w tle, a [opcje w kluczową Foundry chmury](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>Czas bazy danych uruchom MySQL/PostgreSQL jako elastyczna Foundry chmury *
Elastyczne środowisko uruchomieniowe CF wymaga dwóch podstawowych składników systemu baz danych:
#### <a name="ccdb"></a>CCDB 
Baza danych kontrolerem chmury.  Kontroler chmury zapewnia punkty końcowe interfejsu API REST dla klientów w celu uzyskania dostępu do systemu. CCDB przechowuje tabele organizacjami, spacje, usług ról użytkownika i bardziej dla kontrolera chmury.
#### <a name="uaadb"></a>UAADB 
Bazy danych dla konta użytkownika i uwierzytelniania. Przechowuje uwierzytelnianie użytkowników, związane z danymi, na przykład szyfrowane nazwy użytkownika i hasła.

Domyślnie system lokalny bazy danych (MySQL) może służyć do. Dla wysokiej dostępności i skali, wykorzystać MySQL zarządzanych platformy Azure lub usługi PostgreSQL. Oto instrukcje dotyczące [włączenie Azure MySQL/PostgreSQL CCDB, UAADB i innych baz danych systemu z otwartych Foundry chmurze źródło](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Otwórz Service Broker
Broker usługi Azure zapewnia spójny interfejs do zarządzania dostępem aplikacji do usług platformy Azure. Nowy [Otwórz brokera usług dla projektu Azure](https://github.com/Azure/open-service-broker-azure) zapewnia jedno- i prosty sposób świadczenia usług do aplikacji w chmurze Foundry, OpenShift i Kubernetes. Zobacz [Azure Otwórz brokera usług dla kafelka PCF](https://network.pivotal.io/products/azure-open-service-broker-pcf/) instrukcje wdrożenia na PCF.

## <a name="6-metrics-and-logging"></a>6. Rejestrowanie i metryki
Końcówkę Analiza dzienników Azure to składnik Foundry chmury, który przesyła dalej metryki z [Foundry chmury loggregator pilnym](https://docs.cloudfoundry.org/loggregator/architecture.html) do [Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/). Z końcówkę można zbierać, wyświetlanie i analizowanie metryki sieci CF w systemie kondycję i wydajność w wielu wdrożeń.
Kliknij przycisk [tutaj](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) Aby dowiedzieć się, jak wdrożyć końcówkę Analiza dzienników Azure zarówno typu Open Source, jak i środowiska kluczową Foundry chmury, a następnie uzyskać dostęp do danych z poziomu konsoli Analiza dzienników Azure OMS. 
> [!NOTE]
> PCF 2.0 BOSH kondycji metryki dla maszyn wirtualnych są przekazywane do pilnym Loggregator domyślnie i są zintegrowane z konsoli Analiza dzienników Azure OMS.

## <a name="7-cost-saving"></a>7. Obniżenie kosztów
### <a name="cost-saving-for-devtest-environments"></a>Oszczędność dla środowisk: tworzenie i testowanie
#### <a name="b-series-"></a>Seria B: *
Gdy F i wirtualna D serii często były zalecane w środowisku produkcyjnym kluczową Foundry chmury nowy "burstable" [serii B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) oferuje nowe opcje. Maszyny wirtualne burstable B serii idealnie nadają się do obciążeń, które nie muszą pełną wydajność procesora, takich jak serwery sieci web, małych baz danych i programowania i testowania środowisk. Te obciążenia mają zwykle burstable wymagania. $0,012 na godzinę (B1) w porównaniu do $0,05 na godzinę (F1), zobacz pełną listę [rozmiarów maszyn wirtualnych](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes-general) i [ceny](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) szczegółowe informacje. 
#### <a name="managed-standard-disk"></a>Dysków zarządzanych w warstwie standardowa: 
Dyski Premium zostały zalecane niezawodnej wydajności w środowisku produkcyjnym.  Z [zarządzane dysku](https://azure.microsoft.com/services/managed-disks/), standardowy magazyn może również udostępniać podobne niezawodności, wydajności różnych. Dla obciążenia, który nie jest zależne od wydajności, takich jak tworzenie/testowanie oprogramowania lub niekrytyczne środowiska zarządzanego dyski standardowe mają alternatywnych opcji z niższym kosztem.  
### <a name="cost-saving-in-general"></a>Ogólnie rzecz biorąc obniżenie kosztów 
#### <a name="significant-vm-cost-saving-with-reserved-instances"></a>Wirtualna znaczących oszczędność wystąpieniami zastrzeżone: 
Obecnie wszystkie CF maszyny wirtualne są rozliczane za pomocą cenach "na żądanie", mimo że tych środowisk zwykle Pozostań na górę nieskończoność. Można teraz wydajność maszyny Wirtualnej rezerwowa termin, 1 lub rok 3 oraz uzyskać rabaty 45 65%. Rabaty są stosowane w systemie rozliczeń, bez żadnych zmian w danym środowisku. Aby uzyskać więcej informacji, zobacz [jak zastrzeżone działa wystąpień](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Zarządzane Premium dysku o rozmiarze mniejszym: 
Zarządzane dyski obsługi mniejsze rozmiary dysku, na przykład P4(32 GB) i P6(64 GB) dla premium i standardowa dysków. Jeśli masz obciążeń małymi można zapisać koszt podczas migracji z dysków premium standardowych dysków zarządzanych w warstwie premium.
#### <a name="utilizing-azure-first-party-services"></a>Przy użyciu usług Azure firm pierwszy: 
Korzystanie z platformy Azure pierwszej strony usługi obniży długoterminowego podawania koszt oprócz wysokiej dostępności i niezawodności wspomnianego powyżej sekcje. 

Kluczową uruchomiła [mały wpływ Wstaw](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) dla klientów PCF składniki wspólnie znajdują się w 4 uruchomionych maszyn wirtualnych, do 2500 wystąpień aplikacji. Wersja próbna jest teraz dostępna za pośrednictwem [Azure rynek](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Następne kroki
Funkcje integracji Azure najpierw są dostępne z [Otwórz źródłowej chmurze Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), zanim będzie dostępna na kluczową Foundry chmury. Funkcje oznaczone * nadal nie są dostępne za pośrednictwem PCF. Chmura Foundry Integracja z usługą Azure stosu nie jest albo omówione w tym dokumencie.
Do obsługi PCF od funkcji oznaczony z *, lub Foundry chmury Integracja z usługą Azure stosu, skontaktuj się z przedstawicielem firmy Microsoft i Pivotal najnowszy stan. 

