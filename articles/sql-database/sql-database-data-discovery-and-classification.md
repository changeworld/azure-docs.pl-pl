---
title: Usługi Azure SQL Database Data odnajdywanie i klasyfikacja | Dokumentacja firmy Microsoft
description: Usługi Azure SQL Database Data odnajdywanie i klasyfikacja
services: sql-database
author: giladmit
manager: craigg
ms.reviewer: vanto
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: giladm
ms.openlocfilehash: a69142ebabc4af8b8543dc5dd276b99130620470
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714976"
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Usługi Azure SQL Database Data odnajdywanie i klasyfikacja
Odnajdywanie i klasyfikacja (obecnie w wersji zapoznawczej) danych zapewnia zaawansowane możliwości wbudowane w usłudze Azure SQL Database dla **odnajdywania**, **klasyfikowania**, **etykietowania**  &  **ochrony** poufnych danych w bazach danych.
Odnajdywanie i klasyfikacja najbardziej poufnych danych (biznesowe, finansów, opieki zdrowotnej, dane osobowe, itp.) można odtworzyć zasadniczą rolę w stature ochrony Twojej organizacji. Może służyć jako infrastruktury:
* Pomaga zaspokoić potrzeby dotyczące standardów ochrony prywatności danych i wymagania dotyczące zgodności z przepisami.
* Różne scenariusze zabezpieczeń, takich jak monitorowanie (inspekcji) i alarmując nietypowego dostępu do poufnych danych.
* Kontrolowanie dostępu do i wzmacniania ochrony bezpieczeństwa baz danych zawierających poufne dane.

Odnajdywanie i klasyfikacja danych jest częścią [SQL Zaawansowana ochrona przed zagrożeniami](sql-advanced-threat-protection.md) oferty (ATP), która to ujednolicone pakiet dla zaawansowane funkcje zabezpieczeń programu SQL. Odnajdywanie i klasyfikacja danych można uzyskać dostęp i zarządzane za pośrednictwem centralnej portal SQL ATP.

