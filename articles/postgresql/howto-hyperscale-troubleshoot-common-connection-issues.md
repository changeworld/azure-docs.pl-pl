---
title: Rozwiązywanie problemów z połączeniami — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Dowiedz się, jak rozwiązywać problemy z połączeniem z usługą Azure Database for PostgreSQL — Hiperskala (Citus)
keywords: połączenie postgresql,parametry połączenia,problemy z łącznością,błąd przejściowy,błąd połączenia
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: c064aca484f85c44dada9888012140784a96863f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977509"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Rozwiązywanie problemów z połączeniem z usługą Azure Database for PostgreSQL — hiperskala (Citus)

Problemy z połączeniem mogą być spowodowane kilkoma rzeczami, takimi jak:

* Ustawienia zapory
* Limit czasu połączenia
* Nieprawidłowe informacje o loguchaniu
* Osiągnięto limit połączenia dla grupy serwerów
* Problemy z infrastrukturą usługi
* Konserwacja serwisowa
* Węzeł koordynatora, który nie działa, na nowym sprzęcie

Ogólnie rzecz biorąc problemy z połączeniem z hiperskali można sklasyfikować w następujący sposób:

* Błędy przejściowe (krótkotrwałe lub przerywane)
* Błędy trwałe lub nieprzejściowe (błędy, które regularnie się powtarzają)

## <a name="troubleshoot-transient-errors"></a>Rozwiązywanie problemów z błędami przejściowymi

Błędy przejściowe występują z wielu powodów. Najczęstsze obejmują konserwację systemu, błąd ze sprzętem lub oprogramowaniem oraz uaktualnienia vcore węzłów koordynatora.

Włączenie wysokiej dostępności węzłów grupy serwerów w skali hiperskali może automatycznie ograniczyć tego typu problemy. Jednak aplikacja powinna być nadal przygotowana do utraty połączenia na krótko. Również inne zdarzenia mogą trwać dłużej, aby ograniczyć, takich jak gdy duża transakcja powoduje długotrwałe odzyskiwanie.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroki mające na celu rozwiązanie problemów z łącznością przejściową

1. Sprawdź [pulpit nawigacyjny usługi platformy Microsoft Azure pod](https://azure.microsoft.com/status) kątem wszelkich znanych awarii, które wystąpiły w czasie, w którym aplikacja zgłaszała błędy.
2. Aplikacje, które łączą się z usługą w chmurze, takich jak Hiperskala (Citus) należy spodziewać się błędów przejściowych i reagować bezpiecznie. Na przykład aplikacje należy zaimplementować logikę ponawiania do obsługi tych błędów zamiast napawania ich jako błędy aplikacji dla użytkowników.
3. Gdy grupa serwerów zbliża się do swoich limitów zasobów, błędy mogą wydawać się przejściowymi problemami z łącznością. Zwiększenie pamięci RAM węzła lub dodanie węzłów procesu roboczego i ponowne zrównoważenie danych może pomóc.
4. Jeśli problemy z łącznością nadal lub trwać dłużej niż 60 sekund lub zdarzają się więcej niż raz dziennie, złożyć żądanie pomocy technicznej platformy Azure, wybierając **Pobierz pomoc techniczną** w witrynie pomocy technicznej platformy [Azure.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Rozwiązywanie problemów z błędami trwałymi

Jeśli aplikacja uporczywie nie łączy się z hiperskali (Citus), najczęstszymi przyczynami są błędna konfiguracja zapory lub błąd użytkownika.

* Konfiguracja zapory węzła koordynatora: Upewnij się, że zapora serwera hiperskali jest skonfigurowana tak, aby zezwalać na połączenia z klienta, w tym serwery proxy i bramy.
* Konfiguracja zapory klienta: zapora na kliencie musi zezwalać na połączenia z serwerem bazy danych. Niektóre zapory wymagają zezwalania nie tylko na aplikację według nazwy, ale na zezwalanie na adresy IP i porty serwera.
* Błąd użytkownika: Sprawdź dwukrotnie parametry połączenia. Być może masz błędnie wpisane parametry, takie jak nazwa serwera. Ciągi połączeń dla różnych struktur języka i psql można znaleźć w witrynie Azure portal. Przejdź do strony Parametry połączenia w grupie **serwerów** Citus (Hyperscale). Należy również pamiętać, że klastry w skali hiperskali (Citus) mają tylko jedną bazę danych, a jej wstępnie zdefiniowaną nazwą jest **citus**.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroki mające na celu rozwiązanie problemów z trwałą łącznością

1. Skonfiguruj [reguły zapory,](howto-hyperscale-manage-firewall-using-portal.md) aby zezwolić na adres IP klienta. Tylko do celów testowania tymczasowego należy skonfigurować regułę zapory przy użyciu 0.0.0.0 jako początkowego adresu IP i używając 255.255.255.255 jako końcowego adresu IP. Ta reguła otwiera serwer na wszystkie adresy IP. Jeśli reguła rozwiązuje problem z łącznością, usuń go i utwórz regułę zapory dla odpowiednio ograniczonego zakresu adresów IP lub adresów.
2. Na wszystkich zaporach między klientem a Internetem upewnij się, że port 5432 jest otwarty dla połączeń wychodzących.
3. Sprawdź parametry połączenia i inne ustawienia połączenia.
4. Sprawdź kondycję usługi na pulpicie nawigacyjnym.

## <a name="next-steps"></a>Następne kroki

* Poznaj pojęcia [reguł zapory w usłudze Azure Database for PostgreSQL — Hiperskala (Citus)](concepts-hyperscale-firewall-rules.md)
* Zobacz, jak [zarządzać regułami zapory dla usługi Azure Database dla postgreSQL — hiperskala (Citus)](howto-hyperscale-manage-firewall-using-portal.md)
