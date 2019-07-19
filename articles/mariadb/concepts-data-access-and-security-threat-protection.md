---
title: Zaawansowana ochrona przed zagrożeniami — Azure Database for MariaDB | Microsoft Docs
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: a96d8c3b9f9e3f9b1aedf269bce141da3e4ccf42
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869692"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Azure Database for MariaDB zaawansowaną ochronę przed zagrożeniami

Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

> [!IMPORTANT]
> Zaawansowana ochrona przed zagrożeniami jest w publicznej wersji zapoznawczej.

Zaawansowana ochrona przed zagrożeniami jest częścią zaawansowanej oferty zabezpieczeń danych, która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń. Dostęp do zaawansowanej ochrony przed zagrożeniami można uzyskać i zarządzać nimi za pośrednictwem [Azure Portal](https://portal.azure.com). Ta funkcja jest dostępna dla serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

> [!NOTE]
> Funkcja zaawansowanej ochrony przed zagrożeniami **nie** jest dostępna w następujących regionach na platformie Azure dla instytucji rządowych i w ramach suwerennej chmury: US Gov Teksas, US Gov Arizona, US Gov Iowa, US, gov Wirginia, US DoD (region wschodni), US DoD (region środkowy), Niemcy środkowe, Niemcy Północne, Chiny Wschodnie, Chiny Wschodnie 2. Odwiedź stronę [dostępne produkty według regionów,](https://azure.microsoft.com/global-infrastructure/services/) Aby uzyskać ogólną dostępność produktu.


## <a name="what-is-advanced-threat-protection"></a>Co to jest zaawansowana ochrona przed zagrożeniami?

Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB zapewnia nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku nietypowych działań. Użytkownicy otrzymują alerty o podejrzanych działaniach związanych z bazą danych oraz potencjalne luki w zabezpieczeniach, a także nietypowego dostępu do bazy danych i wzorców zapytań. Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB integruje alerty z [Azure Security Center](https://azure.microsoft.com/services/security-center/), które zawierają szczegółowe informacje o podejrzanych działaniach i zalecane działania dotyczące badania i łagodzenia zagrożeń. Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB ułatwia rozwiązywanie potencjalnych zagrożeń dla bazy danych bez konieczności zapewnienia ekspertowi zabezpieczeń lub zarządzania zaawansowanymi systemami monitorowania zabezpieczeń. 

![Koncepcja zaawansowanej ochrony przed zagrożeniami](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alerty zaawansowanej ochrony przed zagrożeniami 
Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania i może wyzwolić następujące alerty:
- **Dostęp z nietypowej lokalizacji**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera Azure Database for MariaDB, w którym ktoś zalogował się do serwera Azure Database for MariaDB z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nietypowego centrum danych platformy Azure**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera Azure Database for MariaDB, w którym ktoś zalogował się na serwerze z nietypowego centrum danych platformy Azure, które było widoczne na tym serwerze w ostatnim okresie. W niektórych przypadkach alert wykrywa legalną akcję (nową aplikację na platformie Azure, Power BI). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nieznanego podmiotu zabezpieczeń**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera Azure Database for MariaDB, w którym ktoś zalogował się na serwerze przy użyciu nietypowego podmiotu zabezpieczeń (Azure Database for MariaDB użytkownika). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy do uzyskiwania dostępu do bazy danych jest używana potencjalnie szkodliwa aplikacja. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
- **Wymuszanie Azure Database for MariaDB poświadczeń**: Ten alert jest wyzwalany, gdy występuje nietypowa duża liczba nieudanych logowań z innymi poświadczeniami. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Aby uzyskać więcej informacji na temat cen, zobacz [stronę z cennikiem Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) . 
* Konfigurowanie [Azure Database for MariaDB zaawansowanej ochrony przed zagrożeniami](howto-database-threat-protection-portal.md) przy użyciu Azure Portal  
