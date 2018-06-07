---
title: Rozwiązania firmy Oracle w systemie Microsoft Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat obsługiwanych konfiguracji i ograniczenia Oracle rozwiązań w systemie Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
manager: jeconnoc
author: rickstercdn
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: rclaus
ms.openlocfilehash: daed709b4b4be87ba75f5539bd31c666b3a37414
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656350"
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Rozwiązania programu Oracle i wdrażania ich w systemie Microsoft Azure
W tym artykule omówiono informacje wymagane do pomyślnego wdrażania różnych rozwiązań Oracle w systemie Microsoft Azure. Te rozwiązania są oparte na obrazy maszyny wirtualnej opublikowane przez firmę Oracle w portalu Azure Marketplace. Aby uzyskać listę dostępnych obrazów, uruchom następujące polecenie:
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
Począwszy od 6-16-2017 listy obrazów są następujące:
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

Te obrazy są traktowane jako "Bring Your Own License" i jako taki można będzie obciążana dla zasobów obliczeniowych, magazynu i sieci kosztów ponoszonych przez uruchomienie maszyny Wirtualnej.  Zakłada się, że licencjonowane do korzystania z oprogramowania Oracle i mieć bieżącej umowy pomocy technicznej w miejscu z programu Oracle. Oracle ma gwarancji przenośność licencji z lokalnej na platformie Azure. Zobacz publikowane [Oracle i Microsoft](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) Uwaga szczegółowe informacje na temat przenośność licencji. 

Osoby można również utworzyć ich rozwiązania na obraz niestandardowy, utworzyć od podstaw na platformie Azure lub Przekaż obraz niestandardowy z ich w środowisku lokalnym.

## <a name="support-for-jd-edwards"></a>Obsługa Dyszkiewicz JD
Zgodnie z Uwaga obsługę Oracle [2178595.1 identyfikator Doc](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) , EnterpriseOne Dyszkiewicz JD wer. 9.2 i nowszej są obsługiwane w **publicznych chmury oferty** spełniające ich właściwe `Minimum Technical Requirements` (MTR).  Musisz utworzyć niestandardowych obrazów, które spełnia ich wymagań MTR zgodności aplikacji dla systemu operacyjnego i oprogramowania. 

