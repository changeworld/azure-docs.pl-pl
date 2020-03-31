---
title: Zaawansowana ochrona przed zagrożeniami — witryna Azure portal — usługa Azure Database for MariaDB
description: Ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące potencjalne zagrożenia bezpieczeństwa dla bazy danych.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: de056f61337311642589ba937c6d9842d8354d36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530142"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Zaawansowana ochrona przed zagrożeniami dla bazy danych platformy Azure dla bazy danych MariaDB

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database dla mariadb wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Zaawansowana ochrona przed zagrożeniami jest częścią oferty Advanced Data Security, która jest ujednoliconym pakietem dla zaawansowanych funkcji zabezpieczeń. Zaawansowana ochrona przed zagrożeniami jest dostępna i zarządzana za pośrednictwem [portalu Azure.](https://portal.azure.com)

> [!IMPORTANT]
> Zaawansowana ochrona przed zagrożeniami jest w publicznej wersji zapoznawczej. Ta funkcja jest dostępna we wszystkich regionach platformy Azure, gdzie usługa Azure Database dla mariadb jest wdrażana dla serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

> [!NOTE]
> Funkcja Zaawansowanej ochrony przed zagrożeniami **nie** jest dostępna w następujących regionach rządowych platformy Azure i suwerennych chmurach: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Aby zapoznać się z ogólną dostępnością produktów, należy zapoznać się z produktami [dostępnymi w](https://azure.microsoft.com/global-infrastructure/services/) pozycyjności.

## <a name="set-up-threat-detection"></a>Konfigurowanie wykrywania zagrożeń
1. Uruchom portal Platformy [https://portal.azure.com](https://portal.azure.com)Azure pod adresem .
2. Przejdź do strony konfiguracji serwera usługi Azure Database dla mariadb, który chcesz chronić. W ustawieniach zabezpieczeń wybierz pozycję **Zaawansowana ochrona przed zagrożeniami (wersja zapoznawcza)**.
3. Na stronie konfiguracji **Zaawansowana ochrona przed zagrożeniami (wersja zapoznawcza):**

   - Włącz zaawansowaną ochronę przed zagrożeniami na serwerze.
   - W **obszarze Zaawansowane ustawienia ochrony przed zagrożeniami**w polu tekstowym Wyślij **alerty do** podaj listę wiadomości e-mail, które będą otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
  
   ![Konfigurowanie wykrywania zagrożeń](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Eksploruj nietypowe działania bazy danych

Otrzymasz powiadomienie e-mail po wykryciu nietypowych działań bazy danych. Wiadomość e-mail zawiera informacje o podejrzanym zdarzeniu zabezpieczeń, w tym charakter nietypowych działań, nazwę bazy danych, nazwę serwera, nazwę aplikacji i czas zdarzenia. Ponadto wiadomość e-mail zawiera informacje na temat możliwych przyczyn i zalecane działania w celu zbadania i złagodzenia potencjalnego zagrożenia dla bazy danych.
 
1. Kliknij **łącze Wyświetl ostatnie alerty** w wiadomości e-mail, aby uruchomić witrynę Azure portal i wyświetlić stronę alertów usługi Azure Security Center, która zawiera omówienie aktywnych zagrożeń wykrytych w bazie danych SQL.
    
    ![Nietypowy raport aktywności](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Wyświetlanie aktywnych zagrożeń:

    ![Aktywne zagrożenia](./media/howto-database-threat-protection-portal/active-threats.png)

2. Kliknij określony alert, aby uzyskać dodatkowe szczegóły i działania dotyczące badania tego zagrożenia i korygowania przyszłych zagrożeń.
    
    ![Konkretny alert](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Eksplorowanie alertów o wykrywaniu zagrożeń

Wykrywanie zagrożeń bazy danych SQL integruje swoje alerty z [usługą Azure Security Center](https://azure.microsoft.com/services/security-center/). Kafelki wykrywania zagrożeń sql na żywo w bazie danych i bloków SQL ATP w witrynie Azure Portal śledzi stan aktywnych zagrożeń.

Kliknij **alert wykrywania zagrożeń,** aby uruchomić stronę alertów usługi Azure Security Center i uzyskać przegląd aktywnych zagrożeń SQL wykrytych w bazie danych.

   ![Alert wykrywania zagrożeń](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Aby uzyskać więcej informacji na temat cen, zobacz [stronę cennik usługi Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/)  
