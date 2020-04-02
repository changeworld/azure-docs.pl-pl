---
title: Łączenie się z bazą danych SQL przy użyciu języka C i C++
description: Użyj przykładowego kodu w tym szybkim uruchomieniu, aby utworzyć nowoczesną aplikację z c++ i poparte zaawansowana relacyjnej bazy danych w chmurze za pomocą usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: ed8e5eaa0ff9b58f80473b052aacfb9f01d45055
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529223"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Łączenie się z bazą danych SQL przy użyciu języka C i C++

Ten post jest skierowany do deweloperów języka C i C++, którzy próbują połączyć się z usługą Azure SQL DB. Jest on podzielony na sekcje, dzięki czemu można przejść do sekcji, która najlepiej oddaje twoje zainteresowanie.

## <a name="prerequisites-for-the-cc-tutorial"></a>Wymagania wstępne samouczka C/C++

Upewnij się, że masz:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz zarejestrować się w celu uzyskania [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Należy zainstalować składniki języka C++, aby skompilować i uruchomić ten przykład.
* [Visual Studio Linux Development](https://docs.microsoft.com/cpp/linux/?view=vs-2019). Jeśli tworzysz na Linuksie, należy również zainstalować rozszerzenie Visual Studio Linux.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Usługa Azure SQL Database i SQL Server na maszynach wirtualnych
Usługa Azure SQL jest oparta na programie Microsoft SQL Server i została zaprojektowana w celu zapewnienia wysokiej dostępności, wydajnej i skalowalnej usługi. Korzystanie z platformy SQL Azure za pośrednictwem zastrzeżonej bazy danych działającej lokalnie ma wiele zalet. Dzięki usłudze SQL Azure nie musisz instalować, instalować, obsługiwać ani zarządzać bazą danych, ale tylko zawartością i strukturą bazy danych. Typowe rzeczy, o które się martwimy dzięki bazom danych, takie jak odporność na uszkodzenia i nadmiarowość, są wbudowane.

Platforma Azure ma obecnie dwie opcje hostowania obciążeń serwera SQL: bazę danych SQL azure, bazę danych jako usługę i serwer SQL na maszynach wirtualnych (VM). Firma Microsoft nie będzie uzyskać szczegółowych informacji na temat różnic między tymi dwoma, z wyjątkiem, że baza danych SQL platformy Azure jest najlepszym rozwiązaniem dla nowych aplikacji opartych na chmurze, aby skorzystać z oszczędności kosztów i optymalizacji wydajności, które zapewniają usługi w chmurze. Jeśli rozważasz migrację lub rozszerzenie aplikacji lokalnych do chmury, serwer SQL na maszynie wirtualnej platformy Azure może działać lepiej dla Ciebie. Aby zachować prostotę w tym artykule, utwórzmy bazę danych SQL platformy Azure.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Technologie dostępu do danych: ODBC i OLE DB
Łączenie się z usługą Azure SQL DB nie różni się i obecnie istnieją dwa sposoby łączenia się z bazami danych: ODBC (łączność z otwartą bazą danych) i OLE DB (baza danych łączenia i osadzania obiektów). W ostatnich latach firma Microsoft dostosowała się do [odbc dla natywnego dostępu do danych relacyjnych](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC jest stosunkowo prosty, a także znacznie szybszy niż OLE DB. Jedynym zastrzeżeniem jest to, że ODBC używa starego interfejsu API w stylu C.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>Krok 1: Tworzenie bazy danych SQL platformy Azure
Zobacz [stronę wprowadzenie,](sql-database-single-database-get-started.md) aby dowiedzieć się, jak utworzyć przykładową bazę danych.  Alternatywnie można wykonać ten [krótki dwuminutowy film,](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) aby utworzyć bazę danych SQL platformy Azure przy użyciu witryny Azure portal.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>Krok 2: Pobierz ciąg połączenia
Po zainicjowaniu obsługi administracyjnej bazy danych SQL platformy Azure należy wykonać następujące kroki w celu określenia informacji o połączeniu i dodania adresu IP klienta w celu uzyskania dostępu do zapory.

W [witrynie Azure portal](https://portal.azure.com/)przejdź do ciągu połączenia ODBC bazy danych SQL platformy Azure przy użyciu pokaż parametry połączenia bazy **danych** wymienione jako część sekcji omówienie bazy danych:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Skopiuj zawartość ciągu **ODBC (Zawiera Node.js) [UWIERZYTELNIANIE SQL].** Używamy tego ciągu później, aby połączyć się z naszego interpretera wiersza polecenia C++ ODBC. Ten ciąg zawiera szczegółowe informacje, takie jak sterownik, serwer i inne parametry połączenia bazy danych.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>Krok 3: Dodaj swój adres IP do zapory
Przejdź do sekcji zapory serwera bazy danych i dodaj [adres IP klienta do zapory, wykonując następujące kroki,](sql-database-configure-firewall-settings.md) aby upewnić się, że możemy ustanowić pomyślne połączenie:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

W tym momencie skonfigurowano usługę Azure SQL DB i można połączyć się z kodu języka C++.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>Krok 4: Łączenie się z aplikacji Windows C/C++
Możesz łatwo połączyć się z [bazą danych SQL platformy Azure przy użyciu odbc w systemie Windows przy użyciu tego przykładu,](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) który tworzy za pomocą programu Visual Studio. Przykład implementuje interpreter wiersza polecenia ODBC, który może służyć do łączenia się z naszą bazą danych SQL azure. W tym przykładzie pobrano plik nazwy źródła bazy danych (DSN) jako argument wiersza polecenia lub pełny ciąg połączenia skopiowany wcześniej z witryny Azure Portal. Wyświetl stronę właściwości dla tego projektu i wklej ciąg połączenia jako argument polecenia, jak pokazano poniżej:

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Upewnij się, że podasz odpowiednie szczegóły uwierzytelniania bazy danych jako część tego ciągu połączenia bazy danych.

Uruchom aplikację, aby ją utworzyć. Powinno zostać wyświetlające następujące okno sprawdzania poprawności pomyślnego połączenia. Można nawet uruchomić kilka podstawowych poleceń SQL, takich jak **tworzenie tabeli,** aby sprawdzić poprawność łączności bazy danych:

![Polecenia SQL](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Alternatywnie można utworzyć plik DSN przy użyciu kreatora, który jest uruchamiany, gdy nie podano żadnych argumentów polecenia. Zalecamy wypróbowanie tej opcji. Tego pliku DSN można używać do automatyzacji i ochrony ustawień uwierzytelniania:

![Tworzenie pliku DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Gratulacje! Teraz pomyślnie połączyłeś się z programem Azure SQL przy użyciu języka C++ i ODBC w systemie Windows. Możesz kontynuować czytanie, aby zrobić to samo dla platformy Linux, jak również.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>Krok 5: Łączenie z aplikacji Linux C/C++
W przypadku, gdy nie słyszałeś jeszcze wiadomości, Visual Studio umożliwia teraz tworzenie aplikacji Systemu Linux w języku C++. Możesz przeczytać o tym nowym scenariuszu w [języku Visual C++ dla programu Linux Rozwoju](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) blogu. Aby zbudować dla Linuksa, potrzebujesz zdalnego komputera, na którym działa twoja dystrybucja Linuksa. Jeśli nie masz go dostępnego, możesz go szybko skonfigurować przy użyciu [maszyn wirtualnych platformy Linux Azure.](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

W tym samouczku załóżmy, że masz skonfigurowaną dystrybucję Ubuntu 16.04 Linux. Kroki tutaj powinny również dotyczyć Ubuntu 15.10, Red Hat 6 i Red Hat 7.

W poniższych krokach zainstalowano biblioteki potrzebne do dystrybucji SQL i ODBC:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Uruchom program Visual Studio. W obszarze Opcje > narzędzia -> Cross Platform -> Menedżer połączeń dodaj połączenie do pola Linux:

![Opcje narzędzi](./media/sql-database-develop-cplusplus-simple/tools.png)

Po nawiązaniu połączenia przez SSH utwórz szablon Pusty projekt (Linux):

![Nowy szablon projektu](./media/sql-database-develop-cplusplus-simple/template.png)

Następnie można dodać [nowy plik źródłowy C i zastąpić go tą zawartością](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Za pomocą interfejsów API ODBC SQLAllocHandle, SQLSetConnectAttr i SQLDriverConnect, powinno być możliwe zainicjowanie i ustanowienie połączenia z bazą danych.
Podobnie jak w przykładzie odbc systemu Windows, należy zastąpić wywołanie SQLDriverConnect szczegółami z parametrów ciągu połączenia bazy danych skopiowanych wcześniej z witryny Azure portal.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Ostatnią rzeczą do zrobienia przed kompilacją jest dodanie **odbc** jako zależności biblioteki:

![Dodawanie odbc jako biblioteki wejściowej](./media/sql-database-develop-cplusplus-simple/lib.png)

Aby uruchomić aplikację, uruchom konsolę Linux z menu **debugowania:**

![Konsola Linuksa](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Jeśli połączenie zakończyło się pomyślnie, bieżąca nazwa bazy danych powinna być teraz widoczna w Konsoli systemu Linux:

![Wyjście okna konsoli systemu Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Gratulacje! Pomyślnie ukończono samouczek i możesz teraz połączyć się z bazą danych SQL platformy Azure z języka C++ na platformach Windows i Linux.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>Pobierz kompletne rozwiązanie samouczka języka C/C++
Rozwiązanie GetStarted, które zawiera wszystkie przykłady w tym artykule w usłudze GitHub:

* [Przykład systemu ODBC C++ Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), Pobierz przykład systemu Windows C++ ODBC, aby połączyć się z programem Azure SQL
* [Odbc C++ Linux sample](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), Pobierz przykład Systemu Linux C++ ODBC, aby połączyć się z usługą Azure SQL

## <a name="next-steps"></a>Następne kroki
* Przejrzyj [omówienie tworzenia baz danych SQL](sql-database-develop-overview.md)
* Więcej informacji na temat [odwołania do API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Wzorce projektowe dla wielodostępnych aplikacji SaaS wykorzystujących usługę Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Poznaj wszystkie [możliwości usługi SQL Database](https://azure.microsoft.com/services/sql-database/)

