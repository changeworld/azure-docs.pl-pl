---
title: Dane z bazy danych Azure SQL odnajdywania & klasyfikacji | Dokumentacja firmy Microsoft
description: Dane z bazy danych Azure SQL odnajdywania & klasyfikacji
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 01/29/2018
ms.author: giladm
ms.openlocfilehash: 375142b0e55c741e6ab914e969751833f989d2fb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Dane z bazy danych Azure SQL odnajdywania i klasyfikacji
Dane odnajdywania & klasyfikacji (obecnie w wersji zapoznawczej) zapewnia zaawansowane możliwości, wbudowane w bazie danych SQL Azure dla **odnajdywania**, **klasyfikacji**, **etykietowania**  &  **ochrony** poufnych danych w bazach danych.
Wykrywanie i klasyfikowania najbardziej poufnych danych (business, finansowych, opieki zdrowotnej, dane osobowe, itp.) mogą odgrywać istotną rolę w Twojej stature ochrony informacji organizacyjnych. Może służyć jako infrastruktury:
* Pomoc w spełniać normy prywatności danych i wymagania zgodności z przepisami, na przykład GDPR.
* Różne scenariusze zabezpieczeń, takich jak monitorowanie (inspekcji) i alarmując o nietypowych dostęp do poufnych danych.
* Kontrolowanie dostępu do i wzmacniania ochrony baz danych zawierających wysoce poufnych danych.

> [!NOTE]
> Ten dokument dotyczy tylko bazy danych SQL Azure. Dla programu SQL Server (lokalnego), zobacz [odnajdywanie danych SQL i klasyfikacji](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Omówienie
Dane odnajdywania & klasyfikacji wprowadza zestaw zaawansowanych usług i nowe możliwości SQL, tworzących nowego modelu SQL Information Protection mających na celu ochronę danych, a nie tylko bazy danych:
* **Odnajdywanie & zalecenia** — aparat klasyfikacji skanowania bazy danych i identyfikuje kolumny zawierające potencjalnie poufnych danych. Następnie umożliwia łatwe Przejrzyj i Zastosuj zalecenia dotyczące odpowiednich klasyfikacji za pośrednictwem portalu Azure.
* **Etykietowanie** — etykiet klasyfikacji czułości być trwale oznakowane w kolumnach za pomocą nowych atrybutów metadanych klasyfikacji wprowadzane do aparatu SQL. Te metadane mogą zostać użyte następnie Zaawansowane na podstawie liter inspekcji i ochrony scenariuszach.
* **Wynik kwerendy ustawić czułość** — czułość zestawu wyników zapytania jest obliczane w czasie rzeczywistym dla inspekcji.
* **Widoczność** — stan klasyfikacji bazy danych można wyświetlić szczegółowe pulpitu nawigacyjnego w portalu. Ponadto można pobrać raportu (w formacie programu Excel) do zastosowania w przypadku zgodności & celów inspekcji, a także innych potrzeb.

## <a id="subheading-2"></a>Odnajdywanie, klasyfikacji i etykietowania poufnych kolumn
W poniższej sekcji opisano kroki dotyczące odnajdywania, klasyfikacji i etykietowania kolumny zawierające poufne dane zawarte w bazy danych, a także wyświetlanie bieżącego stanu klasyfikacji bazy danych i eksportowanie raportów.

Klasyfikacja zawiera dwa atrybuty metadanych:
* Etykiety — atrybuty głównego klasyfikacji są używane do definiowania charakterystyki danych przechowywanych w kolumnie.  
* Typy informacji – zapewniają dodatkowe szczegółowości na typ danych przechowywanych w kolumnie.

<br>
**Do klasyfikowania bazy danych SQL:**

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

2. Przejdź do **danych odnajdywania & klasyfikacji (wersja zapoznawcza)** ustawienia w bazie danych SQL.

    ![Okienko nawigacji][1]

3. **Omówienie** karta zawiera podsumowanie bieżącego stanu z bazy danych, w tym szczegółową listę wszystkich niejawnych kolumn, które można również filtrować Aby wyświetlić tylko części schematu, typów informacji i etykiety klasyfikacji. Jeśli nie zostało jeszcze sklasyfikowane żadnych kolumn [przejdź do kroku 5](#step-5).

    ![Okienko nawigacji][2]

4. Aby pobrać raportu w formacie programu Excel, kliknij na **wyeksportować** opcję w menu u góry okna.

    ![Okienko nawigacji][3]

5.  <a id="step-5"></a>Aby rozpocząć, klasyfikacji danych, kliknij na **kartę klasyfikacji** w górnej części okna.

    ![Okienko nawigacji][4]

6. Aparat klasyfikacji skanuje bazy danych dla kolumny zawierające potencjalnie poufnych danych i udostępnia listę **zalecane klasyfikacje kolumny**. Aby wyświetlić i stosować zalecenia klasyfikacji:

    * Aby wyświetlić listę zalecanych kolumny klasyfikacje, kliknij panel zalecenia w dolnej części okna:

        ![Okienko nawigacji][5]

    * Zapoznaj się z listą zaleceń — aby zaakceptować zalecenia dla określonej kolumny, zaznacz pole wyboru w kolumnie po lewej stronie odpowiednich wiersza. Można również zaznaczyć *wszystkie zalecenia* jako zaakceptowane, zaznaczając pole wyboru w nagłówku tabeli zalecenia.

        ![Okienko nawigacji][6]

    * Aby zastosować wybrany zalecenia, kliknij na niebieski **zaakceptować zalecenia wybranego** przycisku.

        ![Okienko nawigacji][7]

7. Możesz również **ręczne klasyfikowanie** kolumn zamiast lub oprócz, klasyfikację na podstawie zalecenia:

    * Polecenie **dodać klasyfikacji** w menu u góry okna.

        ![Okienko nawigacji][8]

    * W otwartym oknie kontekstu, wybierz schemat > tabeli > kolumny, które chcesz sklasyfikować, a etykieta typ i ważność informacji. Następnie kliknij polecenie niebieski **dodać klasyfikacji** przycisk w dolnej części okna kontekstu.

        ![Okienko nawigacji][9]

8. Aby zakończyć własnej klasyfikacji i trwale etykiet kolumn (tagów) bazy danych z nowymi metadanymi klasyfikacji, kliknij **zapisać** w menu u góry okna.

    ![Okienko nawigacji][10]

## <a id="subheading-3"></a>Inspekcja dostępu do danych poufnych

Ważnym aspektem modelu ochrona informacji jest możliwość monitorowania dostęp do poufnych danych.

[Usługa Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) zostało rozszerzone, aby uwzględnić nowe pole w dzienniku inspekcji o nazwie *data_sensitivity_information*, która rejestruje klasyfikacje czułość (etykiety) rzeczywistych danych, który został zwrócony przez Zapytanie.

![Okienko nawigacji][11]

## <a id="subheading-4"></a>Następne kroki
Należy rozważyć skonfigurowanie [Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) na monitorowanie i przeprowadzanie inspekcji dostępu do danych poufnych niejawnych.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4

<!--Image references-->
[1]: ./media/sql-data-discovery-and-classification/1_data_classification_settings_menu.png
[2]: ./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png
[3]: ./media/sql-data-discovery-and-classification/3_data_classification_export_report.png
[4]: ./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png
[5]: ./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png
[6]: ./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png
[7]: ./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png
[8]: ./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png
[9]: ./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png
[10]: ./media/sql-data-discovery-and-classification/10_data_classification_save.png
[11]: ./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png
