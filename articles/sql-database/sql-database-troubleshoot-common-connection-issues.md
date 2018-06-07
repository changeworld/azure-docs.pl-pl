---
title: Rozwiązywanie typowych problemów z połączeniem z usługą Azure SQL Database
description: Kroki w celu identyfikacji i rozwiązywania typowych błędów połączenia bazy danych SQL Azure.
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: daleche
ms.openlocfilehash: 06a0abd673785a572fb8efa8df199562dc13067f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648874"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Rozwiązywanie problemów z połączeniem z bazą danych SQL Azure
Podczas połączenia z bazą danych SQL Azure nie powiedzie się, zostanie wyświetlony [komunikaty o błędach](sql-database-develop-error-messages.md). W tym artykule jest scentralizowane temat, który pomaga w rozwiązywaniu problemów z połączeniem bazy danych SQL Azure. Podaj [typowe przyczyny](#cause) problemów połączenie zaleca [rozwiązywania problemów](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) pomaga tożsamości problemu i instrukcje dotyczące rozwiązywania problemów do rozwiązania [przejściowej błędy](#troubleshoot-transient-errors) i [trwałe lub nieprzejściowego błędów](#troubleshoot-persistent-errors). 

Jeśli wystąpią problemy dotyczące połączenia, spróbuj rozwiązywanie opisanych w tym artykule.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Przyczyna
Problemy z połączeniem może być spowodowane jedną z następujących czynności:

* Niepowodzenie w zastosowaniu najlepszych rozwiązań i wskazówki dotyczące projektowania podczas projektowania aplikacji.  Zobacz [omówienie tworzenia bazy danych SQL](sql-database-develop-overview.md) rozpocząć pracę.
* Rekonfiguracji bazy danych SQL Azure
* Ustawienia zapory
* Limit czasu połączenia
* Informacje logowania niepoprawne
* Osiągnięto maksymalny limit dla niektórych zasobów bazy danych SQL Azure

Ogólnie rzecz biorąc problemy z połączeniem z bazą danych SQL Azure mogą być klasyfikowane w następujący sposób:

* [Błędów przejściowych (krótkim okresie lub sporadyczne)](#troubleshoot-transient-errors)
* [Trwałe lub nieprzejściowego błędów (błędy, które regularnie powtarzać)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Spróbuj rozwiązywania problemów łączności bazy danych SQL Azure
W razie wystąpienia błędu określonego połączenia spróbować [to narzędzie](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), który będzie pomagają w szybkim tożsamości i rozwiązania problemu.

## <a name="troubleshoot-transient-errors"></a>Rozwiązywanie błędów przejściowych

Jeśli aplikacja łączy się z bazą danych Azure SQL, pojawi się następujący komunikat o błędzie:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Ten komunikat o błędzie jest zwykle charakter przejściowy (krótkim okresie).
> 
> 

Ten błąd występuje, gdy trwa bazy danych Azure przenieść (lub ponownie skonfigurować) i aplikacji utraci połączenie z bazą danych SQL. Występują zdarzenia ponownej konfiguracji bazy danych SQL z powodu planowanych zdarzeń (na przykład uaktualnienie oprogramowania) lub nieplanowanego zdarzenia (na przykład awaria procesu lub równoważenia obciążenia). Większość zdarzeń zmiany konfiguracji są zazwyczaj w krótkim okresie i należy wykonać w mniej niż 60 sekund co najwyżej. Jednak te zdarzenia czasami może trwać dłużej, aby zakończyć, takie jak kiedy dużych transakcji powoduje odzyskiwania długotrwałe.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Czynności umożliwiające rozwiązywanie problemów z łącznością przejściowej

1. Sprawdź [pulpicie nawigacyjnym usługi systemu Microsoft Azure](https://azure.microsoft.com/status) dla znanych wystąpienia awarii, które wystąpiły w czasie, w którym błędy zostały zgłoszone przez aplikację.
2. Aplikacje łączące się usługa w chmurze, takich jak bazy danych SQL Azure należy oczekiwać zdarzenia okresowe ponowna konfiguracja i wdrożenie ponów logiki do obsługi tych błędów zamiast udostępniając je jako błędy aplikacji dla użytkowników. Przegląd [błędów przejściowych](sql-database-connectivity-issues.md) sekcji i najlepszych rozwiązań i zaleceń dotyczących projektowania w [omówienie tworzenia bazy danych SQL](sql-database-develop-overview.md) uzyskać więcej informacji oraz ogólne ponów strategii. Następnie, zobacz przykłady kodu w [biblioteki połączeń dla bazy danych SQL i programu SQL Server](sql-database-libraries.md) dla szczegółowych informacji.
3. Jako bazę danych zbliża się limit zasobów, może wydawać się to problem przejściowy łączności. Zobacz [Rozwiązywanie problemów z wydajnością](sql-database-troubleshoot-performance.md).
4. Jeśli występują problemy z łącznością kontynuować, lub jeśli czas trwania, dla których aplikacja napotka błąd przekracza 60 sekund, lub jeśli wiele wystąpień błędu w danym dniu, pliku żądania pomocy technicznej platformy Azure, wybierając **uzyskać obsługuje**na [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options) lokacji.

## <a name="troubleshoot-persistent-errors"></a>Rozwiązywanie problemów trwałych
Jeśli aplikacja trwale nie może nawiązać połączenia z bazą danych SQL Azure, zwykle wskazuje problem z jedną z następujących czynności:

* Konfiguracja zapory. Azure SQL bazy danych lub po stronie klienta Zapora blokuje połączenia z bazą danych SQL Azure.
* Ponowna konfiguracja po stronie klienta sieci: na przykład nowy adres IP lub serwer proxy.
* Błąd użytkownika: na przykład błędnie parametry połączenia, takie jak nazwa serwera w parametrach połączenia.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Czynności umożliwiające rozwiązywanie problemów z łącznością trwałych
1. Konfigurowanie [reguły zapory](sql-database-configure-firewall-settings.md) umożliwia adres IP klienta. Dla tymczasowych do celów testowych Skonfiguruj regułę zapory przy użyciu 0.0.0.0 jako początkowy zakres adresów IP i 255.255.255.255 jako końcową zakresu adresów IP. Spowoduje to otwarcie serwera dla wszystkich adresów IP. Jeśli to rozwiązuje problem z łącznością, należy usunąć tę regułę i utworzyć regułę zapory odpowiednio ograniczone adres IP lub zakres adresów. 
2. Upewnij się, że port 1433 jest otwarty dla połączeń wychodzących na wszystkie zapory między klientem a Internetem. Przegląd [konfigurowania Zapory systemu Windows, aby umożliwić dostępu programu SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) i [hybrydowego tożsamości wymagane porty i protokoły](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) dla dodatkowych wskaźników związane z dodatkowych portów, które należy otworzyć na platformie Azure Uwierzytelnianie usługi Active Directory.
3. Sprawdź ciąg połączenia i inne ustawienia połączenia. W sekcji parametrów połączenia w [tematu problemy z łącznością](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Sprawdź kondycję usług na pulpicie nawigacyjnym. Jeśli uważasz, że istnieje regionalnej awarii, zobacz [odzyskiwanie po awarii](sql-database-disaster-recovery.md) dla czynności, aby odzyskać do nowego regionu.

## <a name="next-steps"></a>Kolejne kroki
* [Rozwiązywanie problemów z wydajnością bazy danych SQL Azure](sql-database-troubleshoot-performance.md)
* [Wyszukiwanie w dokumentacji w systemie Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Wyświetl najnowsze aktualizacje do usługi baza danych SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Omówienie tworzenia bazy danych SQL](sql-database-develop-overview.md)
* [Ogólne wskazówki obsługi błędów przejściowych](../best-practices-retry-general.md)
* [Biblioteki połączeń dla bazy danych SQL i programu SQL Server](sql-database-libraries.md)

