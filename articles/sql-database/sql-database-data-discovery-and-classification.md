---
title: Dane z bazy danych Azure SQL odnajdywania & klasyfikacji | Dokumentacja firmy Microsoft
description: Dane z bazy danych Azure SQL odnajdywania & klasyfikacji
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: giladm
ms.openlocfilehash: 673286c8dc9ec688199fe80cf5a763f249192de5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646783"
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Dane z bazy danych Azure SQL odnajdywania i klasyfikacji
Dane odnajdywania & klasyfikacji (obecnie w wersji zapoznawczej) zapewnia zaawansowane możliwości, wbudowane w bazie danych SQL Azure dla **odnajdywania**, **klasyfikacji**, **etykietowania**  &  **ochrony** poufnych danych w bazach danych.
Wykrywanie i klasyfikowania najbardziej poufnych danych (business, finansowych, opieki zdrowotnej, dane osobowe, itp.) mogą odgrywać istotną rolę w Twojej stature ochrony informacji organizacyjnych. Może służyć jako infrastruktury:
* Pomaga spełnić wymagania dotyczące zgodności z przepisami i standardy prywatności danych.
* Różne scenariusze zabezpieczeń, takich jak monitorowanie (inspekcji) i alarmując o nietypowych dostęp do poufnych danych.
* Kontrolowanie dostępu do i wzmacniania ochrony baz danych zawierających wysoce poufnych danych.

Dane odnajdywania & klasyfikacji jest częścią [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) oferty (ATP), która jest ujednoliconego pakietu dla zaawansowane funkcje zabezpieczeń programu SQL. Dane odnajdywania & klasyfikacji można uzyskać dostępu do i zarządzane za pośrednictwem portalu centralnej SQL ATP.

