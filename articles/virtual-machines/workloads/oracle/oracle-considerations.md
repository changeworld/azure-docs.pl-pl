---
title: Rozwiązania firmy Oracle w systemie Microsoft Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat obsługiwanych konfiguracji i ograniczenia dotyczące rozwiązania Oracle w systemie Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/02/2018
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 945ba9b2ba4dbc22941ca6b105417f591f2dd837
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835417"
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Rozwiązania Oracle i ich wdrażania w systemie Microsoft Azure
W tym artykule omówiono informacje wymagane do pomyślnego wdrożenia w różnych rozwiązaniach Oracle w systemie Microsoft Azure. Te rozwiązania są oparte na obrazach maszyn wirtualnych opublikowanych przez Oracle w witrynie Azure Marketplace. Aby uzyskać listę dostępnych obrazów, uruchom następujące polecenie:
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
Od 6-16-2017 listy obrazów są następujące:
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Te obrazy są traktowane jako "Bring Your Own License" i jako takie tylko opłata wyniesie zasobów obliczeniowych, magazynu i sieci kosztów poniesionych przez uruchomienie maszyny Wirtualnej.  Zakłada się, że są poprawnie licencjonowane oprogramowanie Oracle i mieć bieżącej umowy dotyczącej pomocy technicznej w miejscu z firmą Oracle. Oracle, ma gwarancję funkcji przeniesienia licencji w ze środowiska lokalnego do platformy Azure. Zobacz opublikowanego [Oracle i Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) należy pamiętać, aby uzyskać szczegółowe informacje na temat przenoszenia licencji. 

Osoby także podstawowa swoje rozwiązania na obrazach niestandardowych tworzenie od podstaw na platformie Azure, lub Przekaż obraz niestandardowy z ich w środowisku lokalnym.

## <a name="support-for-jd-edwards"></a>Obsługa rozwiązań JD Edwards
Zgodnie z Uwaga obsługę Oracle [2178595.1 identyfikator dokumentu](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) , JD Edwards EnterpriseOne wer. 9.2 i nowszej są obsługiwane w **oferty w chmurze wszystkie publiczne** który spełnia ich określonych `Minimum Technical Requirements` (MTR).  Należy utworzyć niestandardowe obrazy, spełniające ich specyfikacje MTR zgodności aplikacji systemu operacyjnego i oprogramowania. 

## <a name="oracle-database-virtual-machine-images"></a>Obrazy maszyn wirtualnych na bazę danych Oracle
Firma Oracle obsługuje uruchomione wersje Oracle DB 12.1 Standard i Enterprise na platformie Azure w obrazach maszyn wirtualnych na podstawie w systemie Oracle Linux.  Aby uzyskać najlepszą wydajność dla obciążeń produkcyjnych, Oracle dB na platformie Azure należy prawidłowo rozmiar obrazu maszyny Wirtualnej i użycie usługi Managed Disks, która jest wspierana przez usługę Premium Storage. Do instrukcji dotyczących szybko rozpocząć Oracle DB pracę na platformie Azure przy użyciu programu Oracle opublikowanego obrazu maszyny Wirtualnej [spróbuj Przewodnik Szybki Start bazy danych Oracle](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opcje konfiguracji dołączonych dysków

Dołączonych dysków zależy od usługi Azure Blob storage. Każdy dysk standardowy jest w stanie teoretyczne Maksimum około 500 operacji wejścia/wyjścia na sekundę (IOPS). Oferty dysku premium była preferowana dla różnych obciążeń bazy danych o wysokiej wydajności i można osiągnąć maksymalnie 5000 operacji We/Wy na dysk. Chociaż można użyć pojedynczego dysku, jeśli który spełnia Twoje potrzeby związane z wydajnością — może poprawić skuteczne wydajność operacji We/Wy, jeśli używasz wielu dołączonych dysków, rozkłada ich dane z bazy danych, a następnie użyj Oracle automatyczne Storage Management (ASM). Zobacz [Omówienie automatycznego przechowywania Oracle](https://www.oracle.com/technetwork/database/index-100339.html) dla Oracle ASM określonych informacji. Na przykład, jak zainstalować i skonfigurować rozwiązanie Oracle ASM na maszynie Wirtualnej systemu Linux platformy Azure — wypróbuj [Instalowanie i konfigurowanie programu Oracle automatyczne zarządzanie magazynem](configure-oracle-asm.md) samouczka.

## <a name="oracle-real-application-cluster-oracle-rac"></a>Klaster rzeczywistej aplikacji Oracle (Oracle RAC)
Oracle RAC zaprojektowano w celu złagodzenia awarii jednego węzła w konfiguracji klastra wielowęzłowego w środowisku lokalnym. Opiera się na dwie technologie w środowisku lokalnym, które nie są w natywnych środowiskach chmury publicznej w hiperskali: rzutowanie wielu sieci, jak i udostępnionego dysku. Jeśli rozwiązanie bazy danych wymaga RAC Oracle na platformie Azure, konieczne będzie 3 oprogramowania umożliwiające tych technologii. Aby uzyskać więcej informacji na temat Oracle RAC, zobacz [stronie rozwiązania FlashGrid](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Zagadnienia wysoką dostępność i odzyskiwanie po awarii
Korzystając z bazami danych Oracle na platformie Azure, ponosisz odpowiedzialność za wdrażanie wysoką dostępność i odzyskiwanie po awarii rozwiązania odzyskiwania w celu uniknięcia żadnych przestojów. 

Wysoka dostępność i odzyskiwanie awaryjne programu Oracle Database Enterprise Edition (bez polegania na Oracle-RAC) może odbyć się na platformie Azure przy użyciu [Data Guard, aktywne Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), lub [bramy Golden Oracle](https://www.oracle.com/technetwork/middleware/goldengate), za pomocą dwóch baz danych na dwóch oddzielnych maszynach wirtualnych. Obie maszyny wirtualne powinny mieć ten sam [sieci wirtualnej](https://azure.microsoft.com/documentation/services/virtual-network/) zapewnienie uzyskaniem dostępu do siebie nawzajem za pośrednictwem prywatnego adresu IP na trwałe.  Ponadto zaleca się umieszczenie maszyn wirtualnych w ten sam zestaw dostępności, aby zezwolić na platformie Azure w celu umieszczenie ich w oddzielnych domenach błędów i uaktualnień.  Należy chcesz mieć nadmiarowość geograficzna — masz te dwie bazy danych, replikacją między dwoma różnymi regionami i łączenie dwóch wystąpień z bramą sieci VPN.

Mamy samouczek "[implementacji programu Oracle DataGuard na platformie Azure](configure-oracle-dataguard.md)", który przeprowadzi Cię przez procedurę podstawowa konfiguracja wersji próbnej to na platformie Azure.  

Za pomocą środowiska Oracle Data Guard, wysoką dostępność można osiągnąć przy użyciu podstawowej bazy danych w jednej maszyny wirtualnej pomocniczej bazy danych (w stanie wstrzymania) w innej maszyny wirtualnej, a replikacja jednokierunkowa między nimi. Wynik jest do odczytu kopię bazy danych. Za pomocą środowiska Oracle GoldenGate można skonfigurować dwukierunkowej replikacji między dwiema bazami danych. Aby uzyskać informacje o skonfigurowanie rozwiązania wysokiej dostępności dla bazy danych za pomocą tych narzędzi, zobacz [Active Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) i [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) dokumentacji w witrynie internetowej Oracle. Jeśli użytkownik należy odczytu i zapisu dostępu do kopii bazy danych, możesz użyć [Active środowiska Oracle Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Mamy samouczek "[implementacji środowiska Oracle GoldenGate na platformie Azure](configure-oracle-golden-gate.md)", który przeprowadzi Cię przez procedurę podstawowa konfiguracja wersji próbnej to na platformie Azure.

Pomimo o wysokiej dostępności i odzyskiwania po awarii rozwiązania, architekturę na platformie Azure, należy upewnić się, że strategii tworzenia kopii zapasowej w celu przywrócenia bazy danych.  Mamy zapoznać się z samouczkiem [tworzenia kopii zapasowych i odzyskiwanie bazy danych Oracle](oracle-backup-recovery.md) który przeprowadzi Cię przez podstawowe procedury dotyczące tworzenia kopii zapasowej spójnej.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Do obrazów maszyn wirtualnych oprogramowania Oracle WebLogic Server
* **Klastrowanie jest obsługiwana w wersji Enterprise Edition tylko.** Są licencjonowane do użycia, WebLogic klastrowania tylko w przypadku korzystania z wersji Enterprise programu WebLogic Server. Nie należy używać klastrowania z WebLogic Server Standard Edition.
* **Multiemisji UDP nie jest obsługiwane.** Platforma Azure obsługuje emisji pojedynczej protokołu UDP, ale nie multiemisji lub emisji. WebLogic Server jest możliwe zagwarantowanie, że przy użyciu funkcji emisji pojedynczej protokołu UDP platformy Azure. Dla najlepsze wyniki, opierając się na UDP emisji pojedynczej, zaleca się rozmiar klastra WebLogic znajdować statyczne, lub być przechowywane z nie więcej niż 10 serwerami zarządzanymi uwzględnione w klastrze.
* **WebLogic Server oczekuje portów publicznych i prywatnych w taki sam dostęp T3 (na przykład, korzystając z Enterprise JavaBeans).** Rozważmy scenariusz obejmujące wiele warstw, w którym działa aplikacji warstwy (EJB) usługi w klastrze WebLogic Server składającą się z co najmniej dwóch maszyn wirtualnych w sieci wirtualnej o nazwie **SLWLS**. Warstwa klienta znajduje się w innej podsieci w tej samej sieci wirtualnej, systemem prosty program Java próby wywołania EJB w warstwie usługi. Ponieważ jest on wymagany do warstwy usługi równoważenia obciążenia, publiczny punkt końcowy o zrównoważonym obciążeniu musi zostać utworzony dla maszyn wirtualnych w klastrze WebLogic Server. Jeśli port prywatny, który określisz różni się od portu publicznego (na przykład 7006:7008), wystąpi błąd, takie jak następujące:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Jest to spowodowane wszelkie dostępu zdalnego T3 WebLogic Server oczekuje, że port modułu równoważenia obciążenia i port serwera zarządzanego WebLogic być takie same. W poprzednim przypadku klient uzyskuje dostęp do portu 7006 (port modułu równoważenia obciążenia) i zarządzany serwer nasłuchuje na 7008 (port prywatny). To ograniczenie ma zastosowanie tylko w przypadku dostępu T3, a nie HTTP.

   Aby uniknąć tego problemu, należy użyć jednej z poniższych rozwiązań:

  * Takie same numery portów prywatnymi i publicznymi na użytek punktów końcowych ze zrównoważonym obciążeniem dedykowane T3 dostępu.
  * Podczas uruchamiania WebLogic Server, należy uwzględnić następujący parametr maszyny JVM:

         -Dweblogic.rjvm.enableprotocolswitch=true

Aby uzyskać powiązane informacje, zobacz artykuł bazy wiedzy **860340.1** na <https://support.oracle.com>.

* **Dynamiczne klastra i równoważenia obciążenia ograniczenia.** Załóżmy, że ma używać klaster dynamicznych w WebLogic Server i je ujawnić za pośrednictwem pojedynczego, publiczne równoważenia obciążenia punktu końcowego na platformie Azure. Będzie to możliwe tak długo, jak używać numeru portu stałą dla każdego z serwerów zarządzanych (nie dynamicznie przypisywany z zakresu) i nie rozpoczynaj więcej serwerów zarządzanych niż administrator służy do śledzenia maszyn (oznacza to, nie więcej niż jednego zarządzanego serwera wirtualnego m achine). Jeśli konfiguracji powoduje większej liczby serwerów WebLogic uruchamiany niż maszyny wirtualne (oznacza to, gdzie wiele wystąpień WebLogic Server udział w tej samej maszyny wirtualnej), a następnie nie jest możliwe dla więcej niż jednego z tych wystąpień serwerów WebLogic Aby powiązać danego numeru portu — pozostałe na tej maszynie wirtualnej zakończyć się niepowodzeniem.

   Jeśli skonfigurujesz serwera administracyjnego, aby automatycznie przypisać unikatowe numery portu dla swoich serwerów zarządzanych, następnie równoważenia obciążenia nie jest możliwe ponieważ platformy Azure nie obsługuje mapowania z jednego portu publicznego do wielu portów prywatnych, jak będą wymagane dla tego Konfiguracja.
* **Wiele wystąpień Weblogic Server na maszynie wirtualnej.** W zależności od wymagań danego wdrożenia można rozważyć opcję uruchomienia wielu wystąpień WebLogic Server na tej samej maszyny wirtualnej, jeśli maszyna wirtualna jest wystarczająco duży. Na przykład na maszynie wirtualnej lub średnim rozmiarze, który zawiera dwa rdzenie, możesz wybrać do uruchomienia do dwóch wystąpień WebLogic Server. Należy jednak pamiętać, że nadal zaleca się unikać wprowadzania pojedynczych punktów awarii do architektury, który będzie tak, jeśli używasz tylko jednej maszyny wirtualnej, która działa wiele wystąpień WebLogic Server. Przy użyciu co najmniej dwóch maszyn wirtualnych może być lepszym rozwiązaniem, a każda z tych maszyn wirtualnych można następnie uruchomić wiele wystąpień WebLogic Server. Każde z tych wystąpień WebLogic Server może być nadal należy do tego samego klastra. Należy pamiętać, jednak obecnie nie jest możliwe korzystanie z systemu Azure do punktów końcowych równoważenia obciążenia, które są dostępne w takich wdrożeniach WebLogic Server, w ramach tej samej maszyny wirtualnej, ponieważ usługa Azure load balancer wymaga serwerów ze zrównoważonym obciążeniem, będą rozpraszane unikatowy maszyny wirtualne.

## <a name="oracle-jdk-virtual-machine-images"></a>Obrazy maszyn wirtualnych zestawu JDK Oracle
* **Zestaw JDK 6 i 7 najnowszych aktualizacji.** Chociaż zaleca się używanie najnowszych publiczną, obsługiwanych wersję języka Java (obecnie Java 8), Azure również udostępnia zestaw JDK 6 i 7 obrazów. Jest przeznaczone dla starszych aplikacji, które nie są jeszcze gotowe do uaktualnienia na zestaw JDK 8. Podczas aktualizacji do poprzedniego zestawu JDK obrazów może nie być dostępne publicznie, biorąc pod uwagę firmą Microsoft z firmą Oracle, zestaw JDK 6 i 7 obrazów, dostarczone przez platformę Azure mają na celu zawiera najnowszą aktualizację niepublicznych, który jest zazwyczaj oferowany przez oprogramowanie Oracle, aby Wybierz grupy Oracle firmy obsługiwane klientów. Nowe wersje obrazów JDK zostanie udostępniona wraz z upływem czasu za pomocą zaktualizowanych wersji zestaw JDK 6 i 7.

   Zestaw JDK dostępne w tym zestaw JDK 6 i 7 obrazów maszyn wirtualnych i obrazy pochodzące z nich, należy używać tylko w obrębie platformy Azure.
* **64-bit JDK.** Obrazy maszyn wirtualnych serwera Oracle WebLogic Server i obrazy maszyn wirtualnych Oracle JDK udostępnianych przez platformę Azure zawierają 64-bitowe wersje systemów Windows Server, jak i zestaw JDK.

## <a name="next-steps"></a>Kolejne kroki
Teraz po omówieniu bieżącego rozwiązania Oracle w systemie Microsoft Azure. Następnym krokiem jest, aby wdrożyć swoją pierwszą bazę danych Oracle na platformie Azure.
- Spróbuj [tworzenie bazy danych Oracle na platformie Azure](oracle-database-quick-create.md) samouczka, aby rozpocząć pracę.
