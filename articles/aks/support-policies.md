---
title: Zasady pomocy technicznej dla usługi Azure Kubernetes Service (AKS)
description: Dowiedz się więcej o zasadach pomocy technicznej usługi Azure Kubernetes (AKS), współużytkowania i funkcji, które znajdują się w wersji zapoznawczej (lub alfa lub beta).
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: a5d90106a85a61cbf499c4c08130392b922a45f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593584"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Zasady pomocy technicznej dla usługi Azure Kubernetes

Ten artykuł zawiera szczegółowe informacje na temat zasad pomocy technicznej i ograniczeń usługi Azure Kubernetes Service (AKS). W tym artykule opisano również zarządzanie węzłami procesu roboczego, składniki płaszczyzny zarządzanego sterowania, składniki open source innych firm oraz zarządzanie zabezpieczeniami lub poprawkami.

## <a name="service-updates-and-releases"></a>Aktualizacje i wydania usług

* Aby uzyskać informacje o wersji, zobacz [informacje o wersji AKS](https://github.com/Azure/AKS/releases).
* Aby uzyskać informacje o funkcjach w wersji zapoznawczej, zobacz [Funkcje w wersji zapoznawczej usługi AKS i powiązane projekty](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Zarządzane funkcje w AKS

Infrastruktura bazowa jako składniki chmury usługi (IaaS), takie jak składniki obliczeniowe lub sieciowe, zapewniają użytkownikom dostęp do formantów niskiego poziomu i opcji dostosowywania. Z drugiej strony usługa AKS zapewnia gotowe wdrożenie kubernetes, które zapewnia klientom wspólny zestaw konfiguracji i możliwości, których potrzebują. Klienci usługi AKS mają ograniczone opcje dostosowywania, wdrażania i innych. Ci klienci nie muszą się martwić o klastry kubernetes ani zarządzać nimi bezpośrednio.

Dzięki AKS klient otrzymuje w pełni zarządzaną *płaszczyznę sterowania.* Płaszczyzna sterowania zawiera wszystkie składniki i usługi, których klient musi obsługiwać i dostarczać klastry kubernetes użytkownikom końcowym. Wszystkie składniki kubernetes są obsługiwane i obsługiwane przez firmę Microsoft.

Firma Microsoft zarządza następującymi składnikami i monitoruje ich za pośrednictwem okienka sterowania:

* Serwery API Kubelet lub Kubernetes
* Etcd lub kompatybilny magazyn klucz-wartość, zapewniający jakość usług (QoS), skalowalność i środowisko wykonawcze
* Usługi DNS (na przykład kube-dns lub CoreDNS)
* Serwer proxy lub sieć Kubernetes

AKS nie jest całkowicie zarządzane rozwiązanie klastra. Niektóre składniki, takie jak węzły procesu roboczego, mają *wspólną odpowiedzialność,* gdzie użytkownicy muszą pomóc w utrzymaniu klastra AKS. Dane wejściowe użytkownika są wymagane, na przykład, aby zastosować poprawkę zabezpieczeń systemu operacyjnego węzła procesu roboczego.

Usługi są *zarządzane* w tym sensie, że firma Microsoft i zespół AKS wdraża, działa i jest odpowiedzialny za dostępność usługi i funkcje. Klienci nie mogą zmieniać tych zarządzanych składników. Firma Microsoft ogranicza dostosowywanie, aby zapewnić spójne i skalowalne środowisko użytkownika. Aby uzyskać w pełni konfigurowalne rozwiązanie, zobacz [AKS Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> Węzły procesu roboczego usługi AKS są wyświetlane w witrynie Azure portal jako zwykłe zasoby usługi Azure IaaS. Ale te maszyny wirtualne są wdrażane w niestandardowej\\grupie zasobów platformy Azure (poprzedzone MC *). Można zmienić węzły procesu roboczego AKS. Na przykład można użyć secure shell (SSH), aby zmienić węzły procesu roboczego AKS w sposób zmiany normalnych maszyn wirtualnych (nie można jednak zmienić obrazu podstawowego systemu operacyjnego, a zmiany mogą nie utrwalić się po aktualizacji lub ponownym uruchomieniu) i można dołączyć inne zasoby platformy Azure do usługi AKS węzłów procesu roboczego. Jednak po wprowadzeniu zmian *poza zarządzaniem pasmem i dostosowywaniem* klaster AKS może stać się nie do popartego. Unikaj zmieniania węzłów procesu roboczego, chyba że pomoc techniczna firmy Microsoft nakazuje wprowadzanie zmian.

Wystawianie nieobsługiwało operacji, jak zdefiniowano powyżej, takich jak poza pasmem unlocation wszystkich węzłów agenta, sprawia, że klaster nie jest obsługiwane. AKS zastrzega sobie prawo do archiwizowania samolotów kontrolnych, które zostały skonfigurowane z wytycznych pomocy technicznej przez dłuższy czas równy i dłuższy niż 30 dni. Usługa AKS przechowuje kopie zapasowe metadanych klastra etcd i może łatwo ponownie przydzielić klaster. Ta ponowna alokacja może być inicjowana przez dowolną operację PUT przywracając klaster do obsługi, taką jak uaktualnienie lub skalowanie do węzłów aktywnego agenta.

## <a name="shared-responsibility"></a>Wspólna odpowiedzialność

Podczas tworzenia klastra klient definiuje węzły procesu roboczego Kubernetes tworzone przez usługi AKS. Obciążenia klientów są wykonywane w tych węzłach. Klienci są właścicielami i mogą wyświetlać lub modyfikować węzły procesu roboczego.

Ponieważ węzły klastra klientów wykonują prywatny kod i przechowują poufne dane, pomoc techniczna firmy Microsoft może uzyskać do nich dostęp tylko w ograniczonym zakresie. Pomoc techniczna firmy Microsoft nie może zalogować się, wykonać poleceń ani wyświetlić dzienników dla tych węzłów bez wyraźnej zgody klienta lub pomocy.

Ponieważ węzły procesu roboczego są poufne, firma Microsoft dokłda wszelkich starań, aby ograniczyć zarządzanie w tle. W wielu przypadkach obciążenie będzie nadal działać, nawet jeśli węzły główne usługi Kubernetes, etcd i inne składniki zarządzane przez firmę Microsoft nie powiedzie się. Niedbale zmodyfikowane węzły procesu roboczego mogą powodować straty danych i obciążeń oraz mogą uniemożliwić nieobsługiwalne obsługi klastra.

## <a name="aks-support-coverage"></a>Zasięg pomocy technicznej AKS

Firma Microsoft zapewnia pomoc techniczną w zakresie następujących czynności:

* Łączność ze wszystkimi składnikami usługi Kubernetes, które zapewnia i obsługuje usługa Kubernetes, takimi jak serwer interfejsu API.
* Zarządzanie, czas pracy bez przestojów, QoS i operacje usług płaszczyzny sterowania Kubernetes (węzły główne Kubernetes, serwer INTERFEJSU API, etcd i kube-dns, na przykład).
* Itd. Obsługa obejmuje automatyczne, przejrzyste kopie zapasowe wszystkich danych etcd co 30 minut do planowania awarii i przywracania stanu klastra. Te kopie zapasowe nie są bezpośrednio dostępne dla klientów lub użytkowników. Zapewniają one niezawodność i spójność danych.
* Wszystkie punkty integracji w sterowniku dostawcy chmury platformy Azure dla usługi Kubernetes. Należą do nich integracje z innymi usługami platformy Azure, takimi jak moduły równoważenia obciążenia, woluminy trwałe lub sieci (Kubernetes i Azure CNI).
* Pytania lub problemy dotyczące dostosowywania składników płaszczyzny sterowania, takich jak serwer interfejsu API Kubernetes, etcd i kube-dns.
* Problemy z siecią, takie jak Azure CNI, kubenet lub inne problemy z dostępem do sieci i funkcjonalnością. Problemy mogą obejmować rozpoznawanie DNS, utratę pakietów, routing i tak dalej. Firma Microsoft obsługuje różne scenariusze sieci:
  * Kubenet (podstawowa) i zaawansowana sieć (Azure CNI) w klastrze i skojarzonych składnikach
  * Łączność z innymi usługami i aplikacjami platformy Azure
  * Sterowniki przychodzące i konfiguracje transferu danych przychodzących lub równoważenia obciążenia
  * Wydajność i opóźnienie sieci

Firma Microsoft nie zapewnia pomocy technicznej w zakresie następujących czynności:

* Pytania dotyczące sposobu korzystania z usługi Kubernetes. Na przykład pomoc techniczna firmy Microsoft nie udziela porad dotyczących tworzenia niestandardowych kontrolerów transferu danych przychodzących, używania obciążeń aplikacji ani stosowania pakietów lub narzędzi oprogramowania innych firm lub oprogramowania typu open source.
  > [!NOTE]
  > Pomoc techniczna firmy Microsoft może doradzić w zakresie funkcji klastra AKS, dostosowywania i dostrajania (na przykład problemów i procedur operacyjnych Kubernetes).
* Projekty typu open source innych firm, które nie są dostarczane jako część płaszczyzny sterowania usługi Kubernetes lub wdrażane przy klastrach AKS. Projekty te mogą obejmować Istio, Helm, Envoy lub inne.
  > [!NOTE]
  > Firma Microsoft może zapewnić najlepszą obsługę projektów typu open source innych firm, takich jak Helm i Kured. Jeśli narzędzie open source innej firmy integruje się z dostawcą chmury Platformy Azure firmy Kubernetes lub innymi błędami specyficznymi dla usługi AKS, firma Microsoft obsługuje przykłady i aplikacje z dokumentacji firmy Microsoft.
* Oprogramowanie zamknięte dla innych firm. To oprogramowanie może zawierać narzędzia do skanowania zabezpieczeń oraz urządzenia lub oprogramowanie sieciowe.
* Problemy z kompilacjami wielochmurowymi lub wielożeńskowymi. Na przykład firma Microsoft nie obsługuje problemów związanych z uruchamianiem sfederowanego rozwiązania dostawcy chmury wielopublicznego.
* Dostosowania sieci inne niż wymienione w [dokumentacji AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Firma Microsoft obsługuje problemy i błędy związane z sieciowymi grupami zabezpieczeń (NSG). Na przykład pomoc techniczna firmy Microsoft może odpowiedzieć na pytania dotyczące niepowodzenia aktualizacji grupy nsg lub nieoczekiwanego zachowania grupy nsg lub modułu równoważenia obciążenia.

## <a name="aks-support-coverage-for-worker-nodes"></a>Pokrycie obsługi AKS dla węzłów procesu roboczego

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Obowiązki firmy Microsoft dotyczące węzłów procesu roboczego AKS

Firma Microsoft i klienci współużytkuje odpowiedzialność za węzły procesu roboczego usługi Kubernetes, w których:

* Podstawowy obraz systemu operacyjnego ma wymagane dodatki (takie jak monitorowanie i agentów sieciowych).
* Węzły procesu roboczego automatycznie odbierają poprawki systemu operacyjnego.
* Problemy ze składnikami płaszczyzny sterowania Kubernetes, które są uruchamiane w węzłach procesu roboczego, są automatycznie korygowane. Składniki są następujące:
  * Kube-proxy
  * Tunele sieciowe zapewniające ścieżki komunikacji do składników głównych usługi Kubernetes
  * Kubelet
  * Demon docker lub Moby

> [!NOTE]
> W węźle procesu roboczego, jeśli składnik płaszczyzny sterowania nie działa, zespół usługi AKS może wymagać ponownego uruchomienia poszczególnych składników lub całego węzła procesu roboczego. Te operacje ponownego uruchomienia są zautomatyzowane i zapewniają automatyczne korygowanie typowych problemów. Te ponowne uruchomienie występuje tylko na poziomie _węzła,_ a nie klastra, chyba że istnieje awaryjna konserwacja lub awaria.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Obowiązki odbiorcy dla węzłów procesu roboczego AKS

Firma Microsoft nie uruchamia automatycznie ponownego uruchamiania węzłów procesu roboczego w celu zastosowania poprawek na poziomie systemu operacyjnego. Mimo że poprawki systemu operacyjnego są dostarczane do węzłów procesu roboczego, *klient* jest odpowiedzialny za ponowne uruchomienie węzłów procesu roboczego w celu zastosowania zmian. Biblioteki współużytkowane, demony, takie jak dysk hybrydowy półprzewodnikowy (SSHD) i inne składniki na poziomie systemu lub systemu operacyjnego, są automatycznie poprawiane.

Klienci są odpowiedzialni za wykonywanie uaktualnień kubernetes. Mogą wykonywać uaktualnienia za pośrednictwem panelu sterowania platformy Azure lub interfejsu wiersza polecenia platformy Azure. Dotyczy to aktualizacji, które zawierają ulepszenia zabezpieczeń lub funkcjonalności kubernetes.

> [!NOTE]
> Ponieważ usługa AKS jest *usługą zarządzaną,* jej cele końcowe obejmują usunięcie odpowiedzialności za poprawki, aktualizacje i zbieranie dzienników, aby zarządzanie usługami było bardziej kompletne i praktyczne. Wraz ze wzrostem pojemności usługi do zarządzania end-to-end przyszłe wersje mogą pomijać niektóre funkcje (na przykład ponowne uruchamianie węzłów i automatyczne łatanie).

### <a name="security-issues-and-patching"></a>Problemy z zabezpieczeniami i łatanie

Jeśli w jednym lub kilku składnikach usługi AKS zostanie znaleziony błąd zabezpieczeń, zespół usługi AKS załata wszystkie klastry, których dotyczy problem, aby rozwiązać ten problem. Alternatywnie zespół udzieli użytkownikom wskazówek dotyczących uaktualniania.

W przypadku węzłów procesu roboczego, których dotyczy luka zabezpieczeń, jeśli dostępna jest poprawka o zerowym przestoju, zespół AKS zastosuje tę poprawkę i powiadomi użytkowników o zmianie.

Gdy poprawka zabezpieczeń wymaga ponownego uruchomienia węzła procesu roboczego, firma Microsoft powiadomi klientów o tym wymaganiu. Klient jest odpowiedzialny za ponowne uruchomienie lub aktualizację w celu uzyskania poprawki klastra. Jeśli użytkownicy nie zastosują poprawek zgodnie ze wskazówkami usługi AKS, ich klaster będzie nadal narażony na problem z zabezpieczeniami.

### <a name="node-maintenance-and-access"></a>Konserwacja i dostęp do węzła

Węzły procesu roboczego są współużytkowane i są własnością klientów. Z tego powodu klienci mają możliwość logowania się do węzłów procesu roboczego i wprowadzania potencjalnie szkodliwych zmian, takich jak aktualizacje jądra oraz instalowanie lub usuwanie pakietów.

Jeśli klienci wnieśają destrukcyjne zmiany lub powodują, że składniki płaszczyzny sterowania przechodzą w tryb offline lub stają się niefunkcjonalne, usługa AKS wykryje ten błąd i automatycznie przywróci węzeł roboczy do poprzedniego stanu roboczego.

Mimo że klienci mogą logować się i zmieniać węzły procesu roboczego, nie jest to odradzane, ponieważ zmiany mogą spowodować, że klaster nie będzie można go posługiwać.

## <a name="network-ports-access-and-nsgs"></a>Porty sieciowe, dostęp i sieciowe sieciowe

Jako usługa zarządzana usługa AKS ma określone wymagania dotyczące sieci i łączności. Wymagania te są mniej elastyczne niż wymagania dla zwykłych komponentów IaaS. W ukas operacje, takie jak dostosowywanie reguł sieciowej grupy danych sieciowych, blokowanie określonego portu (na przykład używanie reguł zapory blokujących port wychodzący 443) i adresy URL na białej liście mogą uniemożliwić klastrowi.

> [!NOTE]
> Obecnie usługa AKS nie zezwala na całkowite zablokowanie ruchu wychodzącego z klastra. Aby kontrolować listę adresów URL i portów, których klaster może używać w ruchu wychodzącym, zobacz [ograniczanie ruchu wychodzącego](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Nieobsługiwały funkcje kubernetes w wersji alfa i beta

Usługa AKS obsługuje tylko stabilne funkcje w ramach projektu kubernetes nadrzędnego. O ile nie zostało to inaczej udokumentowane, usługa AKS nie obsługuje funkcji alfa i beta, które są dostępne w projekcie nadrzędnego kubernetes.

W dwóch scenariuszach funkcje alfa lub beta mogą zostać wdrożone, zanim będą ogólnie dostępne:

* Klienci spotkali się z zespołami aks, pomocy technicznej lub inżynierów i zostali poproszeni o wypróbowanie tych nowych funkcji.
* Te funkcje zostały [włączone przez flagę funkcji](https://github.com/Azure/AKS/blob/master/previews.md). Klienci muszą jawnie wyrazić zgodę na korzystanie z tych funkcji.

## <a name="preview-features-or-feature-flags"></a>Podgląd funkcji lub flag obiektów

W przypadku funkcji i funkcji wymagających rozszerzonych testów i opinii użytkowników firma Microsoft udostępnia nowe funkcje w wersji zapoznawczej lub funkcje za flagą funkcji. Należy wziąć pod uwagę te funkcje jako funkcje wersji wstępnej lub beta.

Funkcje w wersji zapoznawczej lub funkcje flagi funkcji nie są przeznaczone do produkcji. Trwające zmiany w interfejsach API i zachowaniu, poprawki błędów i inne zmiany mogą spowodować niestabilne klastry i przestoje.

Funkcje w publicznej wersji zapoznawczej są objęte pomocą techniczną "najlepszego wysiłku", ponieważ te funkcje są w wersji zapoznawczej i nie są przeznaczone do produkcji i są obsługiwane przez zespoły pomocy technicznej AKS tylko w godzinach pracy. Dodatkowe informacje można znaleźć na przykład:

* [Często zadawane pytania dotyczące pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Funkcje w wersji zapoznawczej zaczynają obowiązywać na poziomie *subskrypcji platformy* Azure. Nie instaluj funkcji w wersji zapoznawczej w subskrypcji produkcyjnej. W ramach subskrypcji produkcyjnej funkcje w wersji zapoznawczej mogą zmieniać domyślne zachowanie interfejsu API i wpływać na regularne operacje.

## <a name="upstream-bugs-and-issues"></a>Nadrzędne błędy i problemy

Biorąc pod uwagę szybkość rozwoju w projekcie Kubernetes, błędy niezmiennie pojawiają się. Niektóre z tych błędów nie mogą być poprawione lub obejść w systemie AKS. Zamiast tego poprawki błędów wymagają większych poprawek do projektów nadrzędnych (takich jak Kubernetes, systemy operacyjne węzłów lub procesowych i jądra). W przypadku składników, które jest właścicielem firmy Microsoft (takich jak dostawca chmury platformy Azure), usługi AKS i personel platformy Azure są zobowiązane do rozwiązywania problemów nadrzędnych w społeczności.

Gdy problem z pomocą techniczną jest głównym spowodowane przez jeden lub więcej błędów nadrzędnych, wsparcie AKS i zespoły inżynierów będzie:

* Identyfikowanie i łączenie błędów nadrzędnych z wszelkimi szczegółami pomocniczymi, aby wyjaśnić, dlaczego ten problem wpływa na klaster lub obciążenie. Klienci otrzymują łącza do wymaganych repozytoriów, dzięki czemu mogą obserwować problemy i zobaczyć, kiedy nowa wersja zapewni poprawki.
* Podaj potencjalne obejścia lub środki zaradcze. Jeśli problem można złagodzić, [znany problem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) zostanie złożony w repozytorium AKS. Znane zgłoszenie problemu wyjaśnia:
  * Problem, w tym linki do błędów nadrzędnych.
  * Obejście i szczegóły dotyczące uaktualnienia lub innej trwałości rozwiązania.
  * Przybliżone terminy włączenia problemu, w oparciu o kadencję wydania wyższego szczebla.
