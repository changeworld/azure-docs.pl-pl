---
title: Odnajdowanie i klasyfikacja danych
description: Odnajdowanie danych & klasyfikacji dla usługi Azure SQL Database i usługi Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: eb4e7907c3dcffed035307c2084160ce6051be13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409953"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Odnajdowanie danych & klasyfikacji dla usługi Azure SQL Database i usługi Azure Synapse Analytics

Odnajdowanie danych & Klasyfikacja zapewnia zaawansowane funkcje wbudowane w usługę Azure SQL Database do **odnajdywania,** **klasyfikowania,** **etykietowania** & **raportowania** poufnych danych w bazach danych.

Odnajdowanie i klasyfikowanie najbardziej poufnych danych (biznesowych, finansowych, opieki zdrowotnej, danych osobowych itd.) może odegrać kluczową rolę w rozwoju ochrony informacji organizacji. Może to stanowić infrastrukturę dla następujących działań:

- Pomoc przy spełnianiu standardów dotyczących prywatności danych i wymagań dotyczących zgodności z przepisami.
- Różne scenariusze zabezpieczeń, takie jak monitorowanie (inspekcja) i alerty o nietypowym dostępie do poufnych danych.
- Kontrolowanie dostępu do baz danych zawierających wysoce poufne informacje i zwiększanie ich bezpieczeństwa.

Odnajdowanie danych & Klasyfikacja jest częścią oferty [Zaawansowane bezpieczeństwo danych](sql-database-advanced-data-security.md) (ADS), która jest ujednoliconym pakietem dla zaawansowanych funkcji zabezpieczeń SQL. odnajdowanie danych & klasyfikacji można uzyskać dostęp do centralnej aplikacji SQL ADS portal i nimi zarządzać.

