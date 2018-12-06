---
title: Ochrona maszyn wirtualnych wdrożonych w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Wskazówki na temat ochrony maszyn wirtualnych wdrożonych w usłudze Azure Stack.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: hector.linares
ms.openlocfilehash: 87ba13334b037f7eb47264a120bb91b2be5f8a79
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963917"
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Ochrona maszyn wirtualnych wdrożonych w usłudze Azure Stack

Użyj tego artykułu jako przewodnika do opracowywania planu ochrony maszyn wirtualnych (VM), które użytkownicy wdrożenia w usłudze Azure Stack.

Do ochrony przed utratą danych i nieplanowanych przestojów, konieczne jest wdrożenie planu odzyskiwania kopii zapasowej lub odzyskiwania po awarii dla aplikacji użytkownika i ich danych. Ten plan muszą być unikatowe dla każdej aplikacji, ale platforma ustanowione przez kompleksowy zestaw funkcji ciągłości Twojej organizacji i strategia odzyskiwania (BC i odzyskiwania po awarii) jest zgodna. To dobry punkt wyjścia [projektowanie aplikacji odpornych na błędy dla platformy Azure](https://docs.microsoft.com/azure/architecture/resiliency), co umożliwia ogólne wzorce i rozwiązania dostępności aplikacji i zwiększa odporność.

>[!IMPORTANT]
> Testowanie planów odzyskiwania kopii zapasowych i odzyskiwania po awarii w sposób ciągły. Musisz tutaj, aby upewnić się, że:
> * Plany działają
> * Plany nadal spełniają wymagania, które zostały zaprojektowane dla.

## <a name="azure-stack-infrastructure-recovery"></a>Usługa Azure Stack infrastruktury odzyskiwania

Użytkownicy są odpowiedzialni za ochronę maszyn wirtualnych osobno w usługach infrastruktury Azure Stack.

Plan odzyskiwania dla usług infrastruktury Azure Stack **nie** obejmują odzyskiwanie maszyn wirtualnych użytkowników, kont magazynu lub baz danych. Jako właściciel aplikacji jesteś odpowiedzialny za realizację planu odzyskiwania w przypadku aplikacji i danych.

Czy chmurą usługi Azure Stack jest w trybie offline przez dłuższy czas trwale nieodwracalny, musisz mieć odzyskiwania planu w miejscu, które:

* Zapewnia minimalnych przestojach
* Przechowuje newralgicznych maszyn wirtualnych, takich jak serwery bazy danych, sieci
* Umożliwia aplikacjom zapewnić obsługę żądań użytkowników

Operator chmury Azure Stack jest odpowiedzialny za tworzenie planu odzyskiwania dla podstawowej infrastruktury Azure Stack i usług. Aby dowiedzieć się więcej, przeczytaj artykuł [sprawności po utracie danych w wyniku katastrofy](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data).

## <a name="sourcetarget-combinations"></a>Źródłowymi/docelowymi kombinacje

Każda chmura usługi Azure Stack jest wdrażany w jednym centrum danych. Oddzielnego środowiska jest wymagany, dzięki czemu można odzyskać Twojej aplikacji. Środowisko odzyskiwania może być innej chmury Azure Stack w innym centrum danych lub chmury publicznej platformy Azure. Swoje wymagania ochrony prywatności danych i niezależność danych określi środowisko odzyskiwania aplikacji. Jak można włączyć ochrony dla każdej aplikacji, masz swobodę wyboru określonych opcji odzyskiwania dla każdej z nich. Aplikacje mogą mieć w jednej subskrypcji, tworzenie kopii zapasowych danych do innego centrum danych. W innej subskrypcji można replikować dane do chmury publicznej platformy Azure.

Zaplanuj strategię odzyskiwania kopii zapasowych i odzyskiwania po awarii dla każdej aplikacji określić cel dla każdej aplikacji. Plan odzyskiwania ułatwi Twojej organizacji, prawidłowo rozmiar magazynu wymaganą pojemność lokalnej i projektu zużycia w chmurze publicznej.

|  | Global Azure | Usługa Azure Stack wdrożone w centrum danych dostawcy usług Kryptograficznych i eksploatowane przy pomocy dostawcy CSP | Usługa Azure Stack wdrożone w centrum danych klienta i obsługiwane przez klienta |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Usługa Azure Stack wdrożone w centrum danych dostawcy usług Kryptograficznych i eksploatowane przy pomocy dostawcy CSP** | Użytkownik maszyny wirtualne są wdrażane do dostawcy usług Kryptograficznych obsługiwane usługi Azure Stack. Maszyny wirtualne użytkowników przywróconej z kopii zapasowej lub Failover bezpośrednio na platformie Azure. | Dostawcy usług Kryptograficznych działa głównych i dodatkowych wystąpień usługi Azure Stack w ich własnych centrach danych. Maszyny wirtualne użytkowników są przywracane lub przełączone w tryb failover między dwoma wystąpieniami usługi Azure Stack. | Dostawcy usług Kryptograficznych działa w usłudze Azure Stack w lokacji głównej. Centrum danych klienta jest celem przywracania lub pracy awaryjnej. |
| **Usługa Azure Stack wdrożone w centrum danych klienta i obsługiwane przez klienta** | Użytkownik maszyny wirtualne są wdrażane na klienta obsługiwane w usłudze Azure Stack. Maszyny wirtualne użytkowników przywróconej z kopii zapasowej lub Failover bezpośrednio na platformie Azure. | Klient działa głównych i dodatkowych wystąpień usługi Azure Stack w ich własnych centrach danych. Maszyny wirtualne użytkowników są przywracane lub przełączone w tryb failover między dwoma wystąpieniami usługi Azure Stack. | Klient działa w usłudze Azure Stack w lokacji głównej. Centrum danych dostawcy rozwiązań w Chmurze jest celem przywracania lub pracy awaryjnej. |

![Kombinacje źródło cel](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Cele odzyskiwania aplikacji

Należy określić ilość przestoju i utraty danych, które Twoja organizacja może tolerować dla każdej aplikacji. Za kwantyfikacja przestoju i utraty danych można utworzyć plan odzyskiwania, które minimalizuje wpływ awarii w Twojej organizacji. Dla każdej aplikacji należy wziąć pod uwagę:

 - **Cel czasu odzyskiwania (RTO)**  
Czas RTO wynosi maksymalny dopuszczalny czas, który aplikacja może być niedostępna po incydencie. Na przykład RTO 90 minut oznacza, że użytkownik musi być możliwe do przywrócenia aplikacji do stanu roboczego w ciągu 90 minut od początku awarii. Jeśli masz niski wskaźnik RTO, możesz przechowywać stale uruchomione w stan wstrzymania, aby zapewnić ochronę przed awarią regionalną drugie wdrożenie.
 - **Cel punktu odzyskiwania (RPO)**  
Cel punktu odzyskiwania jest maksymalny czas trwania utraty danych, który jest dopuszczalny podczas awarii. Jeśli na przykład przechowujesz dane w pojedynczej bazie danych bez replikacji do innych baz danych i co godzinę wykonujesz kopię zapasową, możesz stracić nawet godzinę danych.

RTO i RPO są wymaganiami biznesowymi. Należy przeprowadzić ocenę ryzyka, aby zdefiniować RTO i RPO aplikacji.

Jest kolejną metrykę **średniego czasu odzyskiwanie** (MTTR), czyli Średni czas potrzebny do przywrócenia aplikacji po awarii. MTTR to empiryczny wartości dla systemu. Jeśli czas MTTR przekracza czas RTO, wówczas awaria w systemie spowoduje niedopuszczalną przerwę w działalności biznesowej, ponieważ nie będzie możliwe przywrócenie systemu w ramach określonego czasu RTO.

### <a name="backup-restore"></a>Przywracanie kopii zapasowej

Najbardziej typowe schemat ochrony dla aplikacji opartych na maszynie Wirtualnej jest użycie oprogramowania do tworzenia kopii zapasowej. Tworzenie kopii zapasowej maszyny Wirtualnej zwykle zawiera system operacyjny, konfiguracja systemu operacyjnego, plików binarnych aplikacji i danych aplikacji. Kopie zapasowe są tworzone przez wykonanie migawki woluminów, dysków lub całą maszynę Wirtualną. Dzięki usłudze Azure Stack elastyczność tworzenia kopii zapasowych z w ramach systemu operacyjnego gościa lub z magazynu usługi Azure Stack i obliczenia interfejsów API. Usługa Azure Stack nie obsługuje wykonywanie kopii zapasowych, na poziomie funkcji hypervisor.
 
![Kopia zapasowa restor](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

Odzyskiwanie aplikacji wymaga, aby przywracanie przynajmniej jednej maszyny wirtualnej do tej samej chmurze lub do nowej chmury. Można wskazać chmurę w centrum danych lub chmury publicznej. Chmury, któremu możesz wybrać, jest w całości w ramach kontroli nad i opiera się na wymagania dotyczące ochrony prywatności i niezależność danych.
 
 - Cel czasu odzyskiwania: Przestój mierzonego w godzinach
 - Cel punktu odzyskiwania: Utraty danych zmiennej (w zależności od częstotliwości wykonywania kopii zapasowych)
 - Topologia wdrażania: aktywny/pasywny

#### <a name="planning-your-backup-strategy"></a>Planowanie strategii tworzenia kopii zapasowych

Planowanie strategii tworzenia kopii zapasowych i definiowanie wymagań skali rozpoczyna się od określającą liczbę wystąpień maszyn wirtualnych, które muszą być chronione. Tworzenie kopii zapasowych wszystkich maszyn wirtualnych na wszystkich serwerach w środowisku jest typowych strategii. Dzięki usłudze Azure Stack, istnieją niektóre maszyny wirtualne, które należy wykonać kopię. Na przykład maszyny wirtualne w zestawie skalowania są traktowane jako efemeryczne zasoby, które mogą występować i przejść, czasami bez powiadomienia. Trwałość danych, które muszą być chronione znajduje się w oddzielnych repozytorium, takich jak sklep bazy danych lub obiektu.

Ważne uwagi dotyczące tworzenia kopii zapasowych maszyn wirtualnych w usłudze Azure Stack:

 - **Kategoryzacji**
    - Należy wziąć pod uwagę modelu, w którym użytkownicy Zezwól na korzystanie z kopii zapasowych maszyn wirtualnych.
    - Zdefiniuj odzyskiwania Umowa dotycząca poziomu usług (SLA) na podstawie priorytetu aplikacji lub wpływ na działalność.
 - **Skalowanie**
    - Podczas dołączania opartego dużą liczbą nowych maszyn wirtualnych (jeśli jest to kopia zapasowa jest wymagana), należy wziąć pod uwagę samodzielnym kopii zapasowych.
    - Ocena kopii zapasowej produktów, które mogą efektywnie przechwytywanie i przesyłanie danych kopii zapasowej, aby zminimalizować zawartości zasobów rozwiązania.
    - Oceń produkty kopii zapasowej, które wydajnie przechowywać dane kopii zapasowej za pomocą przyrostowych lub różnicowej kopii zapasowej, aby zminimalizować konieczność wykonywania pełnych kopii zapasowych dla wszystkich maszyn wirtualnych w środowisku.
 - **Przywracanie**
    - Produkty kopii zapasowej można przywrócić dyski wirtualne, dane aplikacji w ramach istniejącej maszyny Wirtualnej lub całego zasobu maszyny Wirtualnej i powiązanymi dyskami wirtualnymi. Schemat przywracania, czego potrzebujesz zależy od tego, jak zamierzasz przywrócić aplikację i będzie mieć wpływ na czas odzyskiwania aplikacji. Na przykład może być łatwiejsze do ponownego wdrożenia programu SQL server na podstawie szablonu, a następnie przywróć bazy danych zamiast całej maszyny Wirtualnej lub zestawu maszyn wirtualnych.

### <a name="replicationmanual-failover"></a>Replikacja/ręcznej pracy awaryjnej

Alternatywne podejście do obsługi wysokiej dostępności jest replikacja maszyn wirtualnych aplikacji do innej chmury i polegać na ręcznej pracy awaryjnej. Replikacja systemu operacyjnego, plików binarnych aplikacji i danych aplikacji można wykonać na poziomie maszyny Wirtualnej lub na poziomie systemu operacyjnego gościa. Przełączenie w tryb failover odbywa się przy użyciu dodatkowego oprogramowania, który nie jest częścią aplikacji.

Dzięki tej metodzie aplikacja jest wdrażana w chmurze jeden i jego maszyny Wirtualnej są replikowane do innych chmury. W przypadku przejścia w tryb failover zostaje wyzwolona, pomocniczej maszyny wirtualne muszą włączony w chmurze drugiego. W niektórych scenariuszach tryb failover tworzy dyski maszyn wirtualnych oraz dołącza do nich. Ten proces może potrwać długo, szczególnie w przypadku aplikacji wielowarstwowych, wymagającego sekwencji rozruchu określone. Można również kroki, które mają zostać wykonane, zanim aplikacja jest gotowa do uruchomienia, obsługi żądań.

![Replikacja ręcznej pracy awaryjnej](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - Cel czasu odzyskiwania: Przestojów w minutach
 - Cel punktu odzyskiwania: Utraty danych zmiennej (w zależności od częstotliwości replikacji)
 - Topologia wdrożenia: autonomiczną przez aktywny/pasywny
 
### <a name="high-availabilityautomatic-failover"></a>Wysoka dostępność/automatyczny tryb failover

W przypadku aplikacji, w którym firmy mogą tolerować kilka sekund lub minut przestojów i minimalną utratą danych należy wziąć pod uwagę konfiguracji o wysokiej dostępności. Aplikacje o wysokiej dostępności są przeznaczone do szybkiego i automatycznie odzyskać z błędami. Dla błędów sprzętu lokalnego infrastruktury Azure Stack implementuje wysokiej dostępności w ramach sieci fizycznej za pomocą dwóch pierwszych stojak przełączników. Dla błędów poziomu obliczeniowego usługi Azure Stack używa wielu węzłów w jednostce skalowania. Na poziomie maszyny Wirtualnej umożliwia zestawów skalowania w połączeniu z domenami błędów upewnij się, że awarie węzłów nie może wyłączyć aplikację.

W połączeniu z zestawami skalowania aplikacji należy zapewnić wysoką dostępność w sposób natywny lub obsługuje klastrowanie oprogramowania. Na przykład Microsoft SQL Server obsługuje wysoką dostępność natywnie dla baz danych przy użyciu trybu zatwierdzania synchronicznego. Jednak w przypadku replikacji asynchronicznej może obsługiwać tylko, następnie nastąpi utrata danych. Aplikacje można wdrożyć w taki sposób, w klastrze trybu failover, gdzie klastrowania oprogramowania obsługuje automatycznej pracy awaryjnej w aplikacji.

W ten sposób aplikacja jest aktywny tylko w jednej chmury, ale oprogramowanie zostanie wdrożone do wielu chmur. Inne chmury są w trybie gotowości gotowe do uruchomienia aplikacji, gdy zostanie wyzwolony przełączenie w tryb failover.

 - Cel czasu odzyskiwania: Przestój mierzony w sekundach
 - Cel punktu odzyskiwania: Minimalną utratą danych
 - Topologia wdrożenia: autonomiczną przez aktywny/aktywny

### <a name="fault-tolerance"></a>Odporność na uszkodzenia

Usługi Azure Stack dostępności usługi nadmiarowość i infrastruktury fizycznej chronić tylko względem sprzętu poziomu błędów/błędy takie dysku, zasilacz, port sieci lub węzeł. Jednak jeśli aplikacja musi być zawsze dostępny i nigdy nie może utracić wszelkie dane, musisz zaimplementować odporności na uszkodzenia natywnie w aplikacji lub użyj dodatkowego oprogramowania, aby zapewnić odporność na uszkodzenia.

Najpierw należy upewnić się, że ustawia zapewnić ochronę przed awariami węzła na poziomie aplikacji, którą maszyny wirtualne są wdrażane przy użyciu skalowania. Aby chronić przed chmury, przechodzi do trybu offline, ta sama aplikacja już należy wdrożyć do nowej chmury, dzięki czemu można kontynuować, obsługi żądań bez przeszkód korzystać z programu. Ten model jest zwykle określany wdrożenie aktywny aktywny.

Należy pamiętać, że każda chmura usługi Azure Stack znajduje niezależne od siebie nawzajem, dlatego chmury są zawsze traktowane jako aktywne z perspektywy infrastruktury. W tym przypadku wiele aktywnych wystąpień aplikacji są wdrażane do co najmniej jedna chmura active.

 - Cel czasu odzyskiwania: Bez przestojów.
 - Cel punktu odzyskiwania: Bez utraty danych
 - Topologia wdrażania: aktywny/aktywny

### <a name="no-recovery"></a>Brak odzyskiwania

Niektóre aplikacje w danym środowisku nie może być konieczne ochrony przed nieplanowane przestoje lub utraty danych. Na przykład maszyny wirtualne używane do tworzenia i testowania zazwyczaj nie trzeba go odzyskać. Należy zdecydować, czy bez ochrony dla aplikacji lub określonej maszyny Wirtualnej. Usługa Azure Stack nie oferuje tworzenia kopii zapasowej lub replikacji maszyn wirtualnych z podstawową infrastrukturą. Podobnie jak na platformie Azure, musisz wyrazić zgodę na ochronę dla każdej maszyny Wirtualnej w każdej subskrypcji.

 - Cel czasu odzyskiwania: nieodwracalny
 - Celu punktu odzyskiwania: Całkowita utrata danych

## <a name="recommended-topologies"></a>Zalecane topologie

Istotne zagadnienia dotyczące wdrożenia usługi Azure Stack:

|     | Zalecenie | Komentarze |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| / Przywracania kopii zapasowej maszyn wirtualnych do zewnętrznych docelowa kopia zapasowa już wdrożone w Twoim centrum danych | Zalecane | Skorzystaj z istniejącej infrastruktury kopii zapasowych i operacyjne umiejętności. Upewnij się, że rozmiar infrastruktury kopii zapasowych, aby był gotowy do ochrony dodatkowych wystąpień maszyn wirtualnych. Upewnij się, że infrastruktura kopii zapasowej nie znajduje się w bliskim sąsiedztwie ze źródłem. Maszyny wirtualne można przywrócić do źródła usługi Azure Stack do dodatkowej wystąpienia usługi Azure Stack lub na platformie Azure. |
| / Przywracania kopii zapasowej maszyn wirtualnych do zewnętrznych docelowa kopia zapasowa dedykowanego dla usługi Azure Stack | Zalecane | Możesz kupić nowej infrastruktury kopii zapasowych lub aprowizacja dedykowana infrastruktura kopii zapasowej dla usługi Azure Stack. Upewnij się, że infrastruktura kopii zapasowej nie znajduje się w bliskim sąsiedztwie ze źródłem. Maszyny wirtualne można przywrócić do źródła usługi Azure Stack do dodatkowej wystąpienia usługi Azure Stack lub na platformie Azure. |
| / Przywracania kopii zapasowej maszyn wirtualnych bezpośrednio do globalnej platformy Azure lub dostawcy usług zaufanych | Zalecane | Tak długo, jak możesz zaspokoić swoje wymagania prawne i prywatność danych, kopie zapasowe można przechowywać w globalnej platformy Azure lub dostawcy usług zaufanych. Najlepiej dostawcy usług uruchomiono również usługę Azure Stack, więc otrzymujesz środowisko operacyjne spójności podczas przywracania. |
| Replikacja i pracy awaryjnej maszyn wirtualnych, do oddzielnego wystąpienia usługi Azure Stack | Zalecane | W przypadku pracy awaryjnej należy mieć drugi chmurą usługi Azure Stack pełni funkcjonalne, więc można uniknąć przerwy w działaniu aplikacji rozszerzonej. |
| Replikacja i pracy awaryjnej maszyn wirtualnych bezpośrednio platformy Azure lub dostawcy usług zaufanych | Zalecane | Tak długo, jak możesz zaspokoić swoje wymagania prawne i prywatność danych, można replikować dane do globalnej platformy Azure lub dostawcy usług zaufanych. Najlepiej dostawcy usług uruchomiono również usługę Azure Stack, więc otrzymujesz spójności w środowisku operacyjnych po włączeniu trybu failover. |
| Wdrażanie kopii zapasowych lokalizację docelową na tej samej chmury Azure Stack przy użyciu danych aplikacji | Nie zaleca się | Unikaj przechowywania kopii zapasowych w ramach tej samej chmurze usługi Azure Stack. Nieplanowane przestoje chmury mogą informować Cię z danych podstawowych i dane kopii zapasowej. Jeśli zdecydujesz się wdrożyć miejsce docelowe kopii zapasowej jako urządzenie wirtualne (na potrzeby optymalizacji dla kopii zapasowej i przywracania), upewnij się, wszystkie dane stale są kopiowane do lokalizacji zewnętrznej kopii zapasowej. |
| Wdrażanie kopii zapasowych urządzenia fizycznego do tego samego stojaku zainstalowanym rozwiązania usługi Azure Stack | Nieobsługiwane | W tym momencie nie można połączyć inne urządzenia na początku przełączników stojaku, które nie są częścią oryginalnej rozwiązania. |

## <a name="next-steps"></a>Kolejne kroki

W tym artykule podano ogólne wskazówki dotyczące ochrony użytkownik maszyn wirtualnych wdrożonych w usłudze Azure Stack. Aby dowiedzieć się, jak przy użyciu usług platformy Azure, aby chronić maszyny wirtualne użytkowników można znaleźć:

 - [Usługa Azure Backup umożliwia tworzenie kopii zapasowych plików i aplikacji w usłudze Azure Stack](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Pomoc techniczna platformy Azure Backup Server dla usługi Azure Stack](https://docs.microsoft.com/azure/backup/ ) 
 - [Pomoc techniczna platformy Azure Site Recovery dla usługi Azure Stack](https://docs.microsoft.com/azure/site-recovery/)  

Aby dowiedzieć się więcej na temat produktów partnerów, które zapewniają ochronę maszyn wirtualnych w usłudze Azure Stack, zobacz "[ochrona aplikacji i danych w usłudze Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)."
