---
title: Zaawansowana ochrona przed zagrożeniami — usługa Azure Database for MySQL
description: Poznaj pojęcia dotyczące zaawansowanej ochrony przed zagrożeniami, która wykrywa nietypowe działania bazy danych wskazujące potencjalne zagrożenia bezpieczeństwa dla bazy danych.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 01ac6ccbc2789d2052bab07e2da51630b6dbf581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537163"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure Database for MySQL Advanced Threat Protection

Usługa Advanced Threat Protection dla usługi Azure Database for MySQL wykrywa nietypowe działania wskazujące na niestandardowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich.

> [!NOTE]
> Zaawansowana ochrona przed zagrożeniami jest w publicznej wersji zapoznawczej.

Zaawansowana ochrona przed zagrożeniami jest częścią oferty Advanced Data Security, która jest ujednoliconym pakietem dla zaawansowanych funkcji zabezpieczeń. Zaawansowana ochrona przed zagrożeniami jest dostępna i zarządzana za pośrednictwem [portalu Azure](https://portal.azure.com) lub interfejsu [API REST.](/rest/api/mysql/serversecurityalertpolicies) Ta funkcja jest dostępna dla serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

> [!NOTE]
> Funkcja Zaawansowanej ochrony przed zagrożeniami **nie** jest dostępna w następujących regionach rządowych platformy Azure i suwerennych chmurach: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Aby zapoznać się z ogólną dostępnością produktów, należy zapoznać się z produktami [dostępnymi w](https://azure.microsoft.com/global-infrastructure/services/) pozycyjności.


## <a name="what-is-advanced-threat-protection"></a>Co to jest zaawansowana ochrona przed zagrożeniami?

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for MySQL zapewnia nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie, ponieważ występują one, zapewniając alerty zabezpieczeń dotyczące nietypowych działań. Użytkownicy otrzymują alert o podejrzanych działaniach bazy danych i potencjalnych lukach w zabezpieczeniach, a także nietypowych wzorcach dostępu do bazy danych i zapytań. Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for MySQL integruje alerty z [usługą Azure Security Center](https://azure.microsoft.com/services/security-center/), która zawiera szczegółowe informacje o podejrzanej aktywności i zaleca działania dotyczące sposobu badania i łagodzenia zagrożenia. Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for MySQL ułatwia rozwiązywanie potencjalnych zagrożeń bazy danych bez konieczności bycia ekspertem w dziedzinie zabezpieczeń lub zarządzania zaawansowanymi systemami monitorowania zabezpieczeń. 

![Zaawansowana koncepcja ochrony przed zagrożeniami](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Zaawansowane alerty ochrony przed zagrożeniami 
Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for MySQL wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania i może wyzwolić następujące alerty:
- **Dostęp z nietypowej lokalizacji:** Ten alert jest wyzwalany, gdy nastąpi zmiana wzorca dostępu do usługi Azure Database dla serwera MySQL, gdzie ktoś zalogował się do usługi Azure Database dla serwera MySQL z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nietypowego centrum danych platformy Azure:** Ten alert jest wyzwalany, gdy nastąpi zmiana wzorca dostępu do usługi Azure Database dla serwera MySQL, gdzie ktoś zalogował się na serwerze z nietypowego centrum danych platformy Azure, który był widoczny na tym serwerze w ostatnim okresie. W niektórych przypadkach alert wykrywa prawidłowe działanie (nowa aplikacja na platformie Azure, Power BI, Azure Database for MySQL Query Editor). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nieznanego podmiotu:** Ten alert jest wyzwalany, gdy nastąpi zmiana wzorca dostępu do usługi Azure Database dla serwera MySQL, gdzie ktoś zalogował się do serwera przy użyciu nietypowego podmiotu zabezpieczeń (azure database dla użytkownika MySQL). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana w celu uzyskania dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
- **Brute życie Usługi Azure Database dla poświadczeń MySQL:** Ten alert jest wyzwalany, gdy istnieje nieprawidłowa duża liczba nie powiodło się logowania z różnych poświadczeń. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Aby uzyskać więcej informacji na temat cen, zobacz [stronę cennik usługi Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/) 
* Konfigurowanie [usługi Azure Database dla zaawansowanej ochrony przed zagrożeniami MySQL](howto-database-threat-protection-portal.md) przy użyciu witryny Azure portal  
