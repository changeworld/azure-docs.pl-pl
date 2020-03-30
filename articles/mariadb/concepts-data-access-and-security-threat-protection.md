---
title: Zaawansowana ochrona przed zagrożeniami — usługa Azure Database dla bazy danych MariaDB
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące potencjalne zagrożenia bezpieczeństwa dla bazy danych.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8485907eba75f80c8f0ed4fd0cc7368c6147b9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532182"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Usługa Azure Database dla zaawansowanej ochrony przed zagrożeniami MariaDB

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database dla mariadb wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

> [!IMPORTANT]
> Zaawansowana ochrona przed zagrożeniami jest w publicznej wersji zapoznawczej.

Zaawansowana ochrona przed zagrożeniami jest częścią oferty Advanced Data Security, która jest ujednoliconym pakietem dla zaawansowanych funkcji zabezpieczeń. Zaawansowana ochrona przed zagrożeniami jest dostępna i zarządzana za pośrednictwem [portalu Azure.](https://portal.azure.com) Ta funkcja jest dostępna dla serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

> [!NOTE]
> Funkcja Zaawansowanej ochrony przed zagrożeniami **nie** jest dostępna w następujących regionach rządowych platformy Azure i suwerennych chmurach: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Aby zapoznać się z ogólną dostępnością produktów, należy zapoznać się z produktami [dostępnymi w](https://azure.microsoft.com/global-infrastructure/services/) pozycyjności.


## <a name="what-is-advanced-threat-protection"></a>Co to jest zaawansowana ochrona przed zagrożeniami?

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for MariaDB zapewnia nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie, ponieważ występują one, udostępniając alerty zabezpieczeń dotyczące nietypowych działań. Użytkownicy otrzymują alert o podejrzanych działaniach bazy danych i potencjalnych lukach w zabezpieczeniach, a także nietypowych wzorcach dostępu do bazy danych i zapytań. Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for MariaDB integruje alerty z [usługą Azure Security Center](https://azure.microsoft.com/services/security-center/), która zawiera szczegółowe informacje o podejrzanej aktywności i zaleca działania dotyczące sposobu badania i łagodzenia zagrożenia. Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database dla bazy danych MariaDB ułatwia rozwiązywanie potencjalnych zagrożeń bazy danych bez konieczności bycia ekspertem w dziedzinie zabezpieczeń lub zarządzania zaawansowanymi systemami monitorowania zabezpieczeń. 

![Zaawansowana koncepcja ochrony przed zagrożeniami](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Zaawansowane alerty ochrony przed zagrożeniami 
Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database dla mariadb wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania i może wyzwolić następujące alerty:
- **Dostęp z nietypowej lokalizacji:** Ten alert jest wyzwalany, gdy nastąpi zmiana wzorca dostępu do usługi Azure Database dla serwera MariaDB, gdzie ktoś zalogował się do usługi Azure Database dla serwera MariaDB z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nietypowego centrum danych platformy Azure:** Ten alert jest wyzwalany, gdy nastąpi zmiana wzorca dostępu do usługi Azure Database dla serwera MariaDB, gdzie ktoś zalogował się na serwerze z nietypowego centrum danych platformy Azure, który był widoczny na tym serwerze w ostatnim okresie. W niektórych przypadkach alert wykrywa prawidłowe działanie (nowa aplikacja na platformie Azure, usługa Power BI). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nieznanego podmiotu:** Ten alert jest wyzwalany, gdy nastąpi zmiana wzorca dostępu do usługi Azure Database dla serwera MariaDB, gdzie ktoś zalogował się na serwerze przy użyciu nietypowego podmiotu zabezpieczeń (usługa Azure Database dla użytkownika MariaDB). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana w celu uzyskania dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
- **Brute wymuś usługę Azure Database dla poświadczeń MariaDB:** Ten alert jest wyzwalany, gdy istnieje nieprawidłowa duża liczba nieudanych logowania z różnymi poświadczeniami. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Aby uzyskać więcej informacji na temat cen, zobacz [stronę cennik usługi Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) 
* Konfigurowanie [usługi Azure Database dla zaawansowanej ochrony przed zagrożeniami MariaDB](howto-database-threat-protection-portal.md) przy użyciu portalu Azure  
