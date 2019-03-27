---
title: Usługa Azure SQL Database i SQL Data Warehouse odnajdywanie i klasyfikacja danych | Dokumentacja firmy Microsoft
description: Usługa Azure SQL Database i data odnajdywanie i klasyfikacja
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: 74bd3af3e1ffd126f8cb4f2347e4566cc4708e25
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58495990"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>Usługa Azure SQL Database i SQL Data Warehouse odnajdywanie i klasyfikacja danych

Odnajdywanie i klasyfikacja (obecnie w wersji zapoznawczej) danych zapewnia zaawansowane możliwości wbudowane w usłudze Azure SQL Database dla **odnajdywania**, **klasyfikowania**, **etykietowania**  &  **ochrony** poufnych danych w bazach danych.
Odnajdowanie i klasyfikowanie najbardziej poufnych danych (biznesowe, finansowych, ochrony zdrowia, dane osobowe (PII) i itp.), można odtwarzać zasadniczą rolę w stature ochrony Twojej organizacji. Może służyć jako infrastruktury:

- Pomaga zaspokoić potrzeby dotyczące standardów ochrony prywatności danych i wymagania dotyczące zgodności z przepisami.
- Różne scenariusze zabezpieczeń, takich jak monitorowanie (inspekcji) i alarmując nietypowego dostępu do poufnych danych.
- Kontrolowanie dostępu do i wzmacniania ochrony bezpieczeństwa baz danych zawierających poufne dane.

Odnajdywanie i klasyfikacja danych jest częścią [zaawansowane zabezpieczenia danych](sql-database-advanced-data-security.md) (ADS) oferty, która to ujednolicone pakiet dla zaawansowane funkcje zabezpieczeń programu SQL. Odnajdywanie i klasyfikacja danych można uzyskać dostęp i zarządzane za pośrednictwem portalu centralnej REKLAM SQL.

