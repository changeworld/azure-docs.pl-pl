---
title: Połącz z bazą danych SQL za pomocą C i C++ | Dokumentacja firmy Microsoft
description: Użyj przykładowy kod w tym szybki start, aby skompilować modern aplikacji z C++ i kopii zapasowej przez zaawansowanych relacyjnej bazy danych w chmurze z bazy danych SQL Azure.
services: sql-database
author: edmacauley
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.devlang: cpp
ms.topic: article
ms.date: 04/01/2018
ms.author: edmacauley
ms.openlocfilehash: 49cbed06d92843fcf0c444e7ef2bb42df2addf7e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Połącz z bazą danych SQL za pomocą C i C++
Ten wpis jest przeznaczony dla deweloperów C i C++ podejmuje próbę nawiązania połączenia z bazą danych SQL Azure. Podzielona jest na sekcje, można przejść do sekcji, która najlepiej przechwytuje zainteresowanie. 

## <a name="prerequisites-for-the-cc-tutorial"></a>Wymagania wstępne dotyczące samouczka C/C++
Upewnij się, że masz:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu uzyskania [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Program [Visual Studio](https://www.visualstudio.com/downloads/). Należy zainstalować składniki języka C++, aby skompilować i uruchomić ten przykład.
* [Programowanie Linux programu Visual Studio](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Jeśli tworzysz w systemie Linux, należy zainstalować rozszerzenie programu Visual Studio systemu Linux. 

## <a id="AzureSQL"></a>Azure SQL Database i programu SQL Server na maszynach wirtualnych
Azure SQL jest oparty na programie Microsoft SQL Server i ma na celu zapewnienie wysokiej dostępności, wydajności i skalowalności usługi. Istnieje wiele korzyści wynikające z używania usługi SQL Azure za pośrednictwem zastrzeżonych uruchomiony lokalnie bazy danych. Z usługami SQL Azure nie trzeba zainstalować, skonfigurować, obsługa lub zarządzać bazy danych, ale tylko zawartości i struktury bazy danych. Typowe rzeczy, które możemy martwić w przypadku baz danych, takich jak odporność na uszkodzenia i nadmiarowość są wbudowane w. 

Platforma Azure ma obecnie dwie opcje do obsługi obciążeń serwera SQL: baza danych Azure SQL, bazy danych jako usługę i programu SQL server na maszynach wirtualnych (VM). Firma Microsoft nie otrzyma do szczegółów na temat różnic między tymi dwiema z tą różnicą, że baza danych Azure SQL najlepsze rozwiązanie dla nowych aplikacji opartej na chmurze skorzystać z oszczędności kosztów i optymalizacji wydajności usługi w chmurze zapewnia. Planując migracja lub rozszerzanie aplikacji lokalnych do chmury, programu SQL server na maszynie wirtualnej Azure może działać lepiej dla Ciebie. Aby zachować rzeczy proste tego artykułu, Utwórzmy bazy danych Azure SQL. 

## <a id="ODBC"></a>Technologie dostępu do danych: ODBC i OLE DB
Nie różni się nawiązywania połączenia z bazą danych SQL Azure i obecnie istnieją dwa sposoby podłączenia do baz danych: ODBC (ODBC) i OLE DB (łączenie i osadzanie obiektów bazy danych). W ostatnich latach ma wyrównane Microsoft [ODBC dla dostępu do natywnych danych relacyjnych](https://blogs.msdn.microsoft.com/sqlnativeclient/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC jest stosunkowo proste, a także znacznie szybsze niż OLE DB. Tylko ostrzeżenie w tym miejscu jest, że ODBC używać starego interfejsu API w stylu języka C. 

## <a id="Create"></a>Krok 1: Tworzenie bazy danych Azure SQL
Zobacz [wprowadzenie strony](sql-database-get-started-portal.md) informacje na temat tworzenia przykładowej bazy danych.  Alternatywnie, można to wykonać [krótki film dwie minuty](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) utworzyć bazę danych Azure SQL przy użyciu portalu Azure.

## <a id="ConnectionString"></a>Krok 2: Uzyskanie parametrów połączenia
Po zainicjowano bazy danych Azure SQL, należy wykonać następujące kroki, aby określić informacje dotyczące połączenia i dodać IP klienta dla dostępu przez zaporę. 

W [portalu Azure](https://portal.azure.com/), przejdź do bazy danych Azure SQL ciąg połączenia ODBC przy użyciu **Pokaż parametry połączenia bazy danych** wyświetlane jako część sekcji Przegląd bazy danych: 

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Skopiuj zawartość **ODBC (obejmuje Node.js) [uwierzytelnianie SQL]** ciągu. Możemy użyć tego ciągu później nawiązać połączenia z naszych interpreter wiersza polecenia języka C++ ODBC. Ten ciąg zawiera szczegółowe informacje, takie jak sterownik, serwera i inne parametry połączenia bazy danych. 

## <a id="Firewall"></a>Krok 3: Dodawanie IP zapory
Przejdź do sekcji zapory dla serwera bazy danych i dodać Twojego [IP klienta do zapory przy użyciu tych kroków](sql-database-configure-firewall-settings.md) się upewnić, że firma Microsoft może nawiązać połączenia: 

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

W tym momencie zostały skonfigurowane w bazie danych SQL Azure i są gotowe do połączenia w kodzie C++. 

## <a id="Windows"></a>Krok 4: Łączenie się z aplikacji systemu Windows C/C++
Możesz łatwo nawiązać połączenie z [bazy danych SQL Azure za pośrednictwem sterownika ODBC w systemie Windows za pomocą tego przykładu](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) który kompiluje się z programem Visual Studio. Przykład implementuje ODBC interpreter wiersza polecenia, który może służyć do nawiązania połączenia naszych bazy danych SQL Azure. W tym przykładzie przyjmuje albo bazy danych źródłowy plik o nazwie pliku (DSN) jako argumentu wiersza polecenia lub parametry połączenia pełne, które firma Microsoft wcześniej skopiowany z portalu Azure. Przywołać stronę właściwości dla tego projektu i Wklej parametry połączenia jako argumentu, jak pokazano poniżej: 

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Upewnij się, że Podaj szczegóły prawo uwierzytelniania bazy danych jako część tego ciągu połączenia bazy danych. 

Uruchom aplikację, aby go skompilować. Powinny zostać wyświetlone następujące okno weryfikowania pomyślnego nawiązania połączenia. Możesz nawet uruchamiać niektórych podstawowych poleceń SQL, takich jak **Utwórz tabelę** Aby sprawdzić łączność z bazą danych:

![Polecenia SQL](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Alternatywnie można utworzyć pliku DSN za pomocą kreatora, który jest uruchamiany, jeśli zostały podane nie argumenty polecenia. Zaleca się, spróbuj wykonać tę opcję, a także. Można użyć tego pliku DSN do automatyzacji i ochrona ustawienia uwierzytelniania: 

![Tworzenie pliku DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Gratulacje! Teraz pomyślnie nawiązano połączenie SQL Azure za pomocą języka C++ i ODBC w systemie Windows. Można kontynuować odczyt wykonaj te same również platformy Linux. 

## <a id="Linux"></a>Krok 5: Łączenie z aplikacji Linux C/C++
W przypadku, gdy jeszcze nie zostało to jeszcze znany wiadomości, Visual Studio umożliwia teraz tworzenie aplikacji C++ Linux, a także. Informacje o tym nowy scenariusz, w [Visual C++ dla systemu Linux programowanie](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/) blogu. Aby utworzyć dla systemu Linux, należy komputer zdalny, którym jest uruchomiona distro Twojego systemu Linux. Jeśli nie masz dostępne, można ustawić jeden szybko przy użyciu [maszyn wirtualnych Azure Linux](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

W tym samouczku Załóżmy, że masz dystrybucji Ubuntu 16.04 Linux Konfigurowanie. Kroki opisane w tym miejscu należy również zastosować Ubuntu 15.10, Red Hat 6 i Red Hat 7. 

Poniższe kroki należy zainstalować bibliotek wymaganych dla programu SQL i ODBC dla Twojej distro:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Uruchom program Visual Studio. W obszarze Narzędzia -> Opcje -> Cross Platform -> Menedżera połączeń, Dodaj połączenie do pola z systemem Linux: 

![Opcje narzędzia](./media/sql-database-develop-cplusplus-simple/tools.png)

Po nawiązaniu połączenia za pomocą protokołu SSH, należy utworzyć pusty (Linux) szablon projektu: 

![Nowy szablon projektu](./media/sql-database-develop-cplusplus-simple/template.png)

Następnie można dodać [C nowy plik źródłowy i zastąp go z tą zawartością](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Przy użyciu funkcja ODBC API SQLAllocHandle, operacja SQLSetConnectAttr i SQLDriverConnect, powinien mieć możliwość inicjowania i nawiązania połączenia z bazą danych. Podobnie jak próbki ODBC systemu Windows należy zastąpić wywołanie SQLDriverConnect szczegóły z wcześniej skopiowany z portalu Azure parametry ciągu połączenia bazy danych. 

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Ostatnim etapem przed kompilacji jest dodanie **odbc** jako zależności biblioteki: 

![Dodawanie jako biblioteki wejściowej ODBC](./media/sql-database-develop-cplusplus-simple/lib.png)

Aby uruchomić aplikację, wyświetlić konsoli systemu Linux z **debugowania** menu: 

![Linux Console](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Jeśli połączenie zakończyło się pomyślnie, powinien zostać wyświetlony nazwa bieżącej bazy danych na konsoli systemu Linux: 

![Dane wyjściowe z okna konsoli systemu Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Gratulacje! Pomyślnie ukończono samouczka i można teraz nawiązać połączenie z bazy danych SQL Azure z C++ na platformach systemu Windows i Linux.

## <a id="GetSolution"></a>Pobieranie kompletnego rozwiązania samouczka C/C++
Można znaleźć rozwiązania GetStarted, które zawiera wszystkie przykłady w tym artykule w witrynie github:

* [Przykładowe Windows w języku C++ ODBC](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), Pobierz Windows C++ ODBC próby połączenia z serwerem Azure SQL
* [Przykładowe ODBC C++ Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), Pobierz Linux C++ ODBC próby połączenia z serwerem Azure SQL

## <a name="next-steps"></a>Kolejne kroki
* Przegląd [omówienie tworzenia bazy danych SQL](sql-database-develop-overview.md)
* Więcej informacji na temat [dokumentacja interfejsu API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Wzorce projektowe dla wielodostępnych aplikacji SaaS wykorzystujących usługę Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Poznaj wszystkie [możliwości usługi SQL Database](https://azure.microsoft.com/services/sql-database/)

