---
title: Często zadawane pytania dotyczące usługi Azure Red Hat OpenShift
description: Poniżej przedstawiono odpowiedzi na często zadawane pytania dotyczące programu Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619498"
---
# <a name="azure-red-hat-openshift-faq"></a>Często zadawane pytania dotyczące zmiany przesuwu typu OpenShift red hat

Ten artykuł dotyczy często zadawanych pytań dotyczących programu Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Które regiony platformy Azure są obsługiwane?

Zobacz [Obsługiwane zasoby](supported-resources.md#azure-regions) dla listy regionów globalnych, w których jest obsługiwana usługa Azure Red Hat OpenShift.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Czy można wdrożyć klaster w istniejącej sieci wirtualnej?

Nie. Ale można połączyć klastra OpenShift Red Hat platformy Azure do istniejącej sieci wirtualnej za pośrednictwem komunikacji równorzędnej. Aby uzyskać szczegółowe informacje, zobacz [Łączenie sieci wirtualnej klastra z istniejącą siecią wirtualną.](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)

## <a name="what-cluster-operations-are-available"></a>Jakie operacje klastra są dostępne?

Można skalować tylko w górę lub w dół liczbę węzłów obliczeniowych. Żadne inne modyfikacje nie `Microsoft.ContainerService/openShiftManagedClusters` są dozwolone do zasobu po utworzeniu. Maksymalna liczba węzłów obliczeniowych jest ograniczona do 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Jakich rozmiarów maszyn wirtualnych można używać?

Zobacz [rozmiary maszyn wirtualnych Azure Red Hat OpenShift,](supported-resources.md#virtual-machine-sizes) aby uzyskać listę rozmiarów maszyn wirtualnych, których można używać z klastrem OpenShift usługi Azure Red Hat.

## <a name="is-data-on-my-cluster-encrypted"></a>Czy dane w klastrze są szyfrowane?

Domyślnie istnieje szyfrowanie w spoczynku. Platforma Usługi Azure Storage automatycznie szyfruje dane przed ich utrwalaniem i odszyfrowuje dane przed pobraniem. Szczegółowe informacje [można znaleźć w części Szyfrowanie usługi Azure Storage, aby](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) uzyskać szczegółowe informacje.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Czy mogę używać Prometeusza/Grawany do monitorowania aplikacji?

Tak, prometeusz można wdrażać w obszarze nazw i monitorować aplikacje w obszarze nazw.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Czy mogę używać prometeusza/grawany do monitorowania metryk związanych ze zdrowiem i pojemnością klastra?

Nie, nie w chwili obecnej.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Czy rejestr platformy Docker jest dostępny zewnętrznie, dzięki czemu można używać narzędzi, takich jak Jenkins?

Rejestr platformy Docker `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` jest dostępny w yd. Można również użyć [usługi Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Czy sieć między przestrzeniami nazw jest obsługiwana?

Klienci i poszczególni administratorzy projektu mogą dostosowywać sieci między obszarami `NetworkPolicy` nazw (w tym odmawiać) na podstawie każdego projektu przy użyciu obiektów.

## <a name="can-an-admin-manage-users-and-quotas"></a>Czy administrator może zarządzać użytkownikami i przydziałami?

Tak. Administrator usługi Azure Red Hat OpenShift może zarządzać użytkownikami i przydziałami oprócz uzyskiwania dostępu do wszystkich projektów utworzonych przez użytkowników.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Czy mogę ograniczyć klaster tylko do niektórych użytkowników usługi Azure AD?

Tak. Użytkownik usługi Azure AD może ograniczyć logowanie się do klastra przez skonfigurowanie aplikacji usługi Azure AD. Aby uzyskać szczegółowe informacje, zobacz [Jak: Ograniczanie aplikacji do zestawu użytkowników](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>Czy mogę ograniczyć użytkownikom możliwość tworzenia projektów?

Tak. Zaloguj się do klastra jako administrator OpenShift usługi Azure Red Hat i wykonaj to polecenie:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Aby uzyskać więcej informacji, zobacz dokumentację OpenShift dotyczącą [wyłączania samoobsługowego inicjowania obsługi administracyjnej](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning).

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Czy klaster może mieć węzły obliczeniowe w wielu regionach platformy Azure?

Nie. Wszystkie węzły w klastrze Azure Red Hat OpenShift muszą pochodzić z tego samego regionu platformy Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Czy węzły główne i infrastruktury są odejmowane, tak jak są z usługą Azure Kubernetes Service (AKS)?

Nie. Wszystkie zasoby, w tym wzorzec klastra, są uruchamiane w ramach subskrypcji klienta. Te typy zasobów są umieszczane w grupie zasobów tylko do odczytu.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Czy open service broker dla platformy Azure (OSBA) obsługiwane?

Tak. OSBA można używać z azure red hat OpenShift. Aby uzyskać więcej informacji, zobacz [Open Service Broker for Azure.](https://github.com/Azure/open-service-broker-azure#openshift-project-template)

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Próbuję zajrzeć do sieci wirtualnej w `Failed to get vnet CIDR` innej subskrypcji, ale pojawia się błąd.

W ramach subskrypcji z siecią wirtualną `Microsoft.ContainerService` należy zarejestrować dostawcę`az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Co to jest proces konserwacji usługi Azure Red Hat OpenShift (ARO)?

Istnieją trzy rodzaje konserwacji dla ARO: uaktualnienia, tworzenie kopii zapasowych i przywracanie danych itp.

+ Aktualizacje obejmują aktualizacje oprogramowania i CVEs. Skerwowanie CVE występuje `yum update` podczas uruchamiania przez uruchomienie i zapewnia natychmiastowe ograniczenie.  Równolegle zostanie utworzona nowa kompilacja obrazu dla przyszłych utworzonych klastra.

+ Tworzenie kopii zapasowych i zarządzanie danymi itp. Jeśli baza danych etcd jest przywracana z kopii zapasowej, nastąpi przestój. Możemy kopii zapasowej etcd co godzinę i zachować ostatnie 6 godzin kopii zapasowych.

+ Konserwacja inicjowana przez dostawcę chmury obejmuje awarie sieci, magazynu i regionalne. Konserwacja jest zależna od dostawcy chmury i opiera się na aktualizacjach dostarczonych przez dostawcę.

## <a name="what-is-the-general-upgrade-process"></a>Co to jest ogólny proces uaktualniania?

Uruchamianie uaktualnienia powinno być bezpiecznym procesem do uruchomienia i nie powinno zakłócać usług klastrowania. SRE może wyzwolić proces uaktualniania, gdy dostępne są nowe wersje lub CVEs są wybitne.

Dostępne aktualizacje są testowane w środowisku scenicznym, a następnie stosowane do klastrów produkcyjnych. Po zastosowaniu nowy węzeł jest tymczasowo dodawany, a węzły są aktualizowane w sposób obrotowy, tak aby zasobniki obsługiwać liczniki replik. Przestrzeganie najlepszych rozwiązań pomaga zapewnić minimalne lub bez przestojów.

W zależności od ważności oczekującego uaktualnienia lub aktualizacji proces może się różnić tym, że aktualizacje mogą być stosowane szybko w celu ograniczenia narażenia usługi na CVE. Nowy obraz zostanie zbudowany asynchronicznie, przetestowany i wdrożony jako uaktualnienie klastra. Poza tym nie ma różnicy między konserwacją awaryjną a planowaną. Planowana konserwacja nie jest wstępnie zaplanowana przez klienta.

Powiadomienia mogą być wysyłane za pośrednictwem ICM i e-mail, jeśli wymagana jest komunikacja z klientem.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>A co z oknami awaryjnymi a planowanymi oknami konserwacyjnymi?

Nie rozróżniamy tych dwóch rodzajów konserwacji. Nasze zespoły są dostępne 24/7/365 i nie korzystają z tradycyjnych zaplanowanych okien konserwacyjnych "poza godzinami pracy".

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>W jaki sposób zostanie zaktualizowany system operacyjny hosta i oprogramowanie OpenShift?

System operacyjny hosta i oprogramowanie OpenShift są aktualizowane przez nasz ogólny proces aktualizacji i tworzenia obrazu.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Co to jest proces ponownego uruchomienia zaktualizowanego węzła?

To powinno być obsługiwane jako część uaktualnienia.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Czy dane przechowywane w etcd zaszyfrowane na ARO?

Nie jest szyfrowany na poziomie etcd. Opcja włączenia jest obecnie nieobsługiwała. OpenShift obsługuje tę funkcję, ale wysiłki inżynierskie są wymagane, aby zrobić to na mapie drogowej. Dane są szyfrowane na poziomie dysku. Więcej informacji można znaleźć [w stronie na stronie Datastore Layer.](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Czy dzienniki podstawowych maszyn wirtualnych mogą być przesyłane strumieniowo do systemu analizy dziennika klienta?

Syslog, dzienniki platformy docker, dziennik i dmesg są obsługiwane przez usługę zarządzaną i nie są narażone na klientów.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Jak klient może uzyskać dostęp do metryk, takich jak PROCESOR/pamięć na poziomie węzła, aby podjąć działania w celu skalowania, problemów z debugowaniem itp. Nie wydaje się `kubectl top` działać na klastrze ARO.

Klienci mogą uzyskać dostęp do metryk procesora CPU/pamięci na poziomie węzła za pomocą polecenia `oc adm top nodes` lub `kubectl top nodes` za pomocą roli klastra klienta-administratora.  Klienci mogą również uzyskać dostęp do `pods` metryk `oc adm top pods` procesora/pamięci za pomocą polecenia lub`kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Co to jest domyślna konfiguracja harmonogramu zasobników dla ARO?

ARO używa domyślnego harmonogramu, który jest dostarczany w trybie OpenShift. Istnieje kilka dodatkowych mechanizmów, które nie są obsługiwane w ARO. Aby uzyskać więcej informacji, zapoznaj się z [domyślną dokumentacją harmonogramu](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) i [harmonogramem głównym.](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json)

Planowanie zaawansowane/niestandardowe jest obecnie nieobsługiwane. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją planowania.](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html)

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Jeśli skalowanie w górę wdrożenia, jak domeny błędów platformy Azure mapowania do lokalizacji zasobników, aby upewnić się, że wszystkie zasobniki dla usługi nie zostanie wykluczony przez błąd w jednej domenie błędów?

Domyślnie istnieje pięć domen błędów podczas korzystania z zestawów skalowania maszyny wirtualnej na platformie Azure. Każde wystąpienie maszyny wirtualnej w zestawie skalowania zostanie umieszczone w jednej z tych domen błędów. Dzięki temu aplikacje wdrożone w węzłach obliczeniowych w klastrze zostaną umieszczone w oddzielnych domenach błędów.

Aby uzyskać więcej informacji, zobacz [Wybieranie odpowiedniej liczby domen błędów dla zestawu skalowania maszyny wirtualnej.](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains)

## <a name="is-there-a-way-to-manage-pod-placement"></a>Czy istnieje sposób zarządzania umieszczeniem zasobnika?

Klienci mają możliwość uzyskania węzłów i wyświetlania etykiet jako administrator klienta.  Zapewni to sposób na kierowanie dowolnej maszyny Wirtualnej w zestawie skalowania.

Należy zachować ostrożność podczas używania określonych etykiet:

- Nie wolno używać nazwy hosta. Nazwa hosta jest często obracana z aktualizacjami i aktualizacjami i jest gwarantowana do zmiany.

- Jeśli klient ma żądanie określonych etykiet lub strategii wdrażania, można to osiągnąć, ale wymagałoby to wysiłków inżynierskich i nie jest obecnie obsługiwane.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Jaka jest maksymalna liczba zasobników w klastrze ARO?Jaka jest maksymalna liczba zasobników na węzeł w ARO?

 Usługa Azure Red Hat OpenShift 3.11 ma limit 50-pod na węzeł z [ARO o 20-obliczeniowym limicie węzła](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available), dzięki czemu ogranicza maksymalną liczbę zasobników obsługiwanych w klastrze ARO do 50 * 20 = 1000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Czy możemy określić zakresy adresów IP do wdrożenia na prywatnej sieci wirtualnej, unikając starć z innymi firmowymi wirtualnymi firmami po równorzędnym?

Usługa Azure Red Hat OpenShift obsługuje komunikację równorzędną sieci wirtualnej i umożliwia klientowi zapewnienie sieci wirtualnej do komunikacji równorzędnej i narzędzia wirtualnego CIDR, w którym będzie działać sieć OpenShift.

Vnet utworzony przez ARO będą chronione i nie zaakceptuje zmian konfiguracji. Sieć wirtualna, która jest równorzędna jest kontrolowana przez klienta i znajduje się w ich subskrypcji.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Czy klaster znajduje się w subskrypcji klienta? 

Aplikacja zarządzana platformy Azure mieszka w zablokowanej grupie zasobów z subskrypcją klienta. Klient może wyświetlać obiekty w tym RG, ale nie modyfikować.

## <a name="is-the-sdn-module-configurable"></a>Czy moduł SDN jest konfigurowany?

SDN jest openshift-ovs-networkpolicy i nie można konfigurować.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Które prawa systemu UNIX (w UaaS) są dostępne dla węzłów Masters/Infra/App?

Nie dotyczy tej oferty. Dostęp do węzła jest zabroniony.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Jakie prawa OCP mamy? Administrator klastra? Project-admin?

Aby uzyskać szczegółowe informacje, zobacz [omówienie administracji klastrem](https://docs.openshift.com/aro/admin_guide/index.html)Azure Red Hat OpenShift .

## <a name="which-kind-of-federation-with-ldap"></a>Jaki rodzaj federacji z LDAP?

Można to osiągnąć za pomocą integracji usługi Azure AD. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Czy w ARO jest jakiś element współdzielony z innymi klientami? A może wszystko jest niezależne?

Każdy klaster OpenShift usługi Azure Red Hat jest dedykowany danemu klientowi i działa w ramach subskrypcji klienta. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Czy możemy wybrać dowolne trwałe rozwiązanie pamięci masowej, takie jak OCS? 

Dostępne są dwie klasy magazynu do wyboru: Azure Disk i Azure File.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>W jaki sposób aktualizowany jest klaster (w tym główne firmy i osoby niepełnoletnie z powodu luk w zabezpieczeniach)?

Zobacz [Co to jest ogólny proces uaktualniania?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Jaki moduł równoważenia obciążenia platformy Azure jest używany przez ARO?Czy jest to standard czy podstawowy i czy można go skonfigurować?

ARO używa standardowego modułu równoważenia obciążenia platformy Azure i nie można go konfigurować.

## <a name="can-aro-use-netapp-based-storage"></a>Czy ARO może korzystać z magazynu opartego na netapp?

Obecnie jedynymi obsługiwanymi opcjami magazynu są klasy usługi Azure Disk i Azure File Storage. 


