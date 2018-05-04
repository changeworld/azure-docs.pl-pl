---
title: Zbieranie Linux wydajność aplikacji w OMS Log Analytics | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera szczegółowe informacje dotyczące konfigurowania agenta pakietu OMS dla systemu Linux można zebrać liczników wydajności dla MySQL i Apache HTTP Server.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: bfb9b62a8965fa1f7daf62d814665ca23491cc04
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="collect-performance-counters-for-linux-applications-in-log-analytics"></a>Zbieraj liczniki wydajności dla aplikacji systemu Linux w analizy dzienników 
Ten artykuł zawiera szczegółowe informacje dotyczące konfigurowania [Agent pakietu OMS Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) można zebrać liczników wydajności dla określonych aplikacji.  Aplikacje zawarte w tym artykule są:  

- [MySQL](#MySQL)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Jeśli serwer MySQL lub MariaDB serwer zostanie wykryta na komputerze jest zainstalowany agent pakietu OMS, zostanie automatycznie zainstalowana dostawcy dla serwera MySQL monitorowania wydajności. Ten dostawca nawiązuje połączenie z lokalnym serwerem MySQL/MariaDB do udostępnienia statystyki. Poświadczenia użytkownika MySQL musi być skonfigurowany tak, aby dostawca dostęp do serwera MySQL.

### <a name="configure-mysql-credentials"></a>Skonfiguruj poświadczenia MySQL
Dostawca MySQL OMI wymaga wstępnie skonfigurowane użytkownika MySQL i zainstalowania bibliotek klienta MySQL wydajności oraz informacje o kondycji z wystąpienia MySQL zapytania.  Te poświadczenia są przechowywane w pliku uwierzytelniania, który jest przechowywany na agenta systemu Linux.  Plik uwierzytelniania określa, jakie bind adres i port wystąpienia MySQL nasłuchuje na i jakie poświadczenia, które będzie używane do zbierania metryk.  

Podczas instalacji agenta pakietu OMS Linux MySQL OMI dostawca skanowania MySQL my.cnf pliki konfiguracji (lokalizacje domyślne) dla wiązania adres i port i plik authentication częściowo zestawu MySQL OMI.

Plik authentication MySQL jest przechowywany w `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Format pliku uwierzytelniania
Poniżej przedstawiono format pliku uwierzytelniania MySQL OMI

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Wpisy w pliku uwierzytelniania są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--|:--|
| Port | Reprezentuje bieżący port, który nasłuchuje wystąpienie MySQL. Port 0 określa, że następujące właściwości dla domyślnego wystąpienia. |
| Adres BIND| Bieżący bind MySQL — adres. |
| nazwa użytkownika| Aby monitorować wystąpienia serwera MySQL używany użytkownika MySQL. |
| Hasło kodowane w formacie Base64| Hasło użytkownika monitorowania MySQL zakodowane w formacie Base64. |
| AutoUpdate| Określa, czy Skanuj ponownie zmiany w pliku my.cnf i nadpisać plik MySQL OMI Authentication, po uaktualnieniu dostawcy OMI MySQL. |

### <a name="default-instance"></a>Domyślne wystąpienie
Plik authentication MySQL OMI można określić domyślnego wystąpienia oraz numer portu do wielu wystąpień MySQL na jednym hoście Linux łatwiejsze zarządzanie.  Domyślne wystąpienie jest wskazywane przez wystąpienie o portu 0. Wszystkie dodatkowe wystąpienia będzie dziedziczyć właściwości ustawić domyślnego wystąpienia, chyba że określają różne wartości. Na przykład jeśli wystąpienie MySQL nasłuchiwanie na porcie "3308" zostanie dodany, adres powiązania domyślnego wystąpienia, username i password kodowany w standardzie Base64 będzie służyć monitorowanie wystąpienia nasłuchiwanie 3308 i spróbuj. Jeśli wystąpienie na 3308 jest powiązany z innym adresem i korzysta z tej samej pary nazwa użytkownika i hasło MySQL tylko adres bind jest niezbędny, a dziedziczone przez inne właściwości.

Poniższa tabela zawiera przykład wystąpienia ustawienia 

| Opis | Plik |
|:--|:--|
| Domyślne wystąpienie i wystąpienia z portem 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Domyślne wystąpienie i wystąpienia z portu 3308 i innej nazwy użytkownika i hasła. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Uwierzytelnianie programu MySQL OMI
Dołączone do instalacji dostawcy MySQL OMI jest program pliku uwierzytelniania MySQL OMI, która może służyć do edycji plik MySQL OMI Authentication. Uwierzytelnianie programu plików można znaleźć w następującej lokalizacji.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Musi być do odczytu przez konto omsagent pliku poświadczeń. Zalecane jest uruchomienie polecenia mycimprovauth jako omsgent.

Poniższa tabela zawiera szczegółowe informacje o składni dla przy użyciu mycimprovauth.

| Operacja | Przykład | Opis
|:--|:--|:--|
| AutoUpdate *false lub wartość true* | mycimprovauth autoupdate false | Ustawia, czy plik authentication zostanie automatycznie zaktualizowana na ponowne uruchomienie lub zaktualizować. |
| domyślne *powiązanego adresu nazwa_użytkownika hasło* | pwd głównych domyślnego 127.0.0.1 mycimprovauth | Ustawia domyślnego wystąpienia MySQL OMI plik uwierzytelniania.<br>Pole hasła powinny być wprowadzane w formacie zwykłego tekstu — hasło w pliku authentication MySQL OMI będzie zakodowanych w Base 64. |
| Usuń *domyślnej lub numer_portu* | mycimprovauth 3308 | Usuwa określone wystąpienie albo domyślnie lub numer portu. |
| pomoc | mycimprov pomocy | Drukuje listę poleceń do użycia. |
| Drukuj | mycimprov drukowania | Drukuje łatwo odczytać pliku uwierzytelniania MySQL OMI. |
| Zaktualizuj numer_portu *powiązanego adresu nazwa_użytkownika hasło* | mycimprov aktualizacji 3307 127.0.0.1 głównego pwd | Aktualizuje określone wystąpienie lub dodaje wystąpienie, jeśli nie istnieje. |

Poniższe przykładowe polecenia zdefiniować domyślne konto użytkownika serwera MySQL localhost.  Pole hasła powinny być wprowadzane w formacie zwykłego tekstu — hasło w pliku authentication MySQL OMI będzie zakodowanych w Base 64

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Uprawnienia bazy danych wymagane dla liczników wydajności MySQL
Użytkownika MySQL wymaga dostępu do następujących zapytań, aby zbierać dane dotyczące wydajności serwera MySQL. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


Użytkownik MySQL wymaga również wybierz dostęp do poniższych tabelach domyślne.

- information_schema
- mysql. 

Te uprawnienia można otrzymać, uruchamiając następujące polecenia grant.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Aby udzielić uprawnień MySQL monitorowania użytkownik udzielającym użytkownika musi mieć uprawnienie "GRANT option", a także udzielenia uprawnienia.

### <a name="define-performance-counters"></a>Zdefiniuj liczniki wydajności

Po skonfigurowaniu Agent pakietu OMS dla systemu Linux w celu wysyłania danych do analizy dzienników, należy skonfigurować liczniki wydajności do.  Użyj procedury [systemu Windows i Linux źródła danych wydajności w analizy dzienników](log-analytics-data-sources-windows-events.md) liczników w poniższej tabeli.

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Baza danych MySQL | Miejsce na dysku w bajtach |
| Baza danych MySQL | Tabele |
| Serwer MySQL | Przerwane połączenie protokołu Pct |
| Serwer MySQL | Procent użycia połączenia |
| Serwer MySQL | Użycie miejsca na dysku w bajtach |
| Serwer MySQL | Tabela pełne skanowanie Pct |
| Serwer MySQL | Pula buforów InnoDB trafień Pct |
| Serwer MySQL | Procent użycia pula buforu InnoDB |
| Serwer MySQL | Procent użycia pula buforu InnoDB |
| Serwer MySQL | Protokół Pct trafień pamięci podręcznej klucza |
| Serwer MySQL | Procent użycia klucza pamięci podręcznej |
| Serwer MySQL | Protokół Pct zapisu klucza pamięci podręcznej |
| Serwer MySQL | Procent trafień pamięci podręcznej zapytania |
| Serwer MySQL | Protokół Pct śliwek pamięci podręcznej zapytania |
| Serwer MySQL | Procent użycia pamięci podręcznej zapytania |
| Serwer MySQL | Procent trafień w pamięci podręcznej tabeli |
| Serwer MySQL | Procent użycia pamięci podręcznej tabeli |
| Serwer MySQL | Protokół Pct rywalizacji blokady tabeli |

## <a name="apache-http-server"></a>Apache HTTP Server 
W przypadku wykrycia Apache HTTP Server na komputerze jest zainstalowany pakiet omsagent, zostanie automatycznie zainstalowana dostawcy dla Apache HTTP Server monitorowania wydajności. Ten dostawca zależy od modułu Apache, które muszą zostać załadowane do Apache HTTP Server w celu uzyskania dostępu do danych dotyczących wydajności. Moduł mogą być ładowane przy użyciu następującego polecenia:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Aby zwolnić modułu monitorowania Apache, uruchom następujące polecenie:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Zdefiniuj liczniki wydajności

Po skonfigurowaniu Agent pakietu OMS dla systemu Linux w celu wysyłania danych do analizy dzienników, należy skonfigurować liczniki wydajności do.  Użyj procedury [systemu Windows i Linux źródła danych wydajności w analizy dzienników](log-analytics-data-sources-windows-events.md) liczników w poniższej tabeli.

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Apache HTTP Server | Zajętych pracowników |
| Apache HTTP Server | Bezczynnych pracowników |
| Apache HTTP Server | Protokół PCT zajętych pracowników. |
| Apache HTTP Server | Łączna liczba Pct Procesora |
| Apache Host wirtualny | Błędów na minutę - klienta |
| Apache Host wirtualny | Błędów na minutę - serwer |
| Apache Host wirtualny | KB na żądanie |
| Apache Host wirtualny | Żądania KB na sekundę |
| Apache Host wirtualny | Liczba żądań na sekundę |



## <a name="next-steps"></a>Kolejne kroki
* [Zebrać liczników wydajności](log-analytics-data-sources-performance-counters.md) z agentów systemu Linux.
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) analizować dane zebrane ze źródeł danych i rozwiązania. 
