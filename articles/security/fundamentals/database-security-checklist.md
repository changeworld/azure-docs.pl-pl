---
title: Lista kontrolna zabezpieczeń bazy danych platformy Azure| Dokumenty firmy Microsoft
description: Ten artykuł zawiera zestaw listy kontrolnej dla zabezpieczeń bazy danych platformy Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: d9283a36d5f7ccb82b2cc211485487d5a3dcce7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79201029"
---
# <a name="azure-database-security-checklist"></a>Lista kontrolna zabezpieczeń bazy danych platformy Azure

Aby zwiększyć bezpieczeństwo, usługa Azure Database zawiera szereg wbudowanych zabezpieczeń, których można użyć do ograniczania i kontrolowania dostępu.

Należą do nich:

-    Zapora umożliwiająca tworzenie [reguł zapory](../../sql-database/sql-database-firewall-configure.md) ograniczających łączność według adresu IP,
-    Zapora na poziomie serwera dostępna w witrynie Azure portal
-    Reguły zapory na poziomie bazy danych dostępne z usługi SSMS
-    Bezpieczna łączność z bazą danych przy użyciu bezpiecznych ciągów połączeń
-    Korzystanie z zarządzania dostępem
-    Szyfrowanie danych
-    Inspekcja usługi SQL Database
-    Wykrywanie zagrożeń bazy danych SQL

## <a name="introduction"></a>Wprowadzenie
Chmura obliczeniowa wymaga nowych paradygmatów zabezpieczeń, które są nieznane wielu użytkownikom aplikacji, administratorom baz danych i programistom. W rezultacie niektóre organizacje są niezdecydowane, aby zaimplementować infrastrukturę chmury do zarządzania danymi ze względu na postrzegane zagrożenia bezpieczeństwa. Jednak wiele z tych obaw można złagodzić poprzez lepsze zrozumienie funkcji zabezpieczeń wbudowanych w microsoft azure i microsoft azure sql database.

## <a name="checklist"></a>Lista kontrolna
Przed przejrzeniem tej listy kontrolnej zaleca się zapoznanie się z [artykułem Sprawdzone rozwiązania dotyczące zabezpieczeń bazy danych usługi Azure](database-best-practices.md) Database. Po zapoznaniu się z najlepszymi praktykami będzie można w pełni wykorzystać tę listę kontrolną. Następnie można użyć tej listy kontrolnej, aby upewnić się, że zostały rozwiązane ważne problemy w zabezpieczeniach bazy danych platformy Azure.


|Lista kontrolna kategoria| Opis|
| ------------ | -------- |
|**Ochrona danych**||
| <br> Szyfrowanie w ruchu/tranzycie| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), do szyfrowania danych, gdy dane są przesuniane do sieci.</li><li>Baza danych wymaga bezpiecznej komunikacji z klientami na podstawie protokołu [TDS(Tabular Data Stream)](https://msdn.microsoft.com/library/dd357628.aspx) za pomocą protokołu TLS (Transport Layer Security).</li></ul> |
|<br>Szyfrowanie w spoczynku| <ul><li>[Przezroczyste szyfrowanie danych](https://go.microsoft.com/fwlink/?LinkId=526242), gdy nieaktywne dane są fizycznie przechowywane w dowolnej formie cyfrowej.</li></ul>|
|**Kontrola dostępu**||  
|<br> Dostęp do bazy danych | <ul><li>[Uwierzytelnianie](../../sql-database/sql-database-manage-logins.md) (uwierzytelnianie usługi Azure Active Directory) Ad uwierzytelnianie używa tożsamości zarządzanych przez usługę Azure Active Directory.</li><li>[Autoryzacja](../../sql-database/sql-database-manage-logins.md) przyznaje użytkownikom najmniej wymaganych uprawnień.</li></ul> |
|<br>Dostęp do aplikacji| <ul><li>[Zabezpieczenia na poziomie wiersza](https://msdn.microsoft.com/library/dn765131) (przy użyciu zasad zabezpieczeń, jednocześnie ograniczając dostęp na poziomie wiersza na podstawie tożsamości użytkownika, roli lub kontekstu wykonywania).</li><li>[Dynamiczne maskowanie danych](../../sql-database/sql-database-dynamic-data-masking-get-started.md) (korzystanie z zasad & uprawnień ogranicza ekspozycję poufnych danych, maskując ją użytkownikom nieuprzywilejowanym)</li></ul>|
|**Proaktywne monitorowanie**||  
| <br>Śledzenie wykrywania &| <ul><li>[Inspekcja](../../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji/ aktywności na [koncie usługi Azure Storage.](../../storage/common/storage-create-storage-account.md)</li><li>Śledzenie kondycji bazy danych usługi Azure przy użyciu [dzienników aktywności usługi Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md).</li><li>[Wykrywanie zagrożeń](../../sql-database/sql-database-threat-detection.md) wykrywa nietypowe działania bazy danych wskazujące potencjalne zagrożenia bezpieczeństwa dla bazy danych. </li></ul> |
|<br>Azure Security Center| <ul><li>[Monitorowanie danych](../../security-center/security-center-enable-auditing-on-sql-databases.md) Użyj usługi Azure Security Center jako scentralizowane rozwiązanie do monitorowania zabezpieczeń dla języka SQL i innych usług platformy Azure.</li></ul>|        

## <a name="conclusion"></a>Podsumowanie
Usługa Azure Database to niezawodna platforma bazy danych z pełną gamą funkcji zabezpieczeń, które spełniają wiele wymagań dotyczących zgodności z przepisami organizacyjnymi i regulacyjnymi. Można łatwo chronić dane, kontrolując fizyczny dostęp do danych i korzystając z różnych opcji zabezpieczeń danych na poziomie plików, kolumn lub wierszy za pomocą przezroczystego szyfrowania danych, szyfrowania na poziomie komórki lub zabezpieczeń na poziomie wiersza. Zawsze szyfrowane umożliwia również operacje przeciwko zaszyfrowanym danym, upraszczając proces aktualizacji aplikacji. Z kolei dostęp do dzienników inspekcji działania bazy danych SQL zapewnia potrzebne informacje, dzięki czemu można wiedzieć, jak i kiedy dane są dostępne.

## <a name="next-steps"></a>Następne kroki
W kilku prostych krokach możesz poprawić zabezpieczenia bazy danych, aby chronić ją przed złośliwymi użytkownikami i nieautoryzowanym dostępem. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie [reguł zapory](../../sql-database/sql-database-firewall-configure.md) dla serwera i bazy danych.
- Chroń swoje dane za pomocą [szyfrowania](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Włącz [inspekcję bazy danych SQL](../../sql-database/sql-database-auditing.md).

