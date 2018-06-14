---
title: Replikowanie wdrażania Citrix XenDesktop i program XenApp wielowarstwową przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak chronić i odzyskiwać Citrix XenDesktop i program XenApp wdrożenia przy użyciu usługi Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: ponatara
ms.openlocfilehash: b117525a4851dee5366aeda77c8aaefd1fdde375
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2018
ms.locfileid: "29559710"
---
# <a name="replicate-a-multi-tier-citrix-xenapp-and-xendesktop-deployment-using-azure-site-recovery"></a>Replikowanie wdrażania program Citrix XenApp i XenDesktop wielowarstwową przy użyciu usługi Azure Site Recovery

## <a name="overview"></a>Przegląd

Citrix XenDesktop to rozwiązanie wirtualizacji pulpitu, polegającego na dostarczaniu pulpity i aplikacje jako usługa na żądanie do dowolnego miejsca i użytkownika. Dzięki technologii dostarczania FlexCast XenDesktop można szybko i bezpiecznie dostarczania aplikacji i pulpitów do użytkowników.
Obecnie program Citrix XenApp nie zapewnia żadnych po awarii możliwości odzyskiwania.

Dobre rozwiązanie odzyskiwania po awarii, powinno umożliwić modelowania planów odzyskiwania wokół powyższych architekturach aplikacji złożonych, a także mieć możliwość dodawania kroków dostosowane do obsługi aplikacji mapowań między różnych warstw, dlatego zapewnienie pojedynczym kliknięciem czy zrzut rozwiązanie w przypadku awarii, co może prowadzić do dolnej RTO.

Ten dokument zawiera wskazówki krok po kroku budowania rozwiązanie odzyskiwania po awarii dla lokalnej program Citrix XenApp wdrożeniach funkcji Hyper-V i oprogramowania VMware vSphere platform. Ten dokument opisuje również sposób wykonywania testowy tryb failover (wyszczególniania odzyskiwania po awarii) i nieplanowany tryb failover na platformie Azure przy użyciu planów odzyskiwania, obsługiwane konfiguracje i wymagania wstępne.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że należy zrozumieć następujące kwestie:

1. [Replikacja maszyny wirtualnej na platformie Azure](site-recovery-vmware-to-azure.md)
1. Jak [projektowania sieci odzyskiwania](site-recovery-network-design.md)
1. [Ten test trybu failover na platformie Azure](site-recovery-test-failover-to-azure.md)
1. [Podczas pracy w trybie failover na platformie Azure](site-recovery-failover.md)
1. Jak [replikacji kontrolera domeny](site-recovery-active-directory.md)
1. Jak [replikacji programu SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Wzorce wdrożenia

Farma program Citrix XenApp i XenDesktop ma zazwyczaj następującego wzorca wdrażania:

**Wzorzec wdrożenia**

Program Citrix XenApp i XenDesktop wdrożenia z serwerem AD, DNS, SQL bazy danych serwera, Citrix dostarczania kontrolera sklepu server, program XenApp głównego (VDA), serwer licencji na program XenApp Citrix

![Wzorzec wdrożenia 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Obsługa odzyskiwania lokacji

Na potrzeby tego artykułu Citrix wdrożeń na maszynach wirtualnych VMware zarządza vSphere w wersji 6.0 / System Center VMM 2012 R2 użyto w celu ustawienia odzyskiwania po awarii.

### <a name="source-and-target"></a>Źródłowa i docelowa

Scenariusz | **Do lokacji dodatkowej** | **Platforma Azure**
--- | --- | ---
**Funkcja Hyper-V** | Nie znajduje się w zakresie | Yes
**VMware** | Nie znajduje się w zakresie | Yes
**Serwer fizyczny** | Nie znajduje się w zakresie | Yes

### <a name="versions"></a>Wersje
Klienci mogą wdrożyć program XenApp składniki jako maszynami wirtualnymi funkcji Hyper-V lub programu VMware lub serwerach fizycznych. Usługa Azure Site Recovery może chronić wdrożeń fizycznych i wirtualnych na platformie Azure.
Ponieważ program XenApp 7,7 lub nowszej jest obsługiwana na platformie Azure, tylko wdrożenia z tymi wersjami mogą można przełączyć do platformy Azure dla odzyskiwania po awarii lub migracji.

### <a name="things-to-keep-in-mind"></a>Warto pamiętać o

1. Ochrona i odzyskiwanie z lokalnego wdrożenia przy użyciu serwerowy system operacyjny maszyny w celu dostarczenia program XenApp opublikowane aplikacje i program XenApp publikowane komputerów stacjonarnych jest obsługiwana.

2. Ochrona i odzyskiwanie wdrożeń lokalnych przy użyciu pulpitu systemu operacyjnego maszyny do dostarczania Pulpitów dla klienta pulpitów wirtualnych, w tym Windows 10 nie jest obsługiwana. Jest tak, ponieważ funkcja automatycznego odzyskiwania systemu nie obsługuje odzyskiwania maszyn z pulpitem OS'es.  Ponadto niektóre klienta wirtualnego systemy operacyjne (np.) Windows 7) nie są jeszcze obsługiwane w przypadku licencjonowania na platformie Azure. [Dowiedz się więcej](https://azure.microsoft.com/pricing/licensing-faq/) na temat licencjonowania dla komputerów stacjonarnych klienta/serwera na platformie Azure.

3.  Usługa Azure Site Recovery nie może replikować i ochronę istniejących klony MCS lub Odwiedziny lokalnymi.
Należy ponownie utworzyć te klony za pomocą Menedżera zasobów Azure inicjowania obsługi administracyjnej z kontrolera dostarczania.

4. NetScaler nie mogą być chronione przy użyciu usługi Azure Site Recovery NetScaler jest oparta na FreeBSD i usługi Azure Site Recovery nie obsługuje ochrony FreeBSD systemu operacyjnego. Będzie potrzebny do wdrożenia i skonfigurowania nowego urządzenia NetScaler z rynku Azure po przejściu w tryb failover Azure.


## <a name="replicating-virtual-machines"></a>Replikowanie maszyn wirtualnych

Następujące składniki wdrożenia program Citrix XenApp muszą być chronione, aby włączyć replikację i odzyskiwania.

* Ochrona serwera AD DNS
* Ochrona serwera bazy danych SQL
* Ochrona kontrolera dostarczania Citrix
* Ochrona serwera sklepu.
* Ochrona program XenApp wzorca (VDA)
* Ochrona program Citrix XenApp serwera licencji


**Replikacja serwer AD DNS**

Zapoznaj się z [ochrony usługi Active Directory i DNS z usługą Azure Site Recovery](site-recovery-active-directory.md) na wskazówki dotyczące replikacji i konfigurowania kontrolera domeny na platformie Azure.

**Replikacja serwer bazy danych SQL**

Zapoznaj się z [ochrony programu SQL Server z odzyskiwania po awarii programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md) szczegółowe wskazówki techniczne na temat zalecanych opcji ochrony serwerów SQL.

Postępuj zgodnie z [w tych wskazówkach](site-recovery-vmware-to-azure.md) do rozpoczęcia replikacji z innych składników maszyn wirtualnych na platformie Azure.

![Ochrona program XenApp składników](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Ustawienia sieci i obliczeń**

Po maszyn objętych ochroną (stan to "Protected" w obszarze elementy replikowane), należy skonfigurować ustawienia obliczeń i sieci.
W obliczenia i sieć > obliczeniowe właściwości, można określić nazwę i docelowy rozmiar maszyny Wirtualnej platformy Azure.
Zmodyfikuj nazwę, aby spełniać wymagania dotyczące usługi Azure, jeśli potrzebujesz. Można również wyświetlić i dodać informacje dotyczące sieci docelowej, podsieci i adres IP, który zostanie przypisany do maszyny Wirtualnej platformy Azure.

Pamiętaj o następujących kwestiach:

* Możesz ustawić docelowy adres IP. Jeśli nie podasz adresu, maszyna w trybie failover będzie używać protokołu DHCP. Jeśli ustawisz adres, który nie jest dostępna w trybie failover, przejście w tryb failover nie będzie działać. Ten sam docelowy adres IP może być użyty do testowania trybu failover, jeśli adres jest dostępny w testowej sieci trybu failover.

* Dla serwera AD DNS zachowując adresu lokalnego pozwala określić ten sam adres jako serwer DNS w sieci wirtualnej platformy Azure.

Liczba kart sieciowych zależy od rozmiaru określonego dla docelowej maszyny wirtualnej, zgodnie z poniższym:

*   Jeśli liczba kart sieciowych w maszynie źródłowej jest mniejsza lub równa liczbie kart sieciowych dozwolonych dla rozmiaru maszyny docelowej, maszyna docelowa będzie mieć taką samą liczbę kart sieciowych jak maszyna źródłowa.
*   Jeśli liczba kart sieciowych dla źródłowej maszyny wirtualnej przekracza liczbę dozwolonych kart sieciowych dla rozmiaru maszyny docelowej, zostanie użyta maksymalna liczba kart dla rozmiaru maszyny docelowej.
* Na przykład, jeśli maszyna źródłowa ma dwie karty sieciowe, a rozmiar maszyny docelowej obsługuje cztery karty, maszyna docelowa będzie mieć dwie karty sieciowe. Jeśli maszyna źródłowa ma dwie karty sieciowe, ale rozmiar maszyny docelowej obsługuje tylko jedną kartę, maszyna docelowa będzie mieć tylko jedną kartę sieciową.
*   Jeśli maszyna wirtualna ma wiele kart sieciowych, wszystkie będą łączyć z tą samą siecią.
*   Jeśli maszyna wirtualna ma wiele kart sieciowych, pierwsza z nich wyświetlane na liście staje się domyślnej karty sieciowej na maszynie wirtualnej platformy Azure.


## <a name="creating-a-recovery-plan"></a>Tworzenie planu odzyskiwania

Po włączeniu replikacji dla maszyn wirtualnych program XenApp składnika następnym krokiem jest utworzenie planu odzyskiwania.
Odzyskiwanie Planowanie grup razem maszyny wirtualne mają podobne wymagania dotyczące trybu failover i odzyskiwania.  

**Kroki umożliwiające utworzenie planu odzyskiwania**

1. Dodawanie maszyn wirtualnych składnika program XenApp w planie odzyskiwania.
2. Kliknij przycisk planów odzyskiwania -> + planu odzyskiwania. Podaj nazwę intuicyjne planu odzyskiwania.
3. W przypadku maszyn wirtualnych VMware: Wybierz źródło jako serwer przetwarzania VMware, element docelowy jako Microsoft Azure i modelu wdrażania usługi Resource Manager i wybierz elementy, kliknij polecenie.
4. W przypadku maszyn wirtualnych funkcji Hyper-V: Wybierz źródło jako serwer programu VMM, docelowy jako Microsoft Azure i modelu wdrażania jak Menedżer zasobów i kliknij pozycję Wybierz elementy, a następnie wybierz program XenApp wdrażania maszyn wirtualnych.

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do trybu failover grupy

Plany odzyskiwania można dostosować, aby dodać grupy pracy awaryjnej dla kolejności uruchamiania określonego, skrypty i działań ręcznych. Następujące grupy muszą być dodane do planu odzyskiwania.

1. Grupa1 trybu failover: DNS AD
2. Grupa2 trybu failover: Maszyny wirtualne SQL Server
2. Trybu failover, grupa 3: Maszyna wirtualna obrazu wzorca VDA
3. Grupa 4 trybu failover: Kontroler dostarczania i maszyn wirtualnych serwera sklepu


### <a name="adding-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania

Można uruchamiać skrypty przed lub po określonej grupy w planie odzyskiwania. Ręczne akcje mogą być również być uwzględnione i wykonywane w trybie failover.

Plan odzyskiwania dostosowane wygląda jak poniżej:

1. Grupa1 trybu failover: DNS AD
2. Grupa2 trybu failover: Maszyny wirtualne SQL Server
3. Trybu failover, grupa 3: Maszyna wirtualna obrazu wzorca VDA

   >[!NOTE]     
   >Kroki 4, 6 i 7 zawierający akcje ręczne lub skryptu dotyczą tylko program XenApp lokalnymi > środowisko z katalogami MCS/Odwiedziny.

4. Akcja ręczna lub skryptu grupa 3: zamknięcie wzorca VDA maszyny Wirtualnej wzorca VDA maszyny Wirtualnej po awarii Azure będzie w stanie uruchomienia. Aby utworzyć nowe katalogi MCS przy użyciu hostingu Azure ARM, głównego maszyna wirtualna VDA jest wymagane będzie zatrzymane (de przydzielone) stanu. Zamknij maszynę Wirtualną z portalu Azure.

5. Grupa 4 trybu failover: Kontroler dostarczania i maszyn wirtualnych serwera sklepu
6. Grupa 3 ręczny lub skryptu akcji 1:

    ***Dodaj połączenie hostów protokołu RM Azure***

    Utwórz połączenie hosta Azure ARM na maszynie kontrolera dostawy do udostępnienia nowego serwera MCS katalogi na platformie Azure. Wykonaj kroki opisane w tym [artykułu](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Grupa 3 ręczny lub skryptu akcji 2:

    ***Utwórz ponownie MCS katalogi na platformie Azure***

    Istniejące klony MCS lub Odwiedziny w lokacji głównej nie będą replikowane do platformy Azure. Należy ponownie utworzyć te klony przy użyciu zreplikowanych VDA wzorca i Azure ARM inicjowania obsługi administracyjnej z kontrolera dostarczania. Wykonaj kroki opisane w tym [artykułu](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) do tworzenia katalogów serwera MCS na platformie Azure.

![Plan odzyskiwania program XenApp składników](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Możesz używać skryptów w [lokalizacji](https://github.com/Azure/azure-quickstart-templates/blob/>master/asr-automation-recovery/scripts) zaktualizować DNS o nowe adresy IP z nieudane za pośrednictwem > maszyn wirtualnych lub dołączyć modułu równoważenia obciążenia na nieudane przez maszynę wirtualną, jeśli to konieczne.


## <a name="doing-a-test-failover"></a>Ten test trybu failover

Postępuj zgodnie z [w tych wskazówkach](site-recovery-test-failover-to-azure.md) przeprowadzić test trybu failover.

![Plan odzyskiwania](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Podczas pracy w trybie failover

Postępuj zgodnie z [w tych wskazówkach](site-recovery-failover.md) podczas wprowadzania trybu failover.

## <a name="next-steps"></a>Kolejne kroki

Możesz [więcej](https://aka.ms/citrix-xenapp-xendesktop-with-asr) o replikowanie wdrożenia program Citrix XenApp i XenDesktop w oficjalnym dokumencie. Przyjrzyj się wskazówki dotyczące [replikacji z innych aplikacji](site-recovery-workload.md) przy użyciu usługi Site Recovery.
