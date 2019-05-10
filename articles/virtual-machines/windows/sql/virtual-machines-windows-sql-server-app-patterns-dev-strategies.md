---
title: Wzorce aplikacji dla programu SQL Server na maszynach wirtualnych | Dokumentacja firmy Microsoft
description: W tym artykule opisano wzorce aplikacji dla programu SQL Server na maszynach wirtualnych platformy Azure. To rozwiązanie stanowi rozwiązanie dla architektów i deweloperów podstawę dla projektowania i architektury dobrej aplikacji.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: ninarn
ms.openlocfilehash: 51d572ac324d0bc875e7ed81879f2456eeea4fbb
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506613"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Wzorce aplikacji i strategie programowania dla programu SQL Server w usłudze Azure Virtual Machines
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Podsumowanie:
Określanie, które wzorca aplikacji lub wzorce do użycia dla programu SQL Server na podstawie aplikacji na platformie Azure środowisko to decyzja ważna projektowania i wymaga to pełny opis jak programu SQL Server i każdy składnik infrastruktury platformy Azure współdziałają ze sobą. Z programem SQL Server w usługach infrastruktury platformy Azure można łatwo migrować, obsługi i monitorowania istniejących aplikacji programu SQL Server utworzone — w systemie Windows Server na maszynach wirtualnych platformy Azure.

Celem tego artykułu jest zapewnienie rozwiązanie dla architektów i deweloperów podstawę dla architektury dobrej aplikacji i projekt, który może wystąpić podczas migrowania istniejących aplikacji na platformie Azure, a także tworzenie nowych aplikacji na platformie Azure.

Dla każdego wzorca aplikacji można znaleźć scenariusz w środowisku lokalnym, rozwiązania do odpowiednich włączoną obsługę chmury i powiązanymi zaleceniami Technical Preview. Ponadto, w tym artykule omówiono strategie programowania specyficzne dla platformy Azure, dzięki czemu można zaprojektować aplikacje poprawnie. Ze względu na wiele wzorców aplikacji to możliwe zalecane jest, że architektów i deweloperów należy wybrać najbardziej odpowiedni wzorzec dla swoich aplikacji i użytkowników.

**Współautorzy techniczne:** Śledź Vargas Luis Carlos, Madhan Arumugam Ramakrishnan

**Recenzenci techniczni:** Corey Sanders, Drew McDaniel, Narayan Annamalai, i Nir Mashkowski Sanjay Mishra, Coriani Silvano, Stefan Schackow Tim Hickey, Tim Wieman Xin Jin

## <a name="introduction"></a>Wprowadzenie
Możesz tworzyć wiele rodzajów aplikacji n warstwowych, oddzielając składniki warstwy innej aplikacji na różnych maszynach, a także jak osobne składniki. Na przykład możesz umieścić aplikację klienta i firm reguł składników w jednej maszynie, warstwy frontonu sieci web i składniki warstwy dostępu do danych w innym komputerze i warstwę bazy danych zaplecza w innej maszynie. Tego rodzaju struktury ułatwia izolowanie każdej warstwy od siebie nawzajem. Jeśli zmienisz, skąd pochodzą dane, nie trzeba zmienić aplikacji sieci web lub klienta, ale tylko składniki warstwy dostępu do danych.

Typowa *n warstwowa* aplikacja zawiera warstwę prezentacji, warstwy biznesowej i warstwy danych:

| Warstwa | Opis |
| --- | --- |
| **Prezentacja** |*Warstwa prezentacji* (warstwy internetowej, warstwy frontonu) jest warstwą, gdzie użytkownicy dokonują interakcji z aplikacją. |
| **biznesowe** |*Warstwy biznesowej* (warstwy środkowej) jest warstwy, która warstwa prezentacji i warstwy danych służy do komunikowania się ze sobą i zawiera podstawowe funkcje systemu. |
| **Dane** |*Warstwy danych* to w zasadzie serwer przechowujący dane aplikacji (na przykład serwer działa program SQL Server). |

Aplikacja warstwy opisują logiczne grupowanie funkcji i składników w aplikacji; natomiast warstwy opisują fizycznej dystrybucji, funkcji i składników na oddzielnych serwerach fizycznych, komputery, sieci lub lokalizacji zdalnych. Warstwy aplikacji może znajdować się na tym samym komputerze fizycznym (tej samej warstwie) lub może być dystrybuowana za pośrednictwem różnych komputerach (n warstwowej) i składniki w każdej warstwie komunikują się ze składników w innych warstwach za pomocą dobrze zdefiniowanych interfejsów. Można traktować jako odnoszące się do fizycznej dystrybucji wzorców, takich jak dwuwarstwowa, trójwarstwowe i n warstwowa warstwy termin. A **2-warstwowy wzorzec aplikacji** zawiera dwie warstwy aplikacji: serwer aplikacji i serwera bazy danych. Bezpośrednia komunikacja odbywa się między serwerem aplikacji i serwera bazy danych. Serwer aplikacji zawiera składniki warstwy biznesowej i warstwy internetowej. W **3-warstwowy wzorzec aplikacji**, istnieją trzy warstwy aplikacji: serwera sieci web, serwera aplikacji, która zawiera warstwy logiki biznesowej i/lub składniki dostępu do danych warstwy biznesowej i serwer bazy danych. Komunikacja między serwerem sieci web a serwerem bazy danych odbywa się za pośrednictwem serwera aplikacji. Szczegółowe informacje na temat warstw aplikacji i warstwach, zobacz [Przewodnik po architekturze aplikacji Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Przed rozpoczęciem przeczytaniu tego artykułu, należy mieć wiedzy na podstawowe pojęcia programu SQL Server i Azure. Aby uzyskać informacje, zobacz [programu SQL Server — książki Online](https://msdn.microsoft.com/library/bb545450.aspx), [programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) i [Azure.com](https://azure.microsoft.com/).

W tym artykule opisano kilka wzorców aplikacji, które mogą być odpowiednie dla prostych aplikacji, a także bardzo złożonych aplikacji dla przedsiębiorstw. Przed ze szczegółami dotyczącymi każdego wzorca, zaleca się, że należy zapoznać się z usługami magazynu danych o dostępności na platformie Azure, takich jak [usługi Azure Storage](../../../storage/common/storage-introduction.md), [usługi Azure SQL Database](../../../sql-database/sql-database-technical-overview.md), i [SQL Serwer w maszynie wirtualnej platformy Azure](virtual-machines-windows-sql-server-iaas-overview.md). Aby najlepsze decyzje dotyczące projektu dla aplikacji, zrozumienie, kiedy należy używać wyraźnie która usługa magazynu danych.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Wybierz program SQL Server w maszynie wirtualnej platformy Azure, gdy:
* Należy kontroli dla programu SQL Server i Windows. Może to obejmować na przykład wersja programu SQL Server, specjalne poprawki, konfigurację wydajności itp.
* Potrzebujesz pełnej zgodności z lokalnego programu SQL Server i chcesz przenieść istniejące aplikacje na platformie Azure jako-to.
* Aby korzystać z możliwości środowiska platformy Azure, ale usługi Azure SQL Database nie obsługuje wszystkie funkcje wymagane przez aplikację. Może to obejmować następujące obszary:
  
  * **Rozmiar bazy danych**: W czasie, który został zaktualizowany w tym artykule SQL Database obsługuje bazę danych do 1 TB. Jeśli aplikacja wymaga więcej niż 1 TB danych, a użytkownik nie chce wdrożyć rozwiązania niestandardowe fragmentowania, zaleca się, że używasz programu SQL Server w maszynie wirtualnej platformy Azure. Aby uzyskać najnowsze informacje, zobacz [skalowanie w poziomie usługi Azure SQL Database](https://msdn.microsoft.com/library/azure/dn495641.aspx), [Model zakupu DTU-Based](../../../sql-database/sql-database-service-tiers-dtu.md), i [Model oparty na rdzeniach wirtualnych zakup](../../../sql-database/sql-database-service-tiers-vcore.md)(wersja zapoznawcza).
  * **Zgodność HIPAA**: Klienci opieki zdrowotnej i niezależnym dostawcom oprogramowania (ISV), może wybrać [programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) zamiast [usługi Azure SQL Database](../../../sql-database/sql-database-technical-overview.md) ponieważ programu SQL Server w maszynie wirtualnej platformy Azure jest objęty przez HIPAA Business Associate Agreement (BAA). Aby uzyskać informacje na temat zgodności, zobacz [Microsoft Azure Trust Center: Zgodność](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funkcje na poziomie wystąpienia**: W tej chwili bazy danych SQL nie obsługuje funkcji, które na żywo poza bazą danych (takich jak serwery połączone, Agent zadania, FileStream programu Service Broker, itp.). Aby uzyskać więcej informacji, zobacz [wytyczne dotyczące bazy danych SQL Azure i ograniczenia](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>warstwy 1 (proste): jedna maszyna wirtualna
W tym wzorcu aplikacji wdrażania aplikacji programu SQL Server i bazy danych do autonomicznej maszyny wirtualnej na platformie Azure. Tej samej maszyny wirtualnej zawiera aplikację klienta/sieci web, składniki biznesowej, warstwy dostępu do danych i serwera bazy danych. Prezentacja, firm i kod dostępu do danych są logicznie oddzielone, ale znajdują się fizycznie na jednym serwerze maszynie. Większość klientów zaczyna od tego wzorca aplikacji, a następnie ich skalowanie w poziomie, dodając więcej ról sieć web lub maszyn wirtualnych do swojego systemu.

Ten wzorzec aplikacji jest przydatne, gdy:

* Chcesz wykonać proste migracji do platformy Azure, aby ocenić, czy platforma odpowiedzi spełnić wymagania aplikacji, czy nie.
* Chcesz zachować wszystkie warstwy aplikacji hostowanej w tej samej maszyny wirtualnej, w tym samym centrum danych platformy Azure w celu zmniejszenia opóźnienia między warstwami.
* Chcesz szybko Udostępnij rozwoju i środowisk testowych przez krótki czas.
* Chcesz wykonać testowanie pod kątem różnych poziomów obciążeń obciążenia, ale w tym samym czasie można zrezygnować z właścicielem i obsługa wielu komputerów fizycznych przez cały czas.

Poniższy diagram przedstawia prostą lokalnych scenariusza i jak może wdrożyć swoje rozwiązanie chmura włączona w pojedynczej maszyny wirtualnej na platformie Azure.

![1-warstwowy wzorzec aplikacji](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Wdrażanie warstwy biznesowej (logika biznesowa i dostęp do składników danych) w tej samej warstwie fizycznej jako Warstwa prezentacji można zmaksymalizować wydajność aplikacji, o ile nie musi używać odrębnej warstwy ze względu na problemy skalowalności i zabezpieczeń.

Ponieważ jest to bardzo typowy wzorzec zaczynać, może być przydatne następujący artykuł w migracji do przenoszenia danych do maszyny Wirtualnej programu SQL Server: [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3-warstwowej (proste): wiele maszyn wirtualnych
W tym wzorcu aplikacji wdrożeniem aplikacji 3-warstwowej na platformie Azure przez umieszczenie każdej warstwy aplikacji w innej maszyny wirtualnej. Zapewnia to elastyczne środowisko scenariuszach łatwe skalowanie w górę i skalowania w poziomie. Po jednej maszyny wirtualnej zawiera aplikację klienta/sieci web, druga hostuje składniki biznesowych, a druga hostuje serwer bazy danych.

Ten wzorzec aplikacji jest przydatne, gdy:

* Chcesz przeprowadzić migrację aplikacji złożonych baz danych do usługi Azure Virtual Machines.
* Chcesz, aby warstwach innej aplikacji, aby znajdować się w różnych regionach. Na przykład może być udostępnione baz danych, które zostały wdrożone w wielu regionach na potrzeby raportowania.
* Chcesz przenieść aplikacje przedsiębiorstwa z platform w środowisku lokalnym zwirtualizowanych na maszynach wirtualnych platformy Azure. Aby uzyskać szczegółowe omówienie dotyczące aplikacji dla przedsiębiorstw, zobacz [co to jest aplikacja dla przedsiębiorstw](https://msdn.microsoft.com/library/aa267045.aspx).
* Chcesz szybko Udostępnij rozwoju i środowisk testowych przez krótki czas.
* Chcesz wykonać testowanie pod kątem różnych poziomów obciążeń obciążenia, ale w tym samym czasie można zrezygnować z właścicielem i obsługa wielu komputerów fizycznych przez cały czas.

Poniższy diagram przedstawia, jak umieścić prostej aplikacji 3-warstwową na platformie Azure przez umieszczenie każdej warstwy aplikacji w innej maszyny wirtualnej.

![3-warstwowy wzorzec aplikacji](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

W tym wzorcu aplikacji istnieje tylko jedna maszyna wirtualna (VM) w przypadku każdej warstwy. Jeśli masz wiele maszyn wirtualnych na platformie Azure, zalecamy Konfigurowanie sieci wirtualnej. [Usługa Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) tworzy granicę zapewniającą zaufanych i umożliwia również maszyny wirtualne do komunikacji między sobą za pośrednictwem prywatnego adresu IP. Ponadto zawsze upewnij się, że wszystkie połączenia internetowe odbywają się tylko warstwy prezentacji. Przy stosowaniu tego wzorca aplikacja, zarządzanie reguły sieciowej grupy zabezpieczeń do kontrolowania dostępu. Aby uzyskać więcej informacji, zobacz [zezwalanie na dostęp zewnętrzny do maszyny Wirtualnej przy użyciu witryny Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Na diagramie protokołów internetowych może być TCP, UDP, HTTP lub HTTPS.

> [!NOTE]
> Konfigurowanie sieci wirtualnych na platformie Azure jest bezpłatne. Jednak opłaty są naliczane dla bramy sieci VPN, który nawiązuje połączenie z lokalnym. Ta opłata jest oparty na czas, jaki połączenie VPN jest obsługiwana i dostępna.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>warstwy 2 i 3-warstwowej przy użyciu skalowania warstwy prezentacji w poziomie
W tym wzorcu aplikacji wdrożeniem aplikacji warstwy 2 lub 3-warstwowej baza danych na maszynach wirtualnych platformy Azure przez umieszczenie każdej warstwy aplikacji w innej maszyny wirtualnej. Ponadto możesz skalować w poziomie warstwy prezentacji z powodu zwiększonej ilości przychodzących żądań klienta.

Ten wzorzec aplikacji jest przydatne, gdy:

* Chcesz przenieść aplikacje przedsiębiorstwa z platform w środowisku lokalnym zwirtualizowanych na maszynach wirtualnych platformy Azure.
* Chcesz skalować w poziomie warstwy prezentacji z powodu zwiększonej ilości przychodzących żądań klienta.
* Chcesz szybko Udostępnij rozwoju i środowisk testowych przez krótki czas.
* Chcesz wykonać testowanie pod kątem różnych poziomów obciążeń obciążenia, ale w tym samym czasie można zrezygnować z właścicielem i obsługa wielu komputerów fizycznych przez cały czas.
* Chcesz być właścicielem środowiska infrastruktury, która może być skalowana w górę i w dół na żądanie.

Poniższy diagram przedstawia, jak można umieścić warstwy aplikacji w wielu maszyn wirtualnych na platformie Azure, skalując w poziomie warstwy prezentacji z powodu zwiększonej ilości przychodzących żądań klienta. Jak widać na diagramie, Azure Load Balancer jest odpowiedzialna za dystrybucję ruchu między wieloma maszynami wirtualnymi, a także określenie których serwer sieci web, aby nawiązać połączenie. Posiadanie wielu wystąpień serwerów sieci web za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwy prezentacji.

![Aplikacja wzorzec - skalowania warstwy prezentacji](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Najlepsze rozwiązania dotyczące warstwy 2, 3 warstwy lub n warstwowa wzorców, które zawierają wiele maszyn wirtualnych w jednej warstwy
Zaleca się umieszczenie maszyn wirtualnych, które należą do tej samej warstwy w tej samej usłudze w chmurze i w tym samym dostępność zestawu. Na przykład umieścić zestawu serwerów sieci web w **CloudService1** i **AvailabilitySet1** oraz zestaw serwerów baz danych w **CloudService2** i  **AvailabilitySet2**. Zestaw dostępności w usłudze Azure pozwala na umieszczenie węzłów o wysokiej dostępności w oddzielnych domenach błędów i uaktualnień.

Aby korzystać z wielu wystąpień maszyn wirtualnych warstwy należy skonfigurować usługi Azure Load Balancer między warstwami aplikacji. Aby skonfigurować moduł równoważenia obciążenia w poszczególnych warstwach, należy utworzyć punkt końcowy z równoważeniem obciążenia na maszynach wirtualnych z każdej warstwy oddzielnie. Dla określonej warstwy należy najpierw utworzyć maszyny wirtualne w tej samej usłudze w chmurze. Gwarantuje to, że mają one ten sam publiczny wirtualny adres IP. Następnie należy utworzyć punkt końcowy w jednej z maszyn wirtualnych w danej warstwie. Następnie Przypisz ten sam punkt końcowy dla innych maszyn wirtualnych w tej warstwie do równoważenia obciążenia. Dzięki utworzeniu zestawu z równoważeniem obciążenia, dystrybuowanie ruchu między wieloma maszynami wirtualnymi i również zezwolić na użycie modułu równoważenia obciążenia określić, które węzłowi łączność, gdy węzeł maszyny Wirtualnej zaplecza nie powiodło się. Na przykład masz wiele wystąpień serwerów sieci web za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwy prezentacji.

Najlepszym rozwiązaniem jest zawsze upewnij się, że wszystkie połączenia internetowe Przejdź najpierw do warstwy prezentacji. Warstwa prezentacji uzyskuje dostęp do warstwy biznesowej, a następnie warstwy biznesowej uzyskuje dostęp do warstwy danych. Aby uzyskać więcej informacji na temat zezwolić na dostęp do warstwy prezentacji, zobacz [zezwalanie na dostęp zewnętrzny do maszyny Wirtualnej przy użyciu witryny Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Należy pamiętać, że moduł równoważenia obciążenia na platformie Azure działa podobnie do obciążenia równoważenia w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [równoważenia obciążenia dla usług infrastruktury platformy Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ponadto zaleca się, że skonfigurowaniu sieci prywatnej dla maszyn wirtualnych przy użyciu usługi Azure Virtual Network. Uprawnienie pozwoli im do komunikacji między sobą za pośrednictwem prywatnego adresu IP. Aby uzyskać więcej informacji, zobacz [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>warstwy 2 i 3-warstwowej z firmą warstwy skalowania w poziomie
W tym wzorcu aplikacji wdrożeniem aplikacji warstwy 2 lub 3-warstwowej baza danych na maszynach wirtualnych platformy Azure przez umieszczenie każdej warstwy aplikacji w innej maszyny wirtualnej. Ponadto można rozpowszechnić serwera aplikacji na wiele maszyn wirtualnych z powodu złożoności aplikacji.

Ten wzorzec aplikacji jest przydatne, gdy:

* Chcesz przenieść aplikacje przedsiębiorstwa z platform w środowisku lokalnym zwirtualizowanych na maszynach wirtualnych platformy Azure.
* Chcesz dystrybuować składników serwera aplikacji na wiele maszyn wirtualnych z powodu złożoności aplikacji.
* Chcesz przenieść duże logiki biznesowej w środowisku lokalnym aplikacji (line-of-business) biznesowych na maszynach wirtualnych platformy Azure. Aplikacje LOB to zbiór aplikacji dla komputerów krytyczne, które są niezbędne do uruchamiania przedsiębiorstwa, takich jak ewidencjonowania aktywności, zarządzania zasobami ludzkimi (HR), Lista płac, zarządzania łańcuchem dostaw i aplikacji do planowania zasobów.
* Chcesz szybko Udostępnij rozwoju i środowisk testowych przez krótki czas.
* Chcesz wykonać testowanie pod kątem różnych poziomów obciążeń obciążenia, ale w tym samym czasie można zrezygnować z właścicielem i obsługa wielu komputerów fizycznych przez cały czas.
* Chcesz być właścicielem środowiska infrastruktury, która może być skalowana w górę i w dół na żądanie.

Poniższy diagram przedstawia scenariusz w środowisku lokalnym i swoje rozwiązanie chmura włączona. Skalowanie warstwy biznesowej, która zawiera warstwy logiki biznesowej i składniki dostępu do danych, w tym scenariuszu, umieść warstwy aplikacji w wielu maszyn wirtualnych na platformie Azure. Jak widać na diagramie, Azure Load Balancer jest odpowiedzialna za dystrybucję ruchu między wieloma maszynami wirtualnymi, a także określenie których serwer sieci web, aby nawiązać połączenie. Posiadanie wielu wystąpień serwerów aplikacji za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwy biznesowej. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące wzorców warstwy 2, 3 warstwy lub n warstwowej aplikacji, które mają wiele maszyn wirtualnych w jednej warstwie](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Wzorzec aplikacji za pomocą skalowania warstwy biznesowej](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>warstwy 2 i 3-warstwowej przy użyciu prezentacji i skalowanie warstwy biznesowej oraz HADR
W tym wzorcu aplikacji wdrożeniem aplikacji warstwy 2 lub 3-warstwowej baza danych na maszynach wirtualnych platformy Azure dzięki rozłożeniu warstwę prezentacji (serwer sieci web) i składniki (aplikacja serwera) warstwy biznesowej na wiele maszyn wirtualnych. Ponadto implementować rozwiązania w zakresie odzyskiwania po awarii i wysoka dostępność dla baz danych w usłudze Azure virtual machines.

Ten wzorzec aplikacji jest przydatne, gdy:

* Chcesz przenieść aplikacje dla przedsiębiorstw z platformami zwirtualizowanym lokalnej na platformę Azure, wdrażając wysokiej dostępności programu SQL Server i możliwości odzyskiwania po awarii.
* Chcesz skalować warstwą prezentacji a warstwą biznesową z powodu zwiększonej ilości przychodzące żądania klientów i złożoność aplikacji.
* Chcesz szybko Udostępnij rozwoju i środowisk testowych przez krótki czas.
* Chcesz wykonać testowanie pod kątem różnych poziomów obciążeń obciążenia, ale w tym samym czasie można zrezygnować z właścicielem i obsługa wielu komputerów fizycznych przez cały czas.
* Chcesz być właścicielem środowiska infrastruktury, która może być skalowana w górę i w dół na żądanie.

Poniższy diagram przedstawia scenariusz w środowisku lokalnym i swoje rozwiązanie chmura włączona. W tym scenariuszu możesz skalować w poziomie warstwą prezentacji a składników warstwy biznesowej w wielu maszyn wirtualnych na platformie Azure. Ponadto zaimplementować wysoką dostępność i odzyskiwanie po awarii technik odzyskiwania (HADR) baz danych programu SQL Server na platformie Azure.

Uruchamianie wielu kopii aplikacji w różnych maszyn wirtualnych upewnij się, że jesteś Równoważenie obciążenia żądań w ich. Jeśli masz wiele maszyn wirtualnych, należy się upewnić, że wszystkie maszyny wirtualne są dostępne i działa w jednym punkcie w czasie. Jeśli skonfigurujesz równoważenia obciążenia, usługi Azure Load Balancer śledzi kondycję maszyn wirtualnych i kieruje wywołań przychodzących do dobrej kondycji węzłów maszyny Wirtualnej działają prawidłowo. Aby uzyskać informacje na temat sposobu konfigurowania równoważenia obciążenia maszyn wirtualnych, zobacz [równoważenia obciążenia dla usług infrastruktury platformy Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Posiadanie wielu wystąpień aplikacji internetowej i serwerami za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwy prezentacji i business.

![Skalowalny w poziomie i wysoka dostępność](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Najlepsze rozwiązania dotyczące wzorców do zastosowań wymagających SQL HADR
Podczas konfigurowania wysokiej dostępności programu SQL Server i rozwiązania odzyskiwania po awarii w usłudze Azure Virtual Machines, konfigurowanie sieci wirtualnej dla maszyn wirtualnych przy użyciu [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) jest obowiązkowe.  Maszyny wirtualne w ramach sieci wirtualnej mają stabilnej prywatny adres IP nawet po zakończeniu przestoju usługi, ten sposób można uniknąć czas aktualizacji wymaganych dla rozpoznawania nazw DNS. Ponadto sieci wirtualnej umożliwia rozszerzanie sieci lokalnych na platformę Azure i tworzy granicę zapewniającą zaufanych. Na przykład, jeśli aplikacja ma ograniczenia domeny firmy (na przykład, Windows uwierzytelniania usługi Active Directory) Konfigurowanie [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) jest konieczne.

Większość klientów, którzy uruchomili kodzie produkcyjnym na platformie Azure, powodują podstawowych i pomocniczych replik w systemie Azure.

Aby uzyskać szczegółowe informacje i samouczki dotyczące wysokiej dostępności i technik odzyskiwania po awarii, zobacz [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>warstwy 2 i 3-warstwowej przy użyciu maszyn wirtualnych platformy Azure i usług w chmurze
W tym wzorcu aplikacji wdrożeniem aplikacji warstwy 2 lub 3-warstwową na platformie Azure przy użyciu zarówno [usług Azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (role sieć web i proces roboczy — platforma jako usługa (PaaS)) i [maszyn wirtualnych platformy Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) () Infrastruktura jako usługa (IaaS)). Za pomocą [usług Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) warstwy biznesowym/warstwy prezentacji i programu SQL Server w [maszyn wirtualnych platformy Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) danych warstwa jest korzystne w przypadku większości aplikacji działających na platformie Azure. Przyczyną jest to, że o wystąpieniu obliczeniowym, w systemie usług w chmurze zapewnia łatwe zarządzanie, wdrażania, monitorowania i skalowania w poziomie.

Z usługami w chmurze platformy Azure obsługuje infrastrukturę dla Ciebie, wykonuje rutynowej konserwacji, poprawek systemów operacyjnych i próbuje odzyskać sprawność po awarii sprzętu i usługi. Gdy Twoja aplikacja potrzebuje skalowania w poziomie, automatycznego i ręcznego opcje skalowania w poziomie są dostępne dla projektu usługi w chmurze przez zwiększenie lub zmniejszenie liczby wystąpień maszyn wirtualnych, które są używane przez aplikację. Ponadto można użyć w środowisku lokalnym program Visual Studio do wdrażania aplikacji do projektu usługi w chmurze na platformie Azure.

Podsumowując Jeśli nie chcesz być właścicielem rozbudowane warstwy zadań administracyjnych dotyczących prezentacji i/lub business i aplikacja nie wymaga złożonych konfiguracji oprogramowania lub systemu operacyjnego, użyj usług Azure Cloud Services. Jeśli usługa Azure SQL Database nie obsługuje wszystkie funkcje, których szukasz, użyj programu SQL Server w maszynie wirtualnej platformy Azure dla warstwy danych. Uruchamianie aplikacji w usługach Azure Cloud Services i przechowywanie danych w usłudze Azure Virtual Machines łączy w sobie zalety obu usług. Aby uzyskać szczegółowe porównanie, zobacz sekcję w tym temacie w [porównanie strategie programowania na platformie Azure](#comparing-web-development-strategies-in-azure).

W tym wzorcu aplikacji Warstwa prezentacji zawiera rolę sieci web, w której działa składnik usług w chmurze w środowisku wykonawczym systemu Azure i jest ono dostosowane do programowania aplikacji sieci web, ponieważ obsługiwane przez usługi IIS i platformy ASP.NET. Warstwy biznesowej lub wewnętrznej bazy danych obejmuje rolę procesu roboczego, który jest uruchomiony składnik usług w chmurze w środowisku wykonawczym systemu Azure i jest przydatne w przypadku ogólnych rozwoju i może wykonywać przetwarzania w tle dla roli sieci web. Warstwa bazy danych znajduje się na maszynie wirtualnej programu SQL Server na platformie Azure. Komunikacja między warstwą prezentacji a warstwą bazy danych odbywa się bezpośrednio lub za pośrednictwem warstwy biznesowej — składniki roli procesu roboczego.

Ten wzorzec aplikacji jest przydatne, gdy:

* Chcesz przenieść aplikacje dla przedsiębiorstw z platformami zwirtualizowanym lokalnej na platformę Azure, wdrażając wysokiej dostępności programu SQL Server i możliwości odzyskiwania po awarii.
* Chcesz być właścicielem środowiska infrastruktury, która może być skalowana w górę i w dół na żądanie.
* Usługa Azure SQL Database nie obsługuje wszystkie funkcje, których potrzebuje Twoja aplikacja lub bazy danych.
* Chcesz wykonać testowanie pod kątem różnych poziomów obciążeń obciążenia, ale w tym samym czasie można zrezygnować z właścicielem i obsługa wielu komputerów fizycznych przez cały czas.

Poniższy diagram przedstawia scenariusz w środowisku lokalnym i swoje rozwiązanie chmura włączona. W tym scenariuszu możesz umieścić warstwy prezentacji w rolach sieci web, warstwy biznesowej w roli procesu roboczego, ale warstwy danych w maszynach wirtualnych platformy Azure. Zapewnia uruchamianie wielu kopii warstwy prezentacji w roli innej witryny sieci web, można wysyłać żądania równoważenia obciążenia między nimi. Podczas łączenia usług Azure Cloud Services w usłudze Azure Virtual Machines zaleca się skonfigurowanie [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) także. Za pomocą [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), możesz mieć stabilnego działania i trwałego prywatnych adresów IP w tej samej usługi w chmurze w chmurze. Po zdefiniowaniu sieci wirtualnej dla maszyn wirtualnych i usług w chmurze, można uruchomić, komunikować się ze sobą za pośrednictwem prywatnego adresu IP. Ponadto o maszyny wirtualne oraz role sieci web/procesu roboczego platformy Azure, w tym samym [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) zapewnia małe opóźnienia i bardziej bezpiecznej łączności. Aby uzyskać więcej informacji, zobacz [co to jest usługa w chmurze](../../../cloud-services/cloud-services-choose-me.md).

Jak widać na diagramie, usługa Azure Load Balancer dystrybuuje ruch między wieloma maszynami wirtualnymi i określa również, które serwer sieci web lub serwera aplikacji, aby nawiązać połączenie. Posiadanie wielu wystąpień serwerów sieci web i aplikacji, za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwą prezentacji a warstwą biznesową. Aby uzyskać więcej informacji, zobacz [najlepszych rozwiązań dla aplikacji wzorców wymagające HADR SQL](#best-practices-for-application-patterns-requiring-sql-hadr).

![Wzorce aplikacji z usługami w chmurze](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Innego podejścia do implementacji tego wzorca aplikacja ma korzystać rola skonsolidowane w sieci web, która zawiera warstwę prezentacji i składników warstwy biznesowej, jak pokazano na poniższym diagramie. Ten wzorzec aplikacji jest przydatne w przypadku aplikacji wymagających stanowych projektu. Platforma Azure udostępnia węzły obliczeniowe o bezstanowa na role sieć web i proces roboczy, zalecamy zaimplementowanie logiki do przechowywania stanu sesji przy użyciu jednej z następujących technologii: [Buforowanie Azure](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [usługi Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) lub [usługi Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).

![Wzorce aplikacji z usługami w chmurze](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Wzorzec z maszyn wirtualnych platformy Azure, Azure SQL Database i Azure App Service (aplikacje sieci Web)
Podstawowym celem tego wzorca aplikacji jest dowiesz się, jak połączyć infrastruktury platformy Azure jako składniki usługi (IaaS), za pomocą składników platformy Azure platforma jako usługa (PaaS) w Twoim rozwiązaniu. Ten wzorzec koncentruje się na usłudze Azure SQL Database do przechowywania danych relacyjnych. Nie obejmuje program SQL Server na maszynie wirtualnej platformy Azure, który jest częścią infrastruktury platformy Azure jako oferty usługi.

W tym wzorcu aplikacji wdrożysz aplikację bazy danych na platformę Azure przez umieszczenie warstwy prezentacji i biznesowych w tej samej maszyny wirtualnej i uzyskiwania dostępu do bazy danych na serwerach usługi Azure SQL Database (usługa SQL Database). Warstwa prezentacji można wdrożyć przy użyciu rozwiązania tradycyjnych oparty na usługach sieci web. Lub możesz zaimplementować połączone prezentacji i warstwy biznesowej przy użyciu [usługi Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/).

Ten wzorzec aplikacji jest przydatne, gdy:

* Masz już istniejącego serwera bazy danych SQL skonfigurowany na platformie Azure i chcesz szybko przetestować aplikację.
* Chcesz przetestować funkcje środowiska platformy Azure.
* Chcesz szybko Udostępnij rozwoju i środowisk testowych przez krótki czas.
* Składniki dostępu logikę i dane firm mogą być niezależne w obrębie aplikacji sieci web.

Poniższy diagram przedstawia scenariusz w środowisku lokalnym i swoje rozwiązanie chmura włączona. W tym scenariuszu możesz umieścić warstwy aplikacji w pojedynczej maszyny wirtualnej platformy Azure i dostęp do danych w usłudze Azure SQL Database.

![Wzorzec mieszane aplikacji](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Jeśli zdecydujesz się wdrożyć połączone sieci web i warstwę aplikacji przy użyciu usługi Azure Web Apps, zaleca się zachować warstwy aplikacji lub warstwy środkowej jako bibliotek dołączanych dynamicznie (dll) w kontekście aplikacji sieci web.

Co więcej, zapoznaj się z zaleceniami podanymi w [Porównanie strategii rozwoju sieci web na platformie Azure](#comparing-web-development-strategies-in-azure) sekcji na końcu tego artykułu, aby dowiedzieć się więcej o technikach programowania.

## <a name="n-tier-hybrid-application-pattern"></a>Wzorzec aplikacji N-warstwowa hybrydowe
Wzorca aplikacji n warstwowa hybrydowego służy do wdrożenia aplikacji w wielu warstwach rozproszone między lokalną i platformą Azure. W związku z tym, Utwórz systemu hybrydowego elastyczne i wielokrotnego użytku, które można zmodyfikować lub dodać określonej warstwy bez wprowadzania zmian w innych warstwach. Aby rozszerzyć sieci firmowej do chmury, należy użyć [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) usługi.

Ten wzorzec aplikacji hybrydowej jest przydatne, gdy:

* Chcesz tworzyć aplikacje, które działają częściowo w chmurze i częściowo lokalnie.
* Użytkownik chce migrować niektóre lub wszystkie elementy w istniejącej aplikacji lokalnych do chmury.
* Chcesz przenieść aplikacje przedsiębiorstwa z platform wirtualizacji lokalnej na platformę Azure.
* Chcesz być właścicielem środowiska infrastruktury, która może być skalowana w górę i w dół na żądanie.
* Chcesz szybko Udostępnij rozwoju i środowisk testowych przez krótki czas.
* Chcesz, aby ekonomiczne rozwiązanie do wykonania kopii zapasowych dla przedsiębiorstw, aplikacji baz danych.

Poniższy diagram przedstawia wzorca aplikacji n warstwowa hybrydowe, obejmujący w środowisku lokalnym i platformą Azure. Jak pokazano na diagramie, w środowisku lokalnym infrastruktura obejmuje [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) kontrolera domeny w celu obsługi uwierzytelniania i autoryzacji użytkowników. Należy pamiętać, że diagram przedstawia scenariusz, gdzie niektóre części warstwy danych na żywo w centrum danych w środowisku lokalnym, podczas gdy niektóre części warstwy danych na żywo na platformie Azure. W zależności od potrzeb aplikacji można zaimplementować kilka scenariuszy hybrydowych. Na przykład w środowisku lokalnym, ale warstwy danych na platformie Azure mogą zachować warstwą prezentacji a warstwą biznesową.

![N-warstwowy wzorzec aplikacji](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

Na platformie Azure, można użyć usługi Active Directory jako autonomiczny katalog chmury dla organizacji lub możesz także zintegrować z istniejącą lokalną usługą Active Directory za pomocą [usługi Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Jak pokazano na diagramie składników warstwy biznesowej dostęp do wielu źródeł danych, takich jak do [programu SQL Server na platformie Azure](virtual-machines-windows-sql-server-iaas-overview.md) za pośrednictwem prywatnych wewnętrzny adres IP, do lokalnego programu SQL Server za pośrednictwem [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), lub [bazy danych SQL](../../../sql-database/sql-database-technical-overview.md) przy użyciu technologii dostawcy danych .NET Framework. Na poniższym diagramie bazy danych Azure SQL Database to usługa magazynu opcjonalnymi danymi.

We wzorcu aplikacji n warstwowa hybrydowe można zaimplementować poniższy przepływ pracy w określonej kolejności:

1. Zidentyfikuj aplikacje bazy danych dla przedsiębiorstw, które mają zostać przeniesione w do chmury przy użyciu [Microsoft Assessment i zestaw narzędzi do planowania (MAP)](https://microsoft.com/map). Zestaw narzędzi Mapa zbiera dane spisu i wydajności z komputerów, które są rozważane do wirtualizacji i zawiera zalecenia dotyczące wydajności i planowania oceny.
2. Planowanie zasobów i wykonywania na platformie Azure, takich jak konta magazynu i maszyn wirtualnych czynności konfiguracyjnych.
3. Skonfiguruj połączenie sieciowe między siecią firmową w środowisku lokalnym i [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Aby skonfigurować połączenie między siecią firmową w środowisku lokalnym i maszynę wirtualną na platformie Azure, użyj jednej z następujących dwóch metod:
   
   1. Nawiąż połączenie między lokalną i platformą Azure za pośrednictwem publiczne punkty końcowe na maszynie wirtualnej na platformie Azure. Ta metoda zapewnia Łatwa konfiguracja i pozwala na użycie uwierzytelniania programu SQL Server na maszynie wirtualnej. Dodatkowo skonfigurować swoje reguły sieciowej grupy zabezpieczeń do kontrolowania ruchu publicznego do maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [zezwalanie na dostęp zewnętrzny do maszyny Wirtualnej przy użyciu witryny Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Nawiąż połączenie między lokalną i platformą Azure przy użyciu Azure wirtualnego prywatnej tunelu sieci VPN. Ta metoda umożliwia rozszerzenie zasad domeny do maszyny wirtualnej na platformie Azure. Ponadto możesz Konfigurowanie reguł zapory i uwierzytelnianie Windows na maszynie wirtualnej. Obecnie platforma Azure obsługuje bezpieczne VPN lokacja lokacja i połączeń VPN typu punkt lokacja:
      
      * Przy użyciu bezpiecznego połączenia lokacja lokacja można ustanowić łączności sieciowej między siecią lokalną i siecią wirtualną na platformie Azure. Zaleca się podłączania środowisku centrum danych lokalnych do platformy Azure.
      * Przy użyciu bezpiecznego połączenia punkt lokacja można ustanowić łączności sieciowej między siecią wirtualną na platformie Azure i poszczególne komputery z uruchomionym w dowolnym miejscu. Zaleca się głównie do celów tworzenia i testowania.
      
      Aby uzyskać informacje na temat sposobu łączenia się z serwerem SQL na platformie Azure, zobacz [Connect do maszyny wirtualnej SQL Server na platformie Azure](virtual-machines-windows-sql-connect.md).
4. Skonfiguruj zaplanowane zadania i alerty, które wykonują kopie zapasowe danych lokalnych w dysku maszyny wirtualnej na platformie Azure. Aby uzyskać więcej informacji, zobacz [kopii zapasowej programu SQL Server i przywracania w usłudze Azure Blob Storage](https://msdn.microsoft.com/library/jj919148.aspx) i [kopia zapasowa i przywracanie programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).
5. W zależności od potrzeb aplikacji można zaimplementować następujące trzy typowe scenariusze:
   
   1. Serwera sieci web, serwera aplikacji i niezależnych od danych można przechowywać w serwer bazy danych na platformie Azure należy przechowywać poufnych danych w sieci lokalnej.
   2. Można zachować na serwerze sieci web i serwera aplikacji lokalnych podczas gdy serwer bazy danych na maszynie wirtualnej na platformie Azure.
   3. Można zachować bazę danych serwera i serwera sieci web i aplikacji — lokalny serwer repliki bazy danych można przechowywać na maszynach wirtualnych na platformie Azure. To ustawienie umożliwia lokalne serwery sieci web lub aplikacji raportowania można uzyskać dostępu do replik bazy danych na platformie Azure. W związku z tym można uzyskać, aby zmniejszyć obciążenie w lokalnej bazie danych. Firma Microsoft zaleca, aby zaimplementować ten scenariusz ciężkich odczytu funkcjonowanie celów i obciążeń. Aby uzyskać informacje na temat tworzenia repliki bazy danych na platformie Azure, zobacz zawsze włączonych grup dostępności w [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Porównywanie strategii rozwoju sieci web na platformie Azure
Aby zaimplementować i wdrożyć wielowarstwowej aplikacji opartych na programie SQL Server na platformie Azure, można użyć jednej z następujących dwóch metod programowania:

* Skonfiguruj serwer tradycyjnej sieci web (IIS - Internet Information Services) w platformy Azure i dostęp do baz danych w programie SQL Server na maszynach wirtualnych platformy Azure.
* Implementowanie i wdrażanie usługi w chmurze na platformie Azure. Następnie należy upewnić się, że ta usługa w chmurze mają dostęp do baz danych w programie SQL Server na maszynach wirtualnych platformy Azure. Usługa w chmurze może zawierać wiele ról sieć web i proces roboczy.

Poniższa tabela zawiera porównanie tworzenia tradycyjnych sieci web za pomocą usług Azure Cloud Services i Azure Web Apps w odniesieniu do programu SQL Server na maszynach wirtualnych platformy Azure. Tabela zawiera aplikacje internetowe platformy Azure można używać programu SQL Server w maszynie Wirtualnej platformy Azure jako źródło danych usługi Azure Web Apps za pośrednictwem jego publiczny wirtualny adres IP lub nazwę DNS.

|  | Programowanie dla tradycyjnych sieci web w usłudze Azure Virtual Machines | Usługi w chmurze na platformie Azure | Hostingu za pomocą usługi Azure Web Apps w sieci Web |
| --- | --- | --- | --- |
| **Migracja aplikacji ze środowiska lokalnego** |Istniejące aplikacje jako-to. |Aplikacje muszą role sieć web i proces roboczy. |Istniejące aplikacje jako — ale nadaje się do aplikacji sieci web niezależna i usług sieci web, które wymagają szybkiej skalowalności. |
| **Tworzenie i wdrażanie** |Visual Studio, program WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, Menedżera usług IIS i programu PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. Każda usługa w chmurze ma dwoma środowiskami, na których można wdrożyć z pakietu usługi i konfiguracji: środowisk przejściowych i produkcyjnych. Usługa w chmurze można wdrożyć w środowisku przejściowym ją przetestować przed Przenieś go do produkcji. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Deploy, PowerShell. |
| **Administracji i konfiguracji** |Odpowiedzialność za zadania administracyjne dotyczące aplikacji, danych, reguły zapory, sieci wirtualnej i systemu operacyjnego. |Odpowiedzialność za zadania administracyjne dotyczące aplikacji, danych, reguły zapory i sieci wirtualnej. |Odpowiedzialność za zadania administracyjne w aplikacji i tylko dane. |
| **Wysokiej dostępności i odzyskiwania awaryjnego (HADR)** |Firma Microsoft zaleca, umieszczenia maszyn wirtualnych, w tym samym zestawie dostępności i w tej samej usłudze w chmurze. Przechowywanie maszyn wirtualnych w tym samym zestawie dostępności umożliwia platformy Azure, aby umieścić węzłów o wysokiej dostępności w oddzielnych domenach błędów i uaktualnień. Podobnie przechowywanie maszyn wirtualnych w tej samej usługi w chmurze umożliwia równoważenie obciążenia i maszyny wirtualne mogą komunikować się bezpośrednio ze sobą za pośrednictwem sieci lokalnej w obrębie centrum danych platformy Azure.<br/><br/>Odpowiedzialność za wdrażanie wysokiej dostępności i rozwiązanie odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines w celu uniknięcia żadnych przestojów. W przypadku obsługiwanych technologii HADR, zobacz [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Odpowiedzialność za kopie zapasowe własnych danych i aplikacji.<br/><br/>Azure można przenosić maszyn wirtualnych, gdy komputer hosta, w centrum danych kończy się niepowodzeniem z powodu problemów ze sprzętem. Ponadto może istnieć planowanym przestoju maszyny wirtualnej podczas aktualizacji maszyny hosta pod kątem bezpieczeństwa ani oprogramowania aktualizacji. Dlatego zaleca się zachować co najmniej dwóch maszyn wirtualnych w każdej warstwy aplikacji dla zapewnienia ciągłej dostępności. Azure nie zapewnia umowę SLA dla pojedynczej maszyny wirtualnej. |Platforma Azure zarządza błędy wynikające z podstawowego oprogramowania sprzętu lub systemu operacyjnego. Zaleca się, że możesz wdrożyć wiele wystąpień rola sieć web lub procesu roboczego, aby zapewnić wysoką dostępność aplikacji. Aby uzyskać informacje, zobacz [usług w chmurze, maszyn wirtualnych i wirtualnych Umowa dotycząca poziomu usług sieciowych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Odpowiedzialność za kopie zapasowe własnych danych i aplikacji.<br/><br/>W przypadku baz danych znajdujących się w bazie danych programu SQL Server w Maszynie wirtualnej platformy Azure odpowiedzialność za wdrażanie wysoką dostępność i odzyskiwanie po awarii rozwiązania odzyskiwania w celu uniknięcia żadnych przestojów. W przypadku obsługiwanych technologii HDAR Zobacz wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server na maszynach wirtualnych platformy Azure.<br/><br/>**Baza danych SQL Server dublowania**: Za pomocą usług Azure Cloud Services (role sieć web/proces roboczy). Maszyny wirtualne SQL Server i projekt usługi w chmurze mogą być w tej samej sieci wirtualnej platformy Azure. Jeśli maszyna wirtualna programu SQL Server nie znajduje się w tej samej sieci wirtualnej, musisz utworzyć Alias serwera SQL, aby przekierowywać komunikację do wystąpienia programu SQL Server. Ponadto nazwa aliasu musi odpowiadać nazwie programu SQL Server. |Wysoka dostępność jest dziedziczony z ról procesu roboczego platformy Azure, usługi Azure blob storage i Azure SQL Database. Na przykład Azure Storage przechowuje trzech replik wszystkich obiektów blob, tabel i dane w kolejce. W dowolnym momencie, Azure SQL Database przechowuje trzy repliki danych, uruchomione — jedną replikę podstawową i dwóch replik pomocniczych. Aby uzyskać więcej informacji, zobacz [usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/) i [usługi Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Korzystając z programu SQL Server w maszynie Wirtualnej platformy Azure jako źródło danych dla aplikacji sieci Web platformy Azure, należy pamiętać o tym, Azure Web Apps nie obsługuje usługi Azure Virtual Network. Innymi słowy wszystkie połączenia z usługi Azure Web Apps na maszynach wirtualnych serwera SQL na platformie Azure musi przejść przez publiczne punkty końcowe maszyn wirtualnych. Może to powodować pewne ograniczenia dotyczące wysokiej dostępności i scenariuszy odzyskiwania po awarii. Na przykład aplikacja kliencka w usłudze Azure Web Apps nawiązywania połączenia z maszyny Wirtualnej programu SQL Server za pomocą funkcji dublowania baz danych nie będzie można połączyć się z nowym serwerem podstawowym i dublowania bazy danych wymaga Konfigurowanie sieci wirtualnej platformy Azure między maszynami wirtualnymi z hosta programu SQL Server na platformie Azure. W związku z tym, za pomocą **dublowania bazy danych serwera SQL** za pomocą usługi Azure Web Apps nie jest obecnie obsługiwana.<br/><br/>**Grupy dostępności programu SQL Server AlwaysOn**: Można skonfigurować zawsze włączonych grup dostępności, korzystając z usługi Azure Web Apps przy użyciu maszyn wirtualnych serwera SQL na platformie Azure. Jednak należy skonfigurować odbiornik grupy dostępności (AlwaysOn) do kierowania komunikację z repliką podstawową, za pomocą publicznych punktów końcowych ze zrównoważonym obciążeniem. |
| **Łączność między wieloma lokalizacjami** |Nawiązać połączenia z lokalną, można użyć usługi Azure Virtual Network. |Nawiązać połączenia z lokalną, można użyć usługi Azure Virtual Network. |Usługa Azure Virtual Network jest obsługiwane. Aby uzyskać więcej informacji, zobacz [Web Apps Integracja z siecią wirtualną](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Skalowalność** |Skalowanie w górę, należy zwiększyć rozmiary maszyn wirtualnych lub dodawanie większej liczby dysków. Aby uzyskać więcej informacji na temat rozmiarów maszyny wirtualnej, zobacz [rozmiarów maszyn wirtualnych na platformie Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Na serwerze bazy danych**: Skalowalny w poziomie jest dostępny za pośrednictwem techniki partycjonowania bazy danych i grup dostępności AlwaysOn programu SQL Server.<br/><br/>W przypadku dużych obciążeń odczytu, można użyć [zawsze włączonych grup dostępności](https://msdn.microsoft.com/library/hh510230.aspx) na wiele węzłów pomocniczych, a także replikacji programu SQL Server.<br/><br/>W przypadku obciążeń wysokie obciążenia podczas zapisu można zaimplementować poziomy partycjonowania danych między wieloma serwerami fizycznymi, aby zapewnić aplikacji skalowalnego w poziomie.<br/><br/>Ponadto, można wdrożyć skalowalnym w poziomie przy użyciu [programu SQL Server z Routing zależny od danych](https://technet.microsoft.com/library/cc966448.aspx). Za pomocą danych zależnych routingu (DDR), należy zaimplementować mechanizm partycjonowania w aplikacji klienckiej zwykle znajduje się w warstwie warstwy biznesowej, aby kierować żądania bazy danych do wielu węzłów programu SQL Server. Warstwa biznesowa zawiera mapowania na sposób partycjonowania danych i węzła, który zawiera dane.<br/><br/>Możesz skalować aplikacje, które są uruchomione maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [jak skalować aplikację](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Ważna Uwaga**: **Skalowania automatycznego** funkcji na platformie Azure pozwala na automatyczne zwiększanie lub zmniejszanie maszyn wirtualnych, które są używane przez aplikację. Ta funkcja gwarantuje, że środowisko użytkownika końcowego nie jest negatywny wpływ w okresach szczytowych i maszyny wirtualne są zamykane, gdy zapotrzebowanie jest niskie. Zaleca się, że nie ustawisz opcji automatycznego skalowania dla usługi w chmurze zawiera maszyny wirtualne SQL Server. Przyczyną jest to, że funkcja automatycznego skalowania pozwala włączyć na maszynie wirtualnej, gdy użycie procesora CPU w tej maszyny Wirtualnej jest większy niż próg niektóre i wyłączyć maszynę wirtualną, jeśli użycie procesora CPU przekroczy niższe niż platformy Azure. Funkcja automatycznego skalowania jest przydatne w przypadku aplikacji bezstanowych, takich jak serwery sieci web, w której dowolnej maszyny Wirtualnej można zarządzać obciążenia bez żadnych odwołań do dowolnego poprzedniego stanu. Jednak funkcja automatycznego skalowania nie jest przydatne w przypadku aplikacji stanowych, takie jak SQL Server, gdzie tylko jedno wystąpienie umożliwia zapisywanie w bazie danych. |Skalowanie w górę jest dostępna za pomocą wielu ról sieć web i proces roboczy. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych dla ról sieć web i ról procesów roboczych, zobacz [skonfigurować rozmiary usług Cloud Services](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Korzystając z **usług w chmurze**, można zdefiniować wiele ról w celu dystrybucji przetwarzania, a także zapewnić, elastyczne skalowanie aplikacji. Każda usługa w chmurze zawiera jeden lub więcej ról sieć web i/lub ról procesów roboczych, każdy z plików aplikacji i konfiguracji. Możesz skalować w górę usługi w chmurze, zwiększając liczbę wystąpień roli (maszyny wirtualne), wdrożone dla roli i dół usługi w chmurze, zmniejszając liczbę wystąpień roli. Aby uzyskać szczegółowe informacje, zobacz [modele wykonywania Azure](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Skalowalny w poziomie jest dostępny za pośrednictwem pomocy technicznej wbudowaną wysoką dostępność platformy Azure w ramach [usług w chmurze, maszyn wirtualnych i wirtualnych Umowa dotycząca poziomu usług sieciowych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) i moduł równoważenia obciążenia.<br/><br/>Dla aplikacji wielowarstwowych firma Microsoft zaleca się połączenie aplikacji ról sieć web/proces roboczy w bazie danych serwera maszyn wirtualnych za pośrednictwem usługi Azure Virtual Network. Ponadto platforma Azure zapewnia równoważenie obciążenia dla maszyn wirtualnych w tej samej usłudze w chmurze rozłożenie żądań użytkowników na ich. Maszyny wirtualne połączone w ten sposób mogą komunikować się bezpośrednio ze sobą za pośrednictwem sieci lokalnej w obrębie centrum danych platformy Azure.<br/><br/>Możesz skonfigurować **skalowania automatycznego** w witrynie Azure portal, a także czasy harmonogramu. Aby uzyskać więcej informacji, zobacz [jak skonfigurować automatyczne skalowanie usługi w chmurze w portalu](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Skalowanie w górę i w dół**: Możesz zwiększyć/zmniejszyć rozmiar wystąpienia (VM) zarezerwowana dla witryny sieci web.<br/><br/>Skalowanie w poziomie: Możesz dodać więcej zarezerwowane wystąpienia (maszyny wirtualne) dla witryny sieci web.<br/><br/>Możesz skonfigurować **skalowania automatycznego** na portalu, a także czasy harmonogramu. Aby uzyskać więcej informacji, zobacz [jak skalować aplikacje internetowe](../../../app-service/web-sites-scale.md). |

Aby uzyskać więcej informacji na temat wybierania między tych metod programowania, zobacz [Azure Web Apps, Cloud Services i maszyn wirtualnych: Kiedy należy używać](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server w usłudze Azure Virtual Machines Overview](virtual-machines-windows-sql-server-iaas-overview.md).

