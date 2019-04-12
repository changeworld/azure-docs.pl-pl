---
title: Usługa Azure Kubernetes Service (AKS) obsługuje zasady
description: Więcej informacji na temat zasad pomocy technicznej usługi Azure Kubernetes Service (AKS), wspólnej odpowiedzialności, funkcje wersji zapoznawczej/alfa/Beta.
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: f173fc7c794729eae8c60cceefa88d153800a816
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502176"
---
# <a name="azure-kubernetes-service-aks-support-policies"></a>Usługa Azure Kubernetes Service (AKS) obsługuje zasady

Ten artykuł zawiera szczegółowe informacje dotyczące zasad pomocy technicznej usługi AKS, ograniczenia i szczegółowe informacje, w tym zarządzania węzłami procesu roboczego, zarządzane kontrolować płaszczyzny składników, składników typu open-source innych firm i zabezpieczenia / Zarządzanie poprawkami.

## <a name="service-updates--releases"></a>Aktualizacje usług i wersje

* Aby uzyskać informacje o wersji, zobacz [informacje o wersji w usłudze AKS][1]
* W przypadku funkcji w publicznej wersji zapoznawczej, zobacz [powiązanych projektów i funkcje w wersji zapoznawczej usługi AKS][2]

## <a name="what-is-managed"></a>Co to jest "zarządzana"

W odróżnieniu od IaaS chmurze składników podstawowych takich jak obliczenia lub sieci, które ujawniają niskiego poziomu kontroli i użytkownikom korzystanie z opcji dostosowywania, usługa AKS zapewnia setką kompleksowych wdrożenia rozwiązania Kubernetes, który reprezentuje zestaw typowych konfiguracji i funkcje wymagane dla rozwiązania Kubernetes. Klienci, którzy korzystają z tej usługi mają ograniczoną liczbę dostosowań, wdrożenia i inne opcje. Oznacza to, że klienci nie muszą się martwić lub bezpośrednie zarządzanie klastrem Kubernetes.

Za pomocą usługi AKS, klient pobiera to w pełni zarządzana **płaszczyznę kontroli** — w przypadku, gdy na płaszczyźnie kontroli zawiera wszystkich składników i usług wymaganych do działania i zapewniać klastrów Kubernetes użytkownikom końcowym. Wszystkie składniki rozwiązania Kubernetes są obsługiwane i świadczona przez firmę Microsoft.

Za pomocą zarządzanych **płaszczyznę kontroli** następujące składniki są zarządzane i monitorowane przez firmę Microsoft:

* Agenta Kubelet / serwery interfejsu API rozwiązania Kubernetes
* Etcd lub zgodny magazyn klucz/wartość — takich jak jakość usługi, skalowalność i środowiska uruchomieniowego
* Usługi DNS (na przykład klastra kubernetes w usłudze dns / CoreDNS)
* Kubernetes Proxy/networking

