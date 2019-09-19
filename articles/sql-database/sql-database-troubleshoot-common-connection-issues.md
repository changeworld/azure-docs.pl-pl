---
title: Rozwiązywanie typowych problemów z połączeniem z usługą Azure SQL Database
description: Kroki umożliwiające zidentyfikowanie i rozwiązanie typowych błędów połączenia dla Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: daleche
ms.reviewer: jrasnik
ms.date: 01/25/2019
ms.openlocfilehash: cd0ab6d89d88c594d283dc0718c0f58ebb98bf43
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090798"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Rozwiązywanie problemów z połączeniem do Azure SQL Database

Gdy połączenie z Azure SQL Database zakończy się niepowodzeniem, pojawią się [komunikaty o błędach](sql-database-develop-error-messages.md). Ten artykuł stanowi scentralizowany temat ułatwiający Rozwiązywanie problemów z łącznością Azure SQL Database. W tym artykule przedstawiono [typowe przyczyny](#cause) problemów z połączeniami, zalecane [Narzędzie do rozwiązywania problemów](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) , które pomaga w identyfikacji problemu, i zawiera kroki rozwiązywania problemów, które rozwiązują [Błędy przejściowe](#troubleshoot-transient-errors) oraz [Błędy trwałe lub nieprzejściowe ](#troubleshoot-persistent-errors). 

Jeśli wystąpią problemy z połączeniem, spróbuj wykonać kroki opisane w tym artykule.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Przyczyna

Problemy z połączeniem mogą być spowodowane jedną z następujących czynności:

* Niestosowanie najlepszych rozwiązań i projektowanie wytycznych podczas procesu projektowania aplikacji.  Aby rozpocząć, zobacz [Omówienie opracowywania SQL Database](sql-database-develop-overview.md) .
* Azure SQL Database ponownej konfiguracji
* Ustawienia zapory
* Limit czasu połączenia
* Nieprawidłowe informacje logowania
* Osiągnięto maksymalny limit dla niektórych zasobów Azure SQL Database

Ogólnie rzecz biorąc problemy z połączeniem do Azure SQL Database mogą być klasyfikowane w następujący sposób:

* [Błędy przejściowe (krótko-lub sporadyczne)](#troubleshoot-transient-errors)
* [Błędy trwałe lub nieprzejściowe (błędy, które regularnie powtarzają się)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Wypróbuj narzędzie do rozwiązywania problemów z Azure SQL Database łączności

Jeśli wystąpi błąd konkretnego połączenia, wypróbuj [to narzędzie](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), które pomoże Ci szybko zidentyfikować i rozwiązać problem.

## <a name="troubleshoot-transient-errors"></a>Rozwiązywanie problemów z błędami przejściowymi

Gdy aplikacja nawiązuje połączenie z bazą danych Azure SQL, zostanie wyświetlony następujący komunikat o błędzie:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Ten komunikat o błędzie jest zazwyczaj przejściowy (krótko-krótko).
> 
> 

Ten błąd występuje, gdy baza danych jest przenoszona (lub ponownie skonfigurowana), a Twoja aplikacja utraci połączenie z bazą danych. Zdarzenia ponownej konfiguracji bazy danych występują ze względu na planowane zdarzenie (na przykład uaktualnienie oprogramowania) lub niezaplanowane zdarzenie (na przykład awaria procesu lub Równoważenie obciążenia). Większość zdarzeń ponownej konfiguracji jest zwykle krótkoterminowa i powinna być wykonana w mniej niż 60 sekund. Jednak te zdarzenia mogą czasami trwać dłużej, na przykład wtedy, gdy duża transakcja powoduje długotrwałe odzyskiwanie.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroki rozwiązywania przejściowych problemów z łącznością

1. Sprawdź [pulpit nawigacyjny usługi Microsoft Azure](https://azure.microsoft.com/status) pod kątem znanych przestojów, które wystąpiły w czasie, w którym błędy zostały zgłoszone przez aplikację.
2. Aplikacje łączące się z usługą w chmurze, takie jak Azure SQL Database, powinny oczekiwać okresowych zdarzeń ponownej konfiguracji i wdrożyć logikę ponawiania w celu obsługi tych błędów zamiast obsłużyć te błędy jako błędy aplikacji dla użytkowników. Zapoznaj się z sekcją [Błędy przejściowe](sql-database-connectivity-issues.md) oraz najlepsze rozwiązania i wskazówki [SQL Database](sql-database-develop-overview.md) dotyczące projektowania, aby uzyskać więcej informacji i ogólnych strategii ponawiania. Następnie zapoznaj się z przykładami kodu w [bibliotekach połączeń dla SQL Database i SQL Server](sql-database-libraries.md) dla określonych.
3. Jako że baza danych zbliża się do limitów zasobów, może wydawać się przejściowy problem z łącznością. Zobacz [limity zasobów](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Jeśli problemy z łącznością będą kontynuowane, lub jeśli czas, przez który aplikacja napotyka błąd, przekracza 60 sekund lub Jeśli zobaczysz wiele wystąpień błędu w danym dniu, zanotuj żądanie pomocy technicznej platformy Azure, wybierając pozycję **Uzyskaj pomoc techniczną** na [platformie Azure Witryna pomocy technicznej](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Rozwiązywanie problemów z trwałymi błędami
Jeśli aplikacja trwale nie może nawiązać połączenia z Azure SQL Database, zazwyczaj wskazuje problem z jedną z następujących czynności:

* Konfiguracja zapory. Usługa Azure SQL Database lub Zapora po stronie klienta blokuje połączenia z Azure SQL Database.
* Ponowna konfiguracja sieci po stronie klienta: na przykład nowy adres IP lub serwer proxy.
* Błąd użytkownika: na przykład błędne typy parametrów połączenia, takie jak nazwa serwera w parametrach połączenia.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroki rozwiązywania problemów z łącznością trwałą
1. Skonfiguruj [reguły zapory](sql-database-configure-firewall-settings.md) w taki sposób, aby zezwalały na adres IP klienta. W celach tymczasowych należy skonfigurować regułę zapory przy użyciu adresu 0.0.0.0 jako początkowy zakres adresów IP i przy użyciu wartości 255.255.255.255 jako końcowego zakresu adresów IP. Spowoduje to otwarcie serwera do wszystkich adresów IP. Jeśli spowoduje to rozwiązanie problemu z łącznością, Usuń tę regułę i Utwórz regułę zapory dla odpowiednio ograniczonego adresu IP lub zakresu adresów. 
2. Na wszystkich zaporach między klientem a Internetem upewnij się, że port 1433 jest otwarty dla połączeń wychodzących. Zapoznaj się z tematem [Konfigurowanie zapory systemu Windows w celu zezwalania na dostęp do SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) oraz [portów i protokołów wymaganych](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) przez usługi, aby uzyskać dodatkowe wskaźniki związane z dodatkowymi portami, które należy otworzyć na potrzeby uwierzytelniania Azure Active Directory.
3. Sprawdź parametry połączenia i inne ustawienia połączenia. Zobacz sekcję parametry połączenia w [temacie problemy z łącznością](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Sprawdź kondycję usługi na pulpicie nawigacyjnym. Jeśli uważasz, że wystąpi awaria regionalna, zapoznaj się z tematem [odzyskiwanie po awarii](sql-database-disaster-recovery.md) w celu wykonania czynności do odzyskania w nowym regionie.

## <a name="next-steps"></a>Następne kroki
* [Wyszukaj w dokumentacji Microsoft Azure](https://azure.microsoft.com/search/documentation/)
* [Wyświetl najnowsze aktualizacje usługi Azure SQL Database](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Przegląd SQL Database Development](sql-database-develop-overview.md)
* [Ogólne wskazówki dotyczące obsługi błędów przejściowych](../best-practices-retry-general.md)
* [Biblioteki połączeń dla SQL Database i SQL Server](sql-database-libraries.md)

