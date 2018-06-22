---
title: Omówienie tworzenia aplikacji korzystających z usługi SQL Database | Microsoft Docs
description: Informacje o dostępnych bibliotekach łączności i najlepsze praktyki dotyczące aplikacji łączących się z usługą SQL Database.
services: sql-database
author: stevestein
manager: craigg
ms.reviewer: genemi
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: sstein
ms.openlocfilehash: 2194293d23e5db277f2ff7aa207c298533f74571
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308870"
---
# <a name="sql-database-application-development-overview"></a>Omówienie tworzenia aplikacji bazy danych SQL
W tym artykule przedstawiono podstawowe zagadnienia, jakie powinien mieć na uwadze programista podczas pisania kodu nawiązującego połączenie z usługą Azure SQL Database.

> [!TIP]
> Aby uzyskać samouczek omawiający tworzenie serwera i opartej na serwerze zapory, wyświetlanie właściwości serwera, nawiązywanie połączenia przy użyciu programu SQL Server Management Studio, odpytywanie bazy danych master, tworzenie przykładowej i pustej bazy danych, tworzenie zapytań o właściwości bazy danych, nawiązywanie połączenia przy użyciu programu SQL Server Management Studio, a także odpytywanie przykładowej bazy danych, zobacz [Samouczek z wprowadzeniem](sql-database-get-started-portal.md).
>

## <a name="language-and-platform"></a>Język i platforma
Dostępne są przykłady kodu dla różnych języków programowania i platform programistycznych. Linki do przykładów kodu można znaleźć tutaj: 

* Więcej informacji: [biblioteki połączeń dla bazy danych SQL i programu SQL Server](sql-database-libraries.md).

## <a name="tools"></a>Narzędzia 
Można korzystać z narzędzia open source, takie jak [domy](https://github.com/wunderlist/cheetah), [interfejsu wiersza polecenia sql](https://www.npmjs.com/package/sql-cli), [kodzie VS](https://code.visualstudio.com/). Ponadto usługa Azure SQL Database współpracuje z narzędziami firmy Microsoft, takimi jak [Visual Studio](https://www.visualstudio.com/downloads/) i [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).  Większą produktywność możesz osiągnąć, używając portalu zarządzania platformy Azure, programu PowerShell i interfejsów API REST.

## <a name="resource-limitations"></a>Ograniczenia zasobów
Baza danych SQL Azure zarządza zasoby dostępne dla bazy danych przy użyciu dwóch różnych mechanizmów: Zarządzanie zasobami i wymuszania ograniczeń. Aby uzyskać więcej informacji, zobacz:

- [Limity modelu zasobów na podstawie jednostek dtu w warstwie - pojedynczej bazy danych](sql-database-dtu-resource-limits-elastic-pools.md)
- [Limity modelu zasobów na podstawie jednostek dtu w warstwie - pojedynczej bazy danych](sql-database-dtu-resource-limits-elastic-pools.md)
- [limity zasobów na podstawie vCore - pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)
- [limity zasobów na podstawie vCore - pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md)

## <a name="security"></a>Bezpieczeństwo
Usługa Azure SQL Database udostępnia zasoby na potrzeby ograniczania dostępu, ochrony danych i monitorowania działań w bazie danych SQL Database.

* Więcej informacji: [Zabezpieczanie bazy danych SQL](sql-database-security-overview.md).

## <a name="authentication"></a>Authentication
* Usługa Azure SQL Database obsługuje użytkowników i logowania korzystające zarówno z uwierzytelniania programu SQL Server, jak i z [uwierzytelniania za pomocą usługi Azure Active Directory](sql-database-aad-authentication.md).
* Należy określić konkretną bazy danych, zamiast używać domyślnej bazy danych *master*.
* W usłudze SQL Database nie można używać instrukcji **USE mojaNazwaBazyDanych;** języka Transact-SQL w celu przełączenia się na inną bazę danych.
* Więcej informacji: [zabezpieczeń bazy danych SQL: Zarządzanie zabezpieczeniami dostępu i nazwy logowania bazy danych](sql-database-manage-logins.md).

## <a name="resiliency"></a>Odporność
W przypadku wystąpienia przejściowego błędu podczas łączenia się z usługą SQL Database kod powinien ponowić wywołanie.  Zalecamy, aby logika ponawiania korzystała z logiki wycofywania, co pozwoli uniknąć przeciążenia usługi SQL Database z powodu jednoczesnych ponownych prób ze strony wielu klientów.

* Przykłady kodu: Logika ponawiania próby przykłady kodu, które ilustrują temacie Przykłady w języku wybranym w: [biblioteki połączeń dla bazy danych SQL i programu SQL Server](sql-database-libraries.md).
* Więcej informacji: [komunikaty o błędach dla programów klienckich bazy danych SQL](sql-database-develop-error-messages.md).

## <a name="managing-connections"></a>Zarządzanie połączeniami
* W logice połączenia klienta zastąp domyślny limit czasu wartością 30 sekund.  Domyślna wartość 15 sekund jest zbyt mała w przypadku połączeń zależnych od Internetu.
* Jeśli korzystasz z [puli połączeń](http://msdn.microsoft.com/library/8xx3tyca.aspx), pamiętaj o zamknięciu połączenia, gdy tylko Twój program nie korzysta z niego aktywnie i nie przygotowuje się do jego ponownego użycia.

## <a name="network-considerations"></a>Zagadnienia dotyczące sieci
* Upewnij się, że zapora na komputerze hostującym program kliencki zezwala na wychodzącą komunikację TCP na porcie 1433.  Więcej informacji: [skonfigurowanie zapory bazy danych SQL Azure](sql-database-configure-firewall-settings.md).
* Jeśli program kliencki nawiąże połączenie z bazy danych SQL, gdy klient działa na maszynie wirtualnej platformy Azure (VM), należy otworzyć określone zakresy portów na maszynie Wirtualnej. Więcej informacji: [porty inne niż 1433 ADO.NET 4.5 i bazy danych SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
* Czasami połączeń klientów z bazy danych SQL Azure pominąć serwer proxy i bezpośrednią interakcję z bazy danych. Porty inne niż 1433 nabierają znaczenia. Aby uzyskać więcej informacji [architektury połączenia bazy danych SQL Azure](sql-database-connectivity-architecture.md) i [porty inne niż 1433 ADO.NET 4.5 i bazy danych SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="data-sharding-with-elastic-scale"></a>Dzielenia na fragmenty danych o elastycznej skali
Elastyczne skalowanie upraszcza proces skalowania w poziomie (i w). 

* [Wzorce projektowe dla aplikacji SaaS wielodostępne z bazą danych Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* [Routing zależnych danych](sql-database-elastic-scale-data-dependent-routing.md).
* [Rozpoczynanie pracy z Podgląd elastycznego skalowania bazy danych Azure SQL](sql-database-elastic-scale-get-started.md).

## <a name="next-steps"></a>Kolejne kroki
Poznaj wszystkie [możliwości usługi SQL Database](sql-database-technical-overview.md).