> [!NOTE]
> W tym dokumencie odnosi się do usługi Azure SQL Database i Azure SQL Data Warehouse. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database. Dla programu SQL Server (lokalnie), zobacz [odnajdywanie danych SQL i klasyfikacji](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Co to jest odnajdywanie i klasyfikacja danych

Wprowadza zestaw zaawansowanych usług i nowe funkcje programu SQL, tworzących paradygmatu SQL Information Protection mających na celu ochronę danych, a nie tylko bazy danych, odnajdowanie i klasyfikacja danych:

- **Odnajdywanie i zalecenia dotyczące**

  Aparat klasyfikacji skanuje bazy danych i identyfikuje kolumny zawierające potencjalnie poufnych danych. Następnie umożliwia łatwe przeglądanie i stosowania zaleceń dotyczących odpowiedniej klasyfikacji w witrynie Azure portal.

- **Etykietowania**

  Czułość etykiet klasyfikacji mogą być oznaczane trwałe w kolumnach za pomocą nowych atrybutów metadanych klasyfikacji wprowadzane do aparatu programu SQL. Te metadane mogą następnie wykorzystywane w celu zaawansowane czułości opartych na inspekcje i ochrony scenariuszach.

- **Czułość zestawu wyników zapytania**

  Czułość zestawu wyników zapytania jest obliczana w czasie rzeczywistym na potrzeby inspekcji.

- **Widoczność**

  Stan klasyfikacji bazy danych mogą być wyświetlane w szczegółowego pulpitu nawigacyjnego w portalu. Ponadto możesz pobrać raport (w formacie programu Excel) ma być używany dla zgodności i celów inspekcji, a także innych potrzeb.

## <a id="subheading-2"></a>Odnajdywanie, klasyfikowanie i etykiety kolumn poufnych

W poniższej sekcji opisano proces odnajdywania, klasyfikowania i etykietowania kolumn zawierających dane poufne w bazy danych, a także wyświetlanie bieżącego stanu klasyfikacji bazy danych i eksportowanie raportów.

Klasyfikacja zawiera dwa atrybuty metadanych:

- Etykiety — atrybuty głównego klasyfikacji są używane do definiowania poziomu poufności danych przechowywanych w kolumnie.  
- Typy informacji — zapewniają dodatkowe poziom szczegółowości na typ danych przechowywanych w kolumnie.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definiowanie i dostosowywanie taksonomii klasyfikacji

SQL odnajdywanie i klasyfikacja danych zawiera zestaw wbudowanych etykiet czułości oraz zestaw wbudowanych typów informacji i logiki odnajdywania. Masz teraz możliwość dostosowywania ta Taksonomia i zdefiniuj zestaw i klasyfikacji konstrukcji klasyfikacji dla określonego środowiska.

Definicja i dostosowywania taksonomii klasyfikacji odbywa się w jednej centralnej lokalizacji dla całej dzierżawie platformy Azure. Lokalizacja znajduje się w [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), jako część zasad zabezpieczeń. To zadanie można wykonać tylko osoby z uprawnieniami administracyjnymi w grupie zarządzania dzierżawy katalogu głównego.

W ramach zarządzania zasadami ochrony informacji można zdefiniować niestandardowe etykiety, ustaw ich pozycję i skojarzyć je z wybranym zestawem typów informacji. Można również dodać własne typy niestandardowych informacji i skonfigurować je przy użyciu ciągu wzorce, które są dodawane do logiki odnajdywania do identyfikacji tego typu danych w bazach danych.
Dowiedz się więcej o dostosowywaniu i zasad w zarządzaniu [Information Protection zasady poradnik](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Po zdefiniowaniu zasad obowiązujących w całej dzierżawy możesz kontynuować klasyfikacji poszczególnych baz danych za pomocą niestandardowych zasad.

## <a name="classify-your-sql-database"></a>Klasyfikowanie SQL Database

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

2. Przejdź do **zaawansowanych danych** w pozycji zabezpieczeń, w okienku usługi Azure SQL Database. Kliknij, aby włączyć zabezpieczeń zaawansowanych danych, a następnie kliknij polecenie **danych, odnajdowanie i klasyfikacja (wersja zapoznawcza)** karty.

   ![Skanuj bazę danych](./media/sql-data-discovery-and-classification/data_classification.png)

3. **Przegląd** karta zawiera podsumowanie bieżącego stanu bazy danych, w tym szczegółową listę wszystkich sklasyfikowanych kolumn, które również można filtrować, aby wyświetlać tylko elementy z określonego schematu, typy informacji i etykiet klasyfikacji. Jeśli nie zostało jeszcze sklasyfikowane żadnych kolumn [przejdź do kroku 5](#step-5).

   ![Podsumowanie bieżącego stanu klasyfikacji](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Aby pobrać raport w formacie programu Excel, kliknij **wyeksportować** opcji w menu u góry okna.

   ![Eksportuj do programu Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Aby rozpocząć, klasyfikacji danych, kliknij pozycję **kartę klasyfikacji** w górnej części okna.

    ![Klasyfikowanie możesz danych](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. Aparat klasyfikacji skanuje bazę danych pod kątem kolumn zawierających potencjalnie poufnych danych i zapewnia listę **zalecane klasyfikacje kolumn**. Aby wyświetlić i stosowania zaleceń klasyfikacji:

   - Aby wyświetlić listę kolumn zalecane klasyfikacje, kliknij panel zalecenia w dolnej części okna:

      ![Klasyfikowanie danych](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Przejrzyj listę zaleceń — aby zaakceptować zalecenie dla określonej kolumny, zaznacz pole wyboru w lewej kolumnie odpowiedni wiersz. Można również oznaczyć *wszystkich zaleceń dotyczących* jako zaakceptowane, zaznaczając pole wyboru w nagłówku tabeli zalecenia.

       ![Lista zaleceń przeglądu](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Aby zastosować wybrane zalecenia, kliknij niebieski **Zaakceptuj wybrane zalecenia** przycisku.

      ![Stosowanie zalecenia](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Możesz również **ręcznie sklasyfikować** kolumny zamiast lub oprócz klasyfikacja na podstawie zaleceń:

   - Kliknij pozycję **Dodaj klasyfikację** w menu u góry okna.

      ![Ręcznie Dodaj klasyfikację](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - W otwartym oknie kontekstu wybierz schemat > tabeli > kolumny, które chcesz sklasyfikować i etykiety typu i poufności informacji. Następnie kliknij przycisk na niebieskim **Dodaj klasyfikację** znajdujący się u dołu okna kontekstu.

      ![Wybierz kolumnę do klasyfikowania](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Aby zakończyć klasyfikacji i trwałe etykiety kolumn (tagów) bazy danych z nowymi metadanymi klasyfikacji, kliknij **Zapisz** w menu u góry okna.

   ![Zapisz](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Inspekcja dostępu do poufnych danych

Ważnym aspektem modelu ochrona informacji jest możliwość monitorowania dostępu do poufnych danych. [Usługa Azure SQL Database Auditing](sql-database-auditing.md) zostało rozszerzone, aby uwzględnić nowe pole w dzienniku inspekcji o nazwie *data_sensitivity_information*, która rejestruje klasyfikacje czułość (etykiety) rzeczywistych danych, który został zwrócony przez Zapytanie.

![Dziennik inspekcji](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Automatyczne Programmatic klasyfikacji

T-SQL umożliwia dodawanie/usuwanie klasyfikacje kolumn, a także pobrać wszystkie klasyfikacje dla całej bazy danych.

> [!NOTE]
> Zarządzanie etykiety za pomocą języka T-SQL, nie ma możliwości weryfikacji etykiety dodawane do kolumny istniejące w organizacji zasad usługi information protection (zestaw etykiet, które są wyświetlane w portalu zalecenia). Dlatego jest maksymalnie można to sprawdzić.

- Dodawanie/aktualizowanie klasyfikacji co najmniej jedną kolumnę: [DODAJ KLASYFIKACJI CHARAKTERU](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Usunąć klasyfikację z co najmniej jedną kolumnę: [KLASYFIKACJA CHARAKTERU LISTY](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Wyświetl wszystkie klasyfikacje w bazie danych: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

Interfejsów API REST można również użyć do programowego zarządzania klasyfikacje. Opublikowanych interfejsów API REST obsługują następujące operacje:

- [Tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) — tworzy lub aktualizuje etykieta poufności danej kolumny
- [Usuń](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) -usuwa etykietę czułości w danej kolumnie
- [Pobierz](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) — pobiera etykieta poufności danej kolumny
- [Lista bieżącą przez bazę danych](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) — pobiera bieżącej etykiety ważności określonej bazy danych
- [Lista zalecanych przez bazę danych](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) — pobiera etykiety zalecane czułości określonej bazy danych

## <a name="permissions"></a>Uprawnienia
Następujące role wbudowane może odczytywać klasyfikację danych Azure SQL database: `Owner`, `Reader`, `Contributor`, `SQL Security Manager` i `User Access Administrator`.

Następujące role wbudowane, można zmodyfikować klasyfikacji danych Azure SQL database: `Owner`, `Contributor`, `SQL Security Manager`.

Dowiedz się więcej o [RBAC dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a id="subheading-5"></a>Następne kroki

- Dowiedz się więcej o [zaawansowanych zabezpieczeń danych](sql-database-advanced-data-security.md).
- Należy rozważyć skonfigurowanie [Azure SQL Database Auditing](sql-database-auditing.md) monitorowania i inspekcji dostępu do danych poufnych niejawnych.

<!--Anchors-->
[SQL data discovery & classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
