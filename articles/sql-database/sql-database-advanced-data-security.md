---
title: Zaawansowane zabezpieczenia danych
description: Dowiedz się więcej o funkcjach wykrywania i klasyfikowania poufnych danych, zarządzania lukami w zabezpieczeniach bazy danych i wykrywania nietypowych działań, które mogą wskazywać na zagrożenie dla bazy danych SQL platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: 1f0e6694e596dc60264dfe0789a2f80090e0da3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269135"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Zaawansowane zabezpieczenia danych dla usługi Azure SQL Database

Zaawansowane zabezpieczenia danych to ujednolicony pakiet dla zaawansowanych funkcji zabezpieczeń SQL. Obejmuje to funkcję wykrywania i klasyfikowania danych poufnych, uwidacznianie i korygowanie potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywanie nietypowych działań, które mogą wskazywać, że baza danych jest zagrożona. Zapewnia ona pojedynczą lokalizację, w której można włączać te możliwości i zarządzać nimi.

## <a name="overview"></a>Omówienie

Zaawansowane zabezpieczenia danych (ADS) zapewniają zestaw zaawansowanych funkcji zabezpieczeń SQL, w tym odnajdowania danych & klasyfikacji, ocenie luk w zabezpieczeniach i zaawansowanej ochronie przed zagrożeniami.

- [Odnajdowanie danych & klasyfikacji](sql-database-data-discovery-and-classification.md) zapewnia możliwości wbudowane w usługę Azure SQL Database do odnajdowania, klasyfikowania, etykietowania & ochrony poufnych danych w bazach danych. Za jej pomocą można zapewniać wgląd w stan klasyfikacji bazy danych oraz śledzić dostęp do danych poufnych w bazie danych i poza jej granicami.
- [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) to łatwa w konfiguracji usługa, która może wykrywać, śledzić i pomagać w korygowania potencjalnych luk w zabezpieczeniach bazy danych. Zapewnia wgląd w stan zabezpieczeń i zapewnia kroki, które można wykonać w celu rozwiązania problemów z zabezpieczeniami oraz zwiększenia bezpieczeństwa bazy danych.
- Usługa [Advanced Threat Protection](sql-database-threat-detection-overview.md) wykrywa nietypowe działania wskazujące na nieprawidłowe i potencjalnie szkodliwe próby uzyskania dostępu do bazy danych lub wykorzystania jej. Stale monitoruje Twoją bazę danych pod kątem podejrzanych działań i zapewnia natychmiastowe alerty zabezpieczeń dotyczące potencjalnych luk w zabezpieczeniach, ataków polegających na wstrzyknięciu kodu SQL oraz anomalii we wzorcach dostępu do bazy danych. Alerty usługi Advanced Threat Protection zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.

Włącz usługę SQL ADS raz, aby włączyć wszystkie te dołączone funkcje. Za pomocą jednego kliknięcia można włączyć usługi ADS dla wszystkich baz danych na serwerze bazy danych SQL lub wystąpieniu zarządzanym. Włączanie ustawień ADS lub zarządzanie nimi wymaga przynależności do roli [menedżera zabezpieczeń SQL,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) roli administratora bazy danych SQL lub roli administratora serwera SQL. 

Ceny ads są zgodne ze standardową warstwą usługi Azure Security Center, w której każdy chroniony serwer bazy danych SQL lub wystąpienie zarządzane jest liczone jako jeden węzeł. Nowo chronione zasoby kwalifikują się do bezpłatnej wersji próbnej warstwy standardowej centrum zabezpieczeń. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem usługi Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Wprowadzenie do ads

Poniższe kroki umożliwiają rozpoczęcie pracy z programem ADS.

## <a name="1-enable-ads"></a>1. Włącz reklamy

Włącz usługę ADS, przechodząc do **pozycji Zaawansowane zabezpieczenia danych** w nagłówku **Zabezpieczenia** serwera bazy danych SQL lub wystąpienia zarządzanego. Aby włączyć usługę ADS dla wszystkich baz danych na serwerze bazy danych lub wystąpieniu zarządzanym, kliknij pozycję **Włącz zaawansowane zabezpieczenia danych na serwerze**.

