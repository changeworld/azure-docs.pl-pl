---
title: Omówienie tworzenia aplikacji
description: Informacje o dostępnych bibliotekach łączności i najlepsze praktyki dotyczące aplikacji łączących się z usługą SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.openlocfilehash: 741906bbe9de68459b2e4a704a243fde4771b3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067325"
---
# <a name="sql-database-application-development-overview"></a>Omówienie tworzenia aplikacji bazy danych SQL

W tym artykule przedstawiono podstawowe zagadnienia, jakie powinien mieć na uwadze programista podczas pisania kodu nawiązującego połączenie z usługą Azure SQL Database. Ten artykuł dotyczy wszystkich modeli wdrażania usługi Azure SQL Database (pojedyncza baza danych, pule elastyczne, wystąpienie zarządzane).

> [!TIP]
> Zapoznaj się z przewodnikami wprowadzenie dla [pojedynczych baz danych](sql-database-single-database-quickstart-guide.md) i [wystąpień zarządzanych,](sql-database-managed-instance-quickstart-guide.md) jeśli chcesz skonfigurować usługę Azure SQL Database.
>

## <a name="language-and-platform"></a>Język i platforma

Można używać różnych [języków programowania i platform](sql-database-connect-query.md) do łączenia i zapytania usługi Azure SQL Database. Można znaleźć [przykładowe aplikacje,](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) których można użyć do nawiązania połączenia z bazą danych SQL Azure.

Możesz korzystać z narzędzi open source, takich jak [gepard](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Code](https://code.visualstudio.com/). Ponadto usługa Azure SQL Database współpracuje z narzędziami firmy Microsoft, takimi jak [Visual Studio](https://www.visualstudio.com/downloads/) i [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Można również użyć witryny Azure portal, PowerShell i REST interfejsów API, które ułatwiają uzyskanie dodatkowej produktywności.

## <a name="authentication"></a>Uwierzytelnianie

Dostęp do usługi Azure SQL Database jest chroniony za pomocą loginów i zapór. Usługa Azure SQL Database obsługuje zarówno użytkowników uwierzytelniania i logowania programu SQL Server, jak i [usługi Azure Active Directory (AAD).](sql-database-aad-authentication.md) Logowania AAD są dostępne tylko w wystąpieniu zarządzanym. 

Dowiedz się więcej o [zarządzaniu dostępem do bazy danych i logowaniem](sql-database-manage-logins.md).

## <a name="connections"></a>Połączenia

W logice połączenia klienta zastąp domyślny limit czasu wartością 30 sekund. Domyślna wartość 15 sekund jest zbyt mała w przypadku połączeń zależnych od Internetu.

Jeśli korzystasz z [puli połączeń](https://msdn.microsoft.com/library/8xx3tyca.aspx), pamiętaj o zamknięciu połączenia, gdy tylko Twój program nie korzysta z niego aktywnie i nie przygotowuje się do jego ponownego użycia.

Unikaj długotrwałych transakcji, ponieważ awaria infrastruktury lub połączenia może wycofać transakcję. Jeśli to możliwe, podziel transakcję w wielu mniejszych transakcjach i użyj [przetwarzania wsadowego, aby zwiększyć wydajność](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Odporność

Usługa Azure SQL Database to usługa w chmurze, w której można oczekiwać błędów przejściowych, które zdarzają się w podstawowej infrastrukturze lub w komunikacji między jednostkami chmury. Mimo że usługa Azure SQL Database jest odporna na błędy infrastruktury przechodniej, te błędy mogą mieć wpływ na łączność. Gdy podczas łączenia się z bazą danych SQL wystąpi błąd przejściowy, kod powinien [ponowić próbę wywołania](sql-database-connectivity-issues.md). Zalecamy, aby logika ponawiania korzystała z logiki wycofywania, co pozwoli uniknąć przeciążenia usługi SQL Database z powodu jednoczesnych ponownych prób ze strony wielu klientów. Logika ponawiania próby zależy od [komunikatów o błędach dla programów klienckich bazy danych SQL](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md).

Aby uzyskać więcej informacji na temat przygotowania się do zdarzeń planowanej konserwacji w bazie danych SQL platformy Azure, zobacz [planowanie zdarzeń konserwacji platformy Azure w usłudze Azure SQL Database.](sql-database-planned-maintenance.md)

## <a name="network-considerations"></a>Kwestie dotyczące sieci

- Upewnij się, że zapora na komputerze hostującym program kliencki zezwala na wychodzącą komunikację TCP na porcie 1433.  Więcej informacji: [Konfigurowanie zapory bazy danych SQL platformy Azure](sql-database-configure-firewall-settings.md).
- Jeśli program kliencki łączy się z bazą danych SQL, gdy klient działa na maszynie wirtualnej platformy Azure (VM), należy otworzyć niektóre zakresy portów na maszynie wirtualnej. Więcej informacji: [Porty poza 1433 dla ADO.NET 4.5 i bazy danych SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
- Połączenia klienta z usługą Azure SQL Database czasami pomijają serwer proxy i współdziałają bezpośrednio z bazą danych. Porty inne niż 1433 nabierają znaczenia. Aby uzyskać więcej informacji, [architektura łączności usługi Azure SQL Database](sql-database-connectivity-architecture.md) i [porty poza 1433 dla ADO.NET 4.5 i bazy danych SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
- Aby uzyskać konfigurację sieci dla wystąpienia zarządzanego, zobacz [konfiguracja sieci dla wystąpień zarządzanych](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Następne kroki

Poznaj wszystkie [możliwości bazy danych SQL](sql-database-technical-overview.md).
