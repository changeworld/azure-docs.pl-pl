---
title: Zbieraj wydajności aplikacji systemu Linux w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera szczegółowe informacje dotyczące konfigurowania agenta usługi Log Analytics dla systemu Linux można zebrać liczników wydajności for MySQL i Apache HTTP Server.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: ea74440a5c8a9a2584e742ec72ccf888b6bb5ad9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628918"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Zbierz liczniki wydajności dla aplikacji systemu Linux w usłudze Azure Monitor 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Ten artykuł zawiera szczegółowe informacje dotyczące konfigurowania [agenta usługi Log Analytics dla systemu Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) można zebrać liczników wydajności określonych aplikacji do usługi Azure Monitor.  Aplikacje zawarte w tym artykule są następujące:  

- [MySQL](#mysql)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Jeśli serwer MySQL lub MariaDB serwer zostanie wykryte na komputerze po zainstalowaniu agenta usługi Log Analytics, zostanie automatycznie zainstalowana dostawcy dla serwera MySQL monitorowania wydajności. Ten dostawca łączy z lokalnym serwerem MySQL/MariaDB do udostępnienia statystyk wydajności. Poświadczenia użytkownika MySQL musi być skonfigurowany tak, aby dostawca może uzyskać dostępu do serwera MySQL.

### <a name="configure-mysql-credentials"></a>Skonfiguruj poświadczenia MySQL
Dostawca MySQL OMI wymaga wstępnie skonfigurowanej użytkownika programu MySQL i zainstalować biblioteki klienckie MySQL w celu wykonywania zapytań dotyczących wydajności i informacje o kondycji z wystąpienia programu MySQL.  Te poświadczenia są przechowywane w pliku uwierzytelniania, który jest przechowywany na agenta systemu Linux.  Pliku uwierzytelniania określa, jakie powiązania adres i port wystąpienia MySQL nasłuchuje na i jakie poświadczenia na potrzeby gromadzenia metryk.  

Podczas instalacji agenta usługi Log Analytics dla systemu Linux MySQL OMI dostawca skanowania plików konfiguracyjnych my.cnf MySQL (domyślne lokalizacje) dla wiązania adres i port i częściowo Ustaw plik uwierzytelniania MySQL OMI.

MySQL pliku uwierzytelniania jest przechowywany w `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Format pliku uwierzytelniania
Poniżej przedstawiono format pliku uwierzytelniania MySQL OMI

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Wpisy w pliku uwierzytelniania są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--|:--|
| Port | Reprezentuje bieżący port, który nasłuchuje wystąpienie programu MySQL. Port 0 określa, że właściwości po są używane domyślne wystąpienie. |
| Bind-Address| Bieżącego powiązania MySQL-adresu. |
| nazwa użytkownika| Użytkownik programu MySQL używane na potrzeby monitorowania wystąpienia serwera MySQL. |
| Hasło kodowany w formacie Base64| Hasło użytkownika monitorowania MySQL zakodowane w formacie Base64. |
| AutoUpdate| Określa, czy ponownego skanowania dla zmian w pliku my.cnf i nadpisać plik uwierzytelniania OMI MySQL, po uaktualnieniu dostawcy OMI bazy danych MySQL. |

### <a name="default-instance"></a>Wystąpienie domyślne
Pliku uwierzytelniania MySQL OMI można zdefiniować domyślne wystąpienie i numer portu do wielu wystąpień MySQL na jednym hoście systemu Linux, łatwiejsze zarządzanie.  Domyślne wystąpienie jest wskazywane przez wystąpienie o portu 0. Wszystkie dodatkowe wystąpienia będzie dziedziczyć właściwości ustawione z wystąpienia domyślnego, o ile nie mogą określać różne wartości. Na przykład jeśli wystąpienie MySQL nasłuchuje na porcie "3308" zostanie dodany, powiązanie adresu domyślnego wystąpienia, username i password zakodowane w formacie Base64 posłuży monitorowanie wystąpienia nasłuchiwać 3308 i spróbuj. Jeśli wystąpienie na 3308 jest powiązany z innym adresem i korzysta z tej samej pary nazwa użytkownika i hasło MySQL tylko adres powiązania, a inne właściwości będą dziedziczone.

Poniższa tabela zawiera przykładowe wystąpienia ustawienia 

| Opis | Plik |
|:--|:--|
| Domyślne wystąpienie i wystąpienia z portem 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Domyślne wystąpienie i wystąpienia przy użyciu portu 3308 i innej nazwy użytkownika i hasła. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Uwierzytelnianie programu MySQL OMI
Dołączone do instalacji dostawcy MySQL OMI jest programem pliku uwierzytelniania MySQL OMI, który może służyć do edycji pliku uwierzytelniania OMI MySQL. Uwierzytelnianie programu plików można znaleźć w następującej lokalizacji.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Plik poświadczeń musi być odczytany przez konta omsagent. Zaleca się uruchomienie polecenia mycimprovauth jako omsgent.

Poniższa tabela zawiera szczegółowe informacje o składni używania mycimprovauth.

| Operacja | Przykład | Opis
|:--|:--|:--|
| Aktualizacje automatyczne *FAŁSZ lub true* | mycimprovauth autoupdate false | Ustawia informację, czy plik uwierzytelniania zostaną automatycznie zaktualizowane na ponowne uruchomienie lub aktualizacji. |
| domyślne *adres powiązania nazwy użytkownika hasła* | pwd katalogu głównego domyślnego 127.0.0.1 mycimprovauth | Ustawia domyślnego wystąpienia MySQL OMI pliku uwierzytelniania.<br>Pole hasła powinny być wprowadzane w postaci zwykłego tekstu — hasło w pliku uwierzytelniania MySQL OMI będzie zakodowanych w Base 64. |
| Usuń *domyślne lub numer_portu* | mycimprovauth 3308 | Usuwa określone wystąpienie, albo domyślnie lub numer portu. |
| pomoc | mycimprov pomocy | Drukuje listę poleceń do użycia. |
| Drukuj | mycimprov drukowania | Drukuje łatwy do prześledzenia MySQL OMI z pliku uwierzytelniania. |
| Aktualizuj numer_portu *adres powiązania nazwy użytkownika hasła* | mycimprov aktualizacji 3307 127.0.0.1 głównego pwd | Aktualizuje określone wystąpienie lub dodaje wystąpienie, jeśli nie istnieje. |

Następujące przykładowe polecenia zdefiniować domyślne konto użytkownika dla serwera MySQL na hoście lokalnym.  Pole hasła powinny być wprowadzane w postaci zwykłego tekstu — hasło w pliku uwierzytelniania MySQL OMI będzie zakodowanych w Base 64

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Uprawnienia bazy danych wymagane dla MySQL, liczniki wydajności
Użytkownik programu MySQL wymaga dostępu do następujące zapytania, aby zbierać dane dotyczące wydajności serwera MySQL. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


Użytkownika programu MySQL wymaga również dostępu do następujących tabel domyślne.

- information_schema
- mysql. 

Tych uprawnień można udzielić, uruchamiając następujące polecenia grant.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Aby udzielić uprawnień do bazy danych MySQL monitorowania, użytkownik musi udzielać użytkownik musi mieć uprawnienie "GRANT option", a także uprawnień, zostanie im przyznany.

### <a name="define-performance-counters"></a>Zdefiniuj liczników wydajności

Po skonfigurowaniu agenta usługi Log Analytics dla systemu Linux w celu wysyłania danych do usługi Azure Monitor, skonfiguruj zbierane liczniki wydajności.  Procedura [Windows i Linux źródła danych dotyczących wydajności w usłudze Azure Monitor](data-sources-performance-counters.md) liczników w poniższej tabeli.

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Baza danych MySQL | Miejsce na dysku w bajtach |
| Baza danych MySQL | Tabele |
| Serwer MySQL | Protokół Pct przerwane połączenie |
| Serwer MySQL | Procent użycia połączenia |
| Serwer MySQL | Użycie miejsca na dysku w bajtach |
| Serwer MySQL | Protokół Pct skanowania pełną tabelę |
| Serwer MySQL | Pula buforów aparatu InnoDB trafień Pct |
| Serwer MySQL | Protokół Pct Użyj puli buforów aparatu InnoDB |
| Serwer MySQL | Protokół Pct Użyj puli buforów aparatu InnoDB |
| Serwer MySQL | Procent trafień klucza pamięci podręcznej |
| Serwer MySQL | Procent użycia klucza pamięci podręcznej |
| Serwer MySQL | Protokół Pct zapisu klucza pamięci podręcznej |
| Serwer MySQL | Procent trafień pamięci podręcznej zapytań |
| Serwer MySQL | Protokół Pct śliwek pamięci podręcznej zapytań |
| Serwer MySQL | Protokół Pct użycia pamięci podręcznej zapytań |
| Serwer MySQL | Procent trafień w pamięci podręcznej tabeli |
| Serwer MySQL | Procent użycia pamięci podręcznej tabeli |
| Serwer MySQL | Protokół Pct Rywalizacja o blokady tabeli |

## <a name="apache-http-server"></a>Apache HTTP Server 
Jeśli po zainstalowaniu pakietu omsagent, Apache HTTP Server zostanie wykryte na komputerze, dostawca Apache HTTP Server monitorowania wydajności zostanie automatycznie zainstalowana. Ten dostawca opiera się na moduł Apache, który musi być załadowany do Apache HTTP Server w celu uzyskania dostępu do danych dotyczących wydajności. Moduł może być załadowany za pomocą następującego polecenia:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Aby zwolnić modułu monitorowania Apache, uruchom następujące polecenie:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Zdefiniuj liczników wydajności

Po skonfigurowaniu agenta usługi Log Analytics dla systemu Linux w celu wysyłania danych do usługi Azure Monitor, skonfiguruj zbierane liczniki wydajności.  Procedura [Windows i Linux źródła danych dotyczących wydajności w usłudze Azure Monitor](data-sources-performance-counters.md) liczników w poniższej tabeli.

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Apache HTTP Server | Zajętych pracowników |
| Apache HTTP Server | Bezczynnych pracowników |
| Apache HTTP Server | Protokół PCT zajętych pracowników. |
| Apache HTTP Server | Całkowity procent procesora CPU |
| Host wirtualny Apache | Błędów na minutę — klient |
| Host wirtualny Apache | Błędów na minutę — serwer |
| Host wirtualny Apache | KB na żądanie |
| Host wirtualny Apache | Żądania KB na sekundę |
| Host wirtualny Apache | Żądań na sekundę |



## <a name="next-steps"></a>Kolejne kroki
* [Liczniki wydajności są zbierane](data-sources-performance-counters.md) z agentów dla systemu Linux.
* Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań. 