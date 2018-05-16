---
title: Ochrona maszyn wirtualnych wdrożonych na stosie Azure | Dokumentacja firmy Microsoft
description: Wskazówki na temat ochrony maszyn wirtualnych wdrożonych na stosie Azure.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/14/2018
ms.author: jeffgilb
ms.reviewer: hector.linares
ms.openlocfilehash: 734ee0e6ffb0dab660a2b63b431780208e0e0484
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Ochrona maszyn wirtualnych wdrożonych na stosie Azure

Użyj w tym artykule jako przewodnik dotyczący tworzenia planu ochrony maszyn wirtualnych (VM), które użytkownicy wdrażanie na stosie Azure.

Do ochrony przed utratą danych i nieplanowanych przestojów, należy wdrożyć plan odzyskiwania kopii zapasowej lub odzyskiwania po awarii dla swoich danych i aplikacji użytkownika. Ten plan muszą być unikatowe dla każdej aplikacji, ale następuje struktury ustala ciągłość kompleksowe Twojej organizacji i strategia odzyskiwania (BC i odzyskiwania po awarii). Dobry punkt wyjścia jest [projektowanie aplikacji odporne na platformie Azure](https://docs.microsoft.com/azure/architecture/resiliency), co umożliwia ogólne wzorce i rozwiązania w zakresie dostępności aplikacji i odporność.

>[!IMPORTANT]
> Testowanie planów odzyskiwania kopii zapasowej i odzyskiwania po awarii w sposób ciągły. Musisz tutaj, aby upewnić się, że:
> * Plany pracy
> * Plany nadal spełniają wymagania, które zostały zaprojektowane tak, aby uzyskać.

## <a name="azure-stack-infrastructure-recovery"></a>Usługa Azure recovery infrastruktury stosu

Użytkownicy są zobowiązani do ochrony ich maszyn wirtualnych oddzielnie z usług infrastruktury platformy Azure stosu.

Plan odzyskiwania usługi infrastruktury platformy Azure stosu **nie** obejmują odzyskiwania maszyny wirtualne użytkowników, konta magazynu i bazy danych. Jako właściciel aplikacji jest odpowiedzialny za wdrażanie plan odzyskiwania danych i aplikacji.

Jeśli chmury Azure stosu jest w trybie offline przez dłuższy czas lub trwale nieodwracalny, musisz mieć odzyskiwania planowanie w miejsce którego:

* Zapewnia minimalnym czasem przestojów
* Przechowuje krytyczne maszyn wirtualnych, np. serwerów bazy danych
* Umożliwia aplikacjom zachować obsługi żądań użytkowników

Operator chmury Azure stosu jest odpowiedzialny za tworzenie planu odzyskiwania dla podstawowej infrastruktury stosu Azure i usług. Aby dowiedzieć się więcej, przeczytaj artykuł na temat [odzyskać przed utratą danych w wyniku katastrofy](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data).

## <a name="sourcetarget-combinations"></a>Kombinacje źródłowego/docelowego

Każdej chmury Azure stosu jest wdrażany do jednego centrum danych. Oddzielne środowiska jest wymagana, więc można odzyskać aplikacji. Środowisko odzyskiwania może być innej chmury Azure stosu w różnych centrach danych lub w chmurze publicznej Azure. Twoje suwerenności danych i wymagania dotyczące zachowania poufności danych określi środowiska odzyskiwania dla aplikacji. Jak można włączyć ochrony dla każdej aplikacji, należy wybrać opcję odzyskiwania określonych dla każdej z nich. Aplikacje mogą mieć w jednej subskrypcji wykonywanie kopii zapasowych danych do innego centrum danych. W ramach innej subskrypcji można replikować danych w chmurze publicznej platformy Azure.

Zaplanuj strategię odzyskiwania kopii zapasowej i odzyskiwania po awarii dla każdej aplikacji określić obiekt docelowy dla każdej aplikacji. Plan odzyskiwania może pomóc organizacji poprawnie rozmiaru magazynu wymaganą wydajność lokalną i projektu zużycie w chmurze publicznej.

|  | Globalne Azure | Azure stosu wdrożone w centrum danych dostawcy usług Kryptograficznych i obsługiwanej przez dostawcy usług Kryptograficznych | Azure stosu wdrożone w centrum danych klienta i obsługiwanej przez klienta |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure stosu wdrożone w centrum danych dostawcy usług Kryptograficznych i obsługiwanej przez dostawcy usług Kryptograficznych** | Maszyny wirtualne użytkowników są wdrażane na stosie Azure obsługiwane dostawcy usług Kryptograficznych. Maszyny wirtualne użytkowników są przywrócone z kopii zapasowej lub przekazała pracę awaryjną bezpośrednio na platformie Azure. | Dostawca usług Kryptograficznych działa podstawowych i pomocniczych wystąpień Azure stosu w ich własnych centrach danych. Maszyny wirtualne użytkowników są przywracane lub przejścia w tryb failover między dwoma wystąpieniami stosu Azure. | Dostawca usług Kryptograficznych działa Azure stosu w lokacji głównej. Centrum danych klienta jest elementem docelowym przywracania lub pracy awaryjnej. |
| **Azure stosu wdrożone w centrum danych klienta i obsługiwanej przez klienta** | Maszyny wirtualne użytkowników są wdrażane do klienta obsługiwane stosu Azure. Maszyny wirtualne użytkowników są przywrócone z kopii zapasowej lub przekazała pracę awaryjną bezpośrednio na platformie Azure. | Klient działa podstawowych i pomocniczych wystąpień Azure stosu w ich własnych centrach danych. Maszyny wirtualne użytkowników są przywracane lub przejścia w tryb failover między dwoma wystąpieniami stosu Azure. | Klient działa Azure stosu w lokacji głównej. Dostawca usług Kryptograficznych w centrum danych jest elementem docelowym przywracania lub pracy awaryjnej. |

![Kombinacje źródłowy i docelowy](media\azure-stack-manage-vm-backup\vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Cele odzyskiwania aplikacji

Należy określić ilość utraconych czasem przestoju oraz danych, które organizacja może tolerować dla każdej aplikacji. Za kwantyfikacja Przestój i utraty danych można utworzyć plan odzyskiwania, który minimalizuje wpływ awarii w Twojej organizacji. Dla każdej aplikacji należy wziąć pod uwagę:

 - **Celu czasu odzyskiwania (RTO)**  
RTO to maksymalny czas dopuszczalne aplikacji mogą być niedostępne po incydentu. Na przykład RTO 90 minut oznacza, że musi mieć możliwość przywrócenia stosowania do uruchomione w ciągu 90 minut od początku awarii. Jeśli masz mało RTO może przechowywać drugie wdrożenie stale uruchomione w stan wstrzymania, aby zapewnić ochronę przed regionalnej awarii.
 - **Cel punktu odzyskiwania (RPO)**  
Cel punktu odzyskiwania to maksymalny czas trwania utraty danych, która jest dopuszczalny podczas awarii. Jeśli na przykład przechowujesz dane w pojedynczej bazie danych bez replikacji do innych baz danych i co godzinę wykonujesz kopię zapasową, możesz stracić nawet godzinę danych.

RTO i RPO są wymaganiami biznesowymi. Przeprowadzenia oceny ryzyka do definiowania RTO aplikacji i cel punktu odzyskiwania.

Metryka innego **średniego czasu Odzyskaj** (MTTR), która jest średni czas potrzebny do przywrócenia aplikacji po awarii. MTTR jest wartością empiryczne dla systemu. Jeśli czas MTTR przekracza czas RTO, wówczas awaria w systemie spowoduje niedopuszczalną przerwę w działalności biznesowej, ponieważ nie będzie możliwe przywrócenie systemu w ramach określonego czasu RTO.

### <a name="backup-restore"></a>Przywracanie kopii zapasowej

Najbardziej typowe schemat ochrony dla aplikacji opartych na maszyna wirtualna ma używać oprogramowania kopii zapasowej. Tworzenie kopii zapasowych maszyn wirtualnych zwykle zawiera system operacyjny, konfiguracja systemu operacyjnego, plików binarnych aplikacji i danych aplikacji. Kopie zapasowe są tworzone przez tworzenie migawki woluminu, dysków lub całą maszynę Wirtualną. Stosu Azure elastyczność tworzenia kopii zapasowych z w kontekście systemu operacyjnego gościa lub z magazynu Azure stosu i obliczeniowe interfejsów API. Stos Azure nie obsługuje pobierania tworzenia kopii zapasowych na poziomie funkcji hypervisor.
 
![Restor kopii zapasowej](media\azure-stack-manage-vm-backup\vm_backupdataflow_03.png)

Odzyskiwanie aplikacji wymaga Przywracanie przynajmniej jednej maszyny wirtualnej do tej samej chmury, lub do nowej chmury. Możesz zastosować chmury w centrum danych lub w chmurze publicznej. Wybrana chmura znajduje się całkowicie w formantu i opierają się na wymagań suwerenności i poufności informacji w danych.
 
 - RTO: Przestój mierzony w godzinach
 - Cel punktu odzyskiwania: Utraty danych zmiennej (w zależności od częstotliwości tworzenia kopii zapasowej)
 - Topologia wdrażania: aktywny/pasywny

#### <a name="planning-your-backup-strategy"></a>Planowanie strategii tworzenia kopii zapasowych

Planowanie strategii tworzenia kopii zapasowych i definiowania wymagań skali rozpoczyna się od określającą liczbę wystąpień maszyn wirtualnych, które muszą być chronione. Tworzenie kopii zapasowych wszystkich maszyn wirtualnych na wszystkich serwerach w środowisku jest wspólnej strategii. Jednak z stosu Azure są niektóre maszyny wirtualne, które należy wykonać kopię zapasową. Na przykład maszyn wirtualnych w zestawie skalowania są traktowane jako tymczasowych zasoby, które mogą występować i przejść, czasami bez powiadomienia. Trwałe dane, które wymagają ochrony są przechowywane w oddzielnych repozytorium, takie jak Magazyn bazy danych lub obiektu.

Ważne uwagi dotyczące tworzenia kopii zapasowej maszyn wirtualnych na stosie Azure:

 - **Kategoryzacji**
    - Należy wziąć pod uwagę modelu, w której użytkownicy będą chcieli w kopii zapasowej maszyny Wirtualnej.
    - Zdefiniuj odzyskiwania umowy dotyczącej poziomu usług (SLA) na podstawie priorytetu aplikacji lub wpływ na działalność biznesową.
 - **Skalowanie**
    - Podczas dołączania dużą liczbą nowych maszyn wirtualnych (jeśli kopia zapasowa jest wymagana), należy wziąć pod uwagę rozłożona kopii zapasowych.
    - Ocena kopii zapasowej produktów, które można skutecznie przechwytywanie i przesyłanie danych kopii zapasowej, aby zminimalizować zawartości zasobów w ramach rozwiązania.
    - Ocena kopii zapasowej produktów, które skutecznie przechowywać dane kopii zapasowej przy użyciu przyrostowej lub różnicowej kopii zapasowych można zminimalizować pełnych kopii zapasowych na wszystkich maszynach wirtualnych w środowisku.
 - **Przywracanie**
    - Dyski wirtualne, dane aplikacji w ramach istniejącej maszyny Wirtualnej, lub całego zasobu maszyny Wirtualnej i skojarzone dyski wirtualne, można przywrócić kopii zapasowej produktów. Schemat przywracania, który należy zależy od tego, jak zamierzasz przywrócić aplikację i będą miały wpływ czasu aplikacji do odzyskiwania. Na przykład może być łatwiejsze do wdrożenia programu SQL server z szablonu, a następnie przywróć bazy danych zamiast Przywracanie całą maszynę Wirtualną lub zestaw maszyn wirtualnych.

### <a name="replicationmanual-failover"></a>Replikacja/ręczną pracę awaryjną

Alternatywne podejście do obsługi wysokiej dostępności jest replikowanie maszyn wirtualnych z aplikacji do innej i zależne od ręcznego przełączania trybu failover. Replikacja systemu operacyjnego, plików binarnych aplikacji i danych aplikacji można przeprowadzić na poziomie systemu operacyjnego gościa lub poziom maszyny Wirtualnej. Tryb failover odbywa się przy użyciu dodatkowego oprogramowania, który nie jest częścią aplikacji.

Takie podejście aplikacja jest wdrażana w jednej chmury i jej maszyny Wirtualnej są replikowane do innych chmury. Jeśli trybu failover zostanie wywołany, pomocniczej maszyny wirtualne muszą włączony w chmurze drugiego. W niektórych scenariuszach pracy awaryjnej tworzy dyski maszyn wirtualnych i dołącza do nich. Ten proces może potrwać długo, szczególnie w przypadku wymaga sekwencji rozruchu określonych aplikacji wielowarstwowych. Można również kroki, które należy uruchomić, zanim aplikacja jest gotowa do uruchomienia, obsługi żądań.

![Replikacja ręczną pracę awaryjną](media\azure-stack-manage-vm-backup\vm_backupdataflow_02.png)

 - RTO: Przestój mierzony w minutach
 - Cel punktu odzyskiwania: Utraty danych zmiennej (w zależności od częstotliwości replikacji)
 - Topologia wdrażania: czuwania aktywny/pasywny
 
### <a name="high-availabilityautomatic-failover"></a>Tryb failover wysokiej dostępności/automatyczne

Dla aplikacji, w którym firma może tolerować kilka sekund lub minut przestoju i minimalna utrata danych należy wziąć pod uwagę konfiguracji o wysokiej dostępności. Aplikacje wysokiej dostępności są zaprojektowane do szybkiego i automatycznie odzyskać z błędami. Dla błędów sprzętu lokalnego stosu Azure infrastruktury implementuje wysokiej dostępności w sieci fizycznej za pomocą dwóch pierwszych stojak przełączników. Dla błędów poziomu obliczeniowej stosu Azure korzysta z wielu węzłów w jednostce skalowania. Na poziomie maszyny Wirtualnej można zestawy skalowania w połączeniu z domen błędów upewnij się, że awarii węzła nie przyjmują dół aplikacji.

W połączeniu z zestawy skalowania aplikacji należy natywnie obsługuje wysoką dostępność lub obsługuje klastrowanie oprogramowania. Na przykład Microsoft SQL Server obsługuje wysoką dostępność natywnie dla baz danych w trybie zatwierdzania synchronicznego. Jednak jeśli może obsługiwać tylko asynchroniczną replikację, będą utratę danych. Aplikacje można wdrażać w taki sposób, w którym oprogramowanie klastrowania obsługuje automatycznej pracy awaryjnej aplikacji klastra pracy awaryjnej.

W ten sposób aplikacja jest aktywna tylko w jednej chmury, ale oprogramowanie zostanie wdrożone do wielu chmur. Innych chmur włączony jest tryb wstrzymania przy zachowaniu gotowy do uruchomienia aplikacji, po wyzwoleniu pracy awaryjnej.

 - RTO: Przestój mierzony w sekundach
 - Cel punktu odzyskiwania: Minimalna utrata danych
 - Topologia wdrażania: czuwania aktywny/aktywny

### <a name="fault-tolerance"></a>Odporność na uszkodzenia

Azure stosu dostępności usługi nadmiarowości i infrastruktury fizycznej tylko ochronę przed sprzętu poziom błędów/błędy takie dysku, zasilacz, port sieci lub węzeł. Jednak jeśli aplikacja musi być zawsze dostępny i nigdy nie może spowodować utratę danych, musisz zaimplementować odporność na uszkodzenia natywnie w aplikacji lub użyj dodatkowego oprogramowania, aby zapewnić odporność na uszkodzenia.

Najpierw należy upewnić się, że maszyny wirtualne są wdrażane przy użyciu skalowania aplikacji ustawia chronić przed awariami poziomu węzła. Aby zapewnić ochronę przed chmury przechodzi do trybu offline, tej samej aplikacji musi już zostać wdrożony do nowej chmury, więc będzie możliwe kontynuowanie obsługi żądań bez przeszkód. Ten model jest zwykle określonych wdrożenia aktywny aktywny.

Należy pamiętać, że każdej chmury Azure stosu jest niezależne od siebie, więc chmury zawsze są traktowane jako aktywne z perspektywy infrastruktury. W takim przypadku wiele aktywnych wystąpień aplikacji są wdrażane na co najmniej jedną chmurę aktywne.

 - RTO: Brak przestojów
 - Cel punktu odzyskiwania: Brak utraty danych
 - Topologia wdrażania: aktywny/aktywny

### <a name="no-recovery"></a>Brak odzyskiwania

Niektóre aplikacje w danym środowisku nie może być konieczne ochrony przed nieplanowane przestoje lub utraty danych. Na przykład maszyn wirtualnych jest używany do tworzenia i testowania zazwyczaj nie jest konieczne do odzyskania. Należy zdecydować, czy bez ochrony dla aplikacji lub określonej maszyny Wirtualnej. Stos Azure oferuje replikacji maszyn wirtualnych lub kopii zapasowej podstawowej infrastruktury. Podobnie jak Azure, musisz wyrazić zgodę na ochronę dla każdej maszyny Wirtualnej w ramach każdej subskrypcji.

 - RTO: nieodwracalny
 - Cel punktu odzyskiwania: Całkowita utrata danych

## <a name="recommended-topologies"></a>Zalecane topologie

Ważne uwagi dotyczące wdrożenia usługi Azure stosu:

|     | Zalecenie | Komentarze |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Wykonywania kopii zapasowej/przywracania maszyn wirtualnych do zewnętrzne miejsce docelowe kopii zapasowej, już wdrożone w centrum danych. | Zalecane | Skorzystaj z istniejącą infrastrukturą kopii zapasowej i operacyjne umiejętności. Upewnij się, że rozmiar kopii zapasowej infrastruktury gotowego do ochrony dodatkowe wystąpienia maszyny Wirtualnej. Upewnij się, że infrastruktura kopii zapasowej nie znajduje się w pobliżu źródła. Maszyny wirtualne można przywrócić do źródła stosu Azure do dodatkowej wystąpienia stosu Azure lub usługi Azure. |
| Wykonywania kopii zapasowej/przywracania maszyn wirtualnych do zewnętrzne miejsce docelowe kopii zapasowej dedykowanych stos Azure | Zalecane | Możesz kupić nowa infrastruktura kopii zapasowej lub udostępnić dedykowanej infrastruktury kopii zapasowych Azure stosu. Upewnij się, że infrastruktura kopii zapasowej nie znajduje się w pobliżu źródła. Maszyny wirtualne można przywrócić do źródła stosu Azure do dodatkowej wystąpienia stosu Azure lub usługi Azure. |
| Maszyny wirtualne bezpośrednio do globalnego Azure lub dostawcy usługi zaufanej kopii zapasowych/przywracania | Zalecane | Tak długo, jak mogą zaspokoić prywatności danych i przepisami dotyczącymi działalności, można przechowywać kopie zapasowe w globalnej Azure lub dostawcy usługi zaufanej. W idealnym przypadku dostawcy usług jest uruchomiona stosu Azure tak uzyskiwanie spójności środowisko operacyjne podczas przywracania. |
| Maszyny wirtualne replikacji i pracy awaryjnej do oddzielnego wystąpienia usługi Azure stosu | Zalecane | W przypadku trybu failover konieczne będzie posiadanie drugi chmury Azure stosu pełnej funkcjonalności, aby uniknąć przestoju rozszerzonej aplikacji. |
| Maszyny wirtualne replikacji i pracy awaryjnej bezpośrednio do platformy Azure lub zaufany usługodawca | Zalecane | Spełniasz prywatności danych i przepisami dotyczącymi działalności, replikujesz danych globalnych Azure lub dostawcy usługi zaufanej. W idealnym przypadku dostawcy usług jest uruchomiona stosu Azure tak uzyskiwanie spójności środowisko operacyjne po pracy awaryjnej. |
| Wdrażanie miejsce docelowe kopii zapasowej na tej samej chmury Azure stosu z danych aplikacji | Niezalecane | Unikaj przechowywania kopii zapasowych w ramach tej samej chmury Azure stosu. Nieplanowane przestoje chmury pozwalają śledzić z danych podstawowych i dane kopii zapasowej. Jeśli wybierzesz do wdrożenia miejsce docelowe kopii zapasowej jako urządzenie wirtualne (do celów optymalizacji dla kopii zapasowej i przywracania), upewnij się, wszystkie dane stale są kopiowane do lokalizacji zewnętrznej kopii zapasowej. |
| Wdrażanie fizyczne urządzenia kopii zapasowej do tego samego stojaku zainstalowanym rozwiązania Azure stosu | Nieobsługiwane | W tym momencie nie można połączyć inne urządzenia na początku przełączników stojaku, które nie są częścią oryginalnym rozwiązaniu. |

## <a name="next-steps"></a>Kolejne kroki

W tym artykule podano ogólne wskazówki dotyczące ochrony użytkownika maszyn wirtualnych wdrożonych na stosie Azure. Informacje o ochrona maszyn wirtualnych użytkownika za pomocą usług Azure można znaleźć:

 - [Tworzyć kopie zapasowe plików i aplikacji na stosie Azure przy użyciu usługi Kopia zapasowa Azure](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure obsługi Utwórz kopię zapasową serwera Azure stosu](https://docs.microsoft.com/azure/backup/ ) 
 - [Azure Site Recovery obsługę Azure stosu](https://docs.microsoft.com/azure/site-recovery/)  

Aby dowiedzieć się więcej na temat produktów partnera, które zapewniają ochronę maszyny Wirtualnej na stosie Azure, zobacz "[ochrona aplikacji i danych na stosie Azure](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)."
