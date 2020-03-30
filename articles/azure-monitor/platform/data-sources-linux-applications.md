---
title: Zbieranie wydajności aplikacji systemu Linux w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Ten artykuł zawiera szczegółowe informacje dotyczące konfigurowania agenta usługi Log Analytics dla systemu Linux do zbierania liczników wydajności dla serwera HTTP MySQL i Apache.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: 2fd148dbb85a4fd60fe63d4fb73128bf92dea1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670563"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Zbieranie liczników wydajności dla aplikacji systemu Linux w usłudze Azure Monitor 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Ten artykuł zawiera szczegółowe informacje dotyczące konfigurowania [agenta usługi Log Analytics dla systemu Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) do zbierania liczników wydajności dla określonych aplikacji w usłudze Azure Monitor.  Aplikacje zawarte w tym artykule są:  

- [Mysql](#mysql)
- [Serwer HTTP Apache](#apache-http-server)

## <a name="mysql"></a>MySQL
Jeśli serwer MySQL lub MariaDB Server zostanie wykryty na komputerze po zainstalowaniu agenta usługi Log Analytics, zostanie automatycznie zainstalowany dostawca monitorowania wydajności serwera MySQL Server. Ten dostawca łączy się z lokalnym serwerem MySQL/MariaDB, aby udostępnić statystyki wydajności. Poświadczenia użytkownika MySQL muszą być skonfigurowane tak, aby dostawca mógł uzyskać dostęp do serwera MySQL.

### <a name="configure-mysql-credentials"></a>Konfigurowanie poświadczeń MySQL
Dostawca OMI MySQL wymaga wstępnie skonfigurowanego użytkownika MySQL i zainstalowanego bibliotek klienta MySQL w celu wykonania kwerendy o wydajności i kondycji informacji z wystąpienia MySQL.  Te poświadczenia są przechowywane w pliku uwierzytelniania, który jest przechowywany w agencie systemu Linux.  Plik uwierzytelniania określa, jaki adres powiązania i port nasłuchuje wystąpienie MySQL i jakie poświadczenia mają być używane do zbierania metryk.  

Podczas instalacji agenta Log Analytics dla Linuksa dostawca OMI MySQL przeskanuje pliki konfiguracyjne MySQL my.cnf (domyślne lokalizacje) w poszukiwaniu adresu wiązania i portu oraz częściowo ustawi plik uwierzytelniania OMI MySQL.

Plik uwierzytelniania MySQL `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`jest przechowywany w pliku .


### <a name="authentication-file-format"></a>Format pliku uwierzytelniania
Poniżej znajduje się format pliku uwierzytelniania OMI MySQL

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Wpisy w pliku uwierzytelniania są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--|:--|
| Port | Reprezentuje bieżący port, na który nasłuchuje wystąpienie MySQL. Port 0 określa, że następujące właściwości są używane dla wystąpienia domyślnego. |
| Adres powiązania| Bieżący adres powiązania MySQL. |
| nazwa użytkownika| Użytkownik MySQL używany do monitorowania wystąpienia serwera MySQL. |
| Hasło zakodowane w base64| Hasło użytkownika monitorującego MySQL zakodowanego w Base64. |
| Autoupdate| Określa, czy należy ponownie wyskanować zmiany w pliku my.cnf i zastąpić plik uwierzytelniania OMI MySQL po uaktualnieniu dostawcy OMI MySQL. |

### <a name="default-instance"></a>Wystąpienie domyślne
Plik uwierzytelniania OMI MySQL może definiować domyślne wystąpienie i numer portu, aby ułatwić zarządzanie wieloma wystąpieniami MySQL na jednym hoście Systemu Linux.  Wystąpienie domyślne jest oznaczane przez wystąpienie z portem 0. Wszystkie dodatkowe wystąpienia będą dziedziczyć właściwości ustawione z wystąpienia domyślnego, chyba że określają różne wartości. Na przykład jeśli zostanie dodane wystąpienie MySQL nasłuchiwanie na porcie "3308", domyślny adres powiązania, nazwa użytkownika i hasło zakodowane w base64 będą używane do monitorowania nasłuchiwania wystąpienia na 3308. Jeśli wystąpienie na 3308 jest powiązany z innym adresem i używa tej samej nazwy użytkownika MySQL i parę haseł tylko adres powiązania jest potrzebne, a inne właściwości zostaną odziedziczone.

W poniższej tabeli przedstawiono przykładowe ustawienia wystąpienia 

| Opis | Plik |
|:--|:--|
| Domyślne wystąpienie i wystąpienie z portem 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Domyślne wystąpienie i wystąpienie z portem 3308 i inną nazwą użytkownika i hasłem. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Program plików uwierzytelniania OMI MySQL
Wraz z instalacją dostawcy OMI MySQL znajduje się program do uwierzytelniania OMI MySQL, który może być używany do edycji pliku uwierzytelniania OMI MySQL. Program plików uwierzytelniania można znaleźć w następującej lokalizacji.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Plik poświadczeń musi być czytelny przez konto omsagent. Zaleca się uruchomienie polecenia mycimprovauth jako omsgent.

Poniższa tabela zawiera szczegółowe informacje na temat składni przy użyciu mycimprovauth.

| Operacja | Przykład | Opis
|:--|:--|:--|
| automatyczne rozlicz y *fałszu lub prawdy* | mycimprovauth autoupdate false | Określa, czy plik uwierzytelniania zostanie automatycznie zaktualizowany po ponownym uruchomieniu lub aktualizacji. |
| domyślne *hasło nazwy użytkownika adresu powiązania* | mycimprovauth domyślnie 127.0.0.1 korzeń pwd | Ustawia domyślne wystąpienie w pliku uwierzytelniania OMI MySQL.<br>Pole hasła należy wprowadzić w postaci zwykłego tekstu - hasło w pliku uwierzytelniania OMI MySQL będzie zakodowane w bazie 64. |
| usuwanie *domyślne lub port_num* | mycimprovauth 3308 | Usuwa określone wystąpienie przez domyślny lub numer portu. |
| Pomoc | mycimprov pomoc | Drukuje listę poleceń do użycia. |
| drukowanie | mycimprov druku | Drukuje łatwy do odczytania plik uwierzytelniania OMI MySQL. |
| aktualizowanie hasła *nazwy użytkownika* port_num adresu powiązania | aktualizacja mycimprov 3307 127.0.0.1 root pwd | Aktualizuje określone wystąpienie lub dodaje wystąpienie, jeśli nie istnieje. |

Poniższe przykładowe polecenia definiują domyślne konto użytkownika serwera MySQL na localhost.  Pole hasła należy wprowadzić w postaci zwykłego tekstu - hasło w pliku uwierzytelniania OMI MySQL będzie zakodowane w bazie 64

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Uprawnienia bazy danych wymagane dla liczników wydajności MySQL
Użytkownik MySQL wymaga dostępu do następujących zapytań do zbierania danych wydajności serwera MySQL. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


Użytkownik MySQL wymaga również dostępu SELECT do następujących tabel domyślnych.

- Information_schema
- Mysql. 

Te uprawnienia mogą być przyznane przez uruchomienie następujących poleceń dotacji.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Aby udzielić uprawnień użytkownikowi monitorującemu MySQL, użytkownik udzielający uprawnień musi mieć uprawnienie "PRZYZNAJ" oraz przyznane uprawnienie.

### <a name="define-performance-counters"></a>Definiowanie liczników wydajności

Po skonfigurowaniu agenta usługi Log Analytics dla systemu Linux do wysyłania danych do usługi Azure Monitor, należy skonfigurować liczniki wydajności do zbierania.  Użyj procedury w [źródłach danych wydajności systemu Windows i Linux w usłudze Azure Monitor](data-sources-performance-counters.md) z licznikami w poniższej tabeli.

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Baza danych MySQL | Miejsce na dysku w bajtach |
| Baza danych MySQL | Tabele |
| Serwer MySQL | Przerwane połączenie pct |
| Serwer MySQL | Użyj połączenia pct |
| Serwer MySQL | Użycie miejsca na dysku w bajtach |
| Serwer MySQL | Skanowanie pełnej tabeli pct |
| Serwer MySQL | InnoDB Bufor Puli Hit Pct |
| Serwer MySQL | Użyj puli buforów InnoDB pct |
| Serwer MySQL | Użyj puli buforów InnoDB pct |
| Serwer MySQL | Trafienie pamięci podręcznej kluczy pct |
| Serwer MySQL | Pamięć podręczna kluczy użyj pct |
| Serwer MySQL | Pct zapisu pamięci podręcznej kluczy |
| Serwer MySQL | Trafienie pamięci podręcznej kwerend pct |
| Serwer MySQL | Pct śliwek pamięci podręcznej kwerend |
| Serwer MySQL | Pamięć podręczna zapytań użyj pct |
| Serwer MySQL | Trafienie pamięci podręcznej tabeli pct |
| Serwer MySQL | Użyj pamięci podręcznej tabeli pct |
| Serwer MySQL | Rywalizacja o blokadę tabeli pct |

## <a name="apache-http-server"></a>Serwer HTTP Apache 
Jeśli serwer APACHE HTTP zostanie wykryty na komputerze po zainstalowaniu pakietu omsagent, zostanie automatycznie zainstalowany dostawca monitorowania wydajności serwera APACHE HTTP. Ten dostawca opiera się na module Apache, który musi zostać załadowany do serwera HTTP Apache w celu uzyskania dostępu do danych wydajności. Moduł można załadować za pomocą następującego polecenia:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Aby zwolnić moduł monitorowania Apache, uruchom następujące polecenie:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definiowanie liczników wydajności

Po skonfigurowaniu agenta usługi Log Analytics dla systemu Linux do wysyłania danych do usługi Azure Monitor, należy skonfigurować liczniki wydajności do zbierania.  Użyj procedury w [źródłach danych wydajności systemu Windows i Linux w usłudze Azure Monitor](data-sources-performance-counters.md) z licznikami w poniższej tabeli.

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Serwer HTTP Apache | Zapracowani pracownicy |
| Serwer HTTP Apache | Bezczynni pracownicy |
| Serwer HTTP Apache | Pct zapracowanych pracowników |
| Serwer HTTP Apache | Całkowita wartość procesora pct |
| Host wirtualny Apache | Błędy na minutę - Klient |
| Host wirtualny Apache | Błędy na minutę - Serwer |
| Host wirtualny Apache | KB na żądanie |
| Host wirtualny Apache | Żądania KB na sekundę |
| Host wirtualny Apache | Żądania na sekundę |



## <a name="next-steps"></a>Następne kroki
* [Zbieraj liczniki wydajności](data-sources-performance-counters.md) od agentów systemu Linux.
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md) do analizowania danych zebranych ze źródeł danych i rozwiązań. 
