---
title: Pule wystąpień (wersja zapoznawcza)
description: W tym artykule opisano pule wystąpień usługi Azure SQL Database (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 0938fbe94cb0d1e6dae3dcb84950a11f90dd9db8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878157"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>Co to są pule wystąpień bazy danych SQL (wersja zapoznawcza)?

Pule wystąpień to nowy zasób w usłudze Azure SQL Database, który zapewnia wygodny i ekonomiczny sposób migracji mniejszych wystąpień SQL do chmury na dużą skalę.

Pule wystąpień umożliwiają wstępną aprowizację zasobów obliczeniowych zgodnie z całkowitymi wymaganiami migracji. Następnie możesz wdrożyć kilka osobnych wystąpień zarządzanych — do osiągnięcia wstępnie aprowizowanego poziomu obliczeń. Na przykład jeśli wstępnie aprowizować 8 vCores można wdrożyć dwa 2 2 vCore i jeden 4 wystąpienia vCore, a następnie migracji baz danych do tych wystąpień. Przed puli wystąpień są dostępne, mniejsze i mniej obciążeń wymagających obliczeniowych często muszą być skonsolidowane w większym wystąpieniu zarządzanym podczas migracji do chmury. Konieczność migracji grup baz danych do dużego wystąpienia zwykle wymaga starannego planowania pojemności i nadzoru zasobów, dodatkowych kwestii zabezpieczeń i niektórych dodatkowych działań konsolidacyjnych danych na poziomie wystąpienia.

Ponadto pule wystąpień obsługują natywną integrację sieci wirtualnej, dzięki czemu można wdrożyć wiele pul wystąpień i wiele pojedynczych wystąpień w tej samej podsieci.


## <a name="key-capabilities-of-instance-pools"></a>Kluczowe możliwości pul wystąpień

Pule wystąpień zapewniają następujące korzyści:

1. Możliwość hosta 2 wystąpień vCore. *Tylko dla wystąpień w pulach wystąpień . \**
2. Przewidywalny i szybki czas wdrażania wystąpienia (do 5 minut).
3. Minimalna alokacja adresu IP.

Na poniższym diagramie przedstawiono pulę wystąpień z wieloma wystąpieniami wdrożonymi w podsieci sieci wirtualnej.

![pula wystąpień z wieloma wystąpieniami](./media/sql-database-instance-pools/instance-pools1.png)

Pule wystąpień umożliwiają wdrażanie wielu wystąpień na tej samej maszynie wirtualnej, gdzie rozmiar obliczeniowy maszyny wirtualnej jest oparty na całkowitej liczbie korpów wirtualnych przydzielonych dla puli. Ta architektura umożliwia *partycjonowanie* maszyny wirtualnej do wielu wystąpień, które mogą mieć dowolny obsługiwany rozmiar, w tym 2 rdzenie wirtualne (2 wystąpienia rdzeni wirtualnych są dostępne tylko dla wystąpień w pulach).

Operacje zarządzania wystąpień w puli są znacznie szybsze, gdy pula jest początkowo wdrożony. Te operacje są szybsze, ponieważ wdrażanie lub rozszerzanie [klastra wirtualnego](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (dedykowany zestaw maszyn wirtualnych) nie jest częścią inicjowania obsługi administracyjnej wystąpienia zarządzanego.

Ponieważ wszystkie wystąpienia w puli współużytkują tę samą maszynę wirtualną, całkowita alokacja adresu IP nie zależy od liczby wdrożonych wystąpień, co jest wygodne do wdrożenia w podsieciach o wąskim zakresie adresów IP.

Każda pula ma stałą alokację adresów IP tylko dziewięciu adresów IP (z wyłączeniem pięciu adresów IP w podsieci, które są zarezerwowane dla własnych potrzeb). Aby uzyskać szczegółowe informacje, zobacz [wymagania dotyczące rozmiaru podsieci dla pojedynczych wystąpień](sql-database-managed-instance-determine-size-vnet-subnet.md).

## <a name="application-scenarios-for-instance-pools"></a>Scenariusze aplikacji dla puli na przykład

Poniższa lista zawiera główne przypadki użycia, w których należy wziąć pod uwagę pule wystąpień:

- Migracja *grupy wystąpień SQL* w tym samym czasie, gdzie większość jest mniejszy rozmiar (na przykład 2 lub 4 vCores).
- Scenariusze, w których *przewidywalne i krótkie tworzenie lub skalowanie wystąpienia* jest ważne. Na przykład wdrożenie nowej dzierżawy w środowisku aplikacji SaaS z wieloma dzierżawcami, które wymaga możliwości na poziomie wystąpienia.
- Scenariusze, w których posiadanie *stałego limitu kosztów* lub *wydatków* jest ważne. Na przykład uruchamianie środowiska dewelopera udostępnionego lub środowiska demonstracyjne o rozmiarze stałego (lub rzadko zmieniającego się), w którym okresowo wdrażane są wystąpienia zarządzane w razie potrzeby.
- Scenariusze, w których *minimalna alokacja adresu IP* w podsieci wirtualnej jest ważne. Wszystkie wystąpienia w puli współużytkują maszynę wirtualną, więc liczba przydzielonych adresów IP jest niższa niż w przypadku pojedynczych wystąpień.


## <a name="architecture-of-instance-pools"></a>Architektura pul wystąpień

Pule wystąpień mają podobną architekturę do zwykłych wystąpień zarządzanych *(pojedyncze wystąpienia).* Aby obsługiwać [wdrożenia w sieciach wirtualnych platformy Azure (sieci wirtualne)](../virtual-network/virtual-network-for-azure-services.md) oraz zapewnić izolację i bezpieczeństwo dla klientów, pule wystąpień również polegają na [klastrach wirtualnych.](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) Klastry wirtualne reprezentują dedykowany zestaw izolowanych maszyn wirtualnych wdrożonych w podsieci sieci wirtualnej klienta.

Główną różnicą między dwoma modelami wdrażania jest to, że pule wystąpień zezwalają na wiele wdrożeń procesów programu SQL Server w tym samym węźle maszyny wirtualnej, które są zasobami zarządzanymi za pomocą [obiektów zadań systemu Windows,](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects)podczas gdy pojedyncze wystąpienia są zawsze same w węźle maszyny wirtualnej.

Na poniższym diagramie przedstawiono pulę wystąpień i dwa pojedyncze wystąpienia wdrożone w tej samej podsieci i przedstawiono główne szczegóły architektury dla obu modeli wdrażania:

![pula wystąpień i dwa pojedyncze wystąpienia](./media/sql-database-instance-pools/instance-pools2.png)

Każda pula wystąpień tworzy oddzielny klaster wirtualny pod spodem. Wystąpienia w puli i pojedyncze wystąpienia wdrożone w tej samej podsieci nie współużytkują zasoby obliczeniowe przydzielone do procesów i składników bramy programu SQL Server, co zapewnia przewidywalność wydajności.

## <a name="instance-pools-resource-limitations"></a>Ograniczenia zasobów pul wystąpienia

Istnieje kilka ograniczeń zasobów dotyczących pul wystąpień i wystąpień wewnątrz pul:

- Pule wystąpień są dostępne tylko na sprzęcie Gen5.
- Wystąpienia w puli mają dedykowany procesor CPU i pamięć RAM, więc zagregowana liczba rdzeni wirtualnych we wszystkich wystąpieniach musi być mniejsza lub równa liczbie rdzeni wirtualnych przydzielonych do puli.
- Wszystkie [limity poziomu wystąpienia](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) mają zastosowanie do wystąpień utworzonych w puli.
- Oprócz limitów na poziomie wystąpienia istnieją również dwa limity nałożone *na poziomie puli wystąpień:*
  - Całkowity rozmiar magazynu na pulę (8 TB).
  - Całkowita liczba baz danych na pulę (100).

Całkowita alokacja magazynu i liczba baz danych we wszystkich wystąpieniach musi być niższa lub równa limitom ujawnionym przez pule wystąpień.

- Pule wystąpień obsługują 8, 16, 24, 32, 40, 64 i 80 vCorów.
- Wystąpienia zarządzane wewnątrz pul obsługują 2, 4, 8, 16, 24, 32, 40, 64 i 80 vCorów.
- Wystąpienia zarządzane wewnątrz pul obsługują rozmiary magazynu o rozmiarach od 32 GB do 8 TB, z wyjątkiem:
  - 2 wystąpienia vCore obsługują rozmiary od 32 GB do 640 GB
  - 4 wystąpienia vCore obsługują rozmiary od 32 GB do 2 TB

[Właściwość warstwy usług](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) jest skojarzona z zasobem puli wystąpień, więc wszystkie wystąpienia w puli muszą być tej samej warstwy usług co warstwa usług puli. W tej chwili dostępna jest tylko warstwa usługi ogólnego przeznaczenia (zobacz następującą sekcję dotyczącą ograniczeń w bieżącej wersji zapoznawczej).

### <a name="public-preview-limitations"></a>Ograniczenia publicznej wersji zapoznawczej

Publiczna wersja zapoznawcza ma następujące ograniczenia:

- Obecnie dostępna jest tylko warstwa usług ogólnego przeznaczenia.
- Pule wystąpień nie można skalować podczas publicznej wersji zapoznawczej, więc ważne jest dokładne planowanie zdolności produkcyjnych przed wdrożeniem.
- Obsługa witryny Azure portal na tworzenie i konfiguracja puli wystąpienia nie jest jeszcze dostępna. Wszystkie operacje w pulach wystąpień są obsługiwane tylko za pośrednictwem programu PowerShell. Początkowe wdrożenie wystąpienia w wstępnie utworzonej puli jest również obsługiwane tylko za pośrednictwem programu PowerShell. Po wdrożeniu w puli wystąpienia zarządzane można aktualizować za pomocą witryny Azure Portal.
- Wystąpienia zarządzane utworzone poza pulą nie mogą być przenoszone do istniejącej puli, a wystąpienia utworzone wewnątrz puli nie mogą być przenoszone na zewnątrz jako pojedyncze wystąpienie lub do innej puli.
- Ceny wystąpienia zarezerwowanego (licencja w zestawie lub z korzyścią hybrydową platformy Azure) są niedostępne.

## <a name="sql-features-supported"></a>Obsługiwane funkcje SQL

Wystąpienia utworzone w pulach obsługują te same [poziomy zgodności i funkcje obsługiwane w pojedynczych wystąpieniach zarządzanych](sql-database-managed-instance.md#sql-features-supported).

Każde wystąpienie zarządzane wdrożone w puli ma oddzielne wystąpienie agenta SQL.

Opcjonalne funkcje lub funkcje, które wymagają wybrania określonych wartości (takich jak sortowanie na poziomie wystąpienia, strefa czasowa, publiczny punkt końcowy dla ruchu danych, grupy trybu failover) są konfigurowane na poziomie wystąpienia i mogą być różne dla każdego wystąpienia w puli.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Chociaż wystąpienia zarządzane w pulach mają dedykowane komputery wirtualne i pamięć RAM, współużytkują dysk lokalny (dla użycia bazy danych tempdb) i zasoby sieciowe. Nie jest prawdopodobne, ale jest możliwe doświadczenie hałaśliwy efekt *sąsiada,* jeśli wiele wystąpień w puli mają wysokie zużycie zasobów w tym samym czasie. Jeśli obserwujesz to zachowanie, należy rozważyć wdrożenie tych wystąpień do większej puli lub jako pojedyncze wystąpienia.

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Ponieważ wystąpienia wdrożone w puli współużytkują tę samą maszynę wirtualną, warto rozważyć wyłączenie funkcji, które wprowadzają wyższe zagrożenia bezpieczeństwa lub zdecydowane kontrolowanie uprawnień dostępu do tych funkcji. Na przykład integracja CLR, natywna kopia zapasowa i przywracanie, poczta e-mail bazy danych itp.

## <a name="instance-pool-support-requests"></a>Żądania pomocy technicznej puli wystąpień

Tworzenie żądań pomocy technicznej i zarządzanie nimi na przykład w [witrynie Azure portal](https://portal.azure.com).

Jeśli występują problemy związane z wdrażaniem puli wystąpień (tworzenie lub usuwanie), upewnij się, że określono **pule wystąpień** w polu **podtyp problemu.**

![żądania pomocy technicznej pul wystąpień](./media/sql-database-instance-pools/support-request.png)

Jeśli występują problemy związane z pojedynczych wystąpień lub baz danych w puli, należy utworzyć bilet regularne pomocy technicznej dla wystąpień zarządzanych usługi Azure SQL Database.

Aby utworzyć większe wdrożenia wystąpienia zarządzanego (z pulami wystąpień lub bez), może być konieczne uzyskanie większego przydziału regionalnego. Aby uzyskać więcej informacji, zobacz [Żądanie zwiększenia przydziału dla usługi Azure SQL Database](quota-increase-request.md). Należy zauważyć, że jeśli używasz pule wystąpień, logika wdrażania porównuje całkowite zużycie pór wirtualnych *na poziomie puli* z przydziałem, aby określić, czy można tworzyć nowe zasoby bez dalszego zwiększania przydziału.

## <a name="instance-pool-billing"></a>Rozliczanie puli wystąpień

Pule wystąpień umożliwiają niezależne skalowanie obliczeń i magazynu. Klienci płacą za obliczenia skojarzone z zasobem puli mierzonym w rach wirtualnych i magazynu skojarzonego z każdym wystąpieniem mierzonym w gigabajtach (pierwsze 32 GB są bezpłatne dla każdego wystąpienia).

Cena vCore dla puli jest naliczana niezależnie od tego, ile wystąpień jest wdrażanych w tej puli.

W przypadku ceny obliczeń (mierzonej w rów wirtualnych) dostępne są dwie opcje cenowe:

  1. *Licencja w zestawie:* Cena licencji SQL jest wliczona w cenę. Dotyczy to klientów, którzy zdecydują się nie stosować istniejących licencji programu SQL Server za pomocą pakietu Software Assurance.
  2. *Korzyści hybrydowe platformy Azure:* obniżona cena, która obejmuje korzyści hybrydowe platformy Azure dla programu SQL Server. Klienci mogą zdecydować się na tę cenę, korzystając z istniejących licencji programu SQL Server z pakietem Software Assurance. Aby uzyskać uprawnienia i inne szczegóły, zobacz [Korzyści hybrydowe platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Ustawienie różnych opcji cen nie jest możliwe dla poszczególnych wystąpień w puli. Wszystkie wystąpienia w puli nadrzędnej muszą być zgodne z ceną zawartą w licencji lub ceną korzyści hybrydowych platformy Azure. Model licencji dla puli można zmienić po utworzeniu puli.

> [!IMPORTANT]
> Jeśli określisz model licencji dla wystąpienia, które jest inne niż w puli, cena puli jest używana, a wartość poziomu wystąpienia jest ignorowana.

Jeśli tworzysz pule wystąpień w [ramach subskrypcji kwalifikujących się do korzyści dewelopersko-testowych,](https://azure.microsoft.com/pricing/dev-test/)automatycznie otrzymasz zniżki w wysokości do 55 procent w przypadku wystąpienia zarządzanego sql platformy Azure.

Aby uzyskać szczegółowe informacje na temat cen puli wystąpień, zapoznaj się z sekcją *Pule wystąpień* na [stronie cennik wystąpienia zarządzanego](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z pulami wystąpień, zobacz [przewodnik po pulach wystąpień bazy danych SQL](sql-database-instance-pools-how-to.md).
- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [Przewodnik szybki start](sql-database-managed-instance-get-started.md).
- Aby uzyskać funkcje i listę porównawczą, zobacz [wspólne funkcje SQL](sql-database-features.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [konfigurację sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
- Aby uzyskać przewodnik Szybki start, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby zapoznać się z samouczkiem przy użyciu usługi migracji bazy danych Azure (DMS) do migracji, zobacz [migracja wystąpienia zarządzanego przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpienia zarządzanego za pomocą wbudowanej analizy rozwiązywania problemów, zobacz [Monitorowanie usługi Azure SQL Database przy użyciu usługi Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Aby uzyskać informacje o cenach, zobacz [cennik zarządzanych wystąpień bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