> [!NOTE]
> Ten dokument dotyczy tylko usługi Azure SQL Database. Dla programu SQL Server (lokalnie), zobacz [odnajdywanie danych SQL i klasyfikacji](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Co to jest danych, odnajdowanie i klasyfikację?
Odnajdywanie i klasyfikacja danych wprowadza zestaw zaawansowanych usług i nowe funkcje programu SQL, tworzących paradygmatu SQL Information Protection mających na celu ochronę danych, a nie tylko bazy danych:
* **Odnajdywanie i zalecenia dotyczące** — aparat klasyfikacji skanuje bazy danych i identyfikuje kolumny zawierające potencjalnie poufnych danych. Następnie umożliwia łatwe przeglądanie i stosowania zaleceń dotyczących odpowiedniej klasyfikacji w witrynie Azure portal.
* **Etykietowanie** — czułości etykiet klasyfikacji, które mogą być trwałe oznaczane w kolumnach za pomocą nowych atrybutów metadanych klasyfikacji wprowadzane do aparatu programu SQL. Te metadane mogą następnie wykorzystywane w celu zaawansowane czułości opartych na inspekcje i ochrony scenariuszach.
* **Wynik zapytania ustawić czułość** — czułość zestawu wyników zapytania jest obliczana w czasie rzeczywistym na potrzeby inspekcji.
* **Widoczność** — stan klasyfikacji bazy danych mogą być wyświetlane w szczegółowego pulpitu nawigacyjnego w portalu. Ponadto możesz pobrać raport (w formacie programu Excel) ma być używany dla zgodności i celów inspekcji, a także innych potrzeb.

## <a id="subheading-2"></a>Odnajdywanie, klasyfikowanie i etykiety kolumn poufnych
W poniższej sekcji opisano proces odnajdywania, klasyfikowania i etykietowania kolumn zawierających dane poufne w bazy danych, a także wyświetlanie bieżącego stanu klasyfikacji bazy danych i eksportowanie raportów.

Klasyfikacja zawiera dwa atrybuty metadanych:
* Etykiety — atrybuty głównego klasyfikacji są używane do definiowania poziomu poufności danych przechowywanych w kolumnie.  
* Typy informacji — zapewniają dodatkowe poziom szczegółowości na typ danych przechowywanych w kolumnie.

## <a name="classify-your-sql-database"></a>Klasyfikowanie SQL Database

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

2. Przejdź do **zaawansowanej ochrony przed zagrożeniami** w pozycji zabezpieczeń, w okienku usługi Azure SQL Database. Kliknij, aby włączyć zaawansowanej ochrony przed zagrożeniami, a następnie kliknij polecenie **danych, odnajdowanie i klasyfikacja (wersja zapoznawcza)** karty.

   ![Skanuj bazę danych](./media/sql-data-discovery-and-classification/data_classification.png)

3. **Przegląd** karta zawiera podsumowanie bieżącego stanu bazy danych, w tym szczegółową listę wszystkich sklasyfikowanych kolumn, które również można filtrować, aby wyświetlać tylko elementy z określonego schematu, typy informacji i etykiet klasyfikacji. Jeśli nie zostało jeszcze sklasyfikowane żadnych kolumn [przejdź do kroku 5](#step-5).

   ![Podsumowanie bieżącego stanu klasyfikacji](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Aby pobrać raport w formacie programu Excel, kliknij **wyeksportować** opcji w menu u góry okna.

   ![Eksportuj do programu Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5.  <a id="step-5"></a>Aby rozpocząć, klasyfikacji danych, kliknij pozycję **kartę klasyfikacji** w górnej części okna.

    ![Klasyfikowanie możesz danych](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. Aparat klasyfikacji skanuje bazę danych pod kątem kolumn zawierających potencjalnie poufnych danych i zapewnia listę **zalecane klasyfikacje kolumn**. Aby wyświetlić i stosowania zaleceń klasyfikacji:

    * Aby wyświetlić listę kolumn zalecane klasyfikacje, kliknij panel zalecenia w dolnej części okna:

      ![Klasyfikowanie danych](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

    * Przejrzyj listę zaleceń — aby zaakceptować zalecenie dla określonej kolumny, zaznacz pole wyboru w lewej kolumnie odpowiedni wiersz. Można również oznaczyć *wszystkich zaleceń dotyczących* jako zaakceptowane, zaznaczając pole wyboru w nagłówku tabeli zalecenia.

       ![Lista zaleceń przeglądu](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

    * Aby zastosować wybrane zalecenia, kliknij niebieski **Zaakceptuj wybrane zalecenia** przycisku.

      ![Stosowanie zalecenia](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Możesz również **ręcznie sklasyfikować** kolumny zamiast lub oprócz klasyfikacja na podstawie zaleceń:

    * Kliknij pozycję **Dodaj klasyfikację** w menu u góry okna.

      ![Ręcznie Dodaj klasyfikację](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

    * W otwartym oknie kontekstu wybierz schemat > tabeli > kolumny, które chcesz sklasyfikować i etykiety typu i poufności informacji. Następnie kliknij przycisk na niebieskim **Dodaj klasyfikację** znajdujący się u dołu okna kontekstu.

      ![Wybierz kolumnę do klasyfikowania](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Aby zakończyć klasyfikacji i trwałe etykiety kolumn (tagów) bazy danych z nowymi metadanymi klasyfikacji, kliknij **Zapisz** w menu u góry okna.

   ![Zapisz](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Inspekcja dostępu do poufnych danych

Ważnym aspektem modelu ochrona informacji jest możliwość monitorowania dostępu do poufnych danych. [Usługa Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) zostało rozszerzone, aby uwzględnić nowe pole w dzienniku inspekcji o nazwie *data_sensitivity_information*, która rejestruje klasyfikacje czułość (etykiety) rzeczywistych danych, który został zwrócony przez Zapytanie.

![Dziennik inspekcji](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Automatyczne Programmatic klasyfikacji

T-SQL umożliwia dodawanie/usuwanie klasyfikacje kolumn, a także pobrać wszystkie klasyfikacje dla całej bazy danych.

> [!NOTE]
> Zarządzanie etykiety za pomocą języka T-SQL, nie ma możliwości weryfikacji etykiety dodawane do kolumny istniejące w organizacji zasad usługi information protection (zestaw etykiet, które są wyświetlane w portalu zalecenia). Jest do nich można to sprawdzić.

* Dodawanie/aktualizowanie klasyfikacji co najmniej jedna kolumna: [Dodaj klasyfikacji charakteru](https://docs.microsoft.com/en-us/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
* Usunąć klasyfikację z co najmniej jedna kolumna: [PORZUCIĆ klasyfikacji charakteru](https://docs.microsoft.com/en-us/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
* Wyświetl wszystkie klasyfikacje w bazie danych: [sys.sensitivity_classifications](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

Interfejsów API REST można również użyć do programowego zarządzania klasyfikacje. Opublikowanych interfejsów API REST obsługują następujące operacje:
* [Tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) — tworzy lub aktualizuje etykieta poufności danej kolumny
* [Usuń](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) -usuwa etykietę czułości w danej kolumnie
* [Pobierz](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) — pobiera etykieta poufności danej kolumny
* [Lista przez bazę danych](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listbydatabase) — pobiera etykiety ważności określonej bazy danych


## <a id="subheading-5"></a>Następne kroki

- Dowiedz się więcej o [SQL zaawansowanej ochrony przed zagrożeniami](sql-advanced-threat-protection.md).
- Należy rozważyć skonfigurowanie [Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) monitorowania i inspekcji dostępu do danych poufnych niejawnych.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
