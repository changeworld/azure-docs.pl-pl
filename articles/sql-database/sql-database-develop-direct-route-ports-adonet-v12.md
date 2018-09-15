---
title: Porty inne niż 1433 dla usługi SQL Database | Dokumentacja firmy Microsoft
description: Połączenia klientów z platformy ADO.NET do usługi Azure SQL Database można pominąć serwer proxy i wchodzić w interakcje bezpośrednio z bazą danych przy użyciu porty inne niż 1433.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: e01de4e25285bfac533ae35380b4264fd422906b
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631807"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Porty inne niż 1433 dla platformy ADO.NET 4.5
W tym temacie opisano zachowanie połączenia usługi Azure SQL Database dla klientów korzystających z platformy ADO.NET 4.5 lub nowszej wersji. 

> [!IMPORTANT]
> Aby uzyskać informacje o architekturze łączności, zobacz [architektura łączności usługi Azure SQL Database](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Poza programem vs wewnątrz
Dla połączeń z usługi Azure SQL Database, należy najpierw poprosimy czy jest uruchamiany program kliencki *poza* lub *wewnątrz* granic chmury Azure. Podsekcje omówiono dwa typowe scenariusze.

#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Poza:* kliencki jest uruchomiony na komputerze stacjonarnym
Port 1433 jest tylko port, który musi być otwarty na Twoim komputerze stacjonarnym, który hostuje aplikację kliencką bazy danych SQL.

#### <a name="inside-client-runs-on-azure"></a>*Wewnątrz:* klienta działa na platformie Azure
Po uruchomieniu klienta wewnątrz granic chmury Azure używa co możemy wywołać *Generowanie trasy bezpośredniej* do interakcji z serwerem usługi SQL Database. Po nawiązaniu połączenia dalszej interakcji między klientem i bazy danych obejmują brak bramy bazy danych SQL Azure.

Sekwencja jest w następujący sposób:

1. ADO.NET 4.5 (lub późniejszy) inicjuje krótki interakcji z chmury platformy Azure i odbiera numeru portu dynamicznego zidentyfikowane.
   
   * Numer portu dynamicznego wskazany jest z zakresu od 11000 11999 lub 14000 14999.
2. ADO.NET następnie łączy się z serwerem usługi SQL Database bezpośrednio, za pomocą nie oprogramowanie pośredniczące między.
3. Zapytania są wysyłane bezpośrednio do bazy danych, a wyniki są zwracane bezpośrednio do klienta.

Upewnij się, że port, który 11000 11999 i 14000-14999 na komputerze klienta usługi Azure pozostaną dostępne dla platformy ADO.NET 4.5 interakcji klienta z bazy danych SQL.

* W szczególności portów z zakresu nie może zawierać innych blokują ruchu wychodzącego.
* Na maszynie Wirtualnej platformy Azure **Zapora Windows z zabezpieczeniami zaawansowanymi** kontroluje ustawienia portu.
  
  * Możesz użyć [interfejsu użytkownika zapory](http://msdn.microsoft.com/library/cc646023.aspx) można dodać reguły, dla którego należy określić **TCP** , takich jak protokół wraz z zakresu portów, przy użyciu składni **11000 11999**.

## <a name="version-clarifications"></a>Wyjaśnienia w wersji
W tej sekcji wyjaśnia monikerów, które odwołują się do wersji produktu. Zawiera również listę niektórych kwestiach wersje produktów.

#### <a name="adonet"></a>ADO.NET
* ADO.NET 4.0 obsługuje protokół TDS 7.3, ale nie w wersji 7.4.
* ADO.NET 4.5 i nowsze obsługuje protokół TDS w wersji 7.4.

#### <a name="odbc"></a>ODBC
* Program Microsoft SQL Server ODBC 11 lub nowszy

#### <a name="jdbc"></a>JDBC
* Microsoft SQL Server JDBC 4.2 lub nowszy (JDBC 4.0 faktycznie obsługuje TDS w wersji 7.4, ale nie implementuje "przekierowania")


## <a name="related-links"></a>Powiązane linki
* ADO.NET 4.6 został wydany 20 lipca 2015 r. Ogłoszenia na blogu zespołu programu .NET jest dostępna [tutaj](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).
* 15 sierpnia 2012 został wydany platformy ADO.NET 4.5. Ogłoszenia na blogu zespołu programu .NET jest dostępna [tutaj](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx). 
  * Wpis w blogu ADO.NET 4.5.1 jest dostępny [tutaj](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).

* Microsoft® ODBC Driver 17 for SQL Server® — Windows, Linux i macOS https://www.microsoft.com/en-us/download/details.aspx?id=56567

* Nawiązać połączenie z usługi Azure SQL Database V12 via przekierowania https://blogs.msdn.microsoft.com/sqlcat/2016/09/08/connect-to-azure-sql-database-v12-via-redirection/

* [Lista wersji protokołu TDS](http://www.freetds.org/userguide/tdshistory.htm)
* [Omówienie tworzenia bazy danych SQL](sql-database-develop-overview.md)
* [Zapora usługi SQL Database platformy Azure](sql-database-firewall-configure.md)
* [Porady: Konfigurowanie ustawień zapory w bazie danych SQL](sql-database-configure-firewall-settings.md)


