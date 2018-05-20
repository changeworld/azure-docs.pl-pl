---
title: Zaawansowana ochrona przed zagrożeniami — baza danych Azure SQL | Dokumentacja firmy Microsoft
description: Więcej informacji na temat funkcji odnajdowania i klasyfikowania danych poufnych, zarządzanie z luk w zabezpieczeniach bazy danych i wykrywanie nietypowych działań, które mogą wskazywać zagrożenie dla Twojej bazy danych Azure SQL.
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 5/17/2018
ms.author: ronitr
ms.reviewer: carlrab
ms.openlocfilehash: da21a0b66d86b4cc3e2dc59bdd972d4e24d7e5ec
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection bazy danych Azure SQL

SQL Advanced Threat Protection jest ujednoliconego pakietu dla zaawansowane funkcje zabezpieczeń programu SQL. Zawiera funkcje odnajdywania i klasyfikacji danych poufnych, zarządzanie z luk w zabezpieczeniach bazy danych i wykrywanie nietypowych działań, które mogą wskazywać na zagrożenie dla bazy danych. Zapewnia jeden przejdź do lokalizacji dotyczące włączania i możliwości zarządzania. 

## <a name="overview"></a>Przegląd

SQL zaawansowane zagrożenia ochrony (ATP) zawiera zestaw zaawansowanych możliwości zabezpieczeń SQL, w tym danych odnajdywania i klasyfikacji, oceny luk w zabezpieczeniach i wykrywanie zagrożeń. 

- [Dane odnajdywania & klasyfikacji](sql-database-data-discovery-and-classification.md) (obecnie w wersji zapoznawczej) zapewnia funkcje wbudowane w bazie danych SQL Azure dla odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Umożliwia zapewnienie wgląd w stan klasyfikacji z bazy danych i śledzenia dostępu do poufnych danych w bazie danych i poza jej granicami.
- [Oceny luk w zabezpieczeniach](sql-vulnerability-assessment.md) jest łatwa do skonfigurowania usługi odnajdywania, śledzenia i pomóc skorygować potencjalnych luk w bazie danych. Zapewnia wgląd w Twoje stan zabezpieczeń i zawiera kroki można wykonać, aby rozwiązywać problemy z zabezpieczeniami oraz ulepszanie fortifications Twojej bazy danych.
- [Wykrywanie zagrożeń](sql-database-threat-detection.md) wykrywa nietypowe działania wskazują nietypowe i potencjalnie szkodliwe próby dostępu lub wykorzystania bazy danych. Stale monitoruje bazy danych dla podejrzanych działań i udostępnia alerty zabezpieczeń natychmiastowego potencjalnych luk w zabezpieczeniach, ataki i wzorce dostępu nietypowych bazy danych. Alertów wykrywania zagrożeń zawierają szczegółowe informacje o podejrzanych działaniach i zalecane działania dotyczące sposobu badania i ograniczyć zagrożenie.

Włącz SQL ATP po włączyć wszystkie te funkcje dołączone. Wystarczy jedno kliknięcie można włączyć ATP na serwerze całej bazy danych mające zastosowanie do wszystkich baz danych na serwerze. 

Cennik ATP wyrównana z Centrum zabezpieczeń Azure warstwy standardowa na $15 węzła/miesięcznie, gdzie każdy chroniony serwer bazy danych SQL jest liczone jako jeden węzeł. Pierwsze 60 dni po aktywacji są uznawane za a bezpłatny okres próbny i nie są naliczane. Aby uzyskać więcej informacji, zobacz [Centrum zabezpieczeń Azure cennikiem](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="getting-started-with-atp"></a>Wprowadzenie do korzystania z ATP 
Poniższe kroki ułatwiające rozpoczęcie pracy z ATP. 

## <a name="1-enable-atp"></a>1. Włącz ATP

Włącz ATP, przechodząc do **Advanced Threat Protection** w obszarze **zabezpieczeń** pozycja w okienku bazy danych SQL Azure. Aby włączyć ATP dla wszystkich baz danych na serwerze, kliknij przycisk **włączyć Advanced Threat Protection na serwerze**.

![Włącz ATP](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> Koszt ATP jest $15 węzła/miesięcznie, gdy węzeł ma całego serwera logicznego SQL. W związku z tym płatność tylko raz dla wszystkich baz danych na serwerze z ATP ochrony. Pierwsze 60 dni są traktowane jako bezpłatnej wersji próbnej.

## <a name="2-configure-vulnerability-assessment"></a>2. Konfigurowanie oceny luk w zabezpieczeniach

Aby rozpocząć korzystanie z oceny luk w zabezpieczeniach, należy skonfigurować konto magazynu, w którym są zapisywane wyniki skanowania. Aby to zrobić, kliknij kartę oceny luk w zabezpieczeniach.

![Skonfiguruj VA](./media/sql-advanced-protection/configure_va.png) 

Wybierz lub Utwórz konto magazynu, aby zapisać wyniki skanowania. Można również włączyć okresowe cykliczne skanowanie, aby skonfigurować oceny luk w zabezpieczeniach do uruchomienia skanowania automatycznego raz w tygodniu. Podsumowanie wyników skanowania są wysyłane na adresy e-mail, podane przez użytkownika.

![Ustawienia VA](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Uruchom klasyfikowania danych, śledzenie luk w zabezpieczeniach i badanie alertów zagrożeń

Kliknij przycisk **danych odnajdywania i klasyfikacji** karty, aby wyświetlić zalecane poufnych kolumn do klasyfikowania i klasyfikowania danych za pomocą etykiety charakter trwały. Kliknij przycisk **oceny luk w zabezpieczeniach** karty do wyświetlania i zarządzania skanowania luki w zabezpieczeniach i raportów oraz śledzenie stature Twojego zabezpieczeń. Jeśli zostały odebrane alerty zabezpieczeń, kliknij przycisk **wykrywanie zagrożeń** karty, aby wyświetlić szczegóły alertów i wyświetlić raport skonsolidowanych na wszystkie alerty w Twojej subskrypcji platformy Azure za pośrednictwem strony alerty zabezpieczeń w Centrum zabezpieczeń Azure.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. Zarządzanie ustawieniami ATP na serwerze SQL

Aby wyświetlić i zarządzania ustawieniami Advanced Threat Protection, przejdź do **Advanced Threat Protection** w obszarze **zabezpieczeń** pozycja w okienku SQL server. Na tej stronie można włączyć lub wyłączyć ATP i zmodyfikuj ustawienia wykrywanie zagrożeń dla całego serwera SQL.

![Ustawienia serwera](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. Zarządzanie ustawieniami ATP bazy danych SQL

Aby zastąpić wykrywanie zagrożeń ATP ustawienia określonej bazy danych, sprawdź **włączyć Advanced Threat Protection na poziomie bazy danych** wyboru. Użyj tej opcji tylko wtedy, gdy specyficznych wymagań do odbierania alertów wykrywania zagrożeń oddzielne poszczególne bazy danych, zamiast lub oprócz alertów dla wszystkich baz danych na serwerze. 

Gdy pole wyboru jest zaznaczone, kliknij przycisk **wykrywanie zagrożeń ustawienia dla tej bazy danych** , a następnie skonfiguruj odpowiednie ustawienia dla tej bazy danych.

![Bazy danych i zagrożeń wykrywania ustawień](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Zaawansowane ustawienia ochrony przed zagrożeniami dla serwera można osiągnąć w taki sposób, z okienka ATP bazy danych. Kliknij przycisk **ustawienia** w okienku głównym ATP, a następnie kliknij przycisk **ustawienia serwera widoku Advanced Threat Protection**. 

![Ustawienia bazy danych](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Kolejne kroki 

- Dowiedz się więcej o [danych odnajdywania & klasyfikacji](sql-database-data-discovery-and-classification.md) 
- Dowiedz się więcej o [oceny luk w zabezpieczeniach](sql-vulnerability-assessment.md) 
- Dowiedz się więcej o [wykrywanie zagrożeń](sql-database-threat-detection.md)
- Dowiedz się więcej o [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
