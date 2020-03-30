---
title: Konfigurowanie odzyskiwania po awarii citrix XenDesktop/XenApp za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii dla wdrożeń Citrix XenDesktop i XenApp przy użyciu usługi Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 29fbe5389da924a2ecc660aa5ce5c4bb0a0902b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084551"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>konfigurowanie odzyskiwania po awarii dla wielowarstwowego wdrożenia Citrix XenApp i XenDesktop



Citrix XenDesktop to rozwiązanie do wirtualizacji komputerów stacjonarnych, które dostarcza pulpity i aplikacje jako usługę ondemand każdemu użytkownikowi i wszędzie. Dzięki technologii dostarczania FlexCast XenDesktop może szybko i bezpiecznie dostarczać aplikacje i komputery stacjonarne użytkownikom.
Obecnie Citrix XenApp nie zapewnia żadnych funkcji odzyskiwania po awarii.

Dobre rozwiązanie do odzyskiwania po awarii, powinno umożliwić modelowanie planów odzyskiwania wokół powyższych złożonych architektur aplikacji, a także możliwość dodawania niestandardowych kroków do obsługi mapowania aplikacji między różnymi warstwami, zapewniając w ten sposób pojedyncze kliknięcie w przypadku katastrofy prowadzącej do niższego RTO.

Ten dokument zawiera wskazówki krok po kroku dotyczące tworzenia rozwiązania do odzyskiwania po awarii dla lokalnych wdrożeń Citrix XenApp na platformach Hyper-V i VMware vSphere. W tym dokumencie opisano również sposób wykonywania testów trybu failover(disaster recovery drill) i nieplanowanego trybu failover na platformie Azure przy użyciu planów odzyskiwania, obsługiwanych konfiguracji i wymagań wstępnych.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz następujące kwestie:

1. [Replikowanie maszyny wirtualnej na platformę Azure](site-recovery-vmware-to-azure.md)
1. Jak [zaprojektować sieć odzyskiwania](site-recovery-network-design.md)
1. [Wykonywanie testowego trybu failover na platformie Azure](site-recovery-test-failover-to-azure.md)
1. [Wykonywanie pracy awaryjnej na platformie Azure](site-recovery-failover.md)
1. Jak [replikować kontroler domeny](site-recovery-active-directory.md)
1. Jak [replikować program SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Wzorce wdrażania

Farma Citrix XenApp i XenDesktop mają zazwyczaj następujący wzorzec wdrażania:

**Wzorzec wdrażania**

Wdrożenie Citrix XenApp i XenDesktop z serwerem DNS usługi AD, serwerem bazy danych SQL, kontrolerem dostarczania Citrix, serwerem StoreFront, XenApp Master (VDA), serwerem licencji Citrix XenApp

![Wzorzec wdrażania 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

Na potrzeby tego artykułu wdrożenia citrix na maszynach wirtualnych VMware zarządzanych przez vSphere 6.0 / System Center VMM 2012 R2 były używane do konfigurowania odzyskiwania po awarii.

### <a name="source-and-target"></a>Źródło i cel

**Scenariusz** | **Do lokacji dodatkowej** | **Na platformę Azure**
--- | --- | ---
**Funkcja Hyper-V** | Brak zakresu | Tak
**Vmware** | Brak zakresu | Tak
**Serwer fizyczny** | Brak zakresu | Tak

### <a name="versions"></a>Wersje
Klienci mogą wdrażać składniki XenApp jako maszyny wirtualne działające na urządzeniach Hyper-V lub VMware lub jako serwery fizyczne. Usługa Azure Site Recovery może chronić zarówno wdrożenia fizyczne, jak i wirtualne na platformie Azure.
Ponieważ XenApp 7.7 lub nowszy jest obsługiwany na platformie Azure, tylko wdrożenia z tych wersji można awaryjnie do platformy Azure do odzyskiwania po awarii lub migracji.

### <a name="things-to-keep-in-mind"></a>Rzeczy, o których należy pamiętać

1. Ochrona i odzyskiwanie wdrożeń lokalnych przy użyciu komputerów z systemem operacyjnym serwera do dostarczania opublikowanych aplikacji XenApp i opublikowanych pulpitów XenApp jest obsługiwane.

2. Ochrona i odzyskiwanie wdrożeń lokalnych przy użyciu komputerów z systemem operacyjnym dla komputerów stacjonarnych w celu dostarczania interfejsu VDI dla pulpitów wirtualnych klienckich, w tym systemu Windows 10, nie jest obsługiwane. Dzieje się tak dlatego, że usługa Site Recovery nie obsługuje odzyskiwania komputerów z pulpitem OS'es.  Ponadto niektóre systemy operacyjne pulpitu wirtualnego klienta (np. Windows 7) nie są jeszcze obsługiwane do licencjonowania na platformie Azure. [Dowiedz się więcej](https://azure.microsoft.com/pricing/licensing-faq/) na temat licencjonowania dla komputerów stacjonarnych klienta/serwera na platformie Azure.

3.  Usługa Azure Site Recovery nie może replikować i chronić istniejących lokalnych klonów MCS lub PVS.
Należy ponownie utworzyć te klony przy użyciu obsługi administracyjnej usługi Azure RM z kontrolera dostarczania.

4. NetScaler nie może być chroniony przy użyciu usługi Azure Site Recovery, ponieważ program NetScaler jest oparty na freebsd, a usługa Azure Site Recovery nie obsługuje ochrony systemu freebsd os. Należy wdrożyć i skonfigurować nowe urządzenie NetScaler z usługi Azure Market place po przejściu w stan failover do platformy Azure.


## <a name="replicating-virtual-machines"></a>Replikowanie maszyn wirtualnych

Następujące składniki wdrożenia Citrix XenApp muszą być chronione, aby umożliwić replikację i odzyskiwanie.

* Ochrona serwera DNS usługi AD
* Ochrona serwera bazy danych SQL
* Ochrona kontrolera dostarczania Citrix
* Ochrona serwera StoreFront.
* Ochrona XenApp Master (VDA)
* Ochrona serwera licencji Citrix XenApp


**Replikacja serwera DNS usługi AD**

Zapoznaj się [z witryną Chroń usługę Active Directory i usługi DNS za pomocą usługi Azure Site Recovery,](site-recovery-active-directory.md) aby uzyskać wskazówki dotyczące replikowania i konfigurowania kontrolera domeny na platformie Azure.

**Replikacja serwera bazy danych SQL**

Szczegółowe wskazówki techniczne dotyczące zalecanych opcji ochrony serwerów SQL Server można znaleźć w programie [Protect SQL Server with SQL Server recovery i Azure Site Recovery.](site-recovery-sql.md)

Postępuj zgodnie [z tymi wskazówkami,](site-recovery-vmware-to-azure.md) aby rozpocząć replikowanie innych maszyn wirtualnych składników na platformę Azure.

![Ochrona komponentów XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Ustawienia obliczeniowe i sieciowe**

Po chronionych komputerach (stan jest wyświetlany jako "Chronione" w obszarze Elementy replikowane) należy skonfigurować ustawienia obliczeniowe i sieciowe.
We właściwościach obliczeniowych i > obliczeniowych można określić nazwę maszyny wirtualnej platformy Azure i rozmiar obiektu docelowego.
Zmodyfikuj nazwę, aby była zgodna z wymaganiami platformy Azure, jeśli zachodzi taka potrzeba. Można również wyświetlać i dodawać informacje o sieci docelowej, podsieci i adresie IP, które zostaną przypisane do maszyny Wirtualnej platformy Azure.

Pamiętaj o następujących kwestiach:

* Możesz ustawić docelowy adres IP. Jeśli nie podasz adresu, maszyna w trybie failover będzie używać protokołu DHCP. Jeśli ustawisz adres, który nie jest dostępny w pracy awaryjnej, praca awaryjna nie będzie działać. Ten sam docelowy adres IP może być użyty do testowania trybu failover, jeśli adres jest dostępny w testowej sieci trybu failover.

* W przypadku serwera AD/DNS zachowanie adresu lokalnego umożliwia określenie tego samego adresu co serwer DNS dla sieci wirtualnej platformy Azure.

Liczba kart sieciowych zależy od rozmiaru określonego dla docelowej maszyny wirtualnej, zgodnie z poniższym:

*   Jeśli liczba kart sieciowych w maszynie źródłowej jest mniejsza lub równa liczbie kart sieciowych dozwolonych dla rozmiaru maszyny docelowej, maszyna docelowa będzie mieć taką samą liczbę kart sieciowych jak maszyna źródłowa.
*   Jeśli liczba kart sieciowych dla źródłowej maszyny wirtualnej przekracza liczbę dozwolonych kart sieciowych dla rozmiaru maszyny docelowej, zostanie użyta maksymalna liczba kart dla rozmiaru maszyny docelowej.
* Na przykład, jeśli maszyna źródłowa ma dwie karty sieciowe, a rozmiar maszyny docelowej obsługuje cztery karty, maszyna docelowa będzie mieć dwie karty sieciowe. Jeśli maszyna źródłowa ma dwie karty sieciowe, ale rozmiar maszyny docelowej obsługuje tylko jedną kartę, maszyna docelowa będzie mieć tylko jedną kartę sieciową.
*   Jeśli maszyna wirtualna ma wiele kart sieciowych, wszystkie będą łączyć się z tą samą siecią.
*   Jeśli maszyna wirtualna ma wiele kart sieciowych, pierwsza z nich jest wyświetlana na liście, staje się domyślną kartą sieciową na maszynie wirtualnej platformy Azure.


## <a name="creating-a-recovery-plan"></a>Tworzenie planu odzyskiwania

Po włączeniu replikacji dla maszyn wirtualnych składnika XenApp następnym krokiem jest utworzenie planu odzyskiwania.
Plan odzyskiwania grupuje maszyny wirtualne z podobnymi wymaganiami dotyczącymi pracy awaryjnej i odzyskiwania.  

**Kroki tworzenia planu odzyskiwania**

1. Dodaj maszyny wirtualne składnika XenApp w planie odzyskiwania.
2. Kliknij pozycję Plany odzyskiwania -> + Plan odzyskiwania. Podaj intuicyjną nazwę planu odzyskiwania.
3. W przypadku maszyn wirtualnych VMware: wybierz źródło jako serwer przetwarzania VMware, wybierz jako Microsoft Azure i model wdrażania jako Menedżer zasobów i kliknij wybierz elementy.
4. W przypadku maszyn wirtualnych funkcji Hyper-V: wybierz źródło jako serwer programu VMM, wybierz pozycję Microsoft Azure i model wdrażania jako Menedżer zasobów i kliknij pozycję Wybierz elementy, a następnie wybierz maszyny wirtualne wdrażania XenApp.

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grup trybu failover

Plany odzyskiwania można dostosować, aby dodać grupy trybu failover dla określonej kolejności uruchamiania, skryptów lub akcji ręcznych. Do planu odzyskiwania należy dodać następujące grupy.

1. Grupa trybu failover1: usługa AD DNS
2. Grupa trybu failover2: maszyny wirtualne programu SQL Server
2. Grupa trybu failover3: maszyna wirtualna obrazu wzorca VDA
3. Grupa trybu failover4: kontroler dostarczania i maszyny wirtualne serwera StoreFront


### <a name="adding-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania

Skrypty można uruchamiać przed lub po określonej grupie w planie odzyskiwania. Ręczne akcje mogą być również uwzględniane i wykonywane podczas pracy awaryjnej.

Dostosowany plan odzyskiwania wygląda następująco:

1. Grupa trybu failover1: usługa AD DNS
2. Grupa trybu failover2: maszyny wirtualne programu SQL Server
3. Grupa trybu failover3: maszyna wirtualna obrazu wzorca VDA

   >[!NOTE]     
   >Kroki 4, 6 i 7 zawierające akcje ręczne lub skryptowe mają zastosowanie tylko do lokalnego środowiska XenApp >z katalogami MCS/PVS.

4. Grupa 3 Akcja ręczna lub skryptowa: Zamknij główną maszynę wirtualną VDA.
Główny vda maszyny Wirtualnej, gdy po awarii na platformie Azure będzie w stanie uruchomionym. Aby utworzyć nowe katalogi MCS przy użyciu hostingu platformy Azure, główna maszyna wirtualna VDA musi być w stanie zatrzymana (de przydzielona). Zamykanie maszyny Wirtualnej z witryny Azure portal.

5. Grupa trybu failover4: kontroler dostarczania i maszyny wirtualne serwera StoreFront
6. Akcja ręczna lub skryptowa Grupy3 1:

    ***Dodawanie połączenia hosta usługi Azure RM***

    Utwórz połączenie hosta platformy Azure na komputerze kontrolera dostarczania, aby aprowizować nowe katalogi MCS na platformie Azure. Wykonaj kroki opisane w tym [artykule](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Akcja ręczna lub skryptowa Grupy3 2:

    ***Ponowne tworzenie katalogów MCS na platformie Azure***

    Istniejące klony MCS lub PVS w lokacji głównej nie będą replikowane na platformę Azure. Należy ponownie utworzyć te klony przy użyciu replikowanego wzorca VDA i azure inicjowania obsługi administracyjnej z kontrolera dostarczania. Wykonaj kroki opisane w tym [artykule,](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) aby utworzyć katalogi MCS na platformie Azure.

![Plan odzyskiwania dla komponentów XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Za pomocą skryptów w [lokalizacji](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) można zaktualizować system DNS za pomocą nowych adresów IP >maszyn wirtualnych w trybie failed lub dołączyć moduł równoważenia obciążenia na maszynie wirtualnej awaryjnej, jeśli to konieczne.


## <a name="doing-a-test-failover"></a>Wykonywanie testu w pracy awaryjnej

Postępuj zgodnie [z tymi wskazówkami,](site-recovery-test-failover-to-azure.md) aby wykonać test pracy awaryjnej.

![Plan odzyskiwania](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Wykonywanie pracy awaryjnej

Postępuj zgodnie [z tymi wskazówkami](site-recovery-failover.md) podczas pracy awaryjnej.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat replikowania wdrożeń Citrix XenApp i XenDesktop można [dowiedzieć się](https://aka.ms/citrix-xenapp-xendesktop-with-asr) więcej na temat replikowania wdrożeń Citrix XenApp i XenDesktop w tym opracowaniu. Zapoznaj się ze wskazówkami, aby [replikować inne aplikacje](site-recovery-workload.md) przy użyciu funkcji Odzysk witryny.