AKS nie jest w 100% zarządzane **klastra** rozwiązania. Niektóre składniki (na przykład węzłów procesu roboczego) ma pewne **wspólnej odpowiedzialności** przypadki, w których działania, aby zachować klaster AKS wymaga interakcji użytkownika. Na przykład węzeł procesu roboczego aplikacji poprawki zabezpieczeń systemu operacyjnego.

 **Zarządzane**, oznacza to, że wdraża firmę Microsoft oraz zespół usługi AKS, działa i jest odpowiedzialny za dostępności i funkcji z tych usług. **Klienci nie można zmienić tych składników**. Dostosowanie jest ograniczony, aby zapewnić spójne i skalowalne interfejsu użytkownika. Aby w pełni dostosowywalnych rozwiązań, zobacz [aparatu AKS](https://github.com/Azure/aks-engine).

> [!NOTE]
> Jest ważne dowiedzieć się, że węzły procesu roboczego usługi Azure Kubernetes Service są wyświetlane w witrynie Azure Portal jako zwykłych zasobów IaaS platformy Azure, mimo że te maszyny wirtualne są wdrażane w niestandardowe grupy zasobów platformy Azure (prefiksem MC\\*). Użytkownik może zmienić je, SSH do nich tak samo jak normalne maszyny wirtualne (nie można jednak zmienić podstawowy obraz systemu operacyjnego, a zmiany nie utrzymują się za pośrednictwem aktualizacji lub ponowne uruchomienie) i dołączenie innych zasobów platformy Azure lub zmodyfikuj je w inny sposób. **Temu o zarządzanie poza pasmem a Dostosowywanie oznacza jednak, że sam klaster AKS może stać się unsupportable. Należy unikać modyfikacją węzła procesu roboczego, chyba że wskazany przez Microsoft Support.**

## <a name="what-is-shared-responsibility"></a>Co to jest udostępniony odpowiedzialności

Podczas tworzenia klastra AKS tworzy liczbę węzłów procesu roboczego platformy Kubernetes, zdefiniowane przez klienta. Te węzły, jak wspomniano są, w których są wykonywane obciążeń klientów. Klienci właścicielem i można wyświetlać lub modyfikować te węzły procesu roboczego.

Ponieważ te węzły są wykonywane kodu prywatnego i przechowywanie danych poufnych, pomocy technicznej firmy Microsoft ma **ograniczony dostęp** klientowi wszystkie węzły klastra. Pomocy technicznej firmy Microsoft nie może zalogować się do, wykonaj polecenia lub wyświetlanie dzienników tych węzłów bez uprawnień odbiorcy express i/lub pomoc do wykonywania poleceń debugowania, zgodnie z zaleceniami z zespołem pomocy technicznej.

Ze względu na charakter poufnych z tych węzłów procesu roboczego firma Microsoft podejmuje szczególną uwagę na ograniczenie zarządzania "w tle" z tych węzłów. Nawet w przypadku rozwiązania Kubernetes głównym, węzłami, etcd i innych błędów składników (zarządzany przez firmę Microsoft) obciążenie będzie będą nadal działać w wielu przypadkach. Jeśli węzły procesu roboczego są modyfikowane bez opieki, jej prowadzić do utraty danych i/lub obciążenia i renderowania unsupportable klastra.

## <a name="azure-kubernetes-service-support-coverage"></a>Usługa Azure objęcia pomocą techniczną usługi Kubernetes

**Firma Microsoft oferuje pomoc techniczną dla następujących elementów:**

* Łączność z wszystkimi składnikami rozwiązania Kubernetes oferowanego i obsługiwanego przez usługę Kubernetes (np. serwera interfejsu API)
* Zarządzanie, czasu dostępności, QoS i operacje w kontrolować warstwy usług (etcd węzłów serwera interfejsu API wzorca Kubernetes, rozwiązanie kubernetes — dns, na przykład.
* Obsługa Etcd obejmuje automatyczne, przejrzyste kopie zapasowe wszystkich danych etcd co 30 minut do przywrócenia stanu planowania i klaster po awarii. Te kopie zapasowe nie są dostępne bezpośrednio dla klientów/użytkowników i są używane w celu zapewnienia niezawodności dotyczącej danych i spójność
* Wszystkie punkty integracji w sterowników dostawcy chmury platformy Azure dla rozwiązania Kubernetes, takich jak integracji z innymi Azure usługach, takich jak moduły równoważenia obciążenia, woluminy trwałe, Networking (Kubernetes i wtyczki Azure CNI)
* Pytania lub problemy dotyczące dostosowywania elementów płaszczyzna kontroli, takich jak serwera interfejsu API rozwiązania Kubernetes, etcd oraz klastra kubernetes w usłudze dns.
* Problemy dotyczące tematów dotyczących obsługi sieci, takich jak wtyczki Azure CNI, wtyczki Kubenet lub innych sieci problemy dotyczące dostępu i funkcje (DNS rozdzielczości, utraty pakietów, routingu i tak dalej).
  * Sieć obsługiwane są następujące scenariusze wtyczki Kubenet (Basic) i zaawansowane sieci (wtyczki Azure CNI) w ramach klastra i skojarzone składniki, łączność z innymi usługami platformy Azure i aplikacjami. Ponadto kontrolery transferu danych przychodzących i konfiguracji usługi równoważenia obciążenia/ruch przychodzący, wydajność sieci i opóźnienia są obsługiwane przez firmę Microsoft.

**Firma Microsoft zapewnia pomoc techniczną następujące czynności:**

* Poradnik / użycia "Instrukcje" Kubernetes pytania na przykład sposobu tworzenia kontrolerów niestandardowe transferu danych przychodzących, pytań związanych z obciążeniem aplikacji i innych — strona/OSS pakietów lub narzędzia znajdą się poza zakresem.
  * Poradnik biletów dla usługi AKS klastra funkcji dostosowywania, dostosowywania — np. rozwiązania Kubernetes operacji w przypadku problemów/jak-tos znajdują się w zakresie.
* Projekty typu open-source innych firm nie są dostarczane jako część rozwiązania Kubernetes sterowania płaszczyzny lub wdrożona za pomocą klastra AKS, np. Istio, narzędzia Helm, usługa Envoy i inne.
  * Dla projektów typu open-source innych firm, takie jak Helm i Kured, optymalną obsługą podano przykłady i aplikacje w dokumentacji firmy Microsoft i gdzie tego narzędzia typu open-source innych firm integruje się z dostawcą chmury Kubernetes Azure lub inne błędy specyficzne dla usługi AKS.
* Zamknięte źródła firm — może to obejmować narzędzia, sieci, urządzenia lub oprogramowanie do skanowania zabezpieczeń.
* Problemy dotyczące "wielu chmur" lub wielu dostawców szczegółowymi kompilacji nie są obsługiwane, na przykład uruchamianie Sfederowane dostawcy chmury publicznej międzyprojektowymi nie jest obsługiwane.
* Dostosowania określonej sieci, niż opisane w oficjalnej [dokumentację dotyczącą usługi AKS][3].
  > [!NOTE]
  > Problemy i usterki wokół grup zabezpieczeń sieci jest obsługiwana. Na przykład pomocy technicznej mogą odpowiadać na pytania wokół sieciowych grup zabezpieczeń, których nie można zaktualizować lub nieoczekiwane zachowanie w sieciowej grupie zabezpieczeń lub moduł równoważenia obciążenia.

## <a name="azure-kubernetes-service-support-coverage-worker-nodes"></a>Objęcia pomocą techniczną usługi Kubernetes platformy Azure (węzłów procesu roboczego)

### <a name="microsoft-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Obowiązki firmy Microsoft dla węzłów procesu roboczego usługi Azure Kubernetes Service

Jak wspomniano w `shared responsibility` sekcji Kubernetes węzłów procesu roboczego dzielą się na modelu wspólnych odpowiedzialność, gdzie:

* Zapewnia obrazu podstawowego systemu operacyjnego wymagane dodatki (takich jak monitorowanie i sieć agentów)
* Automatyczne dostarczanie poprawek systemu operacyjnego dla węzłów procesu roboczego
* Automatyczne korygowanie problemów z rozwiązaniem Kubernetes kontrolować płaszczyzny składników działających w węzłach procesu roboczego, takich jak:
  * kube-proxy
  * tunele sieci, które jest zapewnienie zaufanych ścieżek komunikacji usługi Kubernetes główne składniki
  * agenta kubelet
  * demon platformy docker/moby

> [!NOTE]
> W przypadku działającym w węźle procesu roboczego składnika płaszczyzna kontroli zespołu AKS może być konieczne ponowny rozruch węzła cały proces roboczy, aby rozwiązać ten problem. Jest to wykonywane w imieniu użytkownika i nie wykonywane, chyba że eskalacji klienta ma zostać (ze względu na dostęp do danych i obciążenia aktywnych klientów). Zawsze, gdy możliwe AKS pracownicy będą działać się wszystkie wymagane ponowne uruchomienie aplikacji bez wywierania wpływu na.

### <a name="customer-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Obowiązki klienta dla węzłów procesu roboczego usługi Azure Kubernetes Service

**Nie firmy Microsoft:**

- Automatycznego ponownego uruchomienia węzłów procesu roboczego dla systemu operacyjnego poziom poprawki zostały uwzględnione **(obecnie, zobacz poniżej)**

Poprawek systemu operacyjnego są dostarczane do węzłów procesu roboczego, chociaż jest on **odpowiedzialności klienta** ponownego uruchomienia węzłów procesu roboczego, aby zmiany zaczęły obowiązywać. Ta automatycznego stosowania poprawek zawiera biblioteki udostępnione, demonów, takich jak SSHD i inne składniki poziomu systemu/OS.

W ramach uaktualnień Kubernetes **klienci są zobowiązani do wykonywania uaktualnienia** za pośrednictwem Panelu sterowania platformy Azure lub interfejsu wiersza polecenia platformy Azure. Dotyczy to aktualizacje zawierające zabezpieczeń lub ulepszenia funkcji rozwiązania Kubernetes.

> [!NOTE]
> Ponieważ AKS `managed service` cele firmy Microsoft zakończenia usługi obejmują usuwanie odpowiedzialność za poprawki, aktualizacje, zbieranie dzienników i więcej zapewnienie więcej usługi całkowicie zarządzane i zdalne. Te elementy (ponowny rozruch węzła, automatyczne stosowanie poprawek) mogą zostać usunięte w przyszłych wersjach, jak zwiększyć możliwości naszych kompleksowe zarządzanie.

### <a name="security-issues-and-patching"></a>Problemy z zabezpieczeniami i stosowanie poprawek

W niektórych przypadkach (na przykład CVEs) lukę w zabezpieczeniach można znaleźć w jednym lub więcej składników usługi AKS. W takich scenariuszach AKS zostanie stosowanie poprawek do wszystkich klastrów których to dotyczy, aby rozwiązać ten problem, jeśli jest to możliwe lub udostępnić użytkownikom instrukcje dotyczące uaktualniania.

Dla węzłów procesu roboczego wpływ taki lukę w zabezpieczeniach Jeżeli poprawki bez jakichkolwiek przestojów, problemu jest dostępny, zespół AKS zastosowaniu tej poprawki i powiadamianie użytkowników o zmiany.

Jeśli poprawka zabezpieczeń są wymagane ponowne uruchomienie węzła procesu roboczego, firma Microsoft powiadomi klienta o tym wymaganiu i klient odpowiada na wykonanie ponownego uruchamiania lub aktualizacji można pobrać poprawkę dla swojego klastra. Jeśli użytkownicy nie mają zastosowania poprawek na wskazówki dotyczące usługi AKS, ich klastra będą nadal podatne na problemy.

### <a name="node-maintenance-and-access"></a>Węzeł konserwacją i dostępem

Ponieważ węzłów procesu roboczego to wspólna odpowiedzialność, a w obszarze własności klientów, klienci mogą zalogować się do tych pracowników i wykonać potencjalnie szkodliwe zmiany, takie jak aktualizacji jądra, usuwania pakietów i instalowanie nowych pakietów.

Jeśli klienci wykonać destrukcyjne lub operacje, które mogą powodować składniki płaszczyzna kontroli do przejścia do trybu offline lub w przeciwnym razie stają się nie działa, usługa AKS wykryje tego błędu i wykonywać automatyczne korygowanie można przywrócić z węzłem procesu roboczego do poprzedniego pracy Stan.

Mimo że klienci mogą się zalogować i zmień węzłów procesu roboczego, jest *nie zaleca się* ponieważ dzięki temu może być klastrem unsupportable.

## <a name="network-ports-access-and-network-security-groups"></a>Porty sieciowe, dostępu i sieciowych grup zabezpieczeń

Jako usługa zarządzana AKS wiążą się specyficzne wymagania sieci i łączności. Te wymagania są mniej elastyczne niż normalny składników modelu IaaS. W odróżnieniu od innych składników IaaS pewnych operacji (np. dostosowywania reguły sieciowej grupy zabezpieczeń, blokuje określonego portu, adresu URL umieszczania na białej liście i tak dalej) umożliwiający renderowanie unsupportable klastra (na przykład, reguł zapory blokujących wychodząca przez port 443).

> [!NOTE]
> Całkowite blokowanie ruchu wychodzącego (na przykład jawne domeny/port umieszczania na białej liście) z klastra usługi nie jest obsługiwanym scenariuszem usługi AKS w tej chwili. Lista adresów URL i portów może ulec zmianie bez uprzedzenia i może dostarczyć za pośrednictwem bilet pomocy technicznej systemu Azure. Podana lista jest tylko dla klientów, którzy zgadzasz się, *dostępność klastra może to mieć wpływ w dowolnym momencie.*

## <a name="alphabeta-kubernetes-features-not-supported"></a>Funkcje w wersji alfa/Beta Kubernetes (nieobsługiwane)

AKS obsługuje tylko stabilne funkcji w obrębie nadrzędnego projektu Kubernetes. Alfa/Beta funkcji dostępnych w oryginalne rozwiązanie Kubernetes nie są obsługiwane, chyba że inaczej przekazane i udokumentowane.

Istnieją dwa przypadki, w których funkcje w wersji Beta i alfa mogła zostać wycofana przed GA:

* Klienci są spełnione z produktem AKS, pomocy technicznej lub zespoły inżynierów i jawnie prośby o ich wypróbować te nowe funkcje.
* Te funkcje zostały [włączyć za pomocą flagi funkcji] [ 2] (jawne opt-in)

## <a name="preview-features--feature-flags"></a>Funkcje w wersji zapoznawczej / flag funkcji

Funkcji i możliwości, które wymagają rozszerzonej testowania, społeczności i opinie użytkowników firma Microsoft udostępni nowe funkcje w wersji zapoznawczej lub funkcje za flagą funkcji. Należy rozważyć następujące funkcje wersji wstępnej / wersji Beta i są narażone na użytkownikom szansę, aby wypróbować te nowe funkcje.

Jednak te w wersji zapoznawczej / flagę funkcji, które funkcje są nie przeznaczone do użytku produkcyjnego — interfejsy API, zmiana zachowania, poprawki i inne zmiany można wprowadzić, może spowodować niestabilność klastrów i przestojów. Obsługa tych funkcji jest ograniczona do raportowania błędów/problem. Nie należy włączać tych funkcji na systemy produkcyjne lub subskrypcji.

> [!NOTE]
> Włączanie funkcji w wersji zapoznawczej będą obowiązywać na platformie Azure **subskrypcji** poziom. Nie należy instalować funkcje w wersji zapoznawczej w środowisku produkcyjnym subskrypcji, ponieważ może zmienić domyślne zachowanie interfejsu API wpływające na ochronę regularnych operacji.

## <a name="upstream-bugs-and-issues"></a>Nadrzędne usterki i problemy

W projekcie nadrzędnego rozwiązania Kubernetes, biorąc pod uwagę szybkość rozwoju, istnieją niezmiennie usterki, które nie można zastosować poprawki lub przepracowanych wokół systemie AKS i zamiast tego wymagają większej poprawki do nadrzędnego projektów (takich jak Kubernetes, węzeł/proces roboczy w systemach operacyjnych i jądra). Dla składników, które firma Microsoft masz (na przykład dostawca usługi Azure w chmurze) personel AKS/Azure zobowiązujemy się do rozwiązywania problemu nadrzędne w społeczności.

W przypadkach, gdzie problem pomoc techniczna jest spowodowane głównego do jednego lub więcej błędów nadrzędnego pomocy technicznej usługi AKS i inżynierii wykona następujące elementy:

* Zidentyfikować i połączyć nadrzędnego usterki za pomocą dowolnego pomocnicze szczegóły dotyczące Dlaczego ma to wpływ na klastra i/lub obciążenia. Klienci otrzymają łącza do wymaganego repozytoriów/problemy oglądanie problemy i zobacz, gdy nowa wersja rozwiązania Kubernetes/inne będzie zawierać fix(es)
* Potencjalne obejść lub środki zaradcze: W niektórych przypadkach może być możliwe obejść ten problem — w tym przypadku "[znany problem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)" zostaną zgłoszone w repozytorium usługi AKS, który objaśnia:
  * Problem, a także łącze do nadrzędnego usterek
  * Obejście problemu i szczegółów dotyczących trwałości uaktualnienia/inne rozwiązania
  * Przybliżony osi czasu do włączenia w oparciu o cykl wersji nadrzędnego

[1]: https://github.com/Azure/AKS/releases
[2]: https://github.com/Azure/AKS/blob/master/previews.md
[3]: https://docs.microsoft.com/azure/aks/