> [!NOTE]
> Ten dokument dotyczy tylko bazy danych SQL Azure. Dla programu SQL Server (lokalnego), zobacz [odnajdywanie danych SQL i klasyfikacji](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Co to jest odnajdowanie danych i klasyfikacji?
Dane odnajdywania & klasyfikacji wprowadza zestaw zaawansowanych usług i nowe możliwości SQL, tworzących nowego modelu SQL Information Protection mających na celu ochronę danych, a nie tylko bazy danych:
* **Odnajdywanie & zalecenia** — aparat klasyfikacji skanowania bazy danych i identyfikuje kolumny zawierające potencjalnie poufnych danych. Następnie umożliwia łatwe Przejrzyj i Zastosuj zalecenia dotyczące odpowiednich klasyfikacji za pośrednictwem portalu Azure.
* **Etykietowanie** — etykiet klasyfikacji czułości być trwale oznakowane w kolumnach za pomocą nowych atrybutów metadanych klasyfikacji wprowadzane do aparatu SQL. Te metadane mogą zostać użyte następnie Zaawansowane na podstawie liter inspekcji i ochrony scenariuszach.
* **Wynik kwerendy ustawić czułość** — czułość zestawu wyników zapytania jest obliczane w czasie rzeczywistym dla inspekcji.
* **Widoczność** — stan klasyfikacji bazy danych można wyświetlić szczegółowe pulpitu nawigacyjnego w portalu. Ponadto można pobrać raportu (w formacie programu Excel) do zastosowania w przypadku zgodności & celów inspekcji, a także innych potrzeb.

## <a id="subheading-2"></a>Odnajdywania, klasyfikowania i etykiety kolumn poufne
W poniższej sekcji opisano kroki dotyczące odnajdywania, klasyfikacji i etykietowania kolumny zawierające poufne dane zawarte w bazy danych, a także wyświetlanie bieżącego stanu klasyfikacji bazy danych i eksportowanie raportów.

Klasyfikacja zawiera dwa atrybuty metadanych:
* Etykiety — atrybuty głównego klasyfikacji są używane do definiowania charakterystyki danych przechowywanych w kolumnie.  
* Typy informacji – zapewniają dodatkowe szczegółowości na typ danych przechowywanych w kolumnie.

## <a name="classify-your-sql-database"></a>Klasyfikowanie bazy danych SQL

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

2. Przejdź do **Advanced Threat Protection** pozycji zabezpieczeń w okienku bazy danych SQL Azure. Kliknij, aby włączyć Advanced Threat Protection, a następnie kliknij polecenie **danych odnajdywania & klasyfikacji (wersja zapoznawcza)** karty.

   ![Skanowanie bazy danych](./media/sql-data-discovery-and-classification/data_classification.png) 

3. **Omówienie** karta zawiera podsumowanie bieżącego stanu z bazy danych, w tym szczegółową listę wszystkich niejawnych kolumn, które można również filtrować Aby wyświetlić tylko części schematu, typów informacji i etykiety klasyfikacji. Jeśli nie zostało jeszcze sklasyfikowane żadnych kolumn [przejdź do kroku 5](#step-5).

   ![Podsumowanie bieżącego stanu klasyfikacji](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png) 

4. Aby pobrać raportu w formacie programu Excel, kliknij na **wyeksportować** opcję w menu u góry okna.

   ![Eksportuj do programu Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png) 

5.  <a id="step-5"></a>Aby rozpocząć, klasyfikacji danych, kliknij na **kartę klasyfikacji** w górnej części okna.

    ![Klasyfikacji można danych](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png) 

6. Aparat klasyfikacji skanuje bazy danych dla kolumny zawierające potencjalnie poufnych danych i udostępnia listę **zalecane klasyfikacje kolumny**. Aby wyświetlić i stosować zalecenia klasyfikacji:

    * Aby wyświetlić listę zalecanych kolumny klasyfikacje, kliknij panel zalecenia w dolnej części okna:
    
      ![Klasyfikacji danych](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png) 

    * Zapoznaj się z listą zaleceń — aby zaakceptować zalecenia dla określonej kolumny, zaznacz pole wyboru w kolumnie po lewej stronie odpowiednich wiersza. Można również zaznaczyć *wszystkie zalecenia* jako zaakceptowane, zaznaczając pole wyboru w nagłówku tabeli zalecenia.

       ![Przejrzyj listę zalecenia](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png) 

    * Aby zastosować wybrany zalecenia, kliknij na niebieski **zaakceptować zalecenia wybranego** przycisku.

      ![Stosować zalecenia](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png) 

7. Możesz również **ręczne klasyfikowanie** kolumn zamiast lub oprócz, klasyfikację na podstawie zalecenia:

    * Polecenie **dodać klasyfikacji** w menu u góry okna.
  
      ![Ręcznie Dodaj klasyfikacji](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png) 

    * W otwartym oknie kontekstu, wybierz schemat > tabeli > kolumny, które chcesz sklasyfikować, a etykieta typ i ważność informacji. Następnie kliknij polecenie niebieski **dodać klasyfikacji** przycisk w dolnej części okna kontekstu.

      ![Wybierz kolumnę do klasyfikowania](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png) 

8. Aby zakończyć własnej klasyfikacji i trwale etykiet kolumn (tagów) bazy danych z nowymi metadanymi klasyfikacji, kliknij **zapisać** w menu u góry okna.

   ![Zapisz](./media/sql-data-discovery-and-classification/10_data_classification_save.png) 

## <a id="subheading-3"></a>Inspekcja dostępu do danych poufnych

Ważnym aspektem modelu ochrona informacji jest możliwość monitorowania dostęp do poufnych danych. [Usługa Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) zostało rozszerzone, aby uwzględnić nowe pole w dzienniku inspekcji o nazwie *data_sensitivity_information*, która rejestruje klasyfikacje czułość (etykiety) rzeczywistych danych, który został zwrócony przez Zapytanie.

![Dziennik inspekcji](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png) 

## <a id="subheading-4"></a>Następne kroki

- Dowiedz się więcej o [SQL Advanced Threat Protection](sql-advanced-threat-protection.md).
- Należy rozważyć skonfigurowanie [Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) na monitorowanie i przeprowadzanie inspekcji dostępu do danych poufnych niejawnych.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4