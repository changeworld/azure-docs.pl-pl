---
title: Zaawansowana ochrona przed zagrożeniami — Azure Database for PostgreSQL
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/20/2018
ms.openlocfilehash: 68027526fb98db4cb5b7eaf75f117692222c6039
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53549024"
---
# <a name="azure-database-for-postgresql-advanced-threat-protection"></a>Azure Database for postgresql — Zaawansowana ochrona przed zagrożeniami

Usługa Advanced Threat Protection dla usługi Azure Database for PostgreSQL wykrywa nietypowe działania wskazujące na nieprawidłowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich.

Ochrona przed zagrożeniami jest częścią oferty zaawansowanej ochrony przed zagrożeniami (ATP), która to ujednolicone pakiet dla zaawansowane funkcje zabezpieczeń. Zaawansowana ochrona przed zagrożeniami mogą być dostępne i zarządzane za pośrednictwem [witryny Azure portal](https://portal.azure.com) i jest obecnie w wersji zapoznawczej.

> [!NOTE]
> Funkcja zaawansowanej ochrony przed zagrożeniami jest **nie** dostępne w następujących platformy Azure dla instytucji rządowych i regiony należących do suwerennej chmury: Wschodnie DoD programu Microsoft Gov Teksas stany USA, administracja USA — Arizona Iowa Gov, US, — Wirginia, US Gov, US dod — środkowe, Niemcy środkowe, Niemcy Północne, Chiny wschodnie, Chiny wschodnie stany USA 2. Odwiedź stronę [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/) dostępności głównej produktu.
>

## <a name="what-is-advanced-threat-protection"></a>Co to jest Zaawansowana ochrona przed zagrożeniami?

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for PostgreSQL udostępnia nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie i odpowiadanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku wystąpienia nietypowych działań. Użytkownicy otrzymują alert po podejrzanych działaniach bazy danych oraz potencjalnych luk w zabezpieczeniach, a także wzorce dostępu i zapytania nietypowego bazy danych. Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for postgresql w warstwie integruje alerty z usługą [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/), który zawiera szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia. Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for PostgreSQL upraszcza reagowanie na potencjalne zagrożenia bazy danych bez konieczności dysponowania eksperta zabezpieczeń lub zarządzania zabezpieczeniami zaawansowanymi systemami monitorowania. 

![Advanced Threat Protection pojęcia](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Zaawansowana ochrona przed zagrożeniami alertów 
Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for PostgreSQL wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych i może wywołać następujące alerty:
- **Dostęp z nietypowej lokalizacji**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do usługi Azure Database for postgresql w warstwie serwera, gdy ktoś zalogował się do usługi Azure Database for postgresql w warstwie serwera z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z centrum danych platformy Azure nietypowe**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do usługi Azure Database for postgresql w warstwie serwera, gdy ktoś zalogował się do serwera z centrum danych platformy Azure, co zostało zaobserwowane na tym serwerze w ostatnim czasie. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (Nowa aplikacja w Azure, usługa Power BI — Azure Database for postgresql — Edytor zapytań). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nieznanego podmiotu zabezpieczeń**: Ten alert jest wyzwalany, gdy występuje w przypadku zmiany wzorca dostępu do usługi Azure Database for postgresql w warstwie serwera, gdy ktoś zalogował się na serwerze przy użyciu nietypowego podmiotu zabezpieczeń (— Azure Database for postgresql w warstwie użytkownika). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana do dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
- **Siłowe — Azure Database for postgresql — poświadczeń**: Ten alert jest wyzwalany, gdy ma nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Aby uzyskać więcej informacji na temat cen, zobacz [— Azure Database for PostgreSQL ceny strony](https://azure.microsoft.com/pricing/details/postgresql/) 
* Konfigurowanie [— Azure Database for postgresql w warstwie zaawansowanej ochrony przed zagrożeniami](howto-database-threat-protection-portal.md) przy użyciu witryny Azure portal  
