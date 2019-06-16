---
title: Konfigurowanie odzyskiwania po awarii dla wdrożenia Citrix XenDesktop i XenApp obejmujące wiele warstw przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii fo Citrix XenDesktop i XenApp korzystające z usługi Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 68f12bb7335da0a996aeadd752f59db0aa360a8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61038238"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>Konfigurowanie odzyskiwania po awarii dla wdrożenia programów Citrix XenApp i XenDesktop wielowarstwowej



Citrix XenDesktop to rozwiązanie wirtualizacji pulpitu, które dostarcza pulpitów i aplikacji jako usługa na żądanie z żadnym użytkownikiem, dowolnym miejscu. Dzięki FlexCast dostarczanie technologii XenDesktop można szybko i bezpiecznie dostarczania aplikacji i pulpitów do użytkowników.
Już dziś Citrix XenApp nie zapewnia awariami możliwości odzyskiwania.

Dobre rozwiązanie odzyskiwania po awarii, powinien zezwalać na modelowanie planów odzyskiwania wokół powyższych architektury aplikacji złożonych i również mieć możliwość dodawania niestandardowych kroków do obsługi aplikacji mapowania między różne warstwy, dlatego zapewnienie jednym kliknięciem się, że zrzut rozwiązanie na wypadek awarii, co prowadzi do niższych cel czasu odzyskiwania.

Ten dokument zawiera wskazówki krok po kroku dotyczące tworzenia rozwiązania odzyskiwania po awarii dla lokalnych wdrożeń Citrix XenApp na platformie vSphere funkcji Hyper-V i VMware. W tym dokumencie opisano również sposób wykonywania testu trybu failover (odzyskiwanie po awarii) i nieplanowany tryb failover na platformie Azure przy użyciu planów odzyskiwania, obsługiwane konfiguracje i wymagania wstępne.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że należy zrozumieć następujące kwestie:

