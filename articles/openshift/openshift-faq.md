---
title: Często zadawane pytania dotyczące usługi Azure Red Hat OpenShift | Microsoft Docs
description: Poniżej znajdują się odpowiedzi na często zadawane pytania dotyczące Microsoft Azure Red Hat OpenShift
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 8f7349310f72c8cccc7b1906239ece3038dd7861
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249213"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift — często zadawane pytania

W tym artykule opisano często zadawane pytania dotyczące Microsoft Azure Red Hat OpenShift.

## <a name="how-do-i-get-started"></a>Jak rozpocząć pracę?

Przed rozpoczęciem korzystania z usługi Azure Red Hat OpenShift należy zakupić co najmniej 4 zastrzeżone aplikacje usługi Azure Red Hat OpenShift.

Jeśli jesteś klientem platformy Azure,[Kup wystąpienia zarezerwowane w systemie Azure Red Hat OpenShift](https://aka.ms/openshift/buy) za pomocą Azure Portal. Po zakupieniu subskrypcja zostanie uaktywniona w ciągu 24 godzin, po upływie której będzie możliwe udostępnienie klastrów.

Jeśli nie jesteś klientem platformy Azure, [skontaktuj się ze sprzedażą](https://aka.ms/openshift/contact-sales) i wypełnij formularz sprzedaży w dolnej części strony, aby rozpocząć proces.

Aby uzyskać więcej informacji, zobacz [stronę z cennikiem usługi Azure Red Hat OpenShift](https://aka.ms/openshift/pricing) .

## <a name="which-azure-regions-are-supported"></a>Które regiony platformy Azure są obsługiwane?

Zobacz [obsługiwane zasoby](supported-resources.md#azure-regions) , aby wyświetlić listę regionów globalnych, w których jest obsługiwana usługa Azure Red Hat OpenShift.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Czy mogę wdrożyć klaster w istniejącej sieci wirtualnej?

Nie. Można jednak połączyć klaster usługi Azure Red Hat OpenShift z istniejącą siecią wirtualną za pośrednictwem komunikacji równorzędnej. Aby uzyskać szczegółowe informacje [, zobacz Łączenie sieci wirtualnej klastra z istniejącą siecią wirtualną](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) .

## <a name="what-cluster-operations-are-available"></a>Jakie operacje klastra są dostępne?

Można skalować w górę lub w dół liczbę węzłów obliczeniowych. Żadne inne modyfikacje nie są dozwolone dla zasobu `Microsoft.ContainerService/openShiftManagedClusters` po utworzeniu. Maksymalna liczba węzłów obliczeniowych jest ograniczona do 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Jakie rozmiary maszyn wirtualnych można użyć?

Zobacz [rozmiary maszyn wirtualnych usługi Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) , aby uzyskać listę rozmiarów maszyn wirtualnych, których można użyć w przypadku klastra Red Hat OpenShift platformy Azure.

## <a name="is-data-on-my-cluster-encrypted"></a>Czy dane w moim klastrze są szyfrowane?

Domyślnie istnieje szyfrowanie w stanie spoczynku. Platforma Azure Storage automatycznie szyfruje dane przed utrwaleniem i odszyfrowuje dane przed pobraniem. Aby uzyskać szczegółowe informacje, zobacz temat [Azure szyfrowanie usługi Storage dla danych przechowywanych w usłudze REST](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) .

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Czy mogę używać Prometheus/Grafana do monitorowania moich aplikacji?

Tak, możesz wdrożyć Prometheus w przestrzeni nazw i monitorować aplikacje w przestrzeni nazw.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Czy można używać Prometheus/Grafana do monitorowania metryk związanych z kondycją i pojemnością klastra?

Nie, nie w bieżącym czasie.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Czy rejestr platformy Docker jest dostępny zewnętrznie, aby można było używać narzędzi takich jak Jenkins?

Rejestr platformy Docker jest dostępny z `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` jednak nie podano silnej gwarancji trwałości magazynu. Możesz również użyć [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Czy obsługa sieci między przestrzenią nazw jest obsługiwana?

Administratorzy klientów i poszczególnych projektów mogą dostosowywać sieci obejmujące wiele przestrzeni nazw (w tym odrzucanie ich) dla poszczególnych projektów przy użyciu obiektów `NetworkPolicy`.

## <a name="can-an-admin-manage-users-and-quotas"></a>Czy administrator może zarządzać użytkownikami i przydziałami?

Tak. Administrator Red Hat OpenShift systemu Azure może zarządzać użytkownikami i przydziałami, a także uzyskiwać dostęp do wszystkich projektów utworzonych przez użytkownika.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Czy mogę ograniczyć klaster tylko do określonych użytkowników usługi Azure AD?

Tak. Można ograniczyć, którzy użytkownicy usługi Azure AD mogą logować się do klastra przez skonfigurowanie aplikacji usługi Azure AD. Aby uzyskać szczegółowe informacje, zobacz [How to: ograniczanie aplikacji do zestawu użytkowników](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Czy klaster może zawierać węzły obliczeniowe w wielu regionach świadczenia usługi Azure?

Nie. Wszystkie węzły w klastrze Red Hat OpenShift platformy Azure muszą pochodzić z tego samego regionu świadczenia usługi Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Czy węzły główne i infrastruktury są abstrakcją, ponieważ są one w usłudze Azure Kubernetes Service (AKS)?

Nie. Wszystkie zasoby, w tym główny klaster, są uruchamiane w ramach subskrypcji klienta. Te typy zasobów są umieszczane w grupie zasobów tylko do odczytu.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Czy jest otwarte Service Broker dla systemu Azure (OSBA)?

Tak. Możesz użyć OSBA z platformą Azure Red Hat OpenShift. Aby uzyskać więcej informacji, zobacz [otwórz Service Broker dla platformy Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) .

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Próbuję nawiązać połączenie równorzędne w sieci wirtualnej w innej subskrypcji, ale pobieranie `Failed to get vnet CIDR` błędu.

Upewnij się, że w subskrypcji z siecią wirtualną zarejestrowano dostawcę `Microsoft.ContainerService` z `az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Co to jest proces konserwacji w systemie Red Hat OpenShift (ARO) systemu Azure?

Istnieją trzy typy czynności konserwacyjnych: uaktualnienia, tworzenie kopii zapasowych i przywracanie danych etcd oraz Konserwacja inicjowana przez dostawcę chmury.

+ Uaktualnienia obejmują uaktualnienia oprogramowania i CVEs. Korygowanie CVE występuje przy uruchamianiu, uruchamiając `yum update` i umożliwia natychmiastowe ograniczenie problemu.  Równolegle zostanie utworzona nowa kompilacja obrazu dla przyszłego tworzenia klastra.

+ Tworzenie kopii zapasowych danych etcd i zarządzanie nimi to zautomatyzowany proces, który może wymagać przestoju klastra w zależności od akcji. Jeśli baza danych etcd jest przywracana z kopii zapasowej, będzie przestoje. Wykonujemy kopie zapasowe etcd co godzinę i zachowuję ostatnie 6 godzin wykonywania kopii zapasowych.

+ Konserwacja inicjowana przez dostawcę chmury obejmuje awarię sieci, magazynu i regionalne. Konserwacja jest zależna od dostawcy chmury i opiera się na aktualizacjach dostarczonych przez dostawcę.

## <a name="what-is-the-general-upgrade-process"></a>Co to jest ogólny proces uaktualniania?

Uruchomienie uaktualnienia powinno być procesem bezpiecznym do uruchomienia i nie powinno zakłócać usług klastra. SRE może wyzwolić proces uaktualniania, gdy dostępne są nowe wersje lub CVEs.

Aktualizacje dostępne są testowane w środowisku etapowym, a następnie stosowane do klastrów produkcyjnych. Po zastosowaniu nowy węzeł jest tymczasowo dodawany, a węzły są aktualizowane w sposób rotacyjny, dzięki czemu w celu utrzymania liczby replik. Poniższe najlepsze rozwiązania ułatwiają zapewnienie nieprzerwanego przestoju.

W zależności od wagi oczekujących uaktualnień lub aktualizacji proces może się różnić w tym, że aktualizacje mogą być stosowane szybko, aby uniknąć zagrożenia dla danej usługi. Nowy obraz zostanie skompilowany asynchronicznie, przetestowany i przeprowadzony jako uaktualnienie klastra. Oprócz tego nie istnieje różnica między sytuacją awaryjną i planowaną konserwacją. Planowana konserwacja nie jest zaplanowana na klienta.

Powiadomienia mogą być wysyłane za pośrednictwem funkcji ICM i poczty e-mail, jeśli wymagana jest komunikacja z klientem.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Co z awaryjnymi oknami konserwacyjnymi a planowanymi konserwacjami?

Nie rozróżniamy między dwoma rodzajami konserwacji. Nasze zespoły są dostępne 24/7/365 i nie należy używać tradycyjnych zaplanowanych okien obsługi.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Jak będzie aktualizowany system operacyjny i oprogramowanie OpenShift?

System operacyjny hosta i oprogramowanie OpenShift są aktualizowane przez ogólny proces uaktualniania i kompilowania obrazu.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Co to jest proces ponownego uruchomienia zaktualizowanego węzła?

Ta wartość powinna być obsługiwana w ramach uaktualnienia.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Czy dane są przechowywane w etcd zaszyfrowane w przypadku wypróbowania?

Nie jest on szyfrowany na poziomie etcd. Opcja włączenia tej opcji nie jest obecnie obsługiwana. OpenShift obsługuje tę funkcję, ale działania inżynieryjne są wymagane do przetworzenia ich na mapie drogowej. Dane są szyfrowane na poziomie dysku. Aby uzyskać więcej informacji, zapoznaj się z artykułem [szyfrowanie danych w warstwie magazynu](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) .

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Czy dzienniki bazowych maszyn wirtualnych są przesyłane strumieniowo do systemu analizy dzienników klienta?

Dziennik systemowy, dzienniki platformy Docker, dziennik i dmesg są obsługiwane przez usługę zarządzaną i nie są dostępne dla klientów.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Jak klient może uzyskać dostęp do metryk, takich jak procesor CPU/pamięć na poziomie węzła, aby podjąć działania w celu skalowania, debugowania problemów itp. Nie można uruchomić `kubectl top` w klastrze ARO.

`kubectl top` nie jest dostępna w Red Hat OpenShift. Wymaga ona źródła metryk zapasowych, Heapster (przestarzałe) lub metryk — serwera (inkubacji lub alfa), nie są uwzględniane w stosie monitorowania OpenShift.

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Co to jest domyślna konfiguracja usługi Scheduler pod kątem wypróbowania?

Funkcja ARO używa domyślnego harmonogramu, który jest dostarczany w OpenShift. Istnieje kilka dodatkowych mechanizmów, które nie są obsługiwane w przypadku tej procedury. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją domyślnego harmonogramu](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) i [dokumentacją usługi Master Scheduler](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json) .

Planowanie zaawansowane/niestandardowe nie jest obecnie obsługiwane. Więcej informacji można znaleźć w [dokumentacji planowania](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) .

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>W przypadku skalowania w górę wdrożenia, jak domeny błędów platformy Azure są mapowane na umieszczenie pod, aby upewnić się, że wszystkie zasobniki usługi nie zostaną oddzielone przez awarię w jednej domenie błędów?

W przypadku korzystania z zestawów skalowania maszyn wirtualnych na platformie Azure domyślnie są używane pięć domen błędów. Każde wystąpienie maszyny wirtualnej w zestawie skalowania zostanie umieszczone w jednej z tych domen błędów. Dzięki temu aplikacje wdrożone w węzłach obliczeniowych w klastrze będą znajdować się w osobnych domenach błędów.

Aby uzyskać więcej informacji, zapoznaj się z tematem [Wybieranie odpowiedniej liczby domen błędów dla zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) .

## <a name="is-there-a-way-to-manage-pod-placement"></a>Czy istnieje sposób, aby zarządzać umieszczaniem pod?

W przypadku oczekującej aktualizacji klienta klient będzie miał możliwość pobrania węzłów i etykiet.  Pozwoli to na docelową maszynę wirtualną w zestawie skalowania.

Należy zachować ostrożność w przypadku używania określonych etykiet:

- Nazwa hosta nie może być używana. Nazwa hosta jest często obracana z uaktualnieniami i aktualizacjami i jest zagwarantowana do zmiany.

- Jeśli klient ma żądanie dotyczące określonych etykiet lub strategii wdrażania, można to osiągnąć, ale wymagałoby to wysiłków inżynieryjnych i nie jest to obecnie obsługiwane.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Jaka jest maksymalna liczba zasobników w klastrze ARO?  Jaka jest maksymalna liczba zasobników na węzeł w wyliczeniu?

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją [OpenShift](https://docs.openshift.com/container-platform/3.11/scaling_performance/cluster_limits.html#scaling-performance-current-cluster-limits) . Red Hat OpenShift 3,11 ma limit 250-poniżej/węzłów, a wartość [ARO ma limit równy 20-obliczeniowy](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available), dzięki czemu Maksymalna liczba numerów na sekundę obsługiwanych w klastrze ARO jest równa 250 * 20 = 5000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Czy można określić zakresy adresów IP do wdrożenia w prywatnej sieci wirtualnej, unikając konfliktów z innymi firmowymi sieci wirtualnych po utworzeniu komunikacji równorzędnej?

System Red Hat OpenShift obsługuje wirtualne sieci równorzędne i umożliwia klientowi dostarczanie sieci wirtualnej do komunikacji równorzędnej oraz sieci wirtualnej CIDR, w której będzie działać sieć OpenShift.

Sieć wirtualna utworzona przy użyciu ARO zostanie zabezpieczona i nie będzie akceptować zmian konfiguracji. Sieć wirtualna, która jest podłączona do sieci równorzędnej, jest kontrolowana przez klienta i znajduje się w jego subskrypcji.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Czy klaster znajduje się w ramach subskrypcji klienta? 

Aplikacja zarządzana przez platformę Azure znajduje się w zablokowanej grupie zasobów z subskrypcją klienta. Klient może przeglądać obiekty w tym RG, ale nie modyfikować.

## <a name="is-the-sdn-module-configurable"></a>Czy moduł SDN jest konfigurowalny?

SDN jest OpenShift-OVS-networkpolicy i nie można go konfigurować.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Które prawa systemu UNIX (w IaaS) są dostępne dla węzłów wzorców/infrastruktury/aplikacji?

Nie dotyczy tej oferty. Dostęp do węzła jest zabroniony.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Jakie są prawa OCP? Klaster-administrator? Projekt — administrator?

Aby uzyskać szczegółowe informacje, zobacz [Omówienie administrowania klastrem](https://docs.openshift.com/aro/admin_guide/index.html)w systemie Red Hat OpenShift.

## <a name="which-kind-of-federation-with-ldap"></a>Jakiego rodzaju Federacji z LDAP?

Można to osiągnąć za pomocą integracji z usługą Azure AD. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Czy istnieją jakieś elementy, które są udostępniane innym klientom? Czy wszystko jest niezależne?

Każdy klaster usługi Azure Red Hat OpenShift jest przeznaczony dla danego klienta i przebywa w ramach subskrypcji klienta. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Czy można wybrać dowolne trwałe rozwiązanie magazynu, takie jak OCS? 

Do wyboru są dostępne dwie klasy magazynu: dysk platformy Azure i plik platformy Azure.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Jak jest aktualizowany klaster (w tym główne i pomocnicze z powodu luk w zabezpieczeniach)?

Zobacz [, co to jest ogólny proces uaktualniania?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Jakiego modułu równoważenia obciążenia platformy Azure używa firma ARO?  Czy jest to wersja standardowa czy podstawowa i czy jest konfigurowana?

Polecenie ARO używa standardowej Azure Load Balancer i nie można go konfigurować.

## <a name="can-aro-use-netapp-based-storage"></a>Czy można użyć magazynu opartego na NetApp?

Obecnie obsługiwane opcje magazynu to Azure Disk i Azure File Storage. 