> [!NOTE]
> Ten dokument dotyczy usługi Azure SQL Database i Usługi Azure Synapse. Dla uproszczenia bazy danych SQL jest używany podczas odwoływania się do bazy danych SQL i platformy Azure Synapse. Dla programu SQL Server (lokalnie) zobacz [Odnajdowanie i klasyfikacja danych SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="subheading-1"></a>Co to jest klasyfikacja & odnajdowania danych

Odnajdowanie danych & Klasyfikacja wprowadza zestaw zaawansowanych usług i nowych możliwości SQL, tworząc nowy paradygmat ochrony informacji SQL mające na celu ochronę danych, a nie tylko bazy danych:

- **Zalecenia dotyczące & odnajdowania**

  Aparat klasyfikacji skanuje bazę danych i identyfikuje kolumny zawierające potencjalnie poufne dane. Następnie udostępnia łatwy sposób przeglądania i stosowania odpowiednich rekomendacji dotyczących klasyfikacji za pośrednictwem witryny Azure Portal.

- **Etykietowania**

  Etykiety klasyfikacji czułości mogą być trwale oznaczane w kolumnach przy użyciu nowych atrybutów metadanych klasyfikacji wprowadzonych do aparatu SQL. Te metadane można następnie wykorzystać na potrzeby zaawansowanych scenariuszy inspekcji i ochrony opartej na poufności.

- **Czułość zestawu wyników kwerendy**

  Czułość zestawu wyników kwerendy jest obliczana w czasie rzeczywistym do celów inspekcji.

- **Widoczność**

  Stan klasyfikacji bazy danych można wyświetlić na szczegółowym pulpicie nawigacyjnym w portalu. Ponadto można pobierać raporty (w formacie programu Excel), których można używać w celu zapewnienia zgodności i inspekcji, a także do innych celów.

## <a name="discover-classify--label-sensitive-columns"></a><a id="subheading-2"></a>Odnajduj, klasyfikuj kolumny & etykiet

W poniższej sekcji opisano kroki dotyczące odnajdywania, klasyfikowania i etykietowania kolumn zawierających poufne dane w bazie danych, a także wyświetlania bieżącego stanu klasyfikacji bazy danych i eksportowania raportów.

Klasyfikacja zawiera dwa atrybuty metadanych:

- Etykiety — główne atrybuty klasyfikacji, używane do definiowania poziomu czułości danych przechowywanych w kolumnie.  
- Typy informacji — zapewniają dodatkowe ziarnistość do typu danych przechowywanych w kolumnie.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definiowanie i dostosowywanie taksonomii klasyfikacji

Odnajdowanie danych & Klasyfikacja jest wyposażony w wbudowany zestaw etykiet czułości i wbudowany zestaw typów informacji i logiki odnajdywania. Teraz masz możliwość dostosowania tej taksonomii i zdefiniować zestaw i ranking konstrukcji klasyfikacji specjalnie dla twojego środowiska.

Definiowanie i dostosowywanie taksonomii klasyfikacji odbywa się w jednym centralnym miejscu dla całej dzierżawy platformy Azure. Ta lokalizacja znajduje się w [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), w ramach zasad zabezpieczeń. Tylko osoba z uprawnieniami administracyjnymi w głównej grupie zarządzania dzierżawcy może wykonać to zadanie.

W ramach zarządzania zasadami ochrony informacji można definiować etykiety niestandardowe, klasyfikować je i kojarzyć z wybranym zestawem typów informacji. Można również dodawać własne niestandardowe typy informacji i konfigurować je za pomocą wzorców ciągów, które są dodawane do logiki odnajdywania na potrzeby identyfikowania tego typu danych w bazach danych.
Dowiedz się więcej o dostosowywaniu zasad i zarządzaniu nimi w [przewodniku po zasadach ochrony informacji](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Po zdefiniowaniu zasad dla całej dzierżawy można kontynuować klasyfikację poszczególnych baz danych przy użyciu niestandardowych zasad.

## <a name="classify-your-sql-database"></a>Klasyfikowanie bazy danych SQL

1. Przejdź do [witryny Azure portal](https://portal.azure.com).

2. Przejdź do **pozycji Zaawansowane zabezpieczenia danych** w nagłówku Zabezpieczenia w okienku usługi Azure SQL Database. Kliknij, aby włączyć zaawansowane zabezpieczenia danych, a następnie kliknij kartę **odnajdowanie danych & klasyfikacji.**

   ![Skanowanie bazy danych](./media/sql-data-discovery-and-classification/data_classification.png)

3. Karta **Przegląd** zawiera podsumowanie bieżącego stanu klasyfikacji bazy danych, w tym szczegółową listę wszystkich kolumn niejawnych, które można również filtrować, aby wyświetlić tylko określone części schematu, typy informacji i etykiety. Jeśli nie zaklasyfikowałeś jeszcze żadnych kolumn, [przejdź do kroku 5](#step-5).

   ![Podsumowanie bieżącego stanu klasyfikacji](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Aby pobrać raport w formacie programu Excel, kliknij opcję **Eksportuj** w górnym menu okna.

   ![Eksportuj do programu Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Aby rozpocząć klasyfikowanie danych, kliknij **kartę Klasyfikacja** u góry okna.

    ![Klasyfikowanie danych](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. Aparat klasyfikacji skanuje bazę danych w poszukiwaniu kolumn zawierających potencjalnie poufne dane i udostępnia listę **zalecanych klasyfikacji kolumn**. Aby wyświetlić i zastosować zalecenia dotyczące klasyfikacji:

   - Aby wyświetlić listę zalecanych klasyfikacji kolumn, kliknij panel rekomendacji u dołu okna:

      ![Klasyfikowanie danych](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Przejrzyj listę zaleceń — aby zaakceptować zalecenie dla określonej kolumny, zaznacz pole wyboru w lewej kolumnie odpowiedniego wiersza. Można również *oznaczyć wszystkie zalecenia* jako zaakceptowane, zaznaczając pole wyboru w nagłówku tabeli rekomendacji.

       ![Przejrzyj listę rekomendacji](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Aby zastosować wybrane rekomendacje, kliknij niebieski przycisk **Zaakceptuj wybrane rekomendacje.**

      ![Stosowanie zaleceń](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Kolumny można również **klasyfikować ręcznie** jako alternatywną lub dodatkowo do klasyfikacji opartej na zaleceniach:

   - Kliknij **dodaj klasyfikację** w górnym menu okna.

      ![Ręczne dodawanie klasyfikacji](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - W otwartym oknie kontekstu wybierz schemat > tabelę > kolumnę, którą chcesz sklasyfikować, oraz etykietę typu informacji i czułości. Następnie kliknij niebieski przycisk **Dodaj klasyfikację** u dołu okna kontekstu.

      ![Wybierz kolumnę do sklasyfikowania](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Aby ukończyć klasyfikację i trwale oznaczyć (oznaczyć) kolumny bazy danych nowymi metadanymi klasyfikacji, kliknij **przycisk Zapisz** w górnym menu okna.

   ![Zapisz](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a name="auditing-access-to-sensitive-data"></a><a id="subheading-3"></a>Inspekcja dostępu do poufnych danych

Ważnym aspektem paradygmatu ochrony informacji jest możliwość monitorowania dostępu do poufnych danych. [Usługa Azure SQL Database Auditing](sql-database-auditing.md) została rozszerzona o nowe pole w dzienniku inspekcji o nazwie *data_sensitivity_information*, które rejestruje klasyfikacje czułości (etykiety) rzeczywistych danych, które zostały zwrócone przez kwerendę.

![Dziennik inspekcji](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="subheading-4"></a>Uprawnienia

Następujące wbudowane role mogą odczytywać klasyfikację danych `Owner` `Reader`bazy `Contributor` `SQL Security Manager` danych `User Access Administrator`SQL platformy Azure: , , i .

Następujące wbudowane role mogą modyfikować klasyfikację danych `Owner`bazy `Contributor` `SQL Security Manager`danych SQL platformy Azure: , , .

Dowiedz się więcej o [zasobach RBAC dla platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="subheading-5"></a>Zarządzanie klasyfikacjami

# <a name="t-sql"></a>[T-SQL](#tab/azure-t-sql)
Za pomocą języka T-SQL można dodawać/usuwać klasyfikacje kolumn, a także pobierać wszystkie klasyfikacje dla całej bazy danych.

> [!NOTE]
> Podczas korzystania z języka T-SQL do zarządzania etykietami, nie ma sprawdzania poprawności, że etykiety dodane do kolumny istnieją w zasadach ochrony informacji organizacji (zestaw etykiet, które pojawiają się w zaleceniach portalu). W związku z tym do Ciebie, aby to potwierdzić.

- Dodawanie/aktualizowanie klasyfikacji jednej lub więcej kolumn: [DODAJ KLASYFIKACJĘ CZUŁOŚCI](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Usuwanie klasyfikacji z jednej lub więcej kolumn: [KLASYFIKACJA WRAŻLIWOŚCI UPUŚCIEŃ](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Wyświetl wszystkie klasyfikacje w bazie danych: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

# <a name="rest-apis"></a>[Interfejsy API odpoczynku](#tab/azure-rest-api)
Interfejsy API REST można używać do programowego zarządzania klasyfikacjami i zaleceniami. Opublikowane interfejsy API REST obsługują następujące operacje:

- [Tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) — tworzy lub aktualizuje etykietę czułości danej kolumny
- [Usuń](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) — usuwa etykietę czułości danej kolumny
- [Wyłącz zalecenie](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) — wyłącza zalecenia dotyczące wrażliwości w danej kolumnie
- [Włącz zalecenie](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) — włącza zalecenia dotyczące wrażliwości w danej kolumnie (zalecenia są domyślnie włączone we wszystkich kolumnach)
- [Pobierz](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) — pobiera etykietę czułości danej kolumny
- [Lista bieżących według bazy danych](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) — pobiera bieżące etykiety czułości danej bazy danych
- [Lista zalecana przez bazę danych](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) — pobiera zalecane etykiety czułości danej bazy danych

# <a name="powershell-cmdlet"></a>[PowerShell Cmdlet](#tab/azure-powelshell)
Program PowerShell służy do zarządzania klasyfikacjami i zaleceniami dla bazy danych SQL azure i wystąpienia zarządzanego.

### <a name="powershell-cmdlet-for-azure-sql-database"></a>Polecenie cmdlet programu PowerShell dla bazy danych SQL platformy Azure
- [Klasyfikacja jakościOwa Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityKlasyfikacja](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Usuń-AzSqlDatabaseSensitivityKlasyfikacja](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRekomemonia](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityReommendation Enable-AzSqlDatabaSesensitivityReommendation Enable-AzSqlDatabaSesensitivityRecommendation Enable-](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Wyłącz-AzSqlDatabaseSensitivityRekomemagnięć](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>Polecenia cmdlet programu PowerShell dla wystąpienia zarządzanego
- [Klasyfikacja informacji o adresach dźwiękowych Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Usuń-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityReommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityReommendation Enable-AzSqlInstanceDatabaseSensitivityRecommendation Enable-AzSqlInstanceDatabaseSensitivityRecommendation Enable-](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Wyłącz-AzSqlInstanceDatabaseSensitivityReommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

---

## <a name="next-steps"></a><a id="subheading-6"></a>Następne kroki

- Dowiedz się więcej o [zaawansowanym bezpieczeństwie danych](sql-database-advanced-data-security.md).
- Należy rozważyć skonfigurowanie [usługi Azure SQL Database Auditing](sql-database-auditing.md) do monitorowania i inspekcji dostępu do niejawnych poufnych danych.
- Aby uzyskać prezentację YouTube zawierającą odnajdowanie danych & klasyfikacja, zobacz [Odnajdywanie, klasyfikowanie, etykietowanie & ochrona danych SQL | Ujawniono dane](https://www.youtube.com/watch?v=itVi9bkJUNc).

<!--Anchors-->
[What is data discovery & classification]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Permissions]: #subheading-4
[Manage classifications]: #subheading-5
[Next Steps]: #subheading-6
