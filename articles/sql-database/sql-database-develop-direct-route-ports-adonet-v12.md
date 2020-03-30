---
title: Porty poza 1433
description: Połączenia klienta z ADO.NET do usługi Azure SQL Database można pominąć serwera proxy i interakcji bezpośrednio z bazą danych przy użyciu portów innych niż 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 04/03/2019
ms.openlocfilehash: c0012b61cf43d01afd5e7f5f52948310b5eb8420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73828057"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Porty wyższe niż 1433 dla platformy ADO.NET 4.5

W tym temacie opisano zachowanie połączenia usługi Azure SQL Database dla klientów korzystających ADO.NET wersji 4.5 lub nowszej.

> [!IMPORTANT]
> Aby uzyskać informacje na temat architektury łączności, zobacz [Architektura łączności usługi Azure SQL Database](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Na zewnątrz vs wewnątrz

W przypadku połączeń z usługą Azure SQL Database musimy najpierw zapytać, czy program kliencki działa *poza* lub *wewnątrz* granicy chmury platformy Azure. Podsekcje omawiają dwa typowe scenariusze.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Na zewnątrz:* Klient działa na komputerze stacjonarnym

Port 1433 jest jedynym portem, który musi być otwarty na komputerze stacjonarnym, który obsługuje aplikację kliencką bazy danych SQL.

### <a name="inside-client-runs-on-azure"></a>*Wewnątrz:* Klient działa na platformie Azure

Gdy klient działa wewnątrz granicy chmury platformy Azure, używa tego, co możemy wywołać *bezpośrednią trasę* do interakcji z serwerem bazy danych SQL. Po nawiązaniu połączenia dalsze interakcje między klientem a bazą danych nie wymagają bramy bazy danych SQL Azure.

Kolejność jest następująca:

1. ADO.NET 4.5 (lub nowszym) inicjuje krótką interakcję z chmurą platformy Azure i odbiera dynamicznie zidentyfikowany numer portu.

   * Dynamicznie identyfikowany numer portu mieści się w zakresie 11000-11999.
2. ADO.NET następnie łączy się bezpośrednio z serwerem bazy danych SQL, bez oprogramowania pośredniczącego pomiędzy nimi.
3. Zapytania są wysyłane bezpośrednio do bazy danych, a wyniki są zwracane bezpośrednio do klienta.

Upewnij się, że zakresy portów 11000-11999 na komputerze klienckim platformy Azure są dostępne dla ADO.NET interakcji klienta 4.5 z bazą danych SQL.

* W szczególności porty w tym zakresie muszą być wolne od innych blokerów ruchu wychodzącego.
* Na maszynie Wirtualnej platformy Azure **Zapora systemu Windows z zabezpieczeniami zaawansowanymi** steruje ustawieniami portu.
  
  * Za pomocą [interfejsu użytkownika zapory](https://msdn.microsoft.com/library/cc646023.aspx) można dodać regułę, dla której określono protokół **TCP** wraz z zakresem portów o składni, takiej jak **11000-11999**.

## <a name="version-clarifications"></a>Wyjaśnienia dotyczące wersji

W tej sekcji wyjaśniono monikery, które odnoszą się do wersji produktu. Zawiera również listę niektórych par wersji między produktami.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4.0 obsługuje protokół TDS 7.3, ale nie 7.4.
* ADO.NET 4.5 i nowsze obsługuje protokół TDS 7.4.

### <a name="odbc"></a>ODBC

* Program Microsoft SQL Server ODBC 11 lub wyższy

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 lub wyższy (JDBC 4.0 faktycznie obsługuje TDS 7.4, ale nie implementuje "przekierowanie")

## <a name="related-links"></a>Powiązane linki

* ADO.NET 4.6 został wydany 20 lipca 2015 roku. Ogłoszenie blogu zespołu .NET jest dostępne [tutaj](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx).
* ADO.NET 4.5 został wydany 15 sierpnia 2012 roku. Ogłoszenie blogu zespołu .NET jest dostępne [tutaj](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  * Post na blogu o ADO.NET 4.5.1 jest dostępny [tutaj](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx).

* Sterownik Odbc firmy Microsoft® 17 dla programu SQL Server® - Windows, Linux, & macOShttps://www.microsoft.com/download/details.aspx?id=56567

* Łączenie się z usługą Azure SQL Database V12 za pomocą przekierowaniahttps://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Lista wersji protokołu TDS](https://www.freetds.org/userguide/tdshistory.htm)
* [Omówienie tworzenia baz danych SQL](sql-database-develop-overview.md)
* [Zapora bazy danych SQL usługi Azure](sql-database-firewall-configure.md)
* [Instrukcje: konfigurowanie ustawień zapory w usłudze SQL Database](sql-database-configure-firewall-settings.md)


