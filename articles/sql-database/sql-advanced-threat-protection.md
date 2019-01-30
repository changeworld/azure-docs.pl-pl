---
title: Zaawansowane zabezpieczenia danych — usługa Azure SQL Database | Dokumentacja firmy Microsoft
description: Więcej informacji na temat funkcji Odnajdywanie i klasyfikowanie danych poufnych, zarządzanie swojej bazy danych, luk w zabezpieczeniach i wykrywanie nietypowych działań, które mogą wskazywać na zagrożenie dla usługi Azure SQL database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 1/29/2019
ms.openlocfilehash: 36d8f878426534c582ce6ada4e7000acf62bceaf
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251847"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Zabezpieczenia zaawansowanych danych dla usługi Azure SQL Database

Zaawansowane zabezpieczenia danych SQL jest ujednoliconego pakietu dla zaawansowane funkcje zabezpieczeń programu SQL. Obejmuje funkcje odnajdywania, klasyfikowania poufnych danych, dzięki czemu są ujawniane i łagodzące potencjalne luki bazy danych i wykrywanie nietypowych działań wskazujących zagrożenie z bazą danych. Zapewnia ona pojedynczą lokalizację, w której można włączać te możliwości i zarządzać nimi. 

## <a name="overview"></a>Przegląd

SQL zaawansowanych danych zabezpieczeń (AD) zapewnia zestaw zaawansowanych funkcji zabezpieczeń programu SQL, w tym odnajdywanie danych i klasyfikacji, ocena luk w zabezpieczeniach i wykrywanie zagrożeń. 

- [Odnajdywanie i klasyfikacja danych](sql-database-data-discovery-and-classification.md) (obecnie w wersji zapoznawczej) zapewnia możliwości wbudowane w usługę Azure SQL Database na potrzeby odnajdywania, klasyfikowania, etykietowania oraz ochrony danych poufnych w bazach danych. Za jej pomocą można zapewniać wgląd w stan klasyfikacji bazy danych oraz śledzić dostęp do danych poufnych w bazie danych i poza jej granicami.
- [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) to łatwa do skonfigurowania usługa umożliwiająca odnajdywanie i śledzenie potencjalnych luk w zabezpieczeniach bazy danych oraz pomagająca w ich usuwaniu. Zapewnia wgląd w stan zabezpieczeń i zapewnia kroki, które można wykonać w celu rozwiązania problemów z zabezpieczeniami oraz zwiększenia bezpieczeństwa bazy danych.
- [Wykrywanie zagrożeń](sql-database-threat-detection-overview.md) wykrywa nietypowe działania wskazujące na nieprawidłowe i potencjalnie szkodliwe próby uzyskania dostępu do bazy danych lub wykorzystania jej. Stale monitoruje Twoją bazę danych pod kątem podejrzanych działań i zapewnia natychmiastowe alerty zabezpieczeń dotyczące potencjalnych luk w zabezpieczeniach, ataków polegających na wstrzyknięciu kodu SQL oraz anomalii we wzorcach dostępu do bazy danych. Alerty wykrywania zagrożeń zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.

Włącz SQL REKLAM po włączyć wszystkie z nich uwzględnione funkcje. Za pomocą jednego kliknięcia można włączyć AD na serwerze całą bazę danych, zastosowanie do wszystkich baz danych na serwerze. Włączanie i zarządzanie ustawieniami REKLAM wymaga należący do [Menedżer zabezpieczeń SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) roli, do roli administratora bazy danych SQL lub rola administratora programu SQL server. 

