---
title: Rozwiązywanie typowych problemów z połączeniem z usługą Azure SQL Database
description: Kroki w celu zidentyfikowania i rozwiązania typowych błędów połączenia usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: daleche
ms.reviewer: jrasnik
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: d278fd6ed06b58db052154e632e565de36853e77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331438"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Rozwiązywanie problemów z połączeniem do usługi Azure SQL Database

Gdy połączenie usługi Azure SQL Database nie powiodło się, pojawi się [komunikaty o błędach](sql-database-develop-error-messages.md). Ten artykuł stanowi scentralizowane temat, który pomoże Ci rozwiązać problemy z połączeniem usługi Azure SQL Database. Wprowadza [najczęstszych przyczyn](#cause) z problemów z łącznością, zaleca się [narzędzia rozwiązywania problemów](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) ulec pomaga tożsamości problem, a także kroki rozwiązywania problemów, aby rozwiązać [przejściowe błędy](#troubleshoot-transient-errors) i [trwałego lub inne niż przejściowe błędy](#troubleshoot-persistent-errors). 

Jeśli wystąpią problemy z połączeniem, spróbuj wykonać kroki rozwiązywania problemów, które są opisane w tym artykule.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Przyczyna

Problemy z połączeniem może być spowodowane jedną z następujących czynności:

* Nie można zastosować najlepsze rozwiązania i wytyczne dotyczące projektowania podczas tworzenia projektu aplikacji.  Zobacz [omówienie programowania w usłudze SQL Database](sql-database-develop-overview.md) na rozpoczęcie pracy.
* Azure SQL Database ponownej konfiguracji.
* Ustawienia zapory
* Limit czasu połączenia
* Nieprawidłowymi informacjami logowania
* Osiągnięto maksymalny limit dla niektórych zasobów usługi Azure SQL Database

Ogólnie rzecz biorąc problemy z połączeniem do bazy danych SQL Azure mogą być klasyfikowane w następujący sposób:

* [Błędów przejściowych (krótkotrwałe lub sporadycznych)](#troubleshoot-transient-errors)
* [Trwałe lub nieprzejściowych błędów (błędy, które regularnie powtarzać)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Wypróbuj narzędzie do rozwiązywania problemów dla problemów z połączeniem usługi Azure SQL Database

W razie wystąpienia błędu określonego połączenia spróbować [to narzędzie](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), co spowoduje pomagają w szybkim tożsamości i rozwiązaniu problemu.

## <a name="troubleshoot-transient-errors"></a>Rozwiązywanie problemów z błędami przejściowymi

Aplikacja nawiązuje połączenie z bazą danych Azure SQL database, pojawi się następujący komunikat o błędzie:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Ten komunikat o błędzie jest zwykle charakter przejściowy (krótkotrwałe).
> 
> 

Ten błąd występuje, gdy baza danych jest przeniesiony (lub ponownie skonfigurować) i aplikacja straci połączenie z bazą danych. Zdarzenia ponownej konfiguracji bazy danych występują ze względu na to zdarzenie planowane (na przykład uaktualnienie oprogramowania) lub nieplanowanego zdarzenia (na przykład awaria procesu lub równoważenia obciążenia). Większość zdarzeń zmiany konfiguracji są zwykle krótkotrwałe i należy wykonać w czasie krótszym niż 60 sekund co najwyżej. Jednak te zdarzenia od czasu do czasu może potrwać dłużej, takie jak kiedy dużej transakcji powoduje, że odzyskiwania długoterminowych.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroki, aby rozwiązać problemy z łącznością przejściowe

1. Sprawdź [pulpitu nawigacyjnego usług systemu Azure firmy Microsoft](https://azure.microsoft.com/status) o znanych awariach, które wystąpiły w czasie, podczas którego błędy zostały zgłoszone przez aplikację.
2. Aplikacje łączące się z usługą w chmurze, takich jak usługi Azure SQL Database należy spodziewać się zdarzenia okresowe ponownej konfiguracji i implementowanie ponów logikę obsługującą te błędy zamiast udostępniając je jako błędy aplikacji dla użytkowników. Przegląd [błędów przejściowych](sql-database-connectivity-issues.md) sekcji i najlepsze rozwiązania i wytyczne dotyczące projektowania w [omówienie programowania w usłudze SQL Database](sql-database-develop-overview.md) uzyskać więcej informacji i ogólne strategie dotyczące ponawiania prób. Następnie zobacz przykłady kodu w [biblioteki połączeń dla usługi SQL Database i programu SQL Server](sql-database-libraries.md) dla szczegółowych informacji.
3. Zgodnie z bazy danych zbliża się limit zasobów, może wydawać się być przejściowy problem łączności. Zobacz [limity zasobów](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Jeśli nadal występują problemy z łącznością lub jeśli czas trwania, dla którego aplikacja napotkała błąd przekracza 60 sekund, lub jeśli wiele wystąpień tego błędu jest widoczny w danym dniu pliku żądanie pomocy technicznej platformy Azure, wybierając **uzyskiwanie pomocy technicznej**na [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options) lokacji.

## <a name="troubleshoot-persistent-errors"></a>Rozwiązywanie problemów z błędami trwałego
Jeśli aplikacja trwałe nie może nawiązać połączenia z bazą danych SQL Azure, zwykle oznacza problem związany z jedną z następujących czynności:

* Konfiguracja zapory. Zapory usługi Azure SQL database lub po stronie klienta nie blokuje połączenia z bazą danych SQL Azure.
* Ponownej konfiguracji po stronie klienta sieci: na przykład nowy adres IP lub serwer proxy.
* Błąd użytkownika: na przykład źle wpisano parametry połączenia, takie jak nazwa serwera w parametrach połączenia.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroki, aby rozwiązać problemy z łącznością trwałego
1. Konfigurowanie [reguły zapory](sql-database-configure-firewall-settings.md) pozwalającego klientowi adres IP. Dla tymczasowych do celów testowych Skonfiguruj regułę zapory przy użyciu 0.0.0.0 jako początkowego zakres adresów IP oraz 255.255.255.255 jako końcową zakresu adresów IP. Spowoduje to otwarcie serwera dla wszystkich adresów IP. Jeśli to rozwiąże problem z łącznością, usunięcie tej reguły i Utwórz regułę zapory dla odpowiednio ograniczone adresu IP lub zakresu adresów. 
2. Upewnij się, że port 1433 jest otwarty dla połączeń wychodzących na wszystkie zapory między klientem a Internetem. Przegląd [Skonfiguruj zaporę Windows tak, aby umożliwić dostęp SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) i [hybrydowego tożsamości porty i protokoły wymagane](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) dla wskaźników dodatkowe powiązane z dodatkowych portów, które należy otworzyć dla platformy Azure Uwierzytelnianie usługi Active Directory.
3. Sprawdź parametry połączenia i inne ustawienia połączenia. Zobacz sekcję parametrów połączenia w [tematu problemy z łącznością](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Sprawdzanie kondycji usług na pulpicie nawigacyjnym. Jeśli występuje awaria regionalna, zobacz [odzyskiwanie sprawności po awarii](sql-database-disaster-recovery.md) kroków odzyskiwania w nowym regionie.

## <a name="next-steps"></a>Kolejne kroki
* [Wyszukiwanie w dokumentacji w systemie Microsoft Azure](https://azure.microsoft.com/search/documentation/)
* [Wyświetl najnowsze aktualizacje usługi Azure SQL Database](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Omówienie tworzenia bazy danych SQL](sql-database-develop-overview.md)
* [Ogólne wskazówki obsługi błędów przejściowych](../best-practices-retry-general.md)
* [Biblioteki połączeń dla usługi SQL Database i programu SQL Server](sql-database-libraries.md)

