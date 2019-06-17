---
title: Nawiązać połączenie z bazą danych SQL przy użyciu języka C i C++ | Dokumentacja firmy Microsoft
description: Użyj przykładowego kodu w tym przewodniku Szybki start do tworzenia nowoczesnych aplikacji przy użyciu języka C++ i kopię przez zaawansowaną relacyjną bazę danych w chmurze z usługą Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 00a3904bd78f3bb76266c726af28582770b23921
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60724094"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Nawiązać połączenie z bazą danych SQL przy użyciu języka C i C++

Ten wpis jest przeznaczony dla deweloperów C i C++ podejmuje próbę nawiązania połączenia bazy danych SQL Azure. Jego dzieli się na sekcje, możesz przejść do sekcji, która najlepiej przechwytuje zainteresowanie.

## <a name="prerequisites-for-the-cc-tutorial"></a>Wymagania wstępne dla samouczka języka C/C++

Upewnij się, że masz:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu uzyskania [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Program [Visual Studio](https://www.visualstudio.com/downloads/). Należy zainstalować składniki języka C++, aby skompilować i uruchomić ten przykład.
* [Programowanie dla systemu Linux programu Visual Studio](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Jeśli tworzysz w systemie Linux, możesz także zainstalować rozszerzenia programu Visual Studio systemu Linux.

## <a id="AzureSQL"></a>Azure SQL Database i programu SQL Server na maszynach wirtualnych
Usługi Azure SQL jest oparta na programie Microsoft SQL Server i ma na celu zapewnienie wysokiej dostępności, wydajne i skalowalne usługi. Istnieje wiele korzyści wynikające z używania usługi SQL Azure za pośrednictwem zastrzeżonych bazy danych lokalnych. Z usługami SQL Azure nie trzeba zainstalować, skonfigurować, obsługiwania i zarządzania nią, bazy danych, ale tylko zawartości i struktury bazy danych. Typowe czynności, które będziemy zajmować się z bazami danych, takich jak odporność na uszkodzenia i nadmiarowość są wbudowane w.

Platforma Azure ma obecnie dwie opcje do obsługi obciążeń programu SQL server: Usługa Azure SQL database, baza danych jako usługa i programu SQL server na maszynach wirtualnych (VM). Firma Microsoft nie otrzyma szczegółowo opisano różnice między tymi dwoma z tą różnicą, że bazy danych Azure SQL database to najlepsze rozwiązanie dla nowych aplikacji opartych na chmurze skorzystać z oszczędności kosztów i optymalizacji wydajności, które zapewniają chmurze usług. Planując migrację lub rozszerzanie aplikacji lokalnych do chmury, program SQL server na maszynie wirtualnej platformy Azure może działać lepiej dla Ciebie. Dla uproszczenia w tym artykule, Utwórzmy bazę danych Azure SQL database.

## <a id="ODBC"></a>Technologie dostępu do danych: ODBC i OLE DB
Łączenie z usługą Azure SQL DB nie różni się i aktualnie nie istnieją dwa sposoby nawiązywania połączenia z bazami danych: ODBC (Open Database connectivity) i OLE DB (łączenie i osadzanie obiektów bazy danych). W ostatnich latach Microsoft została powiązana z [ODBC uzyskać dostęp do natywnych danych relacyjnych](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC jest stosunkowo proste, a także znacznie szybsze niż OLE DB. W tym miejscu tylko ostrzeżenie jest ODBC starego interfejsu API w stylu języka C.

## <a id="Create"></a>Krok 1.  Tworzenie usługi Azure SQL Database
Zobacz [stronę wprowadzenia](sql-database-single-database-get-started.md) informacje na temat Tworzenie przykładowej bazy danych.  Alternatywnie, możesz skorzystać z tego [krótki film dwie minuty](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) do utworzenia usługi Azure SQL database przy użyciu witryny Azure portal.

## <a id="ConnectionString"></a>Krok 2.  Pobieranie parametrów połączenia
Po zainicjowaniu obsługi usługi Azure SQL database należy wykonać następujące kroki, aby określić informacje o połączeniu i Dodaj adres IP klienta, aby uzyskać dostęp przez zaporę.

W [witryny Azure portal](https://portal.azure.com/), przejdź do usługi Azure SQL database ciąg połączenia ODBC za pomocą **Pokaż parametry połączenia bazy danych** wyświetlany jako część sekcji Przegląd bazy danych:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Skopiuj zawartość **ODBC (obejmuje środowisko Node.js) [uwierzytelniania SQL]** ciągu. Aby połączyć się z naszym interpretera wiersza polecenia języka C++ ODBC możemy użyć tego ciągu później. Ten ciąg zawiera szczegółowe informacje, takie jak sterownik, serwera i inne parametry połączenia bazy danych.

## <a id="Firewall"></a>Krok 3.  Dodaj adres IP zapory
Przejdź do sekcji zapory dla serwera bazy danych i dodać Twojego [adres IP klienta do zapory, wykonując następujące kroki](sql-database-configure-firewall-settings.md) się upewnić, że firma Microsoft może nawiązać połączenia:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

W tym momencie skonfigurowano bazy danych SQL Azure i są gotowe połączyć się z kodu C++.

## <a id="Windows"></a>Krok 4. Nawiązywanie połączenia z aplikacji Windows C/C++
Można łatwo nawiązać połączenie z [bazy danych SQL Azure przy użyciu interfejsu ODBC w Windows, używając w tym przykładzie](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) opiera się z programem Visual Studio. Przykład implementuje ODBC interpretera wiersza polecenia, którego można połączyć się z naszym bazy danych SQL Azure. W tym przykładzie pobiera bazy danych źródłowy plik o nazwie pliku (DSN) jako argument wiersza polecenia albo parametry połączenia pełne, które firma Microsoft skopiowany wcześniej z witryny Azure portal. Wyświetlenie strony właściwości dla tego projektu, a następnie wklej parametry połączenia jako argument polecenia, jak pokazano poniżej:

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Upewnij się, że możesz podać szczegóły dotyczące uwierzytelniania odpowiednie dla bazy danych jako część te parametry połączenia bazy danych.

Uruchom aplikację na tworzenie. Powinny pojawić się następujące okno weryfikowanie pomyślnego połączenia. Możesz nawet uruchamiać niektórych podstawowych poleceń SQL, takich jak **Utwórz tabelę** Aby sprawdzić łączność z bazą danych:

![Polecenia SQL](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Alternatywnie można utworzyć pliku DSN przy użyciu kreatora, który jest uruchamiany, gdy są podane żadne argumenty polecenia. Zaleca się, spróbuj wykonać tę opcję, a także. Możesz użyć tego pliku DSN dotyczące automatyzacji i zabezpieczania ustawienia uwierzytelniania:

![Utwórz plik DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Gratulacje! Teraz pomyślnie podłączono się do usługi Azure SQL przy użyciu języka C++ i ODBC na Windows. Możesz kontynuować odczytywanie to samo dla platformy Linux, jak również.

## <a id="Linux"></a>Krok 5. Nawiązywanie połączenia z aplikacji w języku Linux C/C++
W przypadku, gdy jeszcze nie wiesz jeszcze wiadomości, Visual Studio umożliwia teraz tworzenie aplikacji systemu Linux w języku C++, a także. Informacje o tym nowy scenariusz w [Visual C++ for Linux Development](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) blogu. Aby utworzyć dla systemu Linux, należy komputer zdalny, w którym jest uruchomiona Twoja dystrybucja systemu Linux. Jeśli nie jest jeszcze dostępny, można ustawić jeden szybko przy użyciu [maszyn wirtualnych platformy Azure z systemem Linux](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W tym samouczku Daj nam założono, że dystrybucji Ubuntu 16.04 Linux Konfigurowanie. Opisane w tym miejscu dotyczą również Ubuntu 15.10, Red Hat 6 i Red Hat 7.

Poniższe kroki instalowania bibliotek wymaganych dla SQL i ODBC dla Twojej dystrybucji:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Uruchom program Visual Studio. W obszarze Narzędzia -> Opcje -> wiele Platform -> Menedżer połączeń, Dodaj połączenie do usługi box systemu Linux:

![Opcje narzędzi](./media/sql-database-develop-cplusplus-simple/tools.png)

Po nawiązaniu połączenia za pośrednictwem protokołu SSH, należy utworzyć szablon pusty projekt (Linux):

![Nowy szablon projektu](./media/sql-database-develop-cplusplus-simple/template.png)

Następnie można dodać [nowy plik źródłowy i zastąp go z tą zawartością](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Za pomocą funkcja SQLAllocHandle interfejsów API ODBC, operacja SQLSetConnectAttr i SQLDriverConnect, powinny mieć możliwość inicjowania i nawiązywania połączenia z bazą danych.
Podobnie jak z przykładu Windows ODBC należy Zastąp wywołanie SQLDriverConnect ze szczegółowymi informacjami z bazy danych parametry ciągu połączenia wcześniej skopiowany z witryny Azure portal.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Ostatnią czynnością, jaką zrobić przed kompilacji jest dodanie **odbc** jako zależności biblioteki:

![Dodawanie ODBC jako danych wejściowych biblioteki](./media/sql-database-develop-cplusplus-simple/lib.png)

Aby uruchomić aplikację, Wywołaj konsolę systemu Linux z **debugowania** menu:

![Linux Console](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Połączenie zakończyło się pomyślnie, powinien zostać wyświetlony nazwa bieżącej bazy danych na konsoli systemu Linux:

![Dane wyjściowe z okna konsoli systemu Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Gratulacje! Pomyślnie ukończono samouczek i może teraz połączyć się z bazy danych SQL Azure z C++ na platformach Windows i Linux.

## <a id="GetSolution"></a>Pobieranie kompletnego rozwiązania samouczka języka C/C++
Możesz znaleźć rozwiązania GetStarted, które zawiera wszystkie przykłady w tym artykule w witrynie GitHub:

* [Przykładowe Windows C++ ODBC](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), Pobierz przykładowe Windows C++ ODBC do połączenia z serwerem Azure SQL
* [ODBC C++ Linux przykładowe](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), Pobierz przykładowe Linux C++ ODBC do połączenia z serwerem Azure SQL

## <a name="next-steps"></a>Kolejne kroki
* Przegląd [omówienie tworzenia bazy danych SQL](sql-database-develop-overview.md)
* Więcej informacji na temat [dokumentacja interfejsu API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Wzorce projektowe dla wielodostępnych aplikacji SaaS wykorzystujących usługę Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Poznaj wszystkie [możliwości usługi SQL Database](https://azure.microsoft.com/services/sql-database/)

