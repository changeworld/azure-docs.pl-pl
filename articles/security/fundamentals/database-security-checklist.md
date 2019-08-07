---
title: Lista kontrolna zabezpieczeń usługi Azure Database | Microsoft Docs
description: Ten artykuł zawiera zestaw list kontrolnych dotyczących zabezpieczeń usługi Azure Database.
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
ms.openlocfilehash: bf11d7a9dbaa37db521da67deddbed977cc1d251
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780548"
---
# <a name="azure-database-security-checklist"></a>Lista kontrolna zabezpieczeń usługi Azure Database

Aby pomóc w ulepszaniu zabezpieczeń, usługa Azure Database zawiera szereg wbudowanych mechanizmów kontroli zabezpieczeń, których można użyć do ograniczania i kontrolowania dostępu.

Należą do nich:

-   Zapora umożliwiająca tworzenie [reguł zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ograniczających łączność przy użyciu adresu IP
-   Zapora na poziomie serwera dostępna z Azure Portal
-   Reguły zapory na poziomie bazy danych dostępne w programie SSMS
-   Zabezpieczanie łączności z bazą danych przy użyciu bezpiecznych parametrów połączenia
-   Korzystanie z zarządzania dostępem
-   Szyfrowanie danych
-   Inspekcja usługi SQL Database
-   Wykrywanie zagrożeń usługi SQL Database

## <a name="introduction"></a>Wprowadzenie
Chmura obliczeniowa wymaga nowych odmian zabezpieczeń, które nie są znane dla wielu użytkowników aplikacji, administratorów bazy danych i programistów. W związku z tym niektóre organizacje są wątpliwości do wdrożenia infrastruktury chmurowej na potrzeby zarządzania danymi ze względu na postrzegane zagrożenia bezpieczeństwa. Wiele z tych problemów można jednak poprawić, aby lepiej zrozumieć funkcje zabezpieczeń wbudowane w Microsoft Azure i Microsoft Azure SQL Database.

## <a name="checklist"></a>Lista kontrolna
Zalecamy zapoznanie się z artykułem [najlepsze rozwiązania w zakresie zabezpieczeń usługi Azure Database](https://docs.microsoft.com/azure/security/fundamentals/database-best-practices) przed przejrzeniem tej listy kontrolnej. Po zrozumieniu najlepszych rozwiązań będzie można maksymalnie wykorzystać tę listę kontrolną. Następnie można użyć tej listy kontrolnej, aby upewnić się, że zostały rozwiązane ważne problemy w zabezpieczeniach usługi Azure Database.


|Kategoria listy kontrolnej| Opis|
| ------------ | -------- |
|**Ochrona danych**||
| <br> Szyfrowanie w ruchu/tranzycie| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol)do szyfrowania danych, gdy dane są przenoszone do sieci.</li><li>Baza danych wymaga bezpiecznej komunikacji od klientów opartych na protokole [TDS (strumienia danych tabelarycznych)](https://msdn.microsoft.com/library/dd357628.aspx) za pośrednictwem protokołu TLS (Transport Layer Security).</li></ul> |
|<br>Szyfrowanie w spoczynku| <ul><li>[Transparent Data Encryption](https://go.microsoft.com/fwlink/?LinkId=526242), gdy nieaktywne dane są przechowywane fizycznie w dowolnym formularzu cyfrowym.</li></ul>|
|**Kontrola dostępu**||  
|<br> Dostęp do bazy danych | <ul><li>[Uwierzytelnianie](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) (Azure Active Directory uwierzytelniania) Uwierzytelnianie usługi AD używa tożsamości zarządzanych przez Azure Active Directory.</li><li>[Autoryzacja](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) użytkowników przyznająca im najmniejsze uprawnienia.</li></ul> |
|<br>Dostęp do aplikacji| <ul><li>[Zabezpieczenia na poziomie wiersza](https://msdn.microsoft.com/library/dn765131) (Przy użyciu zasad zabezpieczeń, jednocześnie ograniczając dostęp na poziomie wierszy na podstawie tożsamości użytkownika, roli lub kontekstu wykonania).</li><li>[Dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (Przy użyciu zasad & uprawnień, ograniczanie narażenia na dane poufne przez zamaskowanie go dla użytkowników bez uprawnień)</li></ul>|
|**Proaktywne monitorowanie**||  
| <br>Śledzenie wykrywania &| <ul><li>[Inspekcja](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji/dzienniku aktywności na [koncie usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account).</li><li>Śledź usługę Azure Database Health przy użyciu [dzienników aktywności Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Wykrywanie zagrożeń](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych. </li></ul> |
|<br>Azure Security Center| <ul><li>[Monitorowanie danych](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases) Użyj Azure Security Center jako scentralizowanego rozwiązania do monitorowania zabezpieczeń dla SQL i innych usług platformy Azure.</li></ul>|       

## <a name="conclusion"></a>Wniosek
Azure Database to niezawodna platforma baz danych z pełnymi funkcjami zabezpieczeń, które spełniają wiele wymagań w zakresie organizacji i zgodności. Możesz łatwo chronić dane, kontrolując fizyczny dostęp do danych i korzystając z różnych opcji zabezpieczeń danych na poziomie plików, kolumn lub wierszy z Transparent Data Encryption, szyfrowanie na poziomie komórki lub zabezpieczenia na poziomie wiersza. Always Encrypted włącza również operacje związane z zaszyfrowanymi danymi, upraszczając proces aktualizacji aplikacji. Z kolei dostęp do dzienników inspekcji SQL Database działania zawiera informacje, które są potrzebne, co pozwala dowiedzieć się, jak i kiedy dane są dostępne.

## <a name="next-steps"></a>Kolejne kroki
W kilku prostych krokach możesz poprawić zabezpieczenia bazy danych, aby chronić ją przed złośliwymi użytkownikami i nieautoryzowanym dostępem. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

- Skonfiguruj [reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) dla serwera i bazy danych.
- Ochrona danych za pomocą [szyfrowania](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Włącz [inspekcję SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).

