---
title: Omówienie tworzenia aplikacji korzystających z usługi SQL Database | Microsoft Docs
description: Informacje o dostępnych bibliotekach łączności i najlepsze praktyki dotyczące aplikacji łączących się z usługą SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 06/20/2018
ms.openlocfilehash: 58f902edcd417809d1bb47a231cb1c2ac2f579d1
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063595"
---
# <a name="sql-database-application-development-overview"></a>Omówienie tworzenia aplikacji bazy danych SQL
W tym artykule przedstawiono podstawowe zagadnienia, jakie powinien mieć na uwadze programista podczas pisania kodu nawiązującego połączenie z usługą Azure SQL Database.

> [!TIP]
> Aby uzyskać samouczek omawiający tworzenie serwera i opartej na serwerze zapory, wyświetlanie właściwości serwera, nawiązywanie połączenia przy użyciu programu SQL Server Management Studio, odpytywanie bazy danych master, tworzenie przykładowej i pustej bazy danych, tworzenie zapytań o właściwości bazy danych, nawiązywanie połączenia przy użyciu programu SQL Server Management Studio, a także odpytywanie przykładowej bazy danych, zobacz [Samouczek z wprowadzeniem](sql-database-get-started-portal.md).
>

## <a name="language-and-platform"></a>Język i platforma
Dostępne są przykłady kodu dla różnych języków programowania i platform programistycznych. Linki do przykładów kodu można znaleźć tutaj: 

* Więcej informacji: [biblioteki połączeń dla usługi SQL Database i programu SQL Server](sql-database-libraries.md).

## <a name="tools"></a>Narzędzia 
Można korzystać z narzędzi open source, takich jak [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [programu VS Code](https://code.visualstudio.com/). Ponadto usługa Azure SQL Database współpracuje z narzędziami firmy Microsoft, takimi jak [Visual Studio](https://www.visualstudio.com/downloads/) i [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).  Większą produktywność możesz osiągnąć, używając portalu zarządzania platformy Azure, programu PowerShell i interfejsów API REST.

## <a name="resource-limitations"></a>Ograniczenia zasobów
Usługa Azure SQL Database zarządza zasobami dostępnymi z bazą danych przy użyciu dwóch różnych mechanizmów: nadzoru nad zasobami i wymuszania limitów. Aby uzyskać więcej informacji, zobacz:

- [Zasób oparty na jednostkach DTU modelu limity — Pojedyncza baza danych](sql-database-dtu-resource-limits-single-databases.md)
- [Zasób oparty na jednostkach DTU modelu limity - pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)
- [limity zasobów opartych na rdzeniach wirtualnych - pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)
- [limity zasobów opartych na rdzeniach wirtualnych - pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md)

## <a name="security"></a>Bezpieczeństwo
Usługa Azure SQL Database udostępnia zasoby na potrzeby ograniczania dostępu, ochrony danych i monitorowania działań w bazie danych SQL Database.

* Więcej informacji: [Zabezpieczanie bazy danych SQL](sql-database-security-overview.md).

## <a name="authentication"></a>Authentication
* Usługa Azure SQL Database obsługuje użytkowników i logowania korzystające zarówno z uwierzytelniania programu SQL Server, jak i z [uwierzytelniania za pomocą usługi Azure Active Directory](sql-database-aad-authentication.md).
* Należy określić konkretną bazy danych, zamiast używać domyślnej bazy danych *master*.
* W usłudze SQL Database nie można używać instrukcji **USE mojaNazwaBazyDanych;** języka Transact-SQL w celu przełączenia się na inną bazę danych.
* Więcej informacji: [zabezpieczeń bazy danych SQL Database: Zarządzanie zabezpieczeniami dostępu i logowania w bazie danych](sql-database-manage-logins.md).

## <a name="resiliency"></a>Odporność
W przypadku wystąpienia przejściowego błędu podczas łączenia się z usługą SQL Database kod powinien ponowić wywołanie.  Zalecamy, aby logika ponawiania korzystała z logiki wycofywania, co pozwoli uniknąć przeciążenia usługi SQL Database z powodu jednoczesnych ponownych prób ze strony wielu klientów.

* Przykłady kodu: Aby uzyskać przykłady kodu ilustrujące logikę ponawiania, zobacz przykłady w języku wybranym na: [biblioteki połączeń dla usługi SQL Database i programu SQL Server](sql-database-libraries.md).
* Więcej informacji: [komunikaty o błędach dotyczących programów klienckich usługi SQL Database](sql-database-develop-error-messages.md).

## <a name="managing-connections"></a>Zarządzanie połączeniami
* W logice połączenia klienta zastąp domyślny limit czasu wartością 30 sekund.  Domyślna wartość 15 sekund jest zbyt mała w przypadku połączeń zależnych od Internetu.
* Jeśli korzystasz z [puli połączeń](http://msdn.microsoft.com/library/8xx3tyca.aspx), pamiętaj o zamknięciu połączenia, gdy tylko Twój program nie korzysta z niego aktywnie i nie przygotowuje się do jego ponownego użycia.

## <a name="network-considerations"></a>Zagadnienia dotyczące sieci
* Upewnij się, że zapora na komputerze hostującym program kliencki zezwala na wychodzącą komunikację TCP na porcie 1433.  Więcej informacji: [skonfigurowanie zapory usługi Azure SQL Database](sql-database-configure-firewall-settings.md).
* Jeśli program kliencki łączy z bazą danych SQL, gdy kliencki jest uruchomiony na maszynie wirtualnej (VM) platformy Azure, musisz otworzyć określone zakresy portów na maszynie Wirtualnej. Więcej informacji: [portów wyższych niż 1433 dla platformy ADO.NET 4.5 i usługi SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
* Połączenia klientów z usługi Azure SQL Database czasami pomijają serwer proxy i bezpośrednią interakcję z bazy danych. Porty inne niż 1433 nabierają znaczenia. Aby uzyskać więcej informacji [architektura łączności usługi Azure SQL Database](sql-database-connectivity-architecture.md) i [portów wyższych niż 1433 dla platformy ADO.NET 4.5 i usługi SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="data-sharding-with-elastic-scale"></a>Fragmentowanie danych przy użyciu elastycznej skali
Elastyczne skalowanie upraszcza proces skalowania w poziomie (i w). 

* [Wzorce projektowe dla wielodostępnych aplikacji SaaS usługi Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* [Routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md).
* [Wprowadzenie do usługi Azure SQL Database elastyczne skalowanie w wersji zapoznawczej](sql-database-elastic-scale-get-started.md).

## <a name="next-steps"></a>Kolejne kroki
Poznaj wszystkie [możliwości usługi SQL Database](sql-database-technical-overview.md).
