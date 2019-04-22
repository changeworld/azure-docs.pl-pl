---
title: Zaawansowane zabezpieczenia danych — usługa Azure SQL Database | Dokumentacja firmy Microsoft
description: Więcej informacji na temat funkcji Odnajdywanie i klasyfikowanie danych poufnych, zarządzanie swojej bazy danych, luk w zabezpieczeniach i wykrywanie nietypowych działań, które mogą wskazywać na zagrożenie dla usługi Azure SQL database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: monhaber
ms.reviewer: vanto
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: a078ac38cef5b395a19481188c474c7f908160d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784487"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Zabezpieczenia zaawansowanych danych dla usługi Azure SQL Database

Zabezpieczeń zaawansowanych danych to ujednolicone pakiet dla zaawansowane funkcje zabezpieczeń programu SQL. Obejmuje funkcje odnajdywania, klasyfikowania poufnych danych, dzięki czemu są ujawniane i łagodzące potencjalne luki bazy danych i wykrywanie nietypowych działań wskazujących zagrożenie z bazą danych. Zapewnia ona pojedynczą lokalizację, w której można włączać te możliwości i zarządzać nimi.

## <a name="overview"></a>Omówienie

Bezpieczeństwo danych Zaawansowane (AD) zapewnia zestaw zaawansowanych funkcji zabezpieczeń programu SQL, w tym odnajdywanie danych i klasyfikacji, ocena luk w zabezpieczeniach oraz Zaawansowana ochrona przed zagrożeniami.

- [Odnajdywanie i klasyfikacja danych](sql-database-data-discovery-and-classification.md) (obecnie w wersji zapoznawczej) zawiera funkcje wbudowane w usłudze Azure SQL Database na potrzeby odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Za jej pomocą można zapewniać wgląd w stan klasyfikacji bazy danych oraz śledzić dostęp do danych poufnych w bazie danych i poza jej granicami.
- [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) jest łatwa do skonfigurowania usługi odnajdywania, śledzenie i ułatwić korygowanie potencjalnych luk w bazie danych. Zapewnia wgląd w stan zabezpieczeń i zapewnia kroki, które można wykonać w celu rozwiązania problemów z zabezpieczeniami oraz zwiększenia bezpieczeństwa bazy danych.
- [Zaawansowana ochrona przed zagrożeniami](sql-database-threat-detection-overview.md) wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania bazy danych. Stale monitoruje Twoją bazę danych pod kątem podejrzanych działań i zapewnia natychmiastowe alerty zabezpieczeń dotyczące potencjalnych luk w zabezpieczeniach, ataków polegających na wstrzyknięciu kodu SQL oraz anomalii we wzorcach dostępu do bazy danych. Zaawansowana ochrona przed zagrożeniami alerty zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.

Włącz SQL REKLAM po włączyć wszystkie z nich uwzględnione funkcje. Jednym kliknięciem można włączyć AD dla wszystkich baz danych na serwerze bazy danych SQL Database lub wystąpienia zarządzanego. Włączanie i zarządzanie ustawieniami REKLAM wymaga należący do [Menedżer zabezpieczeń SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) roli, do roli administratora bazy danych SQL lub rola administratora programu SQL server. 