## <a name="oracle-database-virtual-machine-images"></a>Obrazy maszyny wirtualnej bazy danych programu Oracle
Oracle obsługuje uruchomione wersje Oracle DB 12.1 Standard i Enterprise na platformie Azure w oparciu Oracle Linux obrazy maszyny wirtualnej.  Aby uzyskać najlepszą wydajność, w przypadku obciążeń produkcyjnych z bazy danych programu Oracle na platformie Azure należy poprawnie rozmiaru obrazu maszyny Wirtualnej i użyj zarządzanych dysków, które bazują na magazyn w warstwie Premium. Do instrukcji dotyczących szybko uzyskać bazy danych Oracle skonfigurowaniu i uruchomieniu na platformie Azure przy użyciu programu Oracle opublikowanego obrazu maszyny Wirtualnej, [spróbuj Przewodnik Szybki Start bazy danych Oracle](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opcje konfiguracji dysku dołączonym

Dołączonych dysków korzystają z usługi Magazyn obiektów Blob platformy Azure. Każdy dysk standardowy jest w stanie teoretyczna maksymalna około 500 operacji wejścia/wyjścia na sekundę (IOPS). Nasze oferty dysku premium jest preferowane w przypadku obciążeń wysokiej wydajności bazy danych i może osiągnąć maksymalnie 5000 IOps dla każdego dysku. Podczas można użyć jednego dysku, jeśli spełniające potrzeby w zakresie wydajności — może zwiększyć skuteczne wydajność IOPS, jeśli używasz wielu dysków dołączonych, rozmieszczenie do ich dane z bazy danych, a następnie użyj Oracle automatyczne zarządzanie magazynu (ASM). Zobacz [Oracle automatyczne magazynowania — omówienie](http://www.oracle.com/technetwork/database/index-100339.html) Aby uzyskać więcej informacji określonych funkcji ASM Oracle. Przykład sposobu instalowania i konfigurowania funkcji ASM Oracle na Maszynę wirtualną systemu Linux platformy Azure — możesz spróbować [Instalowanie i konfigurowanie programu Oracle automatycznego zarządzania magazynem](configure-oracle-asm.md) samouczka.

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle rzeczywistej aplikacji klastra (Oracle RAC)
Oracle RAC zaprojektowano w celu złagodzenia awarii jednego węzła w konfiguracji klastra wielowęzłowego lokalnymi. Opiera się na dwie technologie lokalnej nie będących kodu natywnego do środowiska chmury publicznej hiperskali: rzutowanie wiele sieci i udostępnionego dysku. Rozwiązania do bazy danych wymaga RAC Oracle na platformie Azure, należy najpierw 3 firmy, aby włączyć te technologie.  A **Microsoft Azure certyfikowane** oferty o nazwie [FlashGrid węzeł Oracle RAC](https://azuremarketplace.microsoft.com/marketplace/apps/flashgrid-inc.flashgrid-racnode?tab=Overview) jest dostępna w portalu Azure Marketplace opublikowanych przez FlashGrid Inc. Aby uzyskać więcej informacji dotyczących tego rozwiązania i jak działa na platformie Azure, zobacz [strona rozwiązania FlashGrid](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Zagadnienia wysoką dostępność i odzyskiwaniem po awarii
Korzystając z bazami danych Oracle na platformie Azure, jest odpowiedzialny za wdrażanie wysokiej dostępności i odzyskiwaniem po awarii rozwiązanie odzyskiwania w celu uniknięcia żadnych przestojów. 

Wysoka dostępność i odzyskiwanie po awarii dla Oracle Database Enterprise Edition (bez polegania na Oracle RAC) można osiągnąć przy użyciu usługi Azure [Data Guard, ochrona danych Active](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), lub [Oracle Golden bramy](http://www.oracle.com/technetwork/middleware/goldengate), z dwóch baz danych na dwóch osobnych maszyn wirtualnych. Zarówno maszyn wirtualnych powinna być w tej samej [sieci wirtualnej](https://azure.microsoft.com/documentation/services/virtual-network/) one siebie dostęp za pośrednictwem prywatnego adresu IP trwałych.  Ponadto zaleca się umieszczanie maszyn wirtualnych w tym samym zestawie umożliwia Azure umieszczenie ich w domenach awarii oddzielne i domen uaktualnienia dostępności.  Należy mają nadmiarowość geograficzna — może mieć te dwie bazy danych replikacji między dwóch różnych regionach i połącz dwa wystąpienia dla bramy sieci VPN.

Mamy samouczek "[DataGuard Oracle wdrożenie na platformie Azure](configure-oracle-dataguard.md)", który przeprowadzi Cię przez procedurę podstawowe ustawienia do wersji próbnej to na platformie Azure.  

Przy użyciu funkcji Guard danych Oracle, można osiągnąć wysoką dostępność z podstawowej bazy danych w jednej maszyny wirtualnej, pomocniczej bazy danych (rezerwy) w innej maszyny wirtualnej i jednokierunkowe replikacja między nimi. Wynik jest do odczytu kopię bazy danych. GoldenGate Oracle można skonfigurować dwukierunkowe replikację między dwiema bazami danych. Aby dowiedzieć się, jak skonfigurować rozwiązania wysokiej dostępności dla baz danych za pomocą tych narzędzi, zobacz [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) i [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) dokumentacji w witrynie sieci Web programu Oracle. Jeśli użytkownik należy odczytu i zapisu dostępu do kopii bazy danych, możesz użyć [Active Oracle Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Mamy samouczek "[GoldenGate Oracle wdrożenie na platformie Azure](configure-oracle-golden-gate.md)", który przeprowadzi Cię przez procedurę podstawowe ustawienia do wersji próbnej to na platformie Azure.

Pomimo o wysokiej dostępności i odzyskiwania po awarii rozwiązania zaprojektowana na platformie Azure, należy upewnić się, że strategii tworzenia kopii zapasowej w celu przywrócenia bazy danych programu.  Mamy samouczek [kopii zapasowej i odzyskiwanie bazy danych programu Oracle](oracle-backup-recovery.md) który przeprowadzi Cię przez procedurę ustanawiania utworzenia spójnej kopii zapasowej.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server maszyny wirtualnej obrazów
* **Klaster jest obsługiwana w wersji Enterprise tylko.** Możesz są licencji na korzystanie z klastrowania WebLogic tylko w przypadku korzystania z wersji Enterprise WebLogic Server. Nie używaj klastrowanie WebLogic Server Standard Edition.
* **Multiemisji UDP nie jest obsługiwane.** Azure obsługuje emisji pojedynczej protokołu UDP, ale nie multiemisji lub emisji. Serwer WebLogic jest polegać na Azure UDP możliwości emisji pojedynczej. Aby najlepiej wyniki polegania na emisji pojedynczej protokołu UDP, firma Microsoft zaleca, aby WebLogic rozmiar klastra były przechowywane statycznych, lub zachowane z nie więcej niż 10 zarządzanych serwerów znajdujących się w klastrze.
* **WebLogic Server oczekuje portów publicznego i prywatnego tę samą T3 dostępu (na przykład używając JavaBeans przedsiębiorstwa).** Rozważmy scenariusz wielowarstwowych, gdzie aplikacja warstwy (EJB) usługi jest uruchomiona w klastrze serwerów WebLogic, składające się z co najmniej dwóch maszyn wirtualnych w sieci wirtualnej o nazwie **SLWLS**. Warstwa klienta znajduje się w innej podsieci w tej samej sieci wirtualnej systemem prosty program Java próby wywołania EJB w warstwie usługi. Ponieważ jest on wymagany do warstwy usługi równoważenia obciążenia, publiczny punkt końcowy równoważenia obciążenia musi zostać utworzona dla maszyn wirtualnych w klastrze serwerów WebLogic. Jeśli port prywatny, który określisz różni się od portu publicznego (na przykład 7006:7008), wystąpi błąd, takie jak następujące:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Jest to spowodowane żadnych dostępu zdalnego T3 WebLogic Server oczekuje portu usługi równoważenia obciążenia i port serwera WebLogic zarządzane na taki sam. W przypadku poprzedniego klient uzyskuje dostęp do portu 7006 (port usługi równoważenia obciążenia) i zarządzany serwer nasłuchuje na 7008 (port prywatny). To ograniczenie ma zastosowanie tylko w przypadku dostępu T3, a nie HTTP.

   Aby uniknąć tego problemu, użyj jednej z poniższych rozwiązań:

  * Użyj takie same numery portów prywatne i publiczne dla punktów końcowych ze zrównoważonym obciążeniem dedykowany T3 dostępu.
  * Podczas uruchamiania serwera WebLogic, należy dodać parametr JVM następujące:

         -Dweblogic.rjvm.enableprotocolswitch=true

Powiązane informacje, zobacz artykuł bazy wiedzy **860340.1** na <http://support.oracle.com>.

* **Dynamiczne klastra i równoważenia obciążenia ograniczenia.** Załóżmy, że chcesz używać dynamicznej klastra WebLogic Server i je ujawnić za pośrednictwem jednej, publiczny równoważeniem obciążenia punktu końcowego na platformie Azure. Będzie to możliwe tak długo, jak używać numeru portu stałej dla każdego z serwerów zarządzanych (przypisywane nie dynamicznie z zakresu) i nie uruchamiaj więcej serwerów zarządzanych niż administrator służy do śledzenia maszyny (to znaczy nie więcej niż jednego zarządzane na m wirtualnego serwera achine). Jeśli konfiguracji powoduje więcej serwerów WebLogic uruchomienia niż maszyny wirtualne (oznacza to, gdzie wielu wystąpień serwera WebLogic udostępnić tę samą maszynę wirtualną), a następnie nie jest możliwe w dla więcej niż jednego z tych wystąpień serwerów WebLogic Aby powiązać danego numeru portu — pozostałe na tej maszynie wirtualnej zakończyć się niepowodzeniem.

   Jeśli konfigurujesz serwera administracyjnego można automatycznie przypisać unikatowe numery portu na jego serwery zarządzane, następnie równoważenia obciążenia jest niemożliwe, ponieważ Azure nie obsługuje mapowania z jednego portu publicznego do wielu portów prywatnych, jak będą wymagane dla tego Konfiguracja.
* **Wiele wystąpień serwera Weblogic na maszynie wirtualnej.** W zależności od wymagań danego wdrożenia należy rozważyć możliwość uruchamianie wielu wystąpień serwera WebLogic na tej samej maszyny wirtualnej, jeśli maszyna wirtualna jest wystarczająco duży. Na przykład na maszynie wirtualnej średnim rozmiarze, który zawiera dwa rdzenie, można wybrać do uruchomienia dwóch wystąpień WebLogic Server. Należy jednak pamiętać, nadal zaleca się wprowadzenie pojedynczego punktu awarii w architekturze byłoby, jeśli użyto tylko jednej maszyny wirtualnej, która jest uruchamianie wielu wystąpień serwera WebLogic uniknąć. Przy użyciu co najmniej dwóch maszyn wirtualnych może być lepszym rozwiązaniem, a każdy z tych maszyn wirtualnych następnie może uruchomić wiele wystąpień WebLogic serwera. Każdy z tych wystąpień WebLogic Server może być nadal należy do tego samego klastra. Uwaga, jednak obecnie nie jest możliwe na potrzeby Azure punkty końcowe równoważenia obciążenia, które są dostępne w takich wdrożeniach WebLogic Server w ramach tej samej maszyny wirtualnej, ponieważ usługa równoważenia obciążenia Azure wymaga rozłożona unikatowy serwerów z równoważeniem obciążenia maszyny wirtualne.

## <a name="oracle-jdk-virtual-machine-images"></a>Obrazy maszyny wirtualnej JDK Oracle
* **JDK 6 i 7 najnowsze aktualizacje.** Gdy firma Microsoft zaleca używanie najnowszych publicznego, obsługiwaną wersję Java (obecnie Java 8), Azure również udostępnia JDK 6 i 7 obrazów. Ta wartość jest przeznaczona dla starszych aplikacji, które nie są jeszcze gotowy do uaktualnienia do JDK 8. Podczas aktualizacji do poprzedniego obrazów JDK może być już dostępne publicznie podane współpracy firmy Microsoft z bazą danych Oracle, JDK 6 i 7 obrazów dostarczany przez platformę Azure mają zawierać nowszą aktualizację niepubliczne, zwykle oferowanych przez firmę Oracle do Wybierz grupę programu Oracle na obsługiwanych klientów. Nowe wersje obrazów JDK zostanie udostępniona w zależności od zaktualizowanych wersji JDK 6 i 7.

   JDK dostępne w tym JDK 6 i 7 obrazów i maszyn wirtualnych i obrazy pochodząca z nich, można używać tylko w obrębie platformy Azure.
* **JDK 64-bitowych.** Oracle WebLogic Server obrazy maszyny wirtualnej i obrazy maszyny wirtualnej Oracle JDK dostarczany przez platformę Azure zawierają 64-bitowe wersje systemów Windows Server, jak i zestaw JDK.

## <a name="next-steps"></a>Kolejne kroki
Omówienie bieżącego rozwiązania firmy Oracle jest teraz dostępna w systemie Microsoft Azure. Następnym krokiem jest do wdrożenia pierwszą bazę danych programu Oracle na platformie Azure.
- Spróbuj [tworzenie bazy danych programu Oracle na platformie Azure](oracle-database-quick-create.md) samouczkiem, aby rozpocząć pracę.