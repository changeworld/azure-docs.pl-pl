---
title: Lista kontrolna zabezpieczeń bazy danych platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw Lista kontrolna zabezpieczeń bazy danych platformy Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 424453e70e5b62e408f408cd5ae8169cddb14dd7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121695"
---
# <a name="azure-database-security-checklist"></a>Lista kontrolna zabezpieczeń bazy danych platformy Azure

Aby pomóc zwiększyć bezpieczeństwo, bazy danych Azure obejmuje wiele wbudowanych mechanizmów kontroli zabezpieczeń, które można użyć do ograniczenia i kontroli dostępu.

Należą do nich:

-   Zaporą, która pozwala na tworzenie [reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ograniczenie łączności przy użyciu adresu IP
-   Zapory poziomu serwera dostępne w witrynie Azure portal
-   Reguły zapory na poziomie bazy danych dostępne w programie SSMS
-   Bezpieczne połączenie z bazą danych przy użyciu bezpiecznego połączenia ciągów
-   Zarządzanie dostępem do użycia
-   Szyfrowanie danych
-   Inspekcja usługi SQL Database
-   Wykrywanie zagrożeń usługi SQL Database

## <a name="introduction"></a>Wprowadzenie
Chmura obliczeniowa wymaga nowych paradygmatów zabezpieczeń, które są Ci znane wielu użytkowników aplikacji, administratorzy baz danych i programistów. W rezultacie niektóre organizacje są wątpliwości do wdrożenia infrastruktury w chmurze do zarządzania danymi ze względu na postrzegany zagrożenia dla bezpieczeństwa. Jednak mogą znacznie tej częste złagodzone za pośrednictwem lepiej zrozumieć funkcje zabezpieczeń wbudowane w Microsoft Azure i Microsoft Azure SQL Database.

## <a name="checklist"></a>Lista kontrolna
Zalecamy przeczytanie [bazy danych usługi Azure Security Best Practices](https://docs.microsoft.com/azure/security/azure-database-security-best-practices) artykuł przed recenzowania tej listy kontrolnej. Będzie można maksymalnie wykorzystać możliwości tej listy kontrolnej po zidentyfikowaniu najlepszych rozwiązań. Ta lista kontrolna umożliwia następnie upewnij się, że uwzględniono istotne problemy w zabezpieczeń bazy danych platformy Azure.


|Lista kontrolna kategorii| Opis|
| ------------ | -------- |
|**Ochrona danych**||
| <br> Szyfrowanie podczas transferu/ruchu| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), do szyfrowania danych, gdy dane są przenoszone do sieci.</li><li>Baza danych wymaga bezpiecznej komunikacji z klientami, na podstawie [TDS (Stream dane tabelaryczne)](https://msdn.microsoft.com/library/dd357628.aspx) protokołu za pośrednictwem protokołu TLS (Transport Layer Security).</li></ul> |
|<br>Szyfrowanie w spoczynku| <ul><li>[Transparent Data Encryption](https://go.microsoft.com/fwlink/?LinkId=526242), gdy nieaktywne dane są przechowywane w fizycznie w dowolnej formie cyfrowej.</li></ul>|
|**Kontrola dostępu**||  
|<br> Dostęp do bazy danych | <ul><li>[Uwierzytelnianie](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) uwierzytelniania (Azure Active Directory Authentication) AD korzysta z tożsamości zarządzanej przez usługę Azure Active Directory.</li><li>[Autoryzacja](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) przyznanie użytkownikom minimalnych niezbędnych uprawnień.</li></ul> |
|<br>Dostęp do aplikacji| <ul><li>[Wiersz poziom zabezpieczeń](https://msdn.microsoft.com/library/dn765131) (przy użyciu zasad zabezpieczeń, w tym samym czasie, ograniczanie dostępu na poziomie wierszy na podstawie kontekstu użytkownika tożsamości, roli lub wykonywania).</li><li>[Dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (przy użyciu uprawnień i zasad, ogranicza ujawnianie poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników)</li></ul>|
|**Aktywne monitorowanie**||  
| <br>Śledzenia i wykrywania| <ul><li>[Inspekcja](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji / Zaloguj się do niego działanie Twojej [konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account).</li><li>Śledź Azure bazę danych kondycji za pomocą [dzienników aktywności usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Wykrywanie zagrożeń](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych. </li></ul> |
|<br>Azure Security Center| <ul><li>[Monitorowanie danych](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases) usługa Azure Security Center jako scentralizowanych zabezpieczeń rozwiązanie do monitorowania dla języków SQL i innymi usługami platformy Azure.</li></ul>|       

## <a name="conclusion"></a>Podsumowanie
Azure Database to platforma niezawodne bazy danych, z pełnym zakresem funkcji zabezpieczeń, które spełniają wiele organizacji i prawne wymagania dotyczące zgodności. Kontrolowanie fizyczny dostęp do danych, a przy użyciu różnych opcji dla bezpieczeństwa danych w plikach, kolumny lub poziomie wiersza za pomocą funkcji Transparent Data Encryption, szyfrowanie na poziomie komórki lub zabezpieczenia na poziomie wiersza można łatwo chronić dane. Zawsze zaszyfrowane również umożliwia wykonywanie operacji względem zaszyfrowane dane, uproszczeniu procesu aktualizacji aplikacji. Z kolei dostęp do inspekcji dzienników aktywności bazy danych SQL zapewnia informacje, których potrzebujesz, dzięki czemu można dowiedzieć się, jak i kiedy dane są używane.

## <a name="next-steps"></a>Kolejne kroki
W kilku prostych krokach możesz poprawić zabezpieczenia bazy danych, aby chronić ją przed złośliwymi użytkownikami i nieautoryzowanym dostępem. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie [reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) dla serwera lub bazy danych.
- Ochrona danych dzięki [szyfrowania](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Włącz [inspekcji usługi SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).