Wyrównuje REKLAM, ceny za pomocą usługi Azure Security Center w warstwie standardowa gdzie każdy chroniony serwer usługi SQL Database lub wystąpienia zarządzanego jest liczony jako jeden węzeł. Nowo chronionych zasobów kwalifikuje się do bezpłatnej wersji próbnej usługi Security Center w warstwie standardowa. Aby uzyskać więcej informacji, zobacz [usługi Azure Security Center, na stronie z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Rozpoczęcie korzystania z REKLAM

Poniższe kroki ułatwiające rozpoczęcie pracy z reklamy.

## <a name="1-enable-ads"></a>1. Włącz REKLAM

Włącz REKLAM, przechodząc do **zaawansowanych danych** w obszarze **zabezpieczeń** nagłówka dla serwera bazy danych SQL Database lub wystąpienia zarządzanych. Aby włączyć REKLAM, dla wszystkich baz danych na serwerze bazy danych lub wystąpienia zarządzanego, kliknij przycisk **Włącz zaawansowane zabezpieczenia danych na serwerze**.

> [!NOTE]
> Konto magazynu jest automatycznie tworzony i skonfigurowane do przechowywania Twojego **oceny luk w zabezpieczeniach** wyników skanowania. Jeśli REKLAM już została włączona dla innego serwera w regionie i tej samej grupie zasobów, istniejące konto magazynu jest używane.

![Włącz REKLAM](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> Koszt ADS jest powiązana z usługi Azure Security Center w warstwie standardowa ceny na węzeł, w których węzeł jest cały serwer bazy danych SQL Database lub wystąpienia zarządzanego. Dlatego płacisz tylko raz ochronę wszystkich baz danych na serwerze bazy danych lub wystąpienia zarządzanego z reklamy. Możesz wypróbować REKLAM początkowo z bezpłatną wersją próbną.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Rozpocznij klasyfikowania danych, Śledzenie usterek i badanie alertów zagrożeń

Kliknij przycisk **odnajdywanie i klasyfikacja danych** karty, aby zobaczyć zalecane poufnych kolumn do klasyfikowania i klasyfikowanie danych za pomocą czułości trwałe etykiety. Kliknij przycisk **oceny luk w zabezpieczeniach** karty do przeglądania i zarządzania, skanowanie luk w zabezpieczeniach i raporty i Śledź swoje stature zabezpieczeń. Jeśli Odebrano alertów zabezpieczeń, kliknij przycisk **zaawansowanej ochrony przed zagrożeniami** kartę, aby wyświetlić szczegóły alertów i skonsolidowane przedstawia informacje o wszystkich alertach w subskrypcji platformy Azure za pośrednictwem strony alerty zabezpieczeń usługi Azure Security Center .

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Zarządzanie ustawieniami REKLAM na serwerze usługi SQL Database lub wystąpienia zarządzanego

Aby przeglądać i zarządzać ustawieniami REKLAM, przejdź do **zaawansowanych danych** w obszarze **zabezpieczeń** nagłówka dla serwera bazy danych SQL Database lub wystąpienia zarządzanego. Na tej stronie można włączyć lub wyłączyć REKLAM i zmodyfikować luk w zabezpieczeniach do oceny i ustawienia zaawansowanej ochrony przed zagrożeniami dla całego serwera bazy danych SQL Database lub wystąpienia zarządzanego.

![Ustawienia serwera](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Zarządzanie ustawieniami REKLAM dla bazy danych SQL

Aby zastąpić ustawienia REKLAM dla konkretnej bazy danych, zapoznaj się z **Włącz zaawansowane zabezpieczenia danych na poziomie bazy danych** pola wyboru. Użyj tej opcji tylko wtedy, gdy masz wymagasz oddzielne alerty zaawansowanej ochrony przed zagrożeniami lub wyniki oceny luk w zabezpieczeniach dla poszczególnych baz danych, zamiast lub oprócz alerty i wyniki otrzymane dla wszystkich baz danych na Serwer bazy danych lub wystąpienia zarządzanego.

Gdy pole wyboru jest zaznaczone, następnie można skonfigurować odpowiednie ustawienia dla tej bazy danych.
 
![Ustawienia bazy danych i zaawansowaną ochronę przed zagrożeniami](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Ustawienia zabezpieczeń zaawansowanych danych dla serwera bazy danych lub wystąpienia zarządzanego jest dostępne w taki sposób, z okienka REKLAM bazy danych. Kliknij przycisk **ustawienia** głównego okienka REKLAM, a następnie kliknij **widoku danych serwera ustawień**. 

![Ustawienia bazy danych](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Kolejne kroki 

- Dowiedz się więcej o [odnajdywanie i klasyfikacja danych](sql-database-data-discovery-and-classification.md) 
- Dowiedz się więcej o [oceny luk w zabezpieczeniach](sql-vulnerability-assessment.md) 
- Dowiedz się więcej o [zaawansowanej ochrony przed zagrożeniami](sql-database-threat-detection.md)
- Dowiedz się więcej o [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
