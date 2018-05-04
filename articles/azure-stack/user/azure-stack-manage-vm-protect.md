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
ms.date: 04/25/2018
ms.author: jeffgilb
ms.reviewer: hector.linares
ms.openlocfilehash: b49a8650611472b5e35c4bdf8373a1d7e3a45589
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Ochrona maszyn wirtualnych wdrożonych na stosie Azure
W tym artykule opisano wskazówki na temat ochrony użytkownika maszyn wirtualnych wdrożonych na stosie Azure.

Do ochrony przed utratą danych i nieplanowanych przestojów, należy wdrożyć plan odzyskiwania kopii zapasowej lub odzyskiwania po awarii dla użytkownika aplikacji i danych. Ten plan jest unikatowa dla każdej aplikacji, ale następuje struktury ustala ciągłość kompleksowe Twojej organizacji i strategia odzyskiwania (BC i odzyskiwania po awarii). Ogólne wzorców i rozwiązania dotyczące aplikacji dostępności i odporności odwołują się do [projektowanie aplikacji odporne na platformie Azure](https://docs.microsoft.com/azure/architecture/resiliency) w Centrum architektura platformy Azure.

## <a name="azure-stack-infrastructure-recovery"></a>Usługa Azure recovery infrastruktury stosu

Użytkownicy będą musieli oddzielnie ochrony ich maszyn wirtualnych z usług infrastruktury platformy Azure stosu.

Chmura Azure stosu jest w trybie offline przez dłuższy czas lub trwale nieodwracalny, musisz mieć plan miejsca do przechowywania aplikacji obsługi żądań użytkowników z minimalnym czasem przestojów. Operator chmury Azure stosu jest odpowiedzialny za przygotowanie planu odzyskiwania dla podstawowej infrastruktury stosu Azure i usług. Aby dowiedzieć się więcej, przeczytaj artykuł na temat [odzyskać przed utratą danych w wyniku katastrofy](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data). 

Plan odzyskiwania usługi infrastruktury platformy Azure stos nie zawiera odzyskiwania maszyny wirtualne użytkowników, konta magazynu i bazy danych. Jako właściciel aplikacji jest odpowiedzialny za wdrażanie plan odzyskiwania danych i aplikacji.
 
## <a name="sourcetarget-combinations"></a>Kombinacje źródłowego/docelowego

Każdej chmury Azure stosu jest wdrażany do jednego centrum danych. Oddzielne środowiska jest wymagana, więc można odzyskać aplikacji. Środowisko może być oddzielne chmury Azure stosu w różnych centrach danych lub w chmurze publicznej Azure. Twoje suwerenności danych i wymagania dotyczące zachowania poufności danych określi środowiska odzyskiwania dla aplikacji. Jak można włączyć ochrony dla każdej aplikacji, należy wybrać opcję odzyskiwania określonych dla każdej z nich. Aplikacje mogą mieć w jednej subskrypcji wykonywanie kopii zapasowych danych do innego centrum danych. W ramach innej subskrypcji można replikować danych w chmurze publicznej platformy Azure. 
 
Zaplanuj strategię odzyskiwania kopii zapasowej i odzyskiwania po awarii dla każdej aplikacji określić obiekt docelowy dla każdej aplikacji. Dzięki temu organizacji prawidłowo rozmiarze wymagana pojemność magazynu lokalnego i projektu zużycie w chmurze publicznej. 

|  | Globalne Azure | Azure stosu wdrożone w centrum danych dostawcy usług Kryptograficznych i obsługiwanej przez dostawcy usług Kryptograficznych | Azure stosu wdrożone w centrum danych klienta i obsługiwanej przez klienta |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure stosu wdrożone w centrum danych dostawcy usług Kryptograficznych i obsługiwanej przez dostawcy usług Kryptograficznych** | Maszyny wirtualne użytkowników są wdrażane na stosie Azure obsługiwane dostawcy usług Kryptograficznych. Maszyny wirtualne użytkowników są przywrócone z kopii zapasowej lub przekazała pracę awaryjną bezpośrednio na platformie Azure. | Dostawca usług Kryptograficznych działa podstawowych i pomocniczych wystąpień Azure stosu w ich własnych centrach danych. Maszyny wirtualne użytkowników są przywracane lub przejścia w tryb failover między dwoma wystąpieniami stosu Azure. | Dostawca usług Kryptograficznych działa Azure stosu w lokacji głównej. Centrum danych klienta jest elementem docelowym przywracania lub pracy awaryjnej. |
| **Azure stosu wdrożone w centrum danych klienta i obsługiwanej przez klienta** | Maszyny wirtualne użytkowników są wdrażane do klienta obsługiwane stosu Azure. Maszyny wirtualne użytkowników są przywrócone z kopii zapasowej lub przekazała pracę awaryjną bezpośrednio na platformie Azure. | Klient działa podstawowych i pomocniczych wystąpień Azure stosu w ich własnych centrach danych. Maszyny wirtualne użytkowników są przywracane lub przejścia w tryb failover między dwoma wystąpieniami stosu Azure. | Klient działa Azure stosu w lokacji głównej. Dostawca usług Kryptograficznych w centrum danych jest elementem docelowym przywracania lub pracy awaryjnej. |

![Kombinacje źródłowy i docelowy](media\azure-stack-manage-vm-backup\vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Cele odzyskiwania aplikacji

Należy określić ilość utraconych czasem przestoju oraz danych, które organizacja może tolerować dla każdej aplikacji, aby określić, najlepszym rozwiązaniem do ochrony aplikacji na stosie Azure. Ilość downtown i ilość wartości utraty danych są niezbędne do identyfikowania celu opracowanie i wdrożenie planu odzyskiwania, który minimalizuje wpływ na działalność swojej organizacji. Dla każdej aplikacji należy wziąć pod uwagę:  
 - **Celu czasu odzyskiwania (RTO)**  
RTO to maksymalny czas dopuszczalne aplikacji mogą być niedostępne po incydentu. Jeśli Twój czas RTO wynosi 90 minut, musisz móc przywrócić aplikację do stanu roboczego w ciągu 90 minut od początku awarii. Jeśli masz mało RTO może przechowywać drugie wdrożenie stale uruchomione w stan wstrzymania, aby zapewnić ochronę przed regionalnej awarii.
 - **Cel punktu odzyskiwania (RPO)**  
Cel punktu odzyskiwania to maksymalny czas trwania utraty danych, która jest dopuszczalny podczas awarii. Jeśli na przykład przechowujesz dane w pojedynczej bazie danych bez replikacji do innych baz danych i co godzinę wykonujesz kopię zapasową, możesz stracić nawet godzinę danych.
 
RTO i RPO są wymaganiami biznesowymi. Przeprowadzenia oceny ryzyka do definiowania RTO aplikacji i cel punktu odzyskiwania. Inny wspólnej Metryka to **średniego czasu Odzyskaj** (MTTR), która jest średni czas potrzebny do przywrócenia aplikacji po awarii. MTTR to empiryczny fakt dotyczący systemu. Jeśli czas MTTR przekracza czas RTO, wówczas awaria w systemie spowoduje niedopuszczalną przerwę w działalności biznesowej, ponieważ nie będzie możliwe przywrócenie systemu w ramach określonego czasu RTO.

### <a name="backup-restore"></a>Przywracanie kopii zapasowej

Najbardziej typowe schemat ochrony dla aplikacji opartych na maszyna wirtualna ma używać oprogramowania kopii zapasowej. Tworzenie kopii zapasowych maszyn wirtualnych zwykle zawiera system operacyjny, konfiguracja systemu operacyjnego, plików binarnych aplikacji i danych aplikacji. Kopie zapasowe są tworzone przez tworzenie migawki woluminu, dysków lub całą maszynę Wirtualną. Stosu Azure elastyczność tworzenia kopii zapasowych z w kontekście systemu operacyjnego gościa lub z magazynu Azure stosu i obliczeniowe interfejsów API. Stos Azure nie obsługuje pobierania tworzenia kopii zapasowych na poziomie funkcji hypervisor. 
 
![Restor kopii zapasowej](media\azure-stack-manage-vm-backup\vm_backupdataflow_03.png)
 
Odzyskiwanie aplikacji wymaga Przywracanie przynajmniej jednej maszyny wirtualnej do tej samej chmury, lub do nowej chmury. Możesz zastosować chmury w centrum danych lub w chmurze publicznej. Chmury, które są przeznaczone jest całkowicie w obrębie formantu i opierają się na wymagań suwerenności i poufności informacji w danych. 
 
 - RTO: Przestój mierzony w godzinach 
 - Cel punktu odzyskiwania: Utraty danych zmiennej (w zależności od częstotliwości tworzenia kopii zapasowej)
 - Topologia wdrażania: aktywny/pasywny 

#### <a name="planning-your-backup-strategy"></a>Planowanie strategii tworzenia kopii zapasowych

Planowanie strategii tworzenia kopii zapasowych i definiowania wymagań skali rozpoczyna się od określającą liczbę wystąpień maszyn wirtualnych, które muszą być chronione. Kopie zapasowe wszystkich maszyn wirtualnych na wszystkich serwerach w środowisku są często strategii. Jednak z stosu Azure są niektóre maszyny wirtualne, które należy wykonać kopię zapasową. Na przykład maszyn wirtualnych w zestawie skalowania są traktowane jako tymczasowych zasoby, które mogą występować i przejść, czasami bez powiadomienia. Trwałe dane, które wymagają ochrony są przechowywane w oddzielnych repozytorium, takie jak Magazyn bazy danych lub obiektu. Ważne uwagi dotyczące tworzenia kopii zapasowej maszyn wirtualnych na stosie Azure:

 - **Kategoryzacji**
    - Należy wziąć pod uwagę modelu, w której użytkownicy będą chcieli w kopii zapasowej maszyny Wirtualnej.
    - Zdefiniuj odzyskiwania, który SLA na podstawie priorytetu aplikacji lub wpływ na działalność biznesową.
 - **Skalowanie**
    - Podczas dołączania dużą liczbą nowych maszyn wirtualnych (jeśli kopia zapasowa jest wymagana), należy wziąć pod uwagę rozłożona kopii zapasowych.
    - Ocena kopii zapasowej produktów, które można skutecznie przechwytywanie i przesyłanie danych kopii zapasowej, aby zminimalizować zawartości zasobów w ramach rozwiązania.
    - Ocena kopii zapasowej produktów, które skutecznie przechowywać dane kopii zapasowej za pomocą przyrostowej lub różnicowej kopii zapasowej aby zminimalizować konieczności prowadzenia pełnych kopii zapasowych na wszystkich maszynach wirtualnych w środowisku.
 - **Przywracanie**
    - Dyski wirtualne, dane aplikacji w ramach istniejącej maszyny Wirtualnej, lub całego zasobu maszyny Wirtualnej i skojarzone dyski wirtualne, można przywrócić kopii zapasowej produktów. Schematu przywracania, który należy zależy od tego, jak zaplanować przywrócić aplikację i będzie miało wpływ na czas odzyskiwania aplikacji. Na przykład może być łatwiejsze do wdrożenia programu SQL server z szablonu, a następnie przywróć bazy danych zamiast Przywracanie całą maszynę Wirtualną lub zestaw maszyn wirtualnych.


### <a name="replicationmanual-failover"></a>Replikacja/ręczną pracę awaryjną

Alternatywne podejście do obsługi wysokiej dostępności jest replikowanie maszyn wirtualnych z aplikacji do innej i zależne od ręcznie wyzwalanych trybu failover. Replikacja systemu operacyjnego, plików binarnych aplikacji i danych aplikacji można przeprowadzić na poziomie systemu operacyjnego gościa lub poziom maszyny Wirtualnej. Zarządzanie trybem failover przy użyciu dodatkowego oprogramowania niezależnie od aplikacji.
 
Z tej metody aplikacja zostanie wdrożona tylko w jednej chmury. Maszyna wirtualna jest następnie replikowane do innych chmury jeden w jednego z centrów danych lub w chmurze publicznej. Jeśli tryb failover jest wyzwalany, pomocniczej maszyn wirtualnych należy włączony w chmurze drugiego. W niektórych przypadkach przejście w tryb failover Tworzenie maszyn wirtualnych i dołącz je do dysków. Ten proces może zająć dużo czasu, szczególnie w przypadku aplikacji wielowarstwowych sekwencji rozruchu określonych. Może to być dodatkowe kroki, które należy wykonać, zanim aplikacja jest gotowa do uruchomienia, obsługi żądań.

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

Azure stosu dostępności usługi nadmiarowości i infrastruktury fizycznej tylko ochronę przed sprzętu poziomu błędów, takich jak dysk nie powiodło się nie powiodło się zasilania, nie port sieci lub nie powiodło się węzeł. Jednak jeśli aplikacja musi być zawsze dostępny i nigdy nie może spowodować utratę danych, musisz zaimplementować odporność na uszkodzenia natywnie w aplikacji lub zapewnić odporność na uszkodzenia z dodatkowe oprogramowanie. 
 
Najpierw należy upewnić się, że maszyny wirtualne są wdrażane przy użyciu skalowania aplikacji ustawia chronić przed awariami poziomu węzła. Aby zapewnić ochronę przed chmury przechodzi do trybu offline, tej samej aplikacji musi już zostać wdrożony do nowej chmury, więc będzie możliwe kontynuowanie obsługi żądań bez przeszkód. Zazwyczaj jest to określane wdrożenia aktywny aktywny.
 
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

W tym artykule firma Microsoft omówione wskazówki na temat ochrony użytkownika maszyn wirtualnych wdrożonych na stosie Azure. Aby uzyskać więcej informacji na temat ochrony maszyn wirtualnych przy użyciu usług Azure można skorzystać z:
 - [Tworzyć kopie zapasowe plików i aplikacji na stosie Azure przy użyciu usługi Kopia zapasowa Azure](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure obsługi Utwórz kopię zapasową serwera Azure stosu](https://docs.microsoft.com/azure/backup/ ) 
 - [Azure Site Recovery obsługę Azure stosu](https://docs.microsoft.com/azure/site-recovery/)  
 
Aby dowiedzieć się więcej na temat produktów partnera, które zapewniają ochronę maszyny Wirtualnej na stosie Azure, zobacz "[ochrona aplikacji i danych na stosie Azure](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)."