> [!NOTE]
> Konto magazynu jest automatycznie tworzone i konfigurowane do przechowywania wyników skanowania **oceny luk w zabezpieczeniach.** Jeśli włączono już usługę ADS dla innego serwera w tej samej grupie zasobów i regionie, używane jest istniejące konto magazynu.

![Włącz reklamy](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> Koszt usługi ADS jest wyrównany ze standardowymi cenami warstwy Azure Security Center za węzeł, gdzie węzeł jest całym serwerem bazy danych SQL lub wystąpieniem zarządzanym. W ten sposób płacisz tylko raz za ochronę wszystkich baz danych na serwerze bazy danych lub wystąpieniu zarządzanym za pomocą usługi ADS. Możesz wypróbować reklamy początkowo z bezpłatną wersję próbną.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Rozpocznij klasyfikowanie danych, śledzenie luk w zabezpieczeniach i badanie alertów o zagrożeniach

Kliknij kartę **Odnajdowanie danych & Klasyfikacja,** aby wyświetlić zalecane poufne kolumny do klasyfikowania i klasyfikowania danych za pomocą trwałych etykiet czułości. Kliknij kartę Ocena luk w **zabezpieczeniach,** aby wyświetlić skany i raporty dotyczące luk w zabezpieczeniach i zarządzać nimi, a także śledzić stan zabezpieczeń. Jeśli odebrano alerty zabezpieczeń, kliknij kartę **Zaawansowanej ochrony przed zagrożeniami,** aby wyświetlić szczegóły alertów i wyświetlić skonsolidowany raport o wszystkich alertach w subskrypcji platformy Azure za pośrednictwem strony alertów zabezpieczeń usługi Azure Security Center.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Zarządzanie ustawieniami ADS na serwerze bazy danych SQL lub wystąpieniu zarządzanym

Aby wyświetlić ustawienia usługi ADS i zarządzać nimi, przejdź do pozycji **Zaawansowane zabezpieczenia danych** w nagłówku **Zabezpieczenia** serwera bazy danych SQL lub wystąpienia zarządzanego. Na tej stronie można włączyć lub wyłączyć usługę ADS oraz zmodyfikować ustawienia oceny luk w zabezpieczeniach i zaawansowanej ochrony przed zagrożeniami dla całego serwera bazy danych SQL lub wystąpienia zarządzanego.

![Ustawienia serwera](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Zarządzanie ustawieniami ADS dla bazy danych SQL

Aby zastąpić ustawienia ADS dla określonej bazy danych, zaznacz pole wyboru **Włącz zaawansowane zabezpieczenia danych na poziomie bazy danych.** Tej opcji należy używać tylko wtedy, gdy istnieje szczególne wymagania dotyczące otrzymywania oddzielnych alertów zaawansowanej ochrony przed zagrożeniami lub wyników oceny luk w zabezpieczeniach dla poszczególnych baz danych, zamiast lub oprócz alertów i wyników otrzymanych dla wszystkich baz danych na serwera bazy danych lub wystąpienia zarządzanego.

Po wybraniu pola wyboru można następnie skonfigurować odpowiednie ustawienia dla tej bazy danych.
 
![Ustawienia bazy danych i zaawansowanej ochrony przed zagrożeniami](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Zaawansowane ustawienia zabezpieczeń danych serwera bazy danych lub wystąpienia zarządzanego można również uzyskać z okienka bazy danych ADS. Kliknij **pozycję Ustawienia** w głównym okienku ADS, a następnie kliknij pozycję Wyświetl ustawienia serwera **zaawansowanego zabezpieczenia danych**. 

![Ustawienia bazy danych](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Następne kroki 

- Dowiedz się więcej o [klasyfikacji & odnajdowania danych](sql-database-data-discovery-and-classification.md) 
- Dowiedz się więcej o [ocenie luk w zabezpieczeniach](sql-vulnerability-assessment.md) 
- Dowiedz się więcej o [zaawansowanej ochronie przed zagrożeniami](sql-database-threat-detection.md)
- Dowiedz się więcej o [Centrum zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
