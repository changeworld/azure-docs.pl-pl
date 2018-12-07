---
title: Zaawansowana ochrona przed zagrożeniami — usługi Azure SQL Database | Dokumentacja firmy Microsoft
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
ms.date: 12/05/2018
ms.openlocfilehash: ea7b4def23f766e6cda1e1e8e1d4e8dc7be302fe
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997592"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Zaawansowana ochrona przed zagrożeniami dla bazy danych Azure SQL

Usługa SQL Advanced Threat Protection to ujednolicony pakiet zaawansowanych możliwości zabezpieczeń SQL. Obejmuje funkcje odnajdywania, klasyfikowania poufnych danych, dzięki czemu są ujawniane i łagodzące potencjalne luki bazy danych i wykrywanie nietypowych działań wskazujących zagrożenie z bazą danych. Zapewnia ona pojedynczą lokalizację, w której można włączać te możliwości i zarządzać nimi. 

## <a name="overview"></a>Przegląd

SQL zaawansowane Threat Protection (ATP) zawiera zestaw zaawansowanych funkcji zabezpieczeń programu SQL, w tym odnajdywanie danych i klasyfikacji, ocena luk w zabezpieczeniach i wykrywanie zagrożeń. 

- [Odnajdywanie i klasyfikacja danych](sql-database-data-discovery-and-classification.md) (obecnie w wersji zapoznawczej) zapewnia możliwości wbudowane w usługę Azure SQL Database na potrzeby odnajdywania, klasyfikowania, etykietowania oraz ochrony danych poufnych w bazach danych. Za jej pomocą można zapewniać wgląd w stan klasyfikacji bazy danych oraz śledzić dostęp do danych poufnych w bazie danych i poza jej granicami.
- [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) to łatwa do skonfigurowania usługa umożliwiająca odnajdywanie i śledzenie potencjalnych luk w zabezpieczeniach bazy danych oraz pomagająca w ich usuwaniu. Zapewnia wgląd w stan zabezpieczeń i zapewnia kroki, które można wykonać w celu rozwiązania problemów z zabezpieczeniami oraz zwiększenia bezpieczeństwa bazy danych.
- [Wykrywanie zagrożeń](sql-database-threat-detection-overview.md) wykrywa nietypowe działania wskazujące na nieprawidłowe i potencjalnie szkodliwe próby uzyskania dostępu do bazy danych lub wykorzystania jej. Stale monitoruje Twoją bazę danych pod kątem podejrzanych działań i zapewnia natychmiastowe alerty zabezpieczeń dotyczące potencjalnych luk w zabezpieczeniach, ataków polegających na wstrzyknięciu kodu SQL oraz anomalii we wzorcach dostępu do bazy danych. Alerty wykrywania zagrożeń zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.

Włączyć usługę SQL ATP po włączyć wszystkie z nich uwzględnione funkcje. Jednym kliknięciem można włączyć usługę ATP na całym serwerze baz danych i zastosować ją do wszystkich baz danych na tym serwerze. 

