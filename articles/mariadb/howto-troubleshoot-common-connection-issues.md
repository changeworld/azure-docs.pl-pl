---
title: Rozwiązywanie problemów z połączeniem — usługa Azure Database for MariaDB
description: Dowiedz się, jak rozwiązywać problemy z połączeniem z usługą Azure Database for MariaDB, w tym błędy przejściowe wymagające ponownych prób, problemów z zaporą i awarii.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: d134bcd0c5f9bfde0fdb095122d54848873174be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536364"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Rozwiązywanie problemów z połączeniem z usługą Azure Database for MariaDB

Problemy z połączeniem mogą być spowodowane przez różne rzeczy, w tym:

* Ustawienia zapory
* Limit czasu połączenia
* Nieprawidłowe dane logowania
* Maksymalny limit osiągnięty w niektórych zasobach usługi Azure Database dla mariadb
* Problemy z infrastrukturą usługi
* Konserwacja wykonywana w serwisie
* Alokacja obliczeniowa serwera jest zmieniana przez skalowanie liczby procesorów wirtualnych lub przejście do innej warstwy usług

Ogólnie rzecz biorąc problemy z połączeniem z usługą Azure Database for MariaDB można sklasyfikować w następujący sposób:

* Błędy przejściowe (krótkotrwałe lub przerywane)
* Błędy trwałe lub nieprzejściowe (błędy, które regularnie się powtarzają)

## <a name="troubleshoot-transient-errors"></a>Rozwiązywanie problemów z błędami przejściowymi

Błędy przejściowe występują podczas konserwacji, system napotyka błąd ze sprzętem lub oprogramowaniem lub zmieniasz liczbę komputerów wirtualnych lub warstwę usług serwera. Usługa Azure Database for MariaDB ma wbudowaną wysoką dostępność i jest przeznaczona do automatycznego ograniczania tego typu problemów. Jednak aplikacja traci połączenie z serwerem na krótki okres czasu zazwyczaj mniej niż 60 sekund, co najwyżej. Niektóre zdarzenia mogą czasami trwać dłużej, aby ograniczyć, na przykład gdy duża transakcja powoduje długotrwałe odzyskiwanie.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroki mające na celu rozwiązanie problemów z łącznością przejściową

1. Sprawdź [pulpit nawigacyjny usługi platformy Microsoft Azure pod](https://azure.microsoft.com/status) kątem wszelkich znanych awarii, które wystąpiły w czasie, w którym błędy zostały zgłoszone przez aplikację.
2. Aplikacje, które łączą się z usługą w chmurze, takich jak Usługa Azure Database dla MariaDB należy się spodziewać błędów przejściowych i zaimplementować logikę ponawiania obsługi tych błędów zamiast napawania ich jako błędy aplikacji dla użytkowników. Przejrzyj [obsługę błędów łączności przejściowej dla usługi Azure Database dla mariadb,](concepts-connectivity.md) aby uzyskać najlepsze rozwiązania i wskazówki dotyczące projektowania dotyczące obsługi błędów przejściowych.
3. Gdy serwer zbliża się do swoich limitów zasobów, błędy mogą wydawać się przejściowym problemem łączności. Zobacz [Ograniczenia w bazie danych platformy Azure dla MariaDB](concepts-limits.md).
4. Jeśli problemy z łącznością nadal lub jeśli czas trwania, dla którego aplikacja napotka błąd przekracza 60 sekund lub jeśli widzisz wiele wystąpień błędu w danym dniu, złóż żądanie pomocy technicznej platformy Azure, wybierając **pozycję Uzyskaj pomoc techniczną** w witrynie pomocy technicznej platformy [Azure.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Rozwiązywanie problemów z błędami trwałymi

Jeśli aplikacja uporczywie nie może połączyć się z usługą Azure Database for MariaDB, zwykle wskazuje na problem z jednym z następujących czynności:

* Konfiguracja zapory: usługa Azure Database dla serwera MariaDB lub zapora po stronie klienta blokuje połączenia.
* Ponowna konfiguracja sieci po stronie klienta: Dodano nowy adres IP lub serwer proxy.
* Błąd użytkownika: Na przykład może mieć błędnie wpisane parametry połączenia, takie * \@* jak nazwa serwera w ciągu połączenia lub brakujący sufiks nazwy serwera w nazwie użytkownika.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroki mające na celu rozwiązanie problemów z trwałą łącznością

1. Skonfiguruj [reguły zapory,](howto-manage-firewall-portal.md) aby zezwolić na adres IP klienta. Tylko do celów testowania tymczasowego należy skonfigurować regułę zapory przy użyciu 0.0.0.0 jako początkowego adresu IP i używając 255.255.255.255 jako końcowego adresu IP. Spowoduje to otwarcie serwera na wszystkie adresy IP. Jeśli to rozwiąże problem z łącznością, usuń tę regułę i utwórz regułę zapory dla odpowiednio ograniczonego zakresu adresów IP lub adresów.
2. Na wszystkich zaporach między klientem a Internetem upewnij się, że port 3306 jest otwarty dla połączeń wychodzących.
3. Sprawdź parametry połączenia i inne ustawienia połączenia. Sprawdź, [jak połączyć aplikacje z usługą Azure Database for MariaDB](howto-connection-string.md).
4. Sprawdź kondycję usługi na pulpicie nawigacyjnym. Jeśli uważasz, że wystąpiła awaria regionalna, zobacz [Omówienie ciągłości działania w usłudze Azure Database for MariaDB,](concepts-business-continuity.md) aby uzyskać kroki do odzyskania do nowego regionu.

## <a name="next-steps"></a>Następne kroki

* [Obsługa błędów łączności przejściowej dla usługi Azure Database dla mariadb](concepts-connectivity.md)
