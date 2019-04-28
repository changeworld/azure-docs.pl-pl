---
title: Przeczytaj replik w usłudze Azure Database for MySQL.
description: W tym artykule opisano odczytu replik dla usługi Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 6e33c7571dc735ce9984a0ce1b37275a6c4c7eca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093220"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Odczytu replik w usłudze Azure Database for MySQL

Funkcja odczytu replik pozwala na replikowanie danych z serwera Azure Database for MySQL (master) na maksymalnie pięciu tylko do odczytu serwerów (repliki) w tym samym regionie platformy Azure. Tylko do odczytu replik asynchronicznie są aktualizowane przy użyciu technologii replikacji na podstawie położenia pliku natywnych dziennik binarny (binlog) aparatu MySQL. Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji usługi MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Repliki utworzone w usługa Azure Database for MySQL są nowe serwery, którymi można zarządzać w taki sam sposób jak normalne/autonomicznych serwerów MySQL. W przypadku każdej repliki do odczytu są naliczane opłaty za zaaprowizowane środowisko obliczeniowe (rozliczane w rdzeniach wirtualnych) i zaaprowizowany magazyn (rozliczany w GB/miesiąc).

Aby dowiedzieć się więcej na temat funkcji replikacji MySQL i problemów, zobacz [dokumentacji replikacji MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-read-replicas"></a>Kiedy należy używać odczytu replik

Aplikacje i obciążenia, które są odczytywane intensywnie korzystających z może być obsługiwany przez repliki tylko do odczytu. Odczytu replik zwiększyć ilość dostępnej pojemności odczytu, gdyby pojedynczego serwera należy używać tylko do odczytu i zapisu w porównaniu do. Obciążeniami odczytu można wyizolować w replikach, natomiast obciążenia zapisu może zostać skierowany do poziomu głównego.

Typowy scenariusz polega na zapewnienie Power BI i obciążeń analitycznych pełnić odczytu repliki źródła danych do raportowania.

## <a name="considerations-and-limitations"></a>Istotne zagadnienia i ograniczenia

### <a name="pricing-tiers"></a>Warstwy cenowe

Repliki do odczytu są obecnie dostępne tylko w warstw cenowych ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

### <a name="master-server-restart"></a>Ponowne uruchomienie serwera głównego

Po utworzeniu repliki dla serwera głównego, który nie ma istniejących replik wzorzec najpierw zostanie uruchomiony ponownie przygotować się do replikacji. Wziąć pod uwagę i wykonywać te operacje podczas okresu poza godzinami.

### <a name="stopping-replication"></a>Zatrzymywanie replikacji

Można zatrzymać replikację między węzłem głównym i serwerem repliki. Zatrzymywanie replikacji spowoduje usunięcie relacji replikacji między serwerem głównego i repliki.

Po zatrzymaniu replikacji na serwerze repliki staje się serwerem autonomicznym. Dane na serwerze autonomicznym są danych, która była dostępna w replice w czasie, którą zainicjowano polecenia "Zatrzymaj replikację". Autonomiczny serwer nie zostanie wyrównany z serwerem głównym. Ten serwer nie wprowadzać ponownie do repliki.

### <a name="replicas-are-new-servers"></a>Repliki są nowe serwery

Repliki są tworzone jako nowej usługi Azure Database dla serwerów MySQL. Nie można dokonać istniejących serwerów do replik.

### <a name="replica-server-configuration"></a>Konfiguracja serwera repliki

Serwer funkcji replica są tworzone przy użyciu tej samej konfiguracji serwera jako głównej, która zawiera następujące konfiguracje:

- Warstwa cenowa
- Generacja obliczeń
- Rdzenie wirtualne
- Magazyn
- Okres przechowywania kopii zapasowej
- Opcja nadmiarowości kopii zapasowej
- Wersja aparatu programu MySQL
- Reguły zapory

Po utworzeniu repliki można zmienić warstwę cenową (z wyjątkiem do i z Basic), obliczenia generacji, rdzenie wirtualne, Magazyn i przechowywania kopii zapasowych, niezależnie od serwera głównego.

### <a name="master-server-configuration"></a>Konfiguracja serwera głównego

Jeśli wzorzec konfiguracji serwera (np.) rdzenie wirtualne lub magazyn) jest aktualizowany, można również zaktualizować konfiguracji tych replik, większa lub równa wartości. Bez tego serwera repliki może nie być w stanie na bieżąco ze zmianami wprowadzonymi do poziomu głównego i może ulec awarii w wyniku.

Nowe reguły zapory dodane do serwera głównego, po utworzeniu serwera repliki nie są replikowane do repliki. Repliki powinien być zaktualizowany o również tej nowej reguły zapory.

### <a name="deleting-the-master-server"></a>Usuwanie serwera głównego

Po usunięciu serwera głównego replikacja jest zatrzymana, aby wszystkie repliki do odczytu. Tych replik stają się serwerów autonomicznych. Sam serwer główny jest usuwany.

### <a name="user-accounts"></a>Konta użytkowników

Użytkownicy na serwerze głównym są replikowane do odczytu replik. Tekst można połączyć tylko do odczytu repliki, przy użyciu konta użytkownika dostępne na serwerze głównym.

### <a name="other"></a>Inne

- Identyfikatory transakcji globalnej (GTID) nie są obsługiwane.
- Tworzenie repliki repliki nie jest obsługiwane.
- Tabele w pamięci, może spowodować replik zsynchronizowany. Jest to ograniczenie technologii replikacji MySQL. Dowiedz się więcej w [dokumentację referencyjną MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) Aby uzyskać więcej informacji.
- Dostrajanie [ `innodb_file_per_table` ](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) parametru na serwerze głównym po tworzeniu serwera repliki może spowodować, że repliki będzie zsynchronizowany. Serwer repliki nie ma informacji o różnych obszary tabel.
- Upewnij się, że tabele serwera głównego kluczy podstawowych. Brak kluczy podstawowych może spowodować opóźnienie replikacji między węzłem głównym i repliki.
- Zapoznaj się z pełną listą programu MySQL ograniczenia replikacji w [MySQL — dokumentacja](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [tworzenie i zarządzanie nimi odczytu replik w witrynie Azure portal](howto-read-replicas-portal.md)
- Dowiedz się, jak [tworzenie i zarządzanie nimi odczytu repliki przy użyciu wiersza polecenia platformy Azure](howto-read-replicas-cli.md)
