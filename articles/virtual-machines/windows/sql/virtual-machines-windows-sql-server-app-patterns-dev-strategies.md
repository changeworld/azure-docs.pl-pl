---
title: Wzorce aplikacji serwera SQL na maszynach wirtualnych | Dokumentacja firmy Microsoft
description: W tym artykule omówiono wzorce aplikacji dla programu SQL Server na maszynach wirtualnych Azure. Zapewnia rozwiązanie architektów i deweloperów podstawę dla aplikacji dobrej architektury i projektu.
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
ms.openlocfilehash: eaeff1e57042b2e6a98559c19dc1dabebbf92ed4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Wzorce aplikacji i strategie programowania dla programu SQL Server w usłudze Azure Virtual Machines
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Podsumowanie:
Określanie, które wzorzec aplikacji lub wzorce do użycia dla aplikacji na serwerze SQL na platformie Azure środowisko jest decyzji projektowych ważne i wymaga pełny opis programu SQL Server i każdego składnika infrastruktury Azure współdziałania. Z programem SQL Server w usługach infrastruktury platformy Azure można łatwo migracji, obsługa i monitorowanie istniejących aplikacji SQL Server wbudowane — w systemie Windows Server na maszynach wirtualnych platformy Azure.

Celem tego artykułu jest zapewnienie rozwiązań dla architektów i deweloperów podstawę dla architektury dobrej aplikacji i projekt, który może wykonać podczas migrowania istniejących aplikacji do platformy Azure, a także tworzenie nowej aplikacji na platformie Azure.

Dla każdego wzorca aplikacji można znaleźć scenariusz lokalnymi, rozwiązanie do odpowiednich włączoną obsługę chmury i powiązane zaleceń technicznych. Ponadto, w tym artykule omówiono strategii rozwoju specyficzne dla platformy Azure, dzięki czemu można projektować aplikacje poprawnie. Z powodu wielu wzorców możliwości zastosowania zaleca się, że architektów i deweloperów należy wybrać wzorcu najbardziej odpowiedni dla aplikacji i użytkowników.

**Współautorzy techniczne:** Artur Luis Vargas śledzi, Madhan Arumugam Ramakrishnan

**Recenzenci techniczni:** szlifierki Corey, Drew McDaniel, Narayan Annamalai, Nir Mashkowski Sanjay Mishra, Coriani Silvano, Stefan Schackow Hickey Timowi, Wieman Timowi, Xin Jin

## <a name="introduction"></a>Wprowadzenie
Możesz utworzyć wiele typów aplikacji wielowarstwowych oddzielając składniki warstwy aplikacji różne na różnych komputerach, a także jak składniki. Na przykład możesz umieścić aplikacji klienckiej i składników na jednym komputerze, warstwa sieci web i składników warstwy dostępu do danych w innym komputerze i warstwy bazy danych zaplecza w innej maszynie reguły biznesowej. Tego rodzaju struktury pomaga wyizolować każdej warstwy od siebie. Jeśli zmienisz, skąd pochodzą dane, nie trzeba zmieniać klienta lub aplikacji sieci web, ale tylko składniki warstwy dostępu do danych.

Typowe *n warstwowa* aplikacja zawiera warstwę prezentacji, warstwą biznesową a warstwą danych:

| Warstwa | Opis |
| --- | --- |
| **Prezentacji** |*Warstwy prezentacji* (warstwa sieci web frontonu warstwy) jest warstwie, w którym użytkownicy korzystają z aplikacji. |
| **Biznesowa** |*Warstwy biznesowej* (warstwy środkowej) jest warstwie warstwy prezentacji i warstwą danych używają do komunikowania się ze sobą i zawiera podstawowe funkcje systemu. |
| **Dane** |*Warstwy danych* jest zasadniczo serwera, która przechowuje dane aplikacji (na przykład serwer działa program SQL Server). |

Logiczne grupowanie funkcje i składniki aplikacji; opisano warstwy aplikacji natomiast warstw opisano fizycznych dystrybucji funkcje i składniki na osobnych serwerach fizycznych, komputery, sieci lub w lokalizacjach zdalnych. Warstwy aplikacji może znajdować się na tym samym komputerze fizycznym (tej samej warstwie) lub mogą być dystrybuowane za pośrednictwem różnych komputerach (n warstwowa) i składniki w każdej warstwie komunikują się ze składnikami w innych warstw za pomocą dobrze zdefiniowanych interfejsów. Można potraktować jako odnoszące się do fizycznego dystrybucji wzorców, takich jak dwuwarstwowa i trójwarstwowa, n warstwowa warstwy terminu. A **2-warstwowy wzorzec aplikacji** zawiera dwie warstwy aplikacji: serwer aplikacji i serwer bazy danych. Bezpośrednia komunikacja odbywa się między serwerem aplikacji i serwer bazy danych. Serwer aplikacji zawiera składniki zarówno warstwa sieci web, jak i warstwy biznesowej. W **3-warstwowy wzorzec aplikacji**, istnieją trzy warstwy aplikacji: serwera sieci web, serwera aplikacji, która zawiera warstwy logiki biznesowej i/lub składników dostępu do danych warstwy biznesowej i serwer bazy danych. Komunikacja między serwerem sieci web a serwerem bazy danych odbywa się za pośrednictwem serwera aplikacji. Aby uzyskać szczegółowe informacje na warstwy i warstwy aplikacji, zobacz [Przewodniku dotyczącym architektury aplikacji Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Przed rozpoczęciem odczytu w tym artykule, powinien mieć wiedzy na podstawowe pojęcia programu SQL Server i usługi Azure. Aby uzyskać informacje, zobacz [programu SQL Server — książki Online](https://msdn.microsoft.com/library/bb545450.aspx), [programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) i [witryny Azure.com](https://azure.microsoft.com/).

W tym artykule opisano kilka wzorców aplikacji, które mogą być odpowiednie dla prostego aplikacji, a także aplikacje skomplikowanej przedsiębiorstwa. Przed przedstawiająca szczegółowo każdy wzorzec, zaleca się, że należy zapoznać się z usługami magazynu danych na platformie Azure, takich jak [usługi Azure Storage](../../../storage/common/storage-introduction.md), [bazy danych SQL Azure](../../../sql-database/sql-database-technical-overview.md), i [programu SQL Server w maszynie wirtualnej platformy Azure](virtual-machines-windows-sql-server-iaas-overview.md). Aby najlepszych decyzji projektowych dla aplikacji, zrozumienie, kiedy należy użyć wyraźnie które usługa Magazyn danych.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Wybierz program SQL Server w maszynie wirtualnej platformy Azure, gdy:
* Należy sterowania w programie SQL Server i Windows. Na przykład może to obejmować wersja programu SQL Server, specjalne poprawek, konfigurację wydajności itd.
* Należy pełnej zgodności z programu SQL Server — lokalnie i chcesz przenieść istniejące aplikacje na platformie Azure jako — jest.
* Aby skorzystać z możliwości środowiska platformy Azure, ale baza danych SQL Azure nie obsługuje wszystkie funkcje wymagane przez aplikację. Może to obejmować następujące obszary:
  
  * **Rozmiar bazy danych**: W czasie, w tym artykule został zaktualizowany, baza danych SQL obsługuje bazę danych do 1 TB. Jeśli aplikacja wymaga więcej niż 1 TB danych i nie chcesz wdrażać rozwiązania niestandardowe dzielenia na fragmenty, zalecane jest użycie programu SQL Server w maszynie wirtualnej platformy Azure. Najnowsze informacje, zobacz [skalowanie limit bazami danych SQL Azure](https://msdn.microsoft.com/library/azure/dn495641.aspx), [modelu zakupu DTU-Based](../../../sql-database/sql-database-service-tiers-dtu.md), i [vCore na podstawie modelu zakup](../../../sql-database/sql-database-service-tiers-vcore.md)(wersja zapoznawcza).
  * **Zgodność HIPAA**: opieki zdrowotnej klientów i niezależni dostawcy oprogramowania (ISV) mogą wybierać [programu SQL Server w maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-server-iaas-overview.md) zamiast [bazy danych SQL Azure](../../../sql-database/sql-database-technical-overview.md) ponieważ program SQL Server w Maszyna wirtualna platformy Azure jest objęty przez umowy skojarzyć firm HIPAA (BAA). Aby uzyskać informacje na temat zgodności, zobacz [Microsoft Azure Trust Center: zgodności](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funkcje na poziomie wystąpienia**: W tej chwili baza danych SQL nie obsługuje funkcji, które na żywo poza bazą danych (np. serwerów połączonych agenta zadania, FileStream Service Broker, itp.). Aby uzyskać więcej informacji, zobacz [wskazówki bazy danych SQL Azure i ograniczenia](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>warstwy 1 (proste): jednej maszyny wirtualnej
W tym wzorcu aplikacji można wdrożyć aplikacji SQL Server i bazy danych do autonomicznej maszyny wirtualnej na platformie Azure. Tej samej maszyny wirtualnej zawiera aplikacji sieci web/klienta, składników biznesowych Warstwa dostępu do danych i serwer bazy danych. Prezentacji, służbowego i kod dostępu do danych są logicznie rozdzielone, ale fizycznie znajdują się w jednym serwerze. Większość klientów rozpoczynać się od tego wzorca aplikacji, a następnie skalować w poziomie przez dodanie więcej ról sieć web lub maszyny wirtualne do systemu.

Ten wzorzec aplikacji jest przydatna, gdy:

* Chcesz przeprowadzić migrację prostego do platformy Azure do oceny, czy platforma odpowiedzi wymagania aplikacji lub nie.
* Chcesz zachować wszystkie warstwy aplikacji hostowanej w tej samej maszyny wirtualnej w tym samym centrum danych Azure w celu zmniejszenia opóźnienia między warstwami.
* Chcesz szybko udostępnić programowanie i testowanie środowisk w krótkich okresach czasu.
* Chcesz wykonać testowanie pod kątem różnych poziomów obciążeń akcent, ale w tym samym czasie nie ma własny i obsługa wielu komputerów fizycznych przez cały czas.

Poniższy diagram przedstawia prostą lokalnymi scenariusza i jak można wdrożyć rozwiązanie chmury włączone w jednej maszyny wirtualnej na platformie Azure.

![1-warstwowy wzorzec aplikacji](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Wdrażanie warstwie business (logika biznesowa i składniki dostępu do danych) w tej samej warstwie fizycznej jako Warstwa prezentacji można zmaksymalizować wydajność aplikacji, chyba, że należy użyć oddzielnych warstwy ze względu na problemy skalowalności ani zabezpieczeń.

Ponieważ jest to bardzo typowy wzorzec zacząć, może być przydatne artykule migracji do przenoszenia danych do maszyny Wirtualnej programu SQL Server: [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>warstwy 3 (proste): wiele maszyn wirtualnych
W tym wzorcu aplikacji wdrożysz aplikację 3-warstwowej na platformie Azure przez umieszczenie każdej warstwy aplikacji w innej maszyny wirtualnej. Zapewnia elastyczne środowisko do łatwego skalowania w górę i skalowania w poziomie scenariuszy. Po jednej maszyny wirtualnej zawiera aplikację klienta/sieci web, innego hostuje składniki firm, a drugi obsługuje serwer bazy danych.

Ten wzorzec aplikacji jest przydatna, gdy:

* Chcesz przeprowadzić migrację aplikacji złożonych baz danych na maszynach wirtualnych platformy Azure.
* Ma warstwy z innej aplikacji ma być obsługiwana w różnych regionach. Na przykład może być udostępnione baz danych, które są wdrażane w wielu regionach, dla celów raportowania.
* Chcesz przenieść aplikacji przedsiębiorstwa z lokalnymi zwirtualizowanych platform na maszynach wirtualnych platformy Azure. Szczegółowe omówienie w aplikacjach dla przedsiębiorstw, zobacz [co to jest aplikacja przedsiębiorstwa](https://msdn.microsoft.com/library/aa267045.aspx).
* Chcesz szybko udostępnić programowanie i testowanie środowisk w krótkich okresach czasu.
* Chcesz wykonać testowanie pod kątem różnych poziomów obciążeń akcent, ale w tym samym czasie nie ma własny i obsługa wielu komputerów fizycznych przez cały czas.

Poniższym diagramie przedstawiono, jak można umieścić prostą aplikację 3-warstwowej na platformie Azure, umieszczając każdej warstwy aplikacji w innej maszyny wirtualnej.

![3-warstwowy wzorzec aplikacji](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

W tym wzorcu aplikacji istnieje tylko jedna maszyna wirtualna (VM) w ramach poszczególnych warstw. Jeśli masz wiele maszyn wirtualnych na platformie Azure, zaleca się konfigurowanie sieci wirtualnej. [Sieć wirtualna platformy Azure](../../../virtual-network/virtual-networks-overview.md) tworzy granicę zaufanego i pozwala również maszyn wirtualnych do komunikacji między sobą za pośrednictwem prywatnego adresu IP. Ponadto zawsze upewnij się, że wszystkie połączenia z Internetem tylko przejdź do warstwy prezentacji. Podczas tego wzorca aplikacji, zarządzanie reguły grupy zabezpieczeń sieci w celu kontroli dostępu. Aby uzyskać więcej informacji, zobacz [dostęp do zewnętrznych sieci maszyny Wirtualnej przy użyciu portalu Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Na diagramie protokołów internetowych można TCP, UDP, HTTP lub HTTPS.

> [!NOTE]
> Konfigurowanie sieci wirtualnej platformy Azure jest bezpłatne. Jednak są naliczane dla bramy sieci VPN, który łączy się z lokalnym. Opłata jest oparta na czas, który połączenie jest udostępnione i dostępny.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>warstwy 2 i 3-warstwowej z skalowalne warstwy prezentacji
W tym wzorcu aplikacji wdrożeniem aplikacji warstwy 2 lub 3-warstwowej bazy danych na maszynach wirtualnych platformy Azure przez umieszczenie każdej warstwy aplikacji w innej maszyny wirtualnej. Ponadto można skalować w poziomie warstwy prezentacji z powodu zwiększenie wielkości przychodzące żądania klientów.

Ten wzorzec aplikacji jest przydatna, gdy:

* Chcesz przenieść aplikacji przedsiębiorstwa z lokalnymi zwirtualizowanych platform na maszynach wirtualnych platformy Azure.
* Chcesz skalowania warstwy prezentacji z powodu zwiększenie wielkości przychodzące żądania klientów.
* Chcesz szybko udostępnić programowanie i testowanie środowisk w krótkich okresach czasu.
* Chcesz wykonać testowanie pod kątem różnych poziomów obciążeń akcent, ale w tym samym czasie nie ma własny i obsługa wielu komputerów fizycznych przez cały czas.
* Ma zostać właścicielem środowisku infrastruktury, w którym można skalować w górę i w dół na żądanie.

Poniższy diagram ilustruje sposób można umieścić w warstwach aplikacji w wielu maszyn wirtualnych na platformie Azure przez skalowania warstwy prezentacji z powodu zwiększenie wielkości przychodzące żądania klientów. Jak pokazano na diagramie, usługi równoważenia obciążenia Azure jest odpowiedzialny za Dystrybucja ruchu między wieloma maszynami wirtualnymi, a także określenie których serwer sieci web, aby nawiązać połączenie. O wiele wystąpień serwerów sieci web za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwy prezentacji.

![Wzorzec aplikacji - skalowania warstwy prezentacji w poziomie](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Najlepsze rozwiązania dotyczące warstwy 2, 3-warstwy lub n warstwowa wzorców, które mają wiele maszyn wirtualnych w jedną warstwę
Zaleca się umieszczenie maszyny wirtualne, które należą do tej samej warstwie, w tej samej usłudze w chmurze i tego samego dostępności ustawić. Na przykład umieścić zestaw serwerów sieci web w **CloudService1** i **AvailabilitySet1** i zestaw serwerów baz danych w **CloudService2** i **AvailabilitySet2**. Zestawem dostępności w Azure pozwala na umieszczenie węzłów wysokiej dostępności w domenach awarii oddzielne i uaktualnienia domen.

Aby korzystać z wielu wystąpień maszyny Wirtualnej z warstwy, należy skonfigurować usługi równoważenia obciążenia Azure między warstwami aplikacji. Aby skonfigurować moduł równoważenia obciążenia w ramach poszczególnych warstw, należy utworzyć punkt końcowy równoważeniem obciążenia na maszynach wirtualnych każda warstwa oddzielnie. Dla określonej warstwy należy najpierw utworzyć maszyny wirtualne w tej samej usłudze w chmurze. Dzięki temu mają tego samego publiczny wirtualny adres IP. Następnie można utworzyć punktu końcowego na jednym z maszyn wirtualnych w danej warstwie. Następnie można przypisać tego samego punktu końcowego dla innych maszyn wirtualnych w tej warstwie do równoważenia obciążenia. Tworząc zestaw o zrównoważonym obciążeniu — Dystrybuuj ruch między wieloma maszynami wirtualnymi, a także umożliwić moduł równoważenia obciążenia określić, który węzeł nawiązanie po awarii węzła wirtualna wewnętrznej bazy danych. Na przykład mających wiele wystąpień serwerów sieci web za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwy prezentacji.

Najlepszym rozwiązaniem zawsze upewnij się, że wszystkie połączenia z Internetem przejdź najpierw do warstwy prezentacji. Warstwa prezentacji uzyskuje dostęp do warstwy biznesowej, a następnie warstwy biznesowej uzyskuje dostęp do warstwy danych. Aby uzyskać więcej informacji na temat zezwolić na dostęp do warstwy prezentacji, zobacz [dostęp do zewnętrznych sieci maszyny Wirtualnej przy użyciu portalu Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Należy pamiętać, że moduł równoważenia obciążenia w Azure działa podobnie do usług w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [równoważenia obciążenia dla usług infrastruktury platformy Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ponadto zalecane jest skonfigurowanie sieci prywatnej dla maszyn wirtualnych przy użyciu sieci wirtualnej platformy Azure. Umożliwia im to komunikują się ze sobą za pośrednictwem prywatnego adresu IP. Aby uzyskać więcej informacji, zobacz [sieci wirtualnej Azure](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>warstwy 2 i 3-warstwowej z skalowalne warstwy biznesowej
W tym wzorcu aplikacji wdrożeniem aplikacji warstwy 2 lub 3-warstwowej bazy danych na maszynach wirtualnych platformy Azure przez umieszczenie każdej warstwy aplikacji w innej maszyny wirtualnej. Ponadto można rozpowszechniać składniki serwera aplikacji do wielu maszyn wirtualnych z powodu złożoności aplikacji.

Ten wzorzec aplikacji jest przydatna, gdy:

* Chcesz przenieść aplikacji przedsiębiorstwa z lokalnymi zwirtualizowanych platform na maszynach wirtualnych platformy Azure.
* Chcesz dystrybuować składniki serwera aplikacji do wielu maszyn wirtualnych z powodu złożoności aplikacji.
* Chcesz przenieść ciężki logiki biznesowej lokalnej aplikacji biznesowych (— biznesowych) na maszynach wirtualnych platformy Azure. Aplikacje LOB są zestawem aplikacji krytyczne komputera, które są niezbędne do uruchomienia przedsiębiorstwa, takich jak ewidencjonowania aktywności, kadr (HR), Lista płac, zarządzania łańcuchem dostaw i aplikacji do planowania zasobów.
* Chcesz szybko udostępnić programowanie i testowanie środowisk w krótkich okresach czasu.
* Chcesz wykonać testowanie pod kątem różnych poziomów obciążeń akcent, ale w tym samym czasie nie ma własny i obsługa wielu komputerów fizycznych przez cały czas.
* Ma zostać właścicielem środowisku infrastruktury, w którym można skalować w górę i w dół na żądanie.

Poniższy diagram przedstawia scenariusz lokalnymi i jego rozwiązanie chmury włączone. W tym scenariuszu umieszczeniu warstwy aplikacji w wielu maszyn wirtualnych na platformie Azure przez skalowania warstwy biznesowej, które zawiera składniki dostępu do danych i warstwy logiki biznesowej. Jak pokazano na diagramie, usługi równoważenia obciążenia Azure jest odpowiedzialny za Dystrybucja ruchu między wieloma maszynami wirtualnymi, a także określenie których serwer sieci web, aby nawiązać połączenie. O wiele wystąpień serwerów aplikacji za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwy biznesowej. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dla warstwy 2, 3 warstwy lub n warstwowa aplikacja wzorców, które mają wiele maszyn wirtualnych w jedną warstwę](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Wzorzec aplikacji z skalowania warstwy biznesowej w poziomie](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>warstwy 2 i 3-warstwowej z prezentacji i skalowalne warstwy biznesowej oraz funkcji HADR
W tym wzorcu aplikacji wdrożeniem aplikacji warstwy 2 lub 3-warstwowej bazy danych na maszynach wirtualnych platformy Azure przez dystrybucję warstwy prezentacji (serwer sieci web) i składniki (serwera aplikacji) warstwy biznesowej do wielu maszyn wirtualnych. Ponadto wdrożenie rozwiązania w zakresie odzyskiwania wysokiej dostępności i odzyskiwaniem po awarii dla baz danych w maszynach wirtualnych platformy Azure.

Ten wzorzec aplikacji jest przydatna, gdy:

* Chcesz przenieść aplikacji przedsiębiorstwa z zwirtualizowanych platform lokalnej na platformie Azure dzięki implementacji wysokiej dostępności programu SQL Server i funkcji odzyskiwania po awarii.
* Chcesz skalować w poziomie warstwą prezentacji a warstwą biznesową ze względu na zwiększenie wielkości przychodzące żądania klientów i złożoność aplikacji.
* Chcesz szybko udostępnić programowanie i testowanie środowisk w krótkich okresach czasu.
* Chcesz wykonać testowanie pod kątem różnych poziomów obciążeń akcent, ale w tym samym czasie nie ma własny i obsługa wielu komputerów fizycznych przez cały czas.
* Ma zostać właścicielem środowisku infrastruktury, w którym można skalować w górę i w dół na żądanie.

Poniższy diagram przedstawia scenariusz lokalnymi i jego rozwiązanie chmury włączone. W tym scenariuszu skalowanie w poziomie z warstwą prezentacji a składników warstwy biznesowej w wielu maszyn wirtualnych na platformie Azure. Ponadto wdrożenie wysokiej dostępności i odzyskiwaniem po awarii technik odzyskiwania (HADR) dla baz danych programu SQL Server na platformie Azure.

Uruchamianie wielu kopii aplikacji w różnych maszyn wirtualnych upewnij się, czy Równoważenie obciążenia żądań w nich. Jeśli masz wiele maszyn wirtualnych, należy upewnić się, że wszystkie maszyny wirtualne są dostępne i działa w jednym punkcie w czasie. Jeśli konfigurujesz równoważenia obciążenia, moduł równoważenia obciążenia Azure śledzi kondycję maszyn wirtualnych i kieruje przychodzące wywołania do dobrej kondycji węzłów maszyna wirtualna działa poprawnie. Aby uzyskać informacje na temat sposobu konfigurowania równoważenia obciążenia maszyn wirtualnych, zobacz [równoważenia obciążenia dla usług infrastruktury platformy Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). O wiele wystąpień serwerów sieci web i aplikacji za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwy prezentacji i biznesowych.

![Skalowalny w poziomie i wysoka dostępność.](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Najlepsze rozwiązania dotyczące wzorców aplikacji wymagających SQL HADR
Po skonfigurowaniu wysokiej dostępności programu SQL Server i rozwiązania w zakresie odzyskiwania po awarii w maszynach wirtualnych platformy Azure, trwa konfigurowanie sieci wirtualnej dla maszyn wirtualnych przy użyciu [sieci wirtualnej Azure](../../../virtual-network/virtual-networks-overview.md) jest obowiązkowe.  Maszyny wirtualne sieci wirtualnej mają stabilna prywatnego adresu IP nawet po przestoje usługi, w związku z tym można uniknąć aktualizacji czas rozpoznawania nazw DNS. Ponadto sieci wirtualnej umożliwia rozszerzenie sieci lokalnej na platformie Azure i tworzy granicę zaufanego. Na przykład jeśli aplikacja ma ograniczenia domeny firmy (takich jak uwierzytelnianie systemu Windows, usługi Active Directory) Konfigurowanie [sieci wirtualnej Azure](../../../virtual-network/virtual-networks-overview.md) jest konieczne.

Większość klientów, którzy korzystają z kodu produkcyjnego na platformie Azure, są utrzymywanie podstawowych i pomocniczych replik w systemie Azure.

Aby uzyskać szczegółowe informacje i samouczki o wysokiej dostępności i technik odzyskiwania po awarii, zobacz [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>warstwy 2 i 3-warstwowej przy użyciu maszyn wirtualnych platformy Azure i usługi w chmurze
W tym wzorcu aplikacji wdrożeniem warstwy 2 lub 3-warstwowych aplikacji na platformie Azure za pomocą obu [usługi w chmurze Azure](../../../cloud-services/cloud-services-choose-me.md#tellmecs) (role sieć web i proces roboczy - platforma jako usługa (PaaS)) i [maszyny wirtualne Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (infrastruktura jako usługa (IaaS)). Przy użyciu [usługi w chmurze Azure](https://azure.microsoft.com/documentation/services/cloud-services/) dla warstwy biznesowym/warstwy prezentacji i programu SQL Server w [maszyny wirtualne Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) danych warstwa jest przydatne w przypadku większości aplikacji działających na platformie Azure. Przyczyną jest to, że o wystąpienia obliczeniowe uruchomione na usługi w chmurze zapewnia łatwe zarządzanie, wdrażania, monitorowania i skalowalnego w poziomie.

Z usługami w chmurze Azure obsługuje infrastrukturę dla Ciebie, wykonuje konserwacja poprawek systemów operacyjnych i próbuje odzyskać przed awariami sprzętu i usługi. Jeśli aplikacja wymaga skalowalnego w poziomie, automatycznej i ręcznej opcje skalowania w poziomie są dostępne dla projektu usługi w chmurze, zwiększ lub Zmniejsz liczbę wystąpień lub maszyn wirtualnych, które są używane przez aplikację. Ponadto można użyć lokalnego programu Visual Studio do wdrażania aplikacji do projektu usługi w chmurze na platformie Azure.

Podsumowując, jeśli nie chcesz właścicielem szeroką gamę zadań administracyjnych dla prezentacji/firm warstwy i aplikacja nie wymaga żadnej złożonych konfiguracji lub aktualizacji systemu operacyjnego, użyj usługi w chmurze Azure. Jeśli baza danych SQL Azure nie obsługuje wszystkich funkcji, których szukasz, użyj programu SQL Server w maszynie wirtualnej platformy Azure w warstwie danych. Uruchamianie aplikacji na usług Azure Cloud Services i przechowywania danych w usłudze Azure Virtual Machines łączy zalety obu usług. Szczegółowe porównanie, zobacz sekcję w tym temacie na [Porównanie strategii rozwoju na platformie Azure](#comparing-web-development-strategies-in-azure).

W tym wzorcu aplikacji Warstwa prezentacji zawiera roli sieci web, która działa składnika usługi w chmurze w środowisku Azure wykonywania i jest dostosowany do programowania aplikacji sieci web, ponieważ obsługiwana przez usługi IIS i platformy ASP.NET. Warstwa biznesowa lub wewnętrznej bazy danych obejmuje rolę procesu roboczego, który jest uruchomiony składnik usługi w chmurze w środowisku Azure wykonywania i przydaje się do rozwoju ogólnych i może wykonywać przetwarzania w tle dla roli sieci web. Warstwy bazy danych znajduje się na maszynie wirtualnej programu SQL Server na platformie Azure. Komunikacja między warstwą prezentacji a warstwą bazy danych odbywa się bezpośrednio lub za pośrednictwem warstwy biznesowej — składniki roli procesu roboczego.

Ten wzorzec aplikacji jest przydatna, gdy:

* Chcesz przenieść aplikacji przedsiębiorstwa z zwirtualizowanych platform lokalnej na platformie Azure dzięki implementacji wysokiej dostępności programu SQL Server i funkcji odzyskiwania po awarii.
* Ma zostać właścicielem środowisku infrastruktury, w którym można skalować w górę i w dół na żądanie.
* Baza danych SQL Azure nie obsługuje wszystkich funkcji potrzebnych aplikacji lub bazy danych.
* Chcesz wykonać testowanie pod kątem różnych poziomów obciążeń akcent, ale w tym samym czasie nie ma własny i obsługa wielu komputerów fizycznych przez cały czas.

Poniższy diagram przedstawia scenariusz lokalnymi i jego rozwiązanie chmury włączone. W tym scenariuszu umieszczeniu warstwy prezentacji w role sieci web, warstwy biznesowej w roli proces roboczy, ale warstwy danych w maszynach wirtualnych platformy Azure. Uruchamianie wielu kopii warstwy prezentacji w różnych rolach zapewnia celu zrównoważenia obciążenia żądaniami między nimi. Połączenie usługi w chmurze Azure z usługi Azure Virtual Machines zaleca się skonfigurowanie [sieci wirtualnej Azure](../../../virtual-network/virtual-networks-overview.md) również. Z [sieci wirtualnej Azure](../../../virtual-network/virtual-networks-overview.md), możesz może mieć stabilny i trwałe prywatnych adresów IP, w tym samym chmury usługi w chmurze. Po zdefiniowaniu sieci wirtualnej dla maszyn wirtualnych i usług w chmurze, można uruchomić, komunikować się ze sobą za pośrednictwem prywatnego adresu IP. Ponadto posiadanie maszyn wirtualnych i Azure web/procesów roboczych w tej samej [sieci wirtualnej Azure](../../../virtual-network/virtual-networks-overview.md) zapewnia małe opóźnienia i bardziej bezpieczne połączenie. Aby uzyskać więcej informacji, zobacz [co to jest usługa w chmurze](../../../cloud-services/cloud-services-choose-me.md).

Jak pokazano na diagramie, usługi równoważenia obciążenia Azure polega na rozłożeniu ruchu sieciowego między wiele maszyn wirtualnych i decyduje również o którym serwer sieci web lub serwera aplikacji, aby nawiązać połączenie. O wiele wystąpień serwerów sieci web i aplikacji za modułem równoważenia obciążenia zapewnia wysoką dostępność warstwą prezentacji a warstwą biznesową. Aby uzyskać więcej informacji, zobacz [najlepszych rozwiązań dla aplikacji wzorce wymagające HADR SQL](#best-practices-for-application-patterns-requiring-sql-hadr).

![Wzorce aplikacji z usługami w chmurze](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Innym rozwiązaniem do zaimplementowania wzorca tej aplikacji jest aby użyć roli skonsolidowanych sieci web, która zawiera warstwę prezentacji i składników warstwy biznesowej, jak pokazano na poniższym diagramie. Ten wzorzec aplikacji jest przydatne w przypadku aplikacji wymagających stanowych projektu. Ponieważ Azure zawiera węzły obliczeniowe bezstanowych role sieci web i proces roboczy, zaleca się stosowanie logiki do przechowywania stanu sesji przy użyciu jednej z następujących technologii: [buforowanie Azure](https://azure.microsoft.com/documentation/services/redis-cache/), [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) lub [bazy danych SQL Azure](../../../sql-database/sql-database-technical-overview.md).

![Wzorce aplikacji z usługami w chmurze](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Wzorzec maszyny wirtualne platformy Azure, baza danych Azure SQL i usługa Azure App Service (aplikacje sieci Web)
Podstawowym celem tego wzorca aplikacji jest jak połączyć infrastruktury platformy Azure jako składniki usługi (IaaS), z platformy Azure składniki platformy jako usługa (PaaS) w rozwiązaniu. Ten wzorzec koncentruje się na bazie danych SQL Azure do przechowywania danych relacyjnych. Nie obejmuje program SQL Server w maszynie wirtualnej platformy Azure, który jest częścią infrastruktury platformy Azure jako oferty usługi.

W tym wzorcu aplikacji wdrożysz aplikację bazy danych na platformie Azure przez umieszczenie warstwy prezentacji i biznesowych w tej samej maszyny wirtualnej i uzyskiwania dostępu do bazy danych na serwerach usługi Azure SQL Database (baza danych SQL). Warstwa prezentacji można wdrożyć przy użyciu tradycyjnych sieci web usług IIS na podstawie rozwiązań. Lub scalonej prezentacji i warstwy biznesowej można wdrożyć przy użyciu [Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/).

Ten wzorzec aplikacji jest przydatna, gdy:

* Masz już istniejącego serwera bazy danych SQL skonfigurowany w usłudze Azure i chcesz szybko przetestować aplikację.
* Chcesz przetestować możliwości środowiska platformy Azure.
* Chcesz szybko udostępnić programowanie i testowanie środowisk w krótkich okresach czasu.
* Składniki dostępu logikę i dane biznesowe mogą być niezależne w obrębie aplikacji sieci web.

Poniższy diagram przedstawia scenariusz lokalnymi i jego rozwiązanie chmury włączone. W tym scenariuszu umieszczeniu warstwy aplikacji w jednej maszyny wirtualnej platformy Azure i dostępu do danych w bazie danych SQL Azure.

![Wzorzec mieszanych aplikacji](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Jeśli wybierzesz opcję wdrażania połączone sieci web i warstwy aplikacji za pomocą aplikacji sieci Web platformy Azure, zaleca się zachować warstwy aplikacji lub warstwy środkowej jako biblioteki dołączanej (dynamicznie dll) w kontekście aplikacji sieci web.

Ponadto, zapoznaj się z zaleceniami podanymi w [Porównanie strategii rozwoju sieci web na platformie Azure](#comparing-web-development-strategies-in-azure) sekcji na końcu tego artykułu, aby dowiedzieć się więcej na temat technik programowania.

## <a name="n-tier-hybrid-application-pattern"></a>N-warstwowa hybrydowych aplikacji wzorca
N warstwowa hybrydowych aplikacji wzorzec służy do wdrożenia aplikacji w wielu warstw dystrybuowane między lokalną i platformą Azure. W związku z tym utworzyć systemu hybrydowe elastyczne i wielokrotnego użytku, które można zmodyfikować lub dodać określonej warstwy bez zmiany innych warstw. Aby rozszerzyć sieci firmowej do chmury, należy użyć [sieci wirtualnej Azure](../../../virtual-network/virtual-networks-overview.md) usługi.

Ten wzorzec aplikacji hybrydowych jest przydatna, gdy:

* Chcesz tworzyć aplikacje, które działają częściowo w chmurze i częściowo lokalnie.
* Użytkownik chce migrować niektóre lub wszystkie elementy istniejącej aplikacji lokalnych do chmury.
* Chcesz przenieść aplikacje przedsiębiorstwa z lokalnymi zwirtualizowanych platformy Azure.
* Ma zostać właścicielem środowisku infrastruktury, w którym można skalować w górę i w dół na żądanie.
* Chcesz szybko udostępnić programowanie i testowanie środowisk w krótkich okresach czasu.
* Ma to ekonomiczne rozwiązanie, aby korzystać z kopii zapasowych dla aplikacji baz danych przedsiębiorstwa.

Poniższy diagram ilustruje wzorzec aplikacji hybrydowych n warstwowa obejmujący lokalną i platformą Azure. Jak pokazano na diagramie, lokalnymi infrastruktura obejmuje [usług domenowych w usłudze Active Directory](https://technet.microsoft.com/library/hh831484.aspx) kontrolera domeny w celu obsługi uwierzytelniania i autoryzacji użytkowników. Należy pamiętać, że diagram przedstawia scenariusza, w którym niektórych części warstwy danych na żywo w lokalnym centrum danych należy niektórych części warstwy danych na żywo na platformie Azure. W zależności od potrzeb aplikacji można zaimplementować kilka scenariuszy hybrydowych. Na przykład w środowisku lokalnym, ale warstwy danych na platformie Azure mogą przechowywać warstwą prezentacji a warstwą biznesową.

![N-warstwowy wzorzec aplikacji](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

Na platformie Azure, można użyć usługi Active Directory jako autonomiczny katalog chmury dla danej organizacji lub można również zintegrować istniejącej lokalnej usługi Active Directory z [usługi Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Jak pokazano na diagramie, składników warstwy biznesowej możesz uzyskać dostępu do wielu źródeł danych, takich jak do [programu SQL Server na platformie Azure](virtual-machines-windows-sql-server-iaas-overview.md) za pośrednictwem prywatnego wewnętrznego adresu IP, do lokalnego programu SQL Server za pośrednictwem [sieci wirtualnej Azure](../../../virtual-network/virtual-networks-overview.md), lub do [bazy danych SQL](../../../sql-database/sql-database-technical-overview.md) przy użyciu technologii dostawcy danych .NET Framework. Na tym diagramie baza danych SQL Azure jest usługą magazynu opcjonalnymi danymi.

We wzorcu aplikacji hybrydowych n warstwowa można zaimplementować poniższy przepływ pracy w określonej kolejności.

1. Zidentyfikować aplikacje baz danych przedsiębiorstwa, które należy przenieść do chmury przy użyciu [zestaw narzędzi planowania (MAP) i Microsoft Assessment](http://microsoft.com/map). Zestaw narzędzi do mapy zbiera dane spisu i wydajności z komputerów, które zamierzasz do wirtualizacji i zawiera zalecenia dotyczące pojemności i planowania oceny.
2. Plan zasobów i konfiguracji potrzebne do platformy Azure, takich jak konta magazynu i maszyn wirtualnych.
3. Skonfiguruj połączenie sieciowe między siecią firmową lokalną i [sieci wirtualnej Azure](../../../virtual-network/virtual-networks-overview.md). Aby skonfigurować połączenie między siecią firmową lokalną i maszynę wirtualną na platformie Azure, użyj jednej z następujących dwóch metod:
   
   1. Nawiąż połączenie między lokalną i platformą Azure za pośrednictwem publicznego punktów końcowych na maszynie wirtualnej na platformie Azure. Ta metoda zapewnia łatwą konfigurację i pozwala na użycie uwierzytelniania programu SQL Server na maszynie wirtualnej. Ponadto Konfigurowanie reguł sieciowej grupy zabezpieczeń do kontroli ruchu publicznego do maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [dostęp do zewnętrznych sieci maszyny Wirtualnej przy użyciu portalu Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Nawiąż połączenie między lokalną i platformą Azure za pośrednictwem tunelu sieci VPN Azure wirtualnego prywatnych. Ta metoda umożliwia rozszerzenie zasad domeny do maszyny wirtualnej na platformie Azure. Ponadto można skonfigurować reguły zapory i uwierzytelniania systemu Windows na maszynie wirtualnej. Obecnie usługa Azure obsługuje bezpieczne połączenie sieci VPN lokacja lokacja i połączeń sieci VPN punkt lokacja:
      
      * Bezpieczne połączenie lokacja lokacja z może nawiązać połączenie sieciowe między siecią lokalną a sieci wirtualnej na platformie Azure. Zaleca się podłączania centrum danych w lokalnym środowisku Azure.
      * Bezpieczne połączenie punkt lokacja z można ustanowić połączenie sieciowe między sieci wirtualnej platformy Azure i poszczególne komputery z uruchomionym w dowolnym miejscu. Zaleca się przede wszystkim do celów programowania i testowania.
      
      Aby uzyskać informacje na temat nawiązywania połączenia z programem SQL Server na platformie Azure, zobacz [Connect do programu SQL Server maszynę wirtualną na platformie Azure](virtual-machines-windows-sql-connect.md).
4. Skonfiguruj zaplanowane zadania i alerty, które wykonują kopie zapasowe danych lokalnych w dysku maszyny wirtualnej na platformie Azure. Aby uzyskać więcej informacji, zobacz [programu SQL Server z kopii zapasowej i przywracania usługi magazynu obiektów Blob Azure](https://msdn.microsoft.com/library/jj919148.aspx) i [kopii zapasowej i przywracania dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).
5. W zależności od potrzeb aplikacji można zaimplementować jedną z trzech następujących scenariuszy:
   
   1. Serwera sieci web, serwera aplikacji i danych nie liter na serwerze bazy danych na platformie Azure można zachować przechowywać poufnych danych w sieci lokalnej.
   2. Można zachować na serwerze sieci web i serwera aplikacji lokalnych podczas gdy serwer bazy danych na maszynie wirtualnej na platformie Azure.
   3. Możesz zachować z serwera bazy danych, serwer sieci web i serwera aplikacji lokalnej repliki bazy danych można przechowywać w maszynach wirtualnych platformy Azure. To ustawienie umożliwia lokalnych serwerów sieci web lub aplikacji raportowania można uzyskać dostępu do replik bazy danych na platformie Azure. W związku z tym można uzyskać, aby zmniejszyć obciążenie w lokalnej bazie danych. Firma Microsoft zaleca, aby zaimplementować ten scenariusz ciężki odczytu obciążeń i rozwoju celów. Informacje dotyczące tworzenia replik bazy danych na platformie Azure, zobacz temat zawsze włączonych grup dostępności w [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Porównanie strategii rozwoju sieci web na platformie Azure
Aby zaimplementować i wdrożyć wielowarstwową aplikacji opartej na programie SQL Server na platformie Azure, można użyć jednej z następujących dwóch metod programowania:

* Skonfiguruj serwer tradycyjnych sieci web (IIS - Internet Information Services) w bazach danych platformy Azure i dostępu w programie SQL Server w maszynach wirtualnych platformy Azure.
* Wdrożenie i wdrażanie usługi w chmurze na platformie Azure. Następnie upewnij się, że dostęp baz danych w programie SQL Server na maszynach wirtualnych Azure to usługa w chmurze. Usługi w chmurze może zawierać wielu ról sieć web i proces roboczy.

W poniższej tabeli przedstawiono porównanie projektowanie tradycyjnych witryn sieci web z usług Azure Cloud Services i aplikacji sieci Web platformy Azure w odniesieniu do programu SQL Server w maszynach wirtualnych platformy Azure. Tabela zawiera Azure Web Apps, jak można używać programu SQL Server w maszynie Wirtualnej platformy Azure jako źródła danych dla aplikacji sieci Web platformy Azure za pośrednictwem jego publiczny wirtualny adres IP lub nazwa DNS.

|  | Projektowanie witryn sieci web tradycyjnych w maszynach wirtualnych platformy Azure | Usługi w chmurze na platformie Azure | Hosting w aplikacjach sieci Web platformy Azure w sieci Web |
| --- | --- | --- | --- |
| **Migracja aplikacji z lokalnymi** |Istniejące aplikacje jako — jest. |Aplikacje muszą role sieci web i proces roboczy. |Istniejące aplikacje jako — ale nadaje się do aplikacji sieci web niezależna i usługi sieci web, które wymagają szybkiej skalowalności. |
| **Projektowanie i wdrażanie** |Program Visual Studio, programu WebMatrix, programu Visual Web Developer, WebDeploy, FTP, TFS, Menedżer usług IIS, programu PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. Każda usługa chmury ma dwóch środowisk, w których można wdrożyć pakiet usługi, a konfiguracja: tymczasową i produkcyjną. Usługi w chmurze można wdrożyć do środowiska pomostowego, aby ją przetestować przed Podwyższ go do produkcji. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Deploy, PowerShell. |
| **Administracja i instalacji** |Jest odpowiedzialny za zadań administracyjnych w aplikacji, dane, reguły zapory, sieci wirtualnej i systemu operacyjnego. |Jest odpowiedzialny za zadań administracyjnych w aplikacji, dane, reguły zapory i sieci wirtualnej. |Jest odpowiedzialny za zadań administracyjnych w aplikacji i tylko dane. |
| **Wysoka dostępność i odzyskiwanie po awarii (HADR)** |Firma Microsoft zaleca umieszczania maszyn wirtualnych w tym samym zestawie dostępności i w tej samej usłudze w chmurze. Przechowywanie maszyn wirtualnych w tym samym zestawu dostępności umożliwia Azure umieszczenie węzłów wysokiej dostępności w domenach awarii oddzielne i uaktualniania domen. Podobnie umożliwia przechowywanie maszyn wirtualnych w tej samej usłudze w chmurze, równoważenia obciążenia i maszyny wirtualne mogą komunikować się bezpośrednio ze sobą za pośrednictwem sieci lokalnej w obrębie centrum danych platformy Azure.<br/><br/>Jest odpowiedzialny za wdrażanie wysokiej dostępności i rozwiązanie odzyskiwania po awarii dla programu SQL Server w maszynach wirtualnych platformy Azure w celu uniknięcia żadnych przestojów. Dla obsługiwanych technologii HADR, zobacz [wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Jest odpowiedzialny za tworzenie kopii zapasowej własnych danych i aplikacji.<br/><br/>Azure można przenosić maszyn wirtualnych, gdy komputer hosta w centrum danych kończy się niepowodzeniem z powodu problemów sprzętowych. Ponadto może być planowanych przestojów maszyny Wirtualnej podczas aktualizacji oprogramowania i zabezpieczeń komputera hosta aktualizacji. Dlatego zaleca się zachowanie przez co najmniej dwie maszyny wirtualne w poszczególnych warstwach aplikacji dla zapewnienia ciągłej dostępności. Azure nie ma umowy SLA dla jednej maszyny wirtualnej. |Azure zarządza się błędów wynikających z podstawowej oprogramowania sprzętu lub systemu operacyjnego. Firma Microsoft zaleca, aby zaimplementować wiele wystąpień roli sieci web lub procesu roboczego, aby zapewnić wysoką dostępność aplikacji. Aby uzyskać informacje, zobacz [usługi w chmurze, maszyny wirtualne i wirtualne umową dotyczącą poziomu usług sieci](http://www.microsoft.com/download/details.aspx?id=38427).<br/><br/>Jest odpowiedzialny za tworzenie kopii zapasowej własnych danych i aplikacji.<br/><br/>W przypadku baz danych znajdującej się w bazie danych programu SQL Server w maszynie Wirtualnej platformy Azure jest odpowiedzialny za wdrażanie wysokiej dostępności i odzyskiwaniem po awarii rozwiązanie odzyskiwania w celu uniknięcia żadnych przestojów. Dla obsługiwanych HDAR technologii Zobacz wysokiej dostępności i odzyskiwania po awarii dla programu SQL Server w maszynach wirtualnych platformy Azure.<br/><br/>**Funkcja dublowania baz danych serwera SQL**: Użyj z usługami w chmurze Azure (role sieć web/proces roboczy). Maszyny wirtualne programu SQL Server i projektu usługi w chmurze mogą być w tej samej sieci wirtualnej platformy Azure. Maszyna wirtualna programu SQL Server nie znajduje się w tej samej sieci wirtualnej, należy utworzyć Alias serwera SQL na potrzeby kierowania komunikacji z wystąpieniem programu SQL Server. Ponadto nazwa aliasu musi odpowiadać nazwie programu SQL Server. |Wysoka dostępność jest odziedziczone role procesu roboczego platformy Azure, magazyn obiektów blob platformy Azure i bazy danych SQL Azure. Na przykład magazyn Azure przechowuje trzy repliki wszystkich obiektów blob, tabel i kolejki danych. W dowolnym momencie, baza danych SQL Azure śledzi trzy repliki danych systemem — jedna replika podstawowa i dwóch replik pomocniczych. Aby uzyskać więcej informacji, zobacz [usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/) i [bazy danych SQL Azure](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Korzystając z programu SQL Server w maszynie Wirtualnej platformy Azure jako źródła danych dla aplikacji sieci Web platformy Azure, należy pamiętać, aplikacje sieci Web Azure nie obsługuje sieci wirtualnej platformy Azure. Innymi słowy wszystkie połączenia z aplikacji sieci Web platformy Azure na maszynach wirtualnych serwera SQL na platformie Azure musi przechodzić przez publicznych punktów końcowych maszyn wirtualnych. Może to spowodować, że niektóre ograniczenia wysokiej dostępności i scenariuszy odzyskiwania po awarii. Na przykład aplikacja kliencka aplikacji sieci Web Azure połączenia dla maszyny Wirtualnej programu SQL Server z dublowania bazy danych nie będzie można nawiązać połączenia z nowym serwerem podstawowym jako dublowania bazy danych wymaga skonfigurowania sieci wirtualnej Azure między maszynami wirtualnymi hosta programu SQL Server na platformie Azure. W związku z tym przy użyciu **dublowania bazy danych serwera SQL** z aplikacjami sieci Web Azure nie jest obsługiwany obecnie.<br/><br/>**Grup dostępności AlwaysOn programu SQL Server**: można skonfigurować zawsze włączonych grup dostępności, podczas korzystania z aplikacji sieci Web platformy Azure z maszyn wirtualnych serwera SQL na platformie Azure. Jednak należy skonfigurować odbiornik grupy dostępności AlwaysOn przekierowujący komunikacji do repliki podstawowej za pośrednictwem publicznego punktów końcowych z równoważeniem obciążenia. |
| **Łączność między lokalizacjami** |Sieci wirtualnej platformy Azure można użyć do nawiązania połączenia lokalnego. |Sieci wirtualnej platformy Azure można użyć do nawiązania połączenia lokalnego. |Sieć wirtualna platformy Azure jest obsługiwana. Aby uzyskać więcej informacji, zobacz [integracji sieci wirtualnej aplikacji sieci Web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Skalowalność** |Skalowanie w pionie, należy zwiększyć rozmiary maszyn wirtualnych lub dodanie większej liczby dysków. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych na platformie Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Dla serwera bazy danych**: skalowalnego w poziomie jest dostępny za pośrednictwem techniki partycjonowania bazy danych i grup dostępności AlwaysOn programu SQL Server.<br/><br/>W przypadku odczytu złożonych zadań, można użyć [zawsze włączonych grup dostępności](https://msdn.microsoft.com/library/hh510230.aspx) na wielu węzłach dodatkowej, a także replikacji programu SQL Server.<br/><br/>W przypadku obciążeń duże zapisu można zaimplementować poziome partycjonowania danych między wieloma serwerami fizycznymi, aby zapewnić skalowalnych w poziomie aplikacji.<br/><br/>Ponadto można wdrożyć skalowanie w poziomie przy użyciu [programu SQL Server z routingiem zależnych danych](https://technet.microsoft.com/library/cc966448.aspx). Z danych zależnych routingu (DDR), musisz zaimplementować mechanizm partycjonowania w aplikacji klienta, zwykle w warstwie business warstwy kierować żądań bazy danych do wielu węzłów programu SQL Server. Warstwa biznesowa zawiera mapowania, jak dane są podzielone na partycje i węzeł, który zawiera dane.<br/><br/>Można skalować aplikacji, które są uruchomione maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [jak skalować aplikację](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Ważna Uwaga**: **skalowania automatycznego** funkcji na platformie Azure umożliwia automatyczne zwiększanie lub zmniejszanie maszyn wirtualnych, które są używane przez aplikację. Ta funkcja gwarantuje, że środowisko użytkownika końcowego nie wpływa negatywnie podczas okresów szczytowego, i maszyny wirtualne są zamknięte, jeśli żądanie jest niska. Zaleca się, że ustawiona opcja automatycznego skalowania dla usługi w chmurze, jeśli zawiera on maszyn wirtualnych programu SQL Server. Przyczyną jest to, że funkcja automatycznego skalowania pozwala Azure, aby włączyć na maszynie wirtualnej, gdy użycie procesora CPU w tej maszyny Wirtualnej jest większy niż próg niektórych i wyłączyć maszynę wirtualną, gdy użycie procesora CPU niższe niż. Funkcja skalowania automatycznego jest przydatne w przypadku aplikacji bezstanowych, takich jak serwery sieci web, w której żadnej maszyny Wirtualnej można zarządzać obciążenia bez żadnych odwołań do dowolnego poprzedniego stanu. Jednak funkcja automatycznego skalowania nie jest przydatne w przypadku aplikacji stanowych, takich jak SQL Server, gdzie tylko jedno wystąpienie umożliwia zapisywanie w bazie danych. |Skalowanie w pionie jest dostępna za pomocą wielu ról sieć web i proces roboczy. Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych dla ról sieci web i proces roboczy, zobacz [skonfigurować rozmiary dla usług w chmurze](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Korzystając z **usługi w chmurze**, można zdefiniować wiele ról do dystrybucji przetwarzania, a także osiągnięcia elastyczne skalowanie aplikacji. Każda usługa chmury zawiera jeden lub więcej ról sieć web i/lub rolę procesu roboczego, każdy z plików aplikacji i konfiguracji. Można skalować w górę usługi w chmurze przez odpowiednie zwiększenie liczby wystąpień roli (maszyny wirtualne), wdrożyć dla roli i dół usługi w chmurze, zmniejszając liczbę wystąpień roli. Aby uzyskać szczegółowe informacje, zobacz [modeli wykonywania Azure](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Skalowalny w poziomie jest dostępny za pośrednictwem obsługi wbudowaną wysoką dostępność Azure za pomocą [usługi w chmurze, maszyny wirtualne i wirtualne umową dotyczącą poziomu usług sieci](http://www.microsoft.com/download/details.aspx?id=38427) i moduł równoważenia obciążenia.<br/><br/>Dla aplikacji wielowarstwowych zaleca się połączyć z aplikacji role sieć web/proces roboczy w bazie danych serwera maszyn wirtualnych za pośrednictwem sieci wirtualnej platformy Azure. Ponadto platforma Azure udostępnia równoważenie obciążenia dla maszyn wirtualnych w tej samej usłudze w chmurze rozłożenie ich żądań użytkowników. Maszyny wirtualne, połączone w ten sposób mogą komunikować się bezpośrednio ze sobą za pośrednictwem sieci lokalnej w obrębie centrum danych platformy Azure.<br/><br/>Możesz skonfigurować **skalowania automatycznego** w portalu Azure, a także razy harmonogramu. Aby uzyskać więcej informacji, zobacz [jak skonfigurować automatyczne skalowanie usługi w chmurze w portalu](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Skalowanie w górę i w dół**: użytkownik może zwiększenie/zmniejszenie rozmiaru wystąpienia (VM) zarezerwowana dla witryny sieci web.<br/><br/>Skalowanie w poziomie: witryny sieci web można dodać więcej zastrzeżone wystąpień (VM).<br/><br/>Możesz skonfigurować **skalowania automatycznego** w portalu, a także razy harmonogramu. Aby uzyskać więcej informacji, zobacz [jak aplikacje sieci Web skali](../../../app-service/web-sites-scale.md). |

Aby uzyskać więcej informacji o wyborze między tych metod programowania, zobacz [Azure Web Apps, usługi w chmurze i maszyn wirtualnych: kiedy należy używać](../../../app-service/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na omówienia maszyn wirtualnych Azure](virtual-machines-windows-sql-server-iaas-overview.md).

