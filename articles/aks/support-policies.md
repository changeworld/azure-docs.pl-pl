---
title: Zasady pomocy technicznej dla usługi Azure Kubernetes Service (AKS)
description: Więcej informacji na temat zasad pomocy technicznej usługi Azure Kubernetes Service (AKS), wspólnej odpowiedzialności i funkcje, które są w wersji zapoznawczej (beta lub alpha).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 9b779021eca11638e8ee52ec11d082e5b0e89cd4
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506684"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Zasady pomocy technicznej dla usługi Azure Kubernetes Service

Ten artykuł zawiera szczegółowe informacje o pomocy technicznej zasady i ograniczenia dotyczące usługi Azure Kubernetes Service (AKS). Artykuł szczegółowo opisuje również zarządzania węzłami procesu roboczego, składniki płaszczyzny zarządzanego formantu, składników typu open-source innych firm i zarządzania zabezpieczeniami lub poprawki.

## <a name="service-updates-and-releases"></a>Aktualizacje usług i wersji

* Aby uzyskać informacje o wersji, zobacz [informacje o wersji usługi AKS](https://github.com/Azure/AKS/releases).
* Aby uzyskać informacje na temat funkcji w wersji zapoznawczej, zobacz [AKS w wersji zapoznawczej funkcji i powiązanych projektów](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Zarządzane funkcji w usłudze AKS

Podstawowej infrastruktury jako części chmury usługi (IaaS), takich jak obliczenia lub składników sieciowych zapewniają użytkownikom dostęp do niskiego poziomu kontroli i opcje dostosowywania. Z kolei AKS zapewnia gotową do użycia wdrożenia rozwiązania Kubernetes, który zapewnia klientom wspólny zbiór konfiguracjami i możliwościami, które są im potrzebne. Klienci usługi AKS mają ograniczoną dostosowywania, wdrażania i innych opcji. Ci klienci nie muszą przejmować się lub bezpośrednie zarządzanie klastrów Kubernetes.

Za pomocą usługi AKS, klient pobiera to w pełni zarządzana *płaszczyznę kontroli*. Na płaszczyźnie kontroli zawiera wszystkie składniki i usługi, którą klient musi działać i zapewniać klastrów Kubernetes użytkownikom końcowym. Wszystkie składniki rozwiązania Kubernetes są obsługiwane i świadczona przez firmę Microsoft.

Firma Microsoft zarządza i monitoruje następujące składniki za pomocą okienka kontrolki:

* Serwery agenta Kubelet lub interfejsu API rozwiązania Kubernetes
* Etcd lub zgodny parach klucz wartość, zapewnianie jakości usług (QoS), skalowalność i środowiska uruchomieniowego
* Usługi DNS (na przykład klastra kubernetes w usłudze dns lub CoreDNS)
* Serwer proxy usługi Kubernetes lub sieć.

AKS, nie jest rozwiązaniem w pełni zarządzany klaster. Niektóre składniki, takie jak węzły procesu roboczego ma *odpowiedzialności*, gdzie użytkownicy muszą zachować klaster AKS. Dane wejściowe użytkownika jest wymagane, na przykład, aby zastosować poprawkę zabezpieczeń systemu operacyjnego (OS) węzłów procesu roboczego.

Te usługi są *zarządzane* w tym sensie, że firmę Microsoft oraz zespół AKS wdraża działa i jest odpowiedzialny za dostępność usług i funkcji. Klienci nie można zmienić tych składników zarządzanych. Microsoft ogranicza dostosowania, aby zapewnić spójne i skalowalne interfejsu użytkownika. Aby w pełni dostosowywalnych rozwiązań, zobacz [aparatu AKS](https://github.com/Azure/aks-engine).

> [!NOTE]
> Węzły procesu roboczego AKS są wyświetlane w witrynie Azure portal jako zwykłych zasobów IaaS platformy Azure. Jednak te maszyny wirtualne są wdrażane w grupie zasobów platformy Azure niestandardowych (prefiksem MC\\*). Istnieje możliwość zmiany węzłów procesu roboczego usługi AKS. Na przykład, można użyć Secure Shell (SSH) Aby zmienić sposób zmiany normalnych maszyn wirtualnych węzłów procesu roboczego AKS (nie można jednak zmienić podstawowy obraz systemu operacyjnego, a zmiany nie utrzymują się za pośrednictwem aktualizacji lub ponowne uruchomienie) i innych zasobów platformy Azure można dołączyć do usługi AKS węzły procesu roboczego. Jednak podczas wprowadzania zmian *Zarządzanie poza pasmem a dostosowywania,* klastra AKS może stać się unsupportable. Należy unikać zmiany węzłów procesu roboczego, chyba że Microsoft Support Określa, że można wprowadzać zmiany.

## <a name="shared-responsibility"></a>Wspólna odpowiedzialność

Po utworzeniu klastra klienta definiuje węzłów procesu roboczego platformy Kubernetes, tworzonych w usłudze AKS. Obciążeń klientów są wykonywane na tych węzłach. Klienci właścicielem i można wyświetlać lub modyfikować węzłów procesu roboczego.

Ponieważ węzłów klastra klienta wykonywanie kodu prywatnego i przechowywanie danych poufnych, Microsoft Support można uzyskiwać do nich dostęp w ograniczony sposób. Microsoft Support nie Zaloguj się do wykonywania poleceń w i przeglądanie dzienników dla tych węzłów bez zgody klienta express lub pomocy.

Ponieważ węzłów procesu roboczego są poufne, firma Microsoft podejmuje szczególną uwagę na ograniczyć zarządzanie nimi w tle. W wielu przypadkach obciążenie będzie kontynuował pracę, nawet w przypadku rozwiązania Kubernetes głównym węzłów, etcd i innych składników zarządzanych przez firmę Microsoft, kończyć się niepowodzeniem. Węzły procesu roboczego zaniedbali zmodyfikowane może spowodować straty danych i obciążeń i może spowodować, że klaster unsupportable.

## <a name="aks-support-coverage"></a>Zakres pomocy technicznej usługi AKS

Firma Microsoft oferuje pomoc techniczną dla następujących elementów:

* Łączność dla wszystkich składników platformy Kubernetes, które usługa Kubernetes zapewnia obsługuje, np. serwera interfejsu API.
* Zarządzanie, czasu dostępności, QoS i operacje w kontroli warstwy usług (węzłów głównych Kubernetes, interfejsu API serwera, etcd i klastra kubernetes w usłudze dns, na przykład).
* Etcd. Obsługa obejmuje automatyczne, przejrzyste kopie zapasowe wszystkich danych etcd co 30 minut do przywrócenia stanu planowania i klaster po awarii. Te kopie zapasowe nie są bezpośrednio dostępne dla klientów lub użytkowników. Zapewniają niezawodność danych i spójność.
* Wszystkie punkty integracji w sterowniku dostawcy chmury platformy Azure dla platformy Kubernetes. Obejmują one integracji do innych usług platformy Azure, takich jak moduły równoważenia obciążenia, woluminy trwałe lub sieci (Kubernetes i wtyczki Azure CNI).
* Pytania lub problemy dotyczące dostosowywania elementów płaszczyzna kontroli, takich jak serwera interfejsu API rozwiązania Kubernetes, etcd oraz klastra kubernetes w usłudze dns.
* Problemy dotyczące sieci, takich jak wtyczki Azure CNI wtyczki kubenet, lub inne dostępu do sieci i funkcjonalność problemów. Problemy mogą obejmować DNS rozdzielczości, utraty pakietów, routingu i tak dalej. Firma Microsoft obsługuje różne scenariusze sieciowe:
  * Wtyczki Kubenet (basic) i zaawansowany siecią (wtyczki Azure CNI) w klastrze i skojarzone składniki
  * Łączność z innymi usługami platformy Azure i aplikacjami
  * Ruch przychodzący kontrolerów i konfiguracji usługi równoważenia ruchu przychodzącego i obciążenia
  * Wydajność sieci i czas oczekiwania

Microsoft nie zapewnia pomoc techniczną dla następujących elementów:

* Pytania dotyczące sposobu używania koordynatora Kubernetes. Na przykład Microsoft Support nie udostępniała informacje na temat jak utworzyć niestandardowe ruch przychodzący kontrolerów, za pomocą obciążenia aplikacji lub stosowanie pakietów oprogramowania innych firm lub typu open source lub narzędzia.
  > [!NOTE]
  > Microsoft Support może poinformować usługi AKS funkcje klastra, dostosowywania i dostrajanie (na przykład problemów dotyczących operacji Kubernetes i procedur).
* Projektów typu open-source innych firm, które nie są dostarczane jako część rozwiązania Kubernetes płaszczyzna kontroli lub wdrożyć przy użyciu klastrów usługi AKS. Te projekty mogą obejmować Istio, narzędzia Helm, usługa Envoy lub inne osoby.
  > [!NOTE]
  > Firma Microsoft może udostępnić obsługę największej staranności dla projektów typu open-source innych firm, takie jak Helm i Kured. W przypadku, gdy narzędzia typu open source innego producenta integruje się z dostawcą chmury Kubernetes Azure lub inne usterki specyficzne dla usługi AKS, firma Microsoft obsługuje, przykłady i aplikacje z dokumentacji firmy Microsoft.
* Oprogramowanie zamknięte source innych firm. To oprogramowanie może zawierać narzędzia do skanowania zabezpieczeń sieciowych urządzeń lub oprogramowania.
* Problemy dotyczące multicloud lub uzyskanymi szczegółowymi kompilacji. Na przykład firma Microsoft nie obsługuje problemy związane z uruchamianiem rozwiązania dostawcy federacyjnego multipublic chmury.
* Dostosowania innych niż te wymienione w sieci [dokumentację dotyczącą usługi AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Firma Microsoft obsługuje problemy i usterki związane z sieciowymi grupami zabezpieczeń (NSG). Na przykład Microsoft Support może odpowiedzieć na pytania dotyczące wystąpił błąd sieciowej grupy zabezpieczeń, aby zaktualizować lub nieoczekiwane zachowanie sieciowej grupy zabezpieczeń lub obciążenia równoważenia.

## <a name="aks-support-coverage-for-worker-nodes"></a>Zakres pomocy technicznej usługi AKS dla węzłów procesu roboczego

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Obowiązki firmy Microsoft dla węzłów procesu roboczego usługi AKS

Firma Microsoft i klienci współodpowiedzialne dla węzłów procesu roboczego platformy Kubernetes gdzie:

* Podstawowy obraz systemu operacyjnego wymaga dodatki (takich jak monitorowanie i sieć agentów).
* Węzły procesu roboczego automatycznie otrzymywać poprawek systemu operacyjnego.
* Problemy związane z rozwiązania Kubernetes kontrolować płaszczyzny, które składniki działające na węzłach procesu roboczego są rozwiązywane automatycznie. Następujące składniki:
  * Kube-proxy
  * tunele sieci, które jest zapewnienie zaufanych ścieżek komunikacji usługi Kubernetes główne składniki
  * agenta kubelet
  * Demon platformy docker lub Moby

> [!NOTE]
> W węźle procesu roboczego Jeśli składnika płaszczyzna kontroli nie działa, zespół AKS może być konieczne ponowny rozruch węzła całego procesu roboczego. Ze względu na ich ograniczony dostęp do danych i obciążenia aktywnych klientów przez zespół usługi AKS ponowny rozruch węzła procesu roboczego tylko wtedy, gdy klient Eskalowanie problem. Wszędzie tam, gdzie to możliwe, zespół AKS działa zapobiec wymagane ponowne uruchomienie aplikacji.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Obowiązki klienta dla węzłów procesu roboczego usługi AKS

Microsoft nie automatyczny ponowny rozruch stosowanie poprawek systemu operacyjnego poziom węzłów procesu roboczego. Mimo że poprawek systemu operacyjnego są dostarczane do węzłów procesu roboczego *klienta* jest odpowiedzialny za ponownego uruchomienia węzłów procesu roboczego, aby zastosować zmiany. Biblioteki udostępnione, demonów, takich jak dysk SSD hybrydowe (SSHD) i inne składniki, na poziomie systemu lub system operacyjny automatycznie poprawić.

Klienci są zobowiązani do wykonywania uaktualnień Kubernetes. One może wykonać uaktualnienia za pomocą Panelu sterowania platformy Azure lub interfejsu wiersza polecenia platformy Azure. Dotyczy to usługi aktualizacji, które zawierają zabezpieczeń lub ulepszenia funkcji rozwiązania Kubernetes.

> [!NOTE]
> Ponieważ AKS *zarządzana usługa*, jego celów końcowego obejmują usuwanie odpowiedzialność za poprawki, aktualizacje i zbierania, aby bardziej kompletny i zdalne zarządzanie usługą dzienników. W miarę zwiększania możliwości usługi zarządzania end-to-end przyszłych wersji może pominąć niektóre funkcje (na przykład ponowny rozruch węzła i automatyczne stosowanie poprawek).

### <a name="security-issues-and-patching"></a>Problemy z zabezpieczeniami i stosowanie poprawek

Jeśli lukę w zabezpieczeniach znajduje się w jednej lub więcej składników programu AKS, zespół AKS spowoduje wypełnienie wszystkich dotyczy klastrów, aby rozwiązać ten problem. Alternatywnie zespół zapewnia użytkownikom instrukcje dotyczące uaktualniania.

Dla węzłów procesu roboczego, czy luka wpływa zabezpieczeń, a jeśli poprawki bez jakichkolwiek przestojów jest dostępny zespół AKS zastosowania tej poprawki i powiadamianie użytkowników o zmiany.

Jeśli poprawka zabezpieczeń wymaga ponownego uruchomienia węzła procesu roboczego, firma Microsoft powiadomi klientów to wymaganie. Klient jest odpowiedzialny za ponowne uruchamianie lub aktualizowanie ułatwią poprawka klastra. Jeśli użytkownicy nie mają zastosowania poprawek według wskazówek AKS, ich klastra będzie narażony na problem z zabezpieczeniami.

### <a name="node-maintenance-and-access"></a>Węzeł konserwacją i dostępem

Węzły procesu roboczego są wspólnej odpowiedzialności i są własnością klientów. W związku z tym klienci mają możliwość Zaloguj się do ich węzłów procesu roboczego i potencjalnie szkodliwe zmiany takich jak aktualizacji jądra i instalowania lub usuwania pakietów.

Jeśli klienci zmiany destrukcyjne lub spowodować, że kontrolka składniki płaszczyzny przejdą w tryb offline lub uniemożliwić, AKS wykryje tego błędu i automatycznym przywróceniem z węzłem procesu roboczego do poprzedniego stanu pracy.

Mimo że klienci mogą zalogować się do i zmienić węzłów procesu roboczego, w ten sposób jest zalecane, ponieważ zmiany klastra unsupportable.

## <a name="network-ports-access-and-nsgs"></a>Porty sieciowe, dostępu i sieciowymi grupami zabezpieczeń

Jako usługa zarządzana AKS wiążą się specyficzne wymagania sieci i łączności. Te wymagania są mniej elastyczne niż wymagania normalne składników modelu IaaS. W usłudze AKS operacje, takie jak dostosowywanie reguł sieciowej grupy zabezpieczeń, blokuje określonych portów (np. przy użyciu reguł zapory, które blokują wychodząca przez port 443), i listę dozwolonych adresów URL może klastra unsupportable.

> [!NOTE]
> Obecnie usługa AKS nie zezwala na całkowicie zablokować ruch wychodzący z klastrem (na przykład jawne domeny lub port umieszczania na białej liście). Lista adresów URL i portów jest mogą ulec zmianie bez uprzedzenia. Zaktualizowaną listę można uzyskać, tworząc bilet pomocy technicznej systemu Azure. Lista jest tylko dla klientów, którzy zgadzasz się, ich dostępność klastra może to mieć wpływ *w dowolnym momencie.*

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Nieobsługiwane funkcje Kubernetes alfa i beta

AKS obsługuje tylko stabilne funkcje w obrębie nadrzędnego projektu Kubernetes. Chyba że inaczej udokumentowane, AKS nie obsługuje funkcji alfa i beta, które są dostępne w projekcie nadrzędnego rozwiązania Kubernetes.

W przypadku dwóch scenariuszy funkcje w wersji beta i alfa może etapowo wdrażana, zanim staną się ogólnie dostępne:

* Klienci są spełnione z produktem AKS, pomocy technicznej lub zespoły inżynierów i zostałeś poproszony o wypróbować te nowe funkcje.
* Te funkcje zostały [włączane przez flagi funkcji](https://github.com/Azure/AKS/blob/master/previews.md). Klienci muszą jawnie zgoda na korzystanie z tych funkcji.

## <a name="preview-features-or-feature-flags"></a>Funkcje w wersji zapoznawczej lub flag funkcji

Dla funkcji i funkcji, które wymagają rozszerzonej testowania i opinie użytkowników firma Microsoft udostępnia nowe funkcje w wersji zapoznawczej lub funkcje za flagą funkcji. Jako funkcje wersję wstępną lub beta, należy wziąć pod uwagę te funkcje.

Wersja zapoznawcza funkcji lub flagi funkcji nie są przeznaczone dla środowiska produkcyjnego. Zmiany zachodzące w interfejsów API i zachowanie, poprawki błędów i innych zmian może spowodować klastrów niestabilny i przestoje.

Funkcje w wersji zapoznawczej są "starań" pomocy technicznej, należą te funkcje są w wersji zapoznawczej i nie przeznaczone dla środowiska produkcyjnego i są obsługiwane przez zespoły pomocy technicznej usługi AKS tylko w godzinach roboczych. Aby uzyskać więcej informacji zobacz:

* [Pomoc techniczna platformy Azure — często zadawane pytania](https://azure.microsoft.com/en-us/support/faq/)

> [!NOTE]
> Funkcje w wersji zapoznawczej obowiązywać Azure *subskrypcji* poziom. Nie instaluj funkcji w wersji zapoznawczej w ramach subskrypcji w środowisku produkcyjnym. W przypadku subskrypcji produkcyjnych funkcji w wersji zapoznawczej można zmienić domyślne zachowanie interfejsu API i wpływających na funkcjonowanie regularne.

## <a name="upstream-bugs-and-issues"></a>Nadrzędne usterki i problemy

W projekcie nadrzędnego rozwiązania Kubernetes, biorąc pod uwagę szybkość rozwoju, błędy niezmiennie pojawiają się. Niektóre z tych błędów nie można zastosować poprawki względem jakiegokolwiek lub przepracowanych wokół systemie AKS. Zamiast tego poprawki wymaga większych poprawki do nadrzędnego projektów (takich jak Kubernetes, węzeł lub proces roboczy systemów operacyjnych i jądra). Dla składników, które należą do firmy Microsoft (takiego jak dostawca chmury platformy Azure) usługi AKS i Azure personel zobowiązujemy się do rozwiązywania problemów nadrzędne w społeczności.

Gdy problem pomoc techniczna jest spowodowane jednym lub kilku błędach nadrzędny głównego, zespoły pomocy technicznej i inżynieria AKS wykonują następujące czynności:

* Zidentyfikować i połączyć nadrzędnego usterki przy użyciu dowolnej obsługi szczegółów ułatwia zrozumienie, dlaczego ten problem dotyczy sieci klastra lub obciążenia. Klienci otrzymują łącza do wymaganego repozytoriów, dzięki czemu mogą oglądać problemy i zobacz, gdy nowa wersja zapewni poprawki.
* Podaj potencjalne rozwiązania i ograniczenia. Jeśli ten problem można zminimalizować, [znany problem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) zostaną zachowane w repozytorium usługi AKS. Wyjaśnia, zgłoszenia znany problem:
  * Problem, wraz z łączami do nadrzędnego usterek.
  * Rozwiązania i szczegółowe informacje dotyczące uaktualniania lub innego stanu trwałego rozwiązania.
  * Przybliżony osi czasu włączenia ten problem, w oparciu o cykl wersji nadrzędnego.
