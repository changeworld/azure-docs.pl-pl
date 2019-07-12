---
title: Często zadawane pytania dotyczące korzystania z usługi Azure Database Migration Service | Dokumentacja firmy Microsoft
description: Dowiedz się, często zadawane pytania dotyczące korzystania z usługi Azure Database Migration Service do przeprowadzania migracji bazy danych.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/10/2019
ms.openlocfilehash: 5077539f6f80784f865bd4c1b52e3b4c147107ed
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718001"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Często zadawane pytania dotyczące korzystania z usługi Azure Database Migration Service

Ten artykuł zawiera listę często zadawane pytania dotyczące korzystania z usługi Azure Database Migration Service wraz z powiązane odpowiedzi.

## <a name="overview"></a>Omówienie

**PYTANIA I ODPOWIEDZI. Co to jest usługa Azure Database Migration Service?**
Azure Database Migration Service to w pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemową migrację z wielu źródłowych baz danych na danych na platformę Azure przy minimalnych przestojach. Usługa jest obecnie ogólnie dostępna, z trwającą programistycznych koncentruje się na:

* Niezawodność i wydajność.
* Iteracyjne dodatkowych par źródło cel.
* Dalsze inwestycje w bezproblemowe migracje.

**PYTANIA I ODPOWIEDZI. Jakie par źródło i cel usługi Azure Database Migration Service obsługuje obecnie?**
Usługa obecnie obsługuje wiele par źródło i cel lub scenariuszy migracji. Aby uzyskać pełną listę stan poszczególnych scenariuszy migracji dostępne, zobacz artykuł [stan scenariusze migracji z obsługiwanych przez usługi Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Inne scenariusze migracji są w wersji zapoznawczej i wymaga przesyłanie nominacji za pośrednictwem witryny DMS (wersja zapoznawcza). Aby uzyskać pełną listę scenariuszy w wersji zapoznawczej i zarejestruj, aby wziąć udział w jednym z tych ofert, zobacz [witryny w wersji zapoznawczej usługi DMS](https://aka.ms/dms-preview/).

**PYTANIA I ODPOWIEDZI. Jakie wersje programu SQL Server, Azure Database Migration Service obsługuje jako źródła?**
Podczas migracji z programu SQL Server, obsługiwanych źródeł dla usługi Azure Database Migration Service to SQL Server 2005 za pomocą programu SQL Server 2017.

**Pyt.: Korzystając z usługi Azure Database Migration Service, jaka jest różnica między w trybie offline i migracji do usługi online?**
Azure Database Migration Service służy do przeprowadzania migracji w trybie offline i online. Za pomocą *offline* migracji aplikacji przestój rozpocznie się po rozpoczęciu migracji. Za pomocą *online* migracji okres przestoju jest ograniczony do czasu jednorazową po zakończeniu migracji. Zalecamy przetestowanie migracji offline w celu ustalenia, czy przestój jest dopuszczalny. Jeśli nie jest on dopuszczalny, przeprowadź migrację online.

> [!NOTE]
> Korzystanie z usługi Azure Database Migration Service w celu wykonania migracji do usługi online wymaga tworzenia wystąpienia na podstawie warstwy cenowej Premium. Więcej informacji znajduje się na stronie [cennika](https://azure.microsoft.com/pricing/details/database-migration/) usługi Azure Database Migration Service.

**PYTANIA I ODPOWIEDZI. Jak wygląda porównanie usługi Azure Database Migration Service do innych narzędzi migracji bazy danych firmy Microsoft, takich jak bazy danych Migration Assistant (DMA) lub SQL Server Migration Assistant (SSMA)?**
Azure Database Migration Service jest preferowaną metodą migracji bazy danych w systemie Microsoft Azure na dużą skalę. Aby uzyskać więcej szczegółów na porównanie usługi Azure Database Migration Service do innych firmy Microsoft bazy danych narzędzi migracji, a następnie uzyskać zalecenia dotyczące używania usługi dla różnych scenariuszy, zobacz ogłaszania blogu [migracji bazy danych rozszerzają firmy Microsoft Narzędzia i usługi](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

**PYTANIA I ODPOWIEDZI. Jak wygląda porównanie usługi Azure Database Migration Service do oferty usługi Azure Migrate?**
Usługa Azure Migrate obsługuje migrację lokalnych maszyn wirtualnych IaaS platformy Azure. Usługa ocenia przydatność migracji i ustalania rozmiaru na podstawie wydajności i zapewnia szacowane koszty uruchamiania lokalnych maszyn wirtualnych na platformie Azure. Usługa Azure Migrate jest przydatne w przypadku migracji lift-and-shift lokalnych obciążeń opartych na maszynę Wirtualną do maszyn wirtualnych IaaS platformy Azure. Jednak w przeciwieństwie do usługi Azure Database Migration Service, usługa Azure Migrate nie usługa migracji bazy danych specjalne przeznaczone dla platform relacyjnej bazy danych modelu PaaS platformy Azure, takich jak Azure SQL Database lub wystąpienia zarządzanego Azure SQL Database.

## <a name="setup"></a>Konfiguracja

**PYTANIA I ODPOWIEDZI. Jakie są wymagania wstępne dotyczące korzystania z usługi Azure Database Migration Service?**
Istnieje kilka wymagań wstępnych wymagane w celu zapewnienia, że usługa Azure Database Migration Service działa sprawnie podczas przeprowadzania migracji bazy danych. Pewne warunki wstępne mają zastosowanie we wszystkich scenariuszach (par źródło cel) obsługiwane przez usługę, podczas gdy inne wymagania wstępne są unikatowe dla konkretnego scenariusza.

Azure Database Migration Service wstępnie wymagane składniki, które są wspólne dla wszystkich obsługiwanych scenariuszy migracji obejmują konieczność:

* Tworzenie sieci wirtualnej dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, co zapewnia łączność lokacja lokacja z serwerów źródłowych w środowisku lokalnym przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [ Sieć VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Upewnij się, że usługi Azure Virtual Network (VNet) reguły sieciowej grupy zabezpieczeń nie blokują następujących komunikacji porty 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółowych informacji dotyczących filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure, zobacz artykuł [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Podczas korzystania z urządzenia zapory przed źródłowych baz danych, użytkownik może być konieczne dodanie reguły zapory zezwalające usługi Azure Database Migration Service dostęp źródłowych baz danych do migracji do.

Aby uzyskać listę wszystkich wymagań wstępnych dotyczących konkurować scenariusze dotyczące migracji za pomocą usługi Azure Database Migration Service, zobacz powiązanych samouczków w usłudze Azure Database Migration Service [dokumentacji](https://docs.microsoft.com/azure/dms/dms-overview) w witrynie docs.microsoft.com.

**PYTANIA I ODPOWIEDZI. Jak znaleźć adres IP dla usługi Azure Database Migration Service, dzięki czemu można utworzyć listę dozwolonych reguł zapory umożliwiające dostęp do bazy danych źródłowego do migracji?**
Może być konieczne dodanie reguły zapory, dzięki czemu Azure Database Migration Service dostęp do źródłowej bazy danych do migracji. Adres IP dla usługi jest dynamiczny, ale jeśli używasz programu Express Route, ten adres prywatnie jest przypisywany przez sieci firmowej. Najprostszym sposobem, aby zidentyfikować odpowiedni adres IP jest Szukaj w tej samej grupie zasobów aprowizowanych zasobu usługi Azure Database Migration Service można znaleźć skojarzonego interfejsu sieciowego. Zazwyczaj nazwą zasobu interfejsu sieciowego zaczyna się od prefiksu karty Sieciowej i następuje znak unikatowy i numer sekwencji, na przykład jj6tnztnmarpsskr82rbndyp karty Sieciowej. Wybranie tego zasobu interfejsu sieciowego, możesz zobaczyć adres IP, który musi być uwzględniony na liście dozwolonych na Przegląd zasobów stronę witryny Azure portal.

Może być również konieczne obejmują źródło portu programu SQL Server nasłuchuje na liście dozwolonych. Domyślnie tego portu 1433, ale źródła, które można skonfigurować programu SQL Server w celu nasłuchiwania również innych portów. W takim przypadku konieczne jest uwzględnienie tych portów z listy dozwolonych. Można określić numer portu, którego nasłuchuje serwer SQL za pomocą zapytania dynamicznego widoku zarządzania:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Można także określić numer portu, którego nasłuchuje serwer SQL, badając dziennik błędów programu SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**PYTANIA I ODPOWIEDZI. Jak skonfigurować usługi Azure Virtual Network?**
Podczas wiele samouczków firmy Microsoft, które prowadzi użytkownika przez proces konfigurowania sieci Wirtualnej platformy Azure, oficjalnej dokumentacji pojawia się w artykule [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Użycie

**PYTANIA I ODPOWIEDZI. Co to jest podsumowanie kroków wymaganych do przeprowadzenia migracji bazy danych przy użyciu usługi Azure Database Migration Service?**
Podczas migracji typowych, prostych bazy danych możesz:

1. Tworzenie bazy danych docelowych.
2. Oceń źródłowych baz danych.
    * Migracji homogenicznych oceny istniejących baz danych przy użyciu [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Heterogenicznych migracji (od źródła możliwość współzawodnictwa), oceny istniejących baz danych przy użyciu [SSMA](https://aka.ms/get-ssma). Możesz także użyć SSMA konwersji obiektów bazy danych i migrację schematu dla platformy docelowej.
3. Utwórz wystąpienie usługi Azure Database Migration Service.
4. Utwórz projekt migracji, określenie źródłowych baz danych, docelowej bazy danych i tabele do migrowania.
5. Uruchom pełne ładowanie.
6. Wybierz następne sprawdzania poprawności.
7. Wykonaj ręczne przełączenie środowiska produkcyjnego do nowej bazy danych opartej na chmurze.

## <a name="troubleshooting-and-optimization"></a>Rozwiązywanie problemów i optymalizacja

**PYTANIA I ODPOWIEDZI. Jestem konfigurowania projektu migracji w DMS i występują trudności z połączeniem do źródłowej bazy danych. Co zrobić?**
Jeśli masz problemy z nawiązaniem połączenia z systemem źródłowej bazy danych podczas pracy nad migracji, należy utworzyć maszynę wirtualną w sieci wirtualnej, z którym skonfigurowanie wystąpienia usługi DMS. Na maszynie wirtualnej można uruchomić test connect, np. przy użyciu pliku UDL, aby przetestować połączenie do programu SQL Server lub pobierania z programu 3T Robo w celu przetestowania połączenia bazy danych MongoDB. Jeśli test połączenia powiedzie się, nie powinny mieć problem z nawiązaniem połączenia z bazą danych źródłowych. Jeśli test połączenia nie powiedzie się, skontaktuj się z administratorem sieci.

**PYTANIA I ODPOWIEDZI. Niedostępna lub zatrzymana, dlaczego jest Moje Azure Database Migration Service?**
Użytkownik jawnie zatrzymuje usługi Azure Database Migration Service (DMS) lub usługi jest nieaktywny przez okres 24 godzin, usługa będzie znajdować się w zatrzymania lub auto stan wstrzymania. W obu przypadkach usługa będzie niedostępne i znajduje się w stanie zatrzymania.  Aby wznowić migracje aktywne, uruchom ponownie usługę.

**PYTANIA I ODPOWIEDZI. Czy istnieją wszystkie zalecenia dotyczące optymalizacji wydajności usługi Azure Database Migration Service?**
Możesz zrobić kilka rzeczy, aby przyspieszyć migrację bazy danych przy użyciu usługi:

* Podczas tworzenia wystąpienia usługi, aby umożliwić usłudze móc korzystać z wielu procesorów wirtualnych dla przetwarzania równoległego i szybszy transfer danych za pomocą wielu Procesora ogólnego przeznaczenia warstwy cenowej.
* Tymczasowo skalowania w górę wystąpienia docelowej usługi Azure SQL Database do warstwy Premium jednostki SKU podczas operacji migracji danych, aby zminimalizować usługi Azure SQL Database, ograniczanie przepustowości, która może mieć wpływ na działania transferu danych podczas korzystania z jednostki SKU niższego poziomu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie usługi Azure Database Migration Service i dostępności regionalnej, zobacz artykuł [co to jest Azure Database Migration Service](dms-overview.md).