Wyrównuje ceny zaawansowanej ochrony przed zagrożeniami za pomocą usługi Azure Security Center w warstwie standardowa czym każdy chroniony serwer usługi SQL Database będzie liczony jako jeden węzeł. Nowo chronionych zasobów kwalifikuje się do bezpłatnej wersji próbnej usługi Security Center w warstwie standardowa. Aby uzyskać więcej informacji, zobacz [usługi Azure Security Center, na stronie z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="getting-started-with-atp"></a>Wprowadzenie do zaawansowanej ochrony przed zagrożeniami 
Poniższe kroki ułatwiające rozpoczęcie pracy za pomocą zaawansowanej ochrony przed zagrożeniami. 

## <a name="1-enable-atp"></a>1. Włączanie zaawansowanej ochrony przed zagrożeniami

Włączanie zaawansowanej ochrony przed zagrożeniami, przechodząc do **zaawansowanej ochrony przed zagrożeniami** w obszarze **zabezpieczeń** nagłówek w okienku usługi Azure SQL Database. Aby włączyć zaawansowanej ochrony przed zagrożeniami dla wszystkich baz danych na serwerze, kliknij przycisk **Włączanie zaawansowanej ochrony przed zagrożeniami na serwerze**.

![Włączanie zaawansowanej ochrony przed zagrożeniami](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> Koszt zaawansowanej ochrony przed zagrożeniami jest powiązana z usługi Azure Security Center w warstwie standardowa ceny na węzeł, w których węzeł jest całego serwera logicznego SQL. Dlatego płacisz tylko raz ochronę wszystkich baz danych na serwerze za pomocą zaawansowanej ochrony przed zagrożeniami. Możesz wypróbować zaawansowanej ochrony przed zagrożeniami początkowo z bezpłatną wersją próbną.

## <a name="2-configure-vulnerability-assessment"></a>2. Konfigurowanie oceny luk w zabezpieczeniach

Aby rozpocząć korzystanie z oceny luk w zabezpieczeniach, musisz skonfigurować konto magazynu, w którym wyniki skanowania są zapisywane. Aby to zrobić, kliknij opcję karty oceny luk w zabezpieczeniach.

![Konfigurowanie oceny luk w zabezpieczeniach](./media/sql-advanced-protection/configure_va.png) 

Wybierz lub Utwórz konto magazynu do zapisywania wyników skanowania. Można również włączyć okresowe skanowania cykliczne do skonfigurowania oceny luk w zabezpieczeniach, aby uruchomić automatyczne skanowanie raz w tygodniu. Podsumowanie wyników skanowania są wysyłane na adresy e-mail, których udzielasz.

![Ustawienia oceny luk w zabezpieczeniach](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Rozpocznij klasyfikowania danych, Śledzenie usterek i badanie alertów zagrożeń

Kliknij przycisk **danych, odnajdowanie i klasyfikację** karty, aby zobaczyć zalecane poufnych kolumn do klasyfikowania i klasyfikowanie danych za pomocą czułości trwałe etykiety. Kliknij przycisk **oceny luk w zabezpieczeniach** karty do przeglądania i zarządzania, skanowanie luk w zabezpieczeniach i raporty i Śledź swoje stature zabezpieczeń. Jeśli Odebrano alertów zabezpieczeń, kliknij przycisk **wykrywania zagrożeń** kartę, aby wyświetlić szczegóły alertów i skonsolidowane przedstawia informacje o wszystkich alertach w subskrypcji platformy Azure za pośrednictwem strony alerty zabezpieczeń usługi Azure Security Center.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. Zarządzanie ustawieniami zaawansowanej ochrony przed zagrożeniami na serwerze SQL

Aby przeglądać i zarządzać ustawieniami zaawansowanej ochrony przed zagrożeniami, przejdź do **zaawansowanej ochrony przed zagrożeniami** w obszarze **zabezpieczeń** nagłówek w okienku programu SQL server. Na tej stronie można włączać lub wyłączać zaawansowanej ochrony przed zagrożeniami i zmodyfikować ustawienia wykrywania zagrożeń dla całego serwera SQL.

![Ustawienia serwera](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. Zarządzanie ustawieniami zaawansowanej ochrony przed zagrożeniami dla bazy danych SQL

Aby zastąpić ustawienia zaawansowanej ochrony przed zagrożeniami, wykrywanie zagrożeń dla konkretnej bazy danych, zapoznaj się z **Włączanie zaawansowanej ochrony przed zagrożeniami na poziomie bazy danych** pola wyboru. Użyj tej opcji tylko wtedy, gdy masz wymagasz, aby otrzymywać alerty wykrywania zagrożeń oddzielnych dla poszczególnych baz danych, zamiast lub oprócz alertów dla wszystkich baz danych na serwerze. 

Gdy pole wyboru jest zaznaczone, kliknij przycisk **ustawień wykrywania zagrożeń dla tej bazy danych** a następnie skonfiguruj odpowiednie ustawienia dla tej bazy danych.

![Ustawienia wykrywania zagrożeń i bazy danych](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Zaawansowane ustawienia ochrony przed zagrożeniami dla serwera można również można połączyć się z poziomu okienka bazy danych zaawansowanej ochrony przed zagrożeniami. Kliknij przycisk **ustawienia** w okienku głównym zaawansowanej ochrony przed zagrożeniami, a następnie kliknij **ustawienia serwera zaawansowanej ochrony przed zagrożeniami dla widoku**. 

![Ustawienia bazy danych](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Kolejne kroki 

- Dowiedz się więcej o [odnajdywanie i klasyfikacja danych](sql-database-data-discovery-and-classification.md) 
- Dowiedz się więcej o [oceny luk w zabezpieczeniach](sql-vulnerability-assessment.md) 
- Dowiedz się więcej o [wykrywanie zagrożeń](sql-database-threat-detection.md)
- Dowiedz się więcej o [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