1. [Replikowanie maszyny wirtualnej na platformie Azure](site-recovery-vmware-to-azure.md)
1. Jak [projektować sieć odzyskiwania](site-recovery-network-design.md)
1. [Podczas testowania trybu failover na platformie Azure](site-recovery-test-failover-to-azure.md)
1. [Przełączeniem w tryb failover na platformie Azure](site-recovery-failover.md)
1. Jak [replikacji kontrolera domeny](site-recovery-active-directory.md)
1. Jak [replikowanie programu SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Wzorce wdrożenia

Citrix XenApp i XenDesktop farmy zwykle mają następujący wzorzec wdrożenia:

**Wzorzec wdrożenia**

Wdrożenia programów Citrix XenApp i XenDesktop z serwer AD DNS, SQL bazy danych, serwer StoreFront serwer, kontroler dostarczania Citrix, XenApp Master (VDA), serwer licencji Citrix XenApp

![Wzorzec wdrożenia 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

Na potrzeby tego artykułu Citrix wdrożenia na maszynach wirtualnych VMware jest zarządzane przez vSphere w wersji 6.0 lub System Center VMM 2012 R2 zostały użyte do konfiguracji odzyskiwania po awarii.

### <a name="source-and-target"></a>Źródłowe i docelowe

**Scenariusz** | **Lokacja dodatkowa** | **Platforma Azure**
--- | --- | ---
**Funkcja Hyper-V** | Nie znajduje się w zakresie | Yes
**VMware** | Nie znajduje się w zakresie | Yes
**Serwer fizyczny** | Nie znajduje się w zakresie | Yes

### <a name="versions"></a>Wersje
Klienci mogą wdrażać składniki XenApp jako maszyny wirtualne uruchomione na funkcji Hyper-V lub VMware lub serwerów fizycznych. Usługa Azure Site Recovery umożliwia ochronę wdrożeń fizycznych i wirtualnych na platformie Azure.
Ponieważ usługa XenApp 7,7 lub nowszej jest obsługiwana na platformie Azure, tylko wdrożenia z tych wersji można przełączyć na platformie Azure dla migracji lub odzyskiwania po awarii.

### <a name="things-to-keep-in-mind"></a>Warto pamiętać o

1. Ochrony i odzyskiwania lokalnych wdrożeń przy użyciu systemu operacyjnego serwera maszyn w celu dostarczenia XenApp opublikowane aplikacje i rozwiązania XenApp opublikowanych pulpitów jest obsługiwana.

2. Ochrony i odzyskiwania lokalnych wdrożeń korzystanie z pulpitu systemu operacyjnego maszyny w celu dostarczania pulpitu VDI dla klienta pulpitów wirtualnych, łącznie z systemem Windows 10 nie jest obsługiwana. Jest to spowodowane Site Recovery nie obsługuje odzyskiwanie maszyn za pomocą programu desktop OS'es.  Ponadto niektóre wirtualnego pulpitu systemy operacyjne klienta (np.) Windows 7) nie są jeszcze obsługiwane w przypadku licencjonowania na platformie Azure. [Dowiedz się więcej](https://azure.microsoft.com/pricing/licensing-faq/) na temat licencjonowania dla komputerów stacjonarnych klienta/serwera na platformie Azure.

3.  Usługa Azure Site Recovery nie może chronić i replikować istniejących klony oddział MCS lub Odwiedziny w środowisku lokalnym.
Należy ponownie utworzyć te klony przy użyciu usługi Azure RM aprowizowania już od kontroler dostarczania.

4. Program NetScaler nie mogą być chronione przy użyciu usługi Azure Site Recovery NetScaler opiera się na FreeBSD i usługi Azure Site Recovery nie obsługuje ochrony systemu operacyjnego FreeBSD. Należy wdrożyć i skonfigurować nowe urządzenie NetScaler z portalu Azure Marketplace, po włączeniu trybu failover na platformie Azure.


## <a name="replicating-virtual-machines"></a>Replikowanie maszyn wirtualnych

Następujące składniki wdrożenia Citrix XenApp muszą być chronione, aby włączyć replikację i odzyskiwanie.

* Ochrona serwer AD DNS
* Ochrona programu SQL server, bazy danych
* Ochrona kontroler dostarczania Citrix
* Ochrona serwer StoreFront.
* Ochrona XenApp Master (VDA)
* Ochrona serwer licencji Citrix XenApp


**Replikacja serwer AD DNS**

Zapoznaj się [ochrony usługi Active Directory i DNS przy użyciu usługi Azure Site Recovery](site-recovery-active-directory.md) na wskazówki dotyczące replikacji i konfigurowania kontrolera domeny na platformie Azure.

**Replikacja serwer bazy danych SQL**

Zapoznaj się [ochrona programu SQL Server za pomocą odzyskiwania po awarii programu SQL Server i usługi Azure Site Recovery](site-recovery-sql.md) Aby uzyskać szczegółowe wskazówki techniczne na temat zalecanych opcji ochrony serwerów SQL.

Postępuj zgodnie z [Niniejsze wskazówki](site-recovery-vmware-to-azure.md) aby rozpocząć replikowanie innych składników maszyn wirtualnych na platformie Azure.

![Ochrona XenApp składników](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Ustawienia sieci i obliczeń**

Po ich ochroną maszyny (stan jest wyświetlany jako "Chronione" w obszarze zreplikowane elementy), należy skonfigurować ustawień obliczenia i sieć.
W obliczenia i sieć > Compute właściwości, można określić nazwę i docelowy rozmiar maszyny Wirtualnej platformy Azure.
Zmodyfikuj nazwę aby spełniać wymagania dotyczące usługi Azure, jeśli potrzebujesz. Można również wyświetlić i dodać informacje dotyczące sieci docelowej, podsieci i adresu IP, który zostanie przypisany do maszyny Wirtualnej platformy Azure.

Pamiętaj o następujących kwestiach:

* Możesz ustawić docelowy adres IP. Jeśli nie podasz adresu, maszyna w trybie failover będzie używać protokołu DHCP. Jeśli ustawisz adres który nie jest dostępna w trybie failover, pracy w trybie failover nie będą działać. Ten sam docelowy adres IP może być użyty do testowania trybu failover, jeśli adres jest dostępny w testowej sieci trybu failover.

* Na serwerze AD/DNS zachowywanie adresu lokalnego pozwala określić ten sam adres jako serwera DNS dla sieci wirtualnej platformy Azure.

Liczba kart sieciowych zależy od rozmiaru określonego dla docelowej maszyny wirtualnej, zgodnie z poniższym:

*   Jeśli liczba kart sieciowych w maszynie źródłowej jest mniejsza lub równa liczbie kart sieciowych dozwolonych dla rozmiaru maszyny docelowej, maszyna docelowa będzie mieć taką samą liczbę kart sieciowych jak maszyna źródłowa.
*   Jeśli liczba kart sieciowych dla źródłowej maszyny wirtualnej przekracza liczbę dozwolonych kart sieciowych dla rozmiaru maszyny docelowej, zostanie użyta maksymalna liczba kart dla rozmiaru maszyny docelowej.
* Na przykład, jeśli maszyna źródłowa ma dwie karty sieciowe, a rozmiar maszyny docelowej obsługuje cztery karty, maszyna docelowa będzie mieć dwie karty sieciowe. Jeśli maszyna źródłowa ma dwie karty sieciowe, ale rozmiar maszyny docelowej obsługuje tylko jedną kartę, maszyna docelowa będzie mieć tylko jedną kartę sieciową.
*   Jeśli maszyna wirtualna ma wiele kart sieciowych, które wszyscy klienci zostaną połączeni z tą samą siecią.
*   Jeśli maszyna wirtualna ma wiele kart sieciowych, pierwszy z nich wyświetlane na liście stanie się domyślną kartę sieciową maszyny wirtualnej platformy Azure.


## <a name="creating-a-recovery-plan"></a>Trwa tworzenie planu odzyskiwania

Po włączeniu replikacji dla maszyn wirtualnych składnika XenApp, następnym krokiem jest utworzenie planu odzyskiwania.
Grupy razem maszyny wirtualne mają podobne wymagania dotyczące trybu failover i odzyskiwania planu odzyskiwania.  

**Kroki, aby utworzyć plan odzyskiwania**

1. Dodaj składnik XenApp wirtualne w planie odzyskiwania.
2. Kliknij opcję plany odzyskiwania -> + planu odzyskiwania. Podaj nazwę intuicyjne dla planu odzyskiwania.
3. W przypadku maszyn wirtualnych programu VMware: Wybierz źródło jako serwer przetwarzania VMware, element docelowy jako Microsoft Azure i modelu wdrażania usługi Resource Manager i kliknij pozycję Wybierz elementy.
4. Dla maszyn wirtualnych funkcji Hyper-V: Wybierz źródło jako serwer programu VMM, element docelowy jako Microsoft Azure i modelu wdrażania przy użyciu jako usługi Resource Manager i kliknij pozycję Wybierz elementy, a następnie wybierz XenApp wdrażania maszyn wirtualnych.

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grupy trybu failover

Plany odzyskiwania można dostosować w taki sposób, aby dodać grupy trybu failover dla określonej kolejności uruchamiania, skrypty i działania ręczne. Następujące grupy muszą zostać dodane do planu odzyskiwania.

1. Grupa1 trybu failover: USŁUGI AD DNS
2. Group2 trybu failover: Maszyny wirtualne z programem SQL Server
2. Grupa trybu failover 3: VDA maszynę Wirtualną z obrazu Master
3. Failover Group4: Kontroler dostarczania StoreFront maszyny wirtualne i server


### <a name="adding-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania

Można uruchamiać skrypty przed lub po określonej grupy w planie odzyskiwania. Można również uwzględnione działania ręczne i wykonywane podczas pracy awaryjnej.

Plan odzyskiwania dostosowany wygląda jak poniżej:

1. Grupa1 trybu failover: USŁUGI AD DNS
2. Group2 trybu failover: Maszyny wirtualne z programem SQL Server
3. Grupa trybu failover 3: VDA maszynę Wirtualną z obrazu Master

   >[!NOTE]     
   >Kroki od 4, 6 i 7 zawierający akcje ręczne lub skrypt mają zastosowanie do lokalnych XenApp > środowisko z katalogami MCS/Odwiedziny.

4. Akcja ręczna lub skryptu grupa 3: Zamknij wzorca VDA maszyny Wirtualnej.
Wzorzec VDA maszyna wirtualna przełączone w tryb failover Azure będzie w stanie uruchomienia. Aby utworzyć nowe katalogi MCS przy użyciu hostingu platformy Azure, wzorca VDA maszyna wirtualna jest wymagane będzie zatrzymane (przydzielone de) stanu. Zamknij maszynę Wirtualną z witryny Azure portal.

5. Failover Group4: Kontroler dostarczania StoreFront maszyny wirtualne i server
6. Grupa 3 ręczny lub skryptu akcji 1:

    ***Dodawanie połączenia hosta usługi Azure RM***

    Utwórz połączenie z hostem platformy Azure na maszynie kontrolera dostarczania, aby aprowizować nowe katalogi MCS na platformie Azure. Wykonaj kroki opisane w tym [artykułu](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Grupa 3 ręczny lub skryptu akcji 2:

    ***Utwórz ponownie MCS katalogi na platformie Azure***

    Klony oddział MCS lub Odwiedziny istniejącego w lokacji głównej nie będą replikowane do platformy Azure. Należy ponownie utworzyć te klony przy użyciu replikowanych VDA głównego i Azure aprowizowania już od kontroler dostarczania. Wykonaj kroki opisane w tym [artykułu](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) tworzenia katalogów serwera MCS na platformie Azure.

![Plan odzyskiwania dla składników XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Możesz używać skryptów w [lokalizacji](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) zaktualizować DNS przy użyciu nowe adresy IP nie powiodło się za pośrednictwem > maszyny wirtualne lub dołączyć modułu równoważenia obciążenia w trybie przez maszynę wirtualną, jeśli to konieczne.


## <a name="doing-a-test-failover"></a>Podczas testowania trybu failover

Postępuj zgodnie z [Niniejsze wskazówki](site-recovery-test-failover-to-azure.md) celu testowy tryb failover.

![Plan odzyskiwania](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Przełączeniem w tryb failover

Postępuj zgodnie z [Niniejsze wskazówki](site-recovery-failover.md) po operacji przejścia w tryb failover.

## <a name="next-steps"></a>Kolejne kroki

Możesz [więcej](https://aka.ms/citrix-xenapp-xendesktop-with-asr) temat replikowania wdrożenia programów Citrix XenApp i XenDesktop, w tym dokumencie. Przyjrzyj się ze wskazówkami, aby [replikować inne aplikacje](site-recovery-workload.md) przy użyciu Site Recovery.