Wyrównuje REKLAM, ceny za pomocą usługi Azure Security Center w warstwie standardowa czym każdy chroniony serwer usługi SQL Database będzie liczony jako jeden węzeł. Nowo chronionych zasobów kwalifikuje się do bezpłatnej wersji próbnej usługi Security Center w warstwie standardowa. Aby uzyskać więcej informacji, zobacz [usługi Azure Security Center, na stronie z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="getting-started-with-ads"></a>Rozpoczęcie korzystania z REKLAM 
Poniższe kroki ułatwiające rozpoczęcie pracy z reklamy. 

## <a name="1-enable-ads"></a>1. Włącz REKLAM

Włącz REKLAM, przechodząc do **zaawansowanych danych** w obszarze **zabezpieczeń** nagłówek w okienku usługi Azure SQL Database. Aby włączyć REKLAM dla wszystkich baz danych na serwerze, kliknij przycisk **Włącz zaawansowane zabezpieczenia danych na serwerze**.

![Włącz REKLAM](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> Koszt ADS jest powiązana z usługi Azure Security Center w warstwie standardowa ceny na węzeł, w których węzeł jest całego serwera logicznego SQL. Dlatego płacisz tylko raz ochronę wszystkich baz danych na serwerze z reklamy. Możesz wypróbować REKLAM początkowo z bezpłatną wersją próbną.

## <a name="2-configure-vulnerability-assessment"></a>2. Konfigurowanie oceny luk w zabezpieczeniach

Aby rozpocząć korzystanie z oceny luk w zabezpieczeniach, musisz skonfigurować konto magazynu, w którym wyniki skanowania są zapisywane. Aby to zrobić, kliknij opcję karty oceny luk w zabezpieczeniach.

![Konfigurowanie oceny luk w zabezpieczeniach](./media/sql-advanced-protection/configure_va.png) 

Wybierz lub Utwórz konto magazynu do zapisywania wyników skanowania. Można również włączyć okresowe skanowania cykliczne do skonfigurowania oceny luk w zabezpieczeniach, aby uruchomić automatyczne skanowanie raz w tygodniu. Podsumowanie wyników skanowania są wysyłane na adresy e-mail, których udzielasz.

![Ustawienia oceny luk w zabezpieczeniach](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Rozpocznij klasyfikowania danych, Śledzenie usterek i badanie alertów zagrożeń

Kliknij przycisk **danych, odnajdowanie i klasyfikację** karty, aby zobaczyć zalecane poufnych kolumn do klasyfikowania i klasyfikowanie danych za pomocą czułości trwałe etykiety. Kliknij przycisk **oceny luk w zabezpieczeniach** karty do przeglądania i zarządzania, skanowanie luk w zabezpieczeniach i raporty i Śledź swoje stature zabezpieczeń. Jeśli Odebrano alertów zabezpieczeń, kliknij przycisk **wykrywania zagrożeń** kartę, aby wyświetlić szczegóły alertów i skonsolidowane przedstawia informacje o wszystkich alertach w subskrypcji platformy Azure za pośrednictwem strony alerty zabezpieczeń usługi Azure Security Center.

## <a name="4-manage-ads-settings-on-your-sql-server"></a>4. Zarządzanie ustawieniami REKLAM na program SQL server

Wyświetlanie i zarządzanie ustawieniami zaawansowanymi zabezpieczeń danych, przejdź do **zaawansowanych danych** w obszarze **zabezpieczeń** nagłówek w okienku programu SQL server. Na tej stronie można włączać lub wyłączać REKLAM i zmodyfikować ustawienia wykrywania zagrożeń dla całego serwera SQL.

![Ustawienia serwera](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-ads-settings-for-a-sql-database"></a>5. Zarządzanie ustawieniami REKLAM dla bazy danych SQL

Aby zastąpić ustawienia wykrywania zagrożeń REKLAM dla konkretnej bazy danych, zapoznaj się z **Włącz zaawansowane zabezpieczenia danych na poziomie bazy danych** pola wyboru. Użyj tej opcji tylko wtedy, gdy masz wymagasz, aby otrzymywać alerty wykrywania zagrożeń oddzielnych dla poszczególnych baz danych, zamiast lub oprócz alertów dla wszystkich baz danych na serwerze. 

Gdy pole wyboru jest zaznaczone, kliknij przycisk **ustawień wykrywania zagrożeń dla tej bazy danych** a następnie skonfiguruj odpowiednie ustawienia dla tej bazy danych.

![Ustawienia wykrywania zagrożeń i bazy danych](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Zaawansowane ustawienia zabezpieczeń danych dla serwera można również można połączyć się z poziomu okienka REKLAM bazy danych. Kliknij przycisk **ustawienia** głównego okienka REKLAM, a następnie kliknij **widoku danych serwera ustawień**. 

![Ustawienia bazy danych](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Kolejne kroki 

- Dowiedz się więcej o [odnajdywanie i klasyfikacja danych](sql-database-data-discovery-and-classification.md) 
- Dowiedz się więcej o [oceny luk w zabezpieczeniach](sql-vulnerability-assessment.md) 
- Dowiedz się więcej o [wykrywanie zagrożeń](sql-database-threat-detection.md)
- Dowiedz się więcej o [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
