---
title: Porty inne niż 1433 dla bazy danych SQL | Dokumentacja firmy Microsoft
description: Połączenia klienta z ADO.NET z bazą danych SQL Azure można pominąć serwer proxy i bezpośrednią interakcję z bazy danych przy użyciu porty inne niż 1433.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: a8c9eef968465ecf9c8a29df471955b89f3585a0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645093"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Porty inne niż 1433 dla ADO.NET 4.5
W tym temacie opisano zachowanie połączenia bazy danych SQL Azure dla klientów używających ADO.NET 4.5 lub nowszej wersji. 

> [!IMPORTANT]
> Informacje o architekturze łączności, zobacz [architektury połączenia bazy danych SQL Azure](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Poza vs wewnątrz
Dla połączeń z bazą danych SQL Azure, musi najpierw poprosimy czy uruchamia program kliencki *poza* lub *wewnątrz* granic chmury Azure. Podpunkty omówiono w nim dwóch typowych scenariuszy.

#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Poza:* klient jest uruchamiany na komputerze stacjonarnym
Port 1433 jest tylko port, który musi być otwarty na komputerze stacjonarnym, hostującego aplikację kliencką bazy danych SQL.

#### <a name="inside-client-runs-on-azure"></a>*Wewnątrz:* klienta działa na platformie Azure
Po uruchomieniu klienta wewnątrz granic chmury Azure używa można zwany *trasy bezpośredniej* do interakcji z serwerem bazy danych SQL. Po nawiązaniu połączenia dalszej interakcji między klientem a bazy danych obejmują brak bramy bazy danych SQL Azure.

Sekwencja jest następujący:

1. ADO.NET 4.5 (lub nowszej) inicjuje krótki interakcji z chmury Azure i odbiera numeru portu dynamicznie zidentyfikowane.
   
   * Numer portu dynamicznie zidentyfikowanych jest w zasięgu 11000 11999 lub 14000 14999.
2. ADO.NET następnie nawiązuje połączenie z serwerem bazy danych SQL bezpośrednio, z nie oprogramowania pośredniczącego między nimi.
3. Zapytania są wysyłane bezpośrednio do bazy danych, a wyniki są zwracane bezpośrednio do klienta.

Upewnij się, że port, który 11000 11999 i 14000-14999 na komputerze klienta usługi Azure pozostają dostępne dla ADO.NET 4.5 interakcji klientów z bazy danych SQL.

* W szczególności porty z zakresu nie może zawierać innych blokują ruchu wychodzącego.
* Na maszynie Wirtualnej Azure **Zapora systemu Windows z zabezpieczeniami zaawansowanymi** kontroluje ustawienia portu.
  
  * Można użyć [interfejsu użytkownika zapory](http://msdn.microsoft.com/library/cc646023.aspx) Aby dodać regułę, dla którego należy określić **TCP** , takich jak protokół wraz z zakresu portów przy użyciu składni **11000 11999**.

## <a name="version-clarifications"></a>Wyjaśnienia wersji
Ta sekcja zawiera wyjaśnienie informujące, krótkie, które odwołują się do wersji produktu. Zawiera także listę par niektóre wersje produktów.

#### <a name="adonet"></a>ADO.NET
* ADO.NET 4.0 obsługuje protokół TDS 7.3, ale nie 7.4.
* ADO.NET 4.5 lub nowszej obsługuje protokół TDS 7.4.

## <a name="related-links"></a>Powiązane linki
* ADO.NET 4.6 został wydany 20 lipca 2015 r. Zawiadomienie blog zespołu .NET jest dostępna [tutaj](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).
* ADO.NET 4.5 został wydany 15 sierpień 2012. Zawiadomienie blog zespołu .NET jest dostępna [tutaj](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  
  * Jest dostępna w blogu o ADO.NET 4.5.1 [tutaj](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).
* [Lista wersji protokołu TDS](http://www.freetds.org/userguide/tdshistory.htm)
* [Omówienie tworzenia bazy danych SQL](sql-database-develop-overview.md)
* [Zapory bazy danych SQL Azure](sql-database-firewall-configure.md)
* [Porady: Konfigurowanie ustawień zapory w bazie danych SQL](sql-database-configure-firewall-settings.md)

