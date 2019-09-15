---
title: Azure SQL Database i SQL Data Warehouse odnajdywania danych & klasyfikacji | Microsoft Docs
description: Klasyfikacja & Azure SQL Database i odnajdywania danych
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: 0bba755ec5683e988bb8ae3f2308a1c298b228b0
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000559"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>Klasyfikacja & Azure SQL Database i SQL Data Warehouse odnajdywania danych

Funkcja odnajdywania danych & zapewnia zaawansowane funkcje wbudowane w Azure SQL Database do **odnajdywania**, **klasyfikowania** & i **etykietowania** **ochrony** poufnych danych w bazach danych.

Odnajdywanie i klasyfikowanie najbardziej poufnych danych (biznes, finanse, opieka, dane osobowe) i tak dalej.) może odgrywać rolę przestawną w schemacieie ochrony informacji organizacji. Może to być infrastruktura dla:

- Pomóc spełnić standardy prywatności danych i wymagania dotyczące zgodności z przepisami.
- Różne scenariusze zabezpieczeń, takie jak monitorowanie (inspekcja) i alerty dotyczące nietypowego dostępu do poufnych danych.
- Kontrolowanie dostępu do i ograniczanie bezpieczeństwa baz danych zawierających wysoce poufne dane.

Funkcja odnajdywania danych & klasyfikacja jest częścią oferty [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń SQL. do funkcji odnajdywania danych & można uzyskać dostęp i zarządzać nimi za pośrednictwem centralnego portalu SQL ADS.

> [!NOTE]
> Ten dokument odnosi się do Azure SQL Database i Azure SQL Data Warehouse. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database. Aby uzyskać SQL Server (lokalnie), zobacz [odnajdywanie i Klasyfikacja danych SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Co to jest funkcja odnajdywania danych & Klasyfikacja

Funkcja odnajdywania danych & Klasyfikacja zawiera zestaw zaawansowanych usług i nowych funkcji SQL, tworząc nowy model Information Protection SQL, którego celem jest ochrona danych, a nie tylko bazy danych:

- **Zalecenia dotyczące & odnajdywania**

  Aparat klasyfikacji skanuje bazę danych i identyfikuje kolumny zawierające potencjalnie poufne dane. Dzięki temu można łatwo przejrzeć i zastosować odpowiednie zalecenia klasyfikacji za pośrednictwem Azure Portal.

- **Etykietowania**

  Etykiety klasyfikacji czułości można trwale oznaczyć w kolumnach przy użyciu nowych atrybutów metadanych klasyfikacji wprowadzonych do aparatu SQL. Te metadane można następnie wykorzystać na potrzeby zaawansowanych scenariuszy inspekcji i ochrony opartej na czułości.

- **Czułość zestawu wyników zapytania**

  Czułość zestawu wyników zapytania jest obliczana w czasie rzeczywistym na potrzeby inspekcji.

- **Widoczność**

  Stan klasyfikacji bazy danych można wyświetlić na szczegółowym pulpicie nawigacyjnym w portalu. Ponadto można pobrać raport (w formacie programu Excel), który będzie używany do celów inspekcji &, a także innych potrzeb.

## <a id="subheading-2"></a>Odkryj, Klasyfikuj & poufne kolumny etykiet

W poniższej sekcji opisano kroki odnajdywania, klasyfikowania i etykietowania kolumn zawierających poufne dane w bazie danych, a także wyświetlania bieżącego stanu klasyfikacji bazy danych i eksportowania raportów.

Klasyfikacja obejmuje dwa atrybuty metadanych:

- Etykiety — główne atrybuty klasyfikacji używane do definiowania poziomu czułości danych przechowywanych w kolumnie.  
- Typy informacji — zapewniają dodatkowy poziom szczegółowości do typu danych przechowywanych w kolumnie.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definiowanie i dostosowywanie taksonomii klasyfikacji

Funkcja SQL Data Discovery & Klasyfikacja zawiera wbudowany zestaw etykiet czułości i wbudowany zestaw typów informacji oraz logiki odnajdywania. Masz teraz możliwość dostosowania tej taksonomii i zdefiniowania zestawu i klasyfikacji konstrukcji klasyfikacji przeznaczonych dla danego środowiska.

Definicja i dostosowanie taksonomii klasyfikacji są wykonywane w jednym centralnym miejscu dla całej dzierżawy platformy Azure. Ta lokalizacja znajduje się w [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)w ramach zasad zabezpieczeń. Tylko ktoś z uprawnieniami administracyjnymi w głównej grupie zarządzania dzierżawcą może wykonać to zadanie.

W ramach zarządzania zasadami Information Protection można definiować etykiety niestandardowe, ustalać ich rangę i kojarzyć je z wybranym zestawem typów informacji. Można również dodać własne niestandardowe typy informacji i skonfigurować je za pomocą wzorców ciągów, które są dodawane do logiki odnajdywania w celu identyfikowania tego typu danych w bazach danych.
Dowiedz się więcej na temat dostosowywania zasad i zarządzania nimi w temacie [zasady Information Protection wskazówki](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Po zdefiniowaniu zasad dla dzierżawy możesz kontynuować klasyfikację poszczególnych baz danych przy użyciu dostosowanych zasad.

## <a name="classify-your-sql-database"></a>Klasyfikowanie SQL Database

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

2. Przejdź do pozycji **Zaawansowane zabezpieczenia danych** w obszarze nagłówek zabezpieczenia w okienku Azure SQL Database. Kliknij, aby włączyć zaawansowane zabezpieczenia danych, a następnie kliknij kartę **odnajdywanie danych &** .

   ![Skanowanie bazy danych](./media/sql-data-discovery-and-classification/data_classification.png)

3. Karta **Przegląd** zawiera podsumowanie bieżącego stanu klasyfikacji bazy danych, w tym szczegółową listę wszystkich sklasyfikowanych kolumn, które można również filtrować w celu wyświetlenia tylko określonych części schematu, typów informacji i etykiet. Jeśli nie zostały jeszcze sklasyfikowane żadne kolumny, [Przejdź do kroku 5](#step-5).

   ![Podsumowanie bieżącego stanu klasyfikacji](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Aby pobrać raport w formacie programu Excel, kliknij opcję **eksportu** w górnym menu okna.

   ![Eksportuj do programu Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Aby rozpocząć klasyfikowanie danych, kliknij **kartę Klasyfikacja** w górnej części okna.

    ![Klasyfikowanie danych](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. Aparat klasyfikacji skanuje bazę danych pod kątem kolumn zawierających potencjalnie wrażliwe dane i zawiera listę **zalecanych klasyfikacji kolumn**. Aby wyświetlić i zastosować zalecenia dotyczące klasyfikacji:

   - Aby wyświetlić listę zalecanych klasyfikacji kolumn, kliknij panel zalecenia u dołu okna:

      ![Klasyfikowanie danych](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Przejrzyj listę zaleceń — aby zaakceptować zalecenie dotyczące konkretnej kolumny, zaznacz pole wyboru w lewej kolumnie odpowiedniego wiersza. Możesz również oznaczyć *wszystkie zalecenia* jako zaakceptowane, zaznaczając pole wyboru w nagłówku tabeli rekomendacji.

       ![Przejrzyj listę rekomendacji](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Aby zastosować wybrane rekomendacje, kliknij przycisk z niebieską **akceptacją wybrane rekomendacje** .

      ![Zastosuj rekomendacje](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Możesz również **ręcznie klasyfikować** kolumny jako alternatywę, a także klasyfikację opartą na rekomendacji:

   - Kliknij pozycję **Dodaj klasyfikację** w górnym menu okna.

      ![Ręcznie Dodaj klasyfikację](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - W otwartym oknie kontekstu wybierz tabelę > schematu > kolumnę, którą chcesz sklasyfikować, a następnie typ informacji i etykieta czułości. Następnie kliknij niebieskie przycisk **Dodaj klasyfikację** u dołu okna kontekstu.

      ![Wybierz kolumnę do klasyfikowania](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Aby dokończyć swoją klasyfikację i trwale oznaczyć (tag) kolumnami bazy danych nowymi metadanymi klasyfikacji, kliknij przycisk **Zapisz** w górnym menu okna.

   ![Zapisanie](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Inspekcja dostępu do poufnych danych

Ważnym aspektem modelu ochrony informacji jest możliwość monitorowania dostępu do poufnych danych. [Inspekcja Azure SQL Database](sql-database-auditing.md) została ulepszona w celu uwzględnienia nowego pola w dzienniku inspekcji o nazwie *data_sensitivity_information*, który rejestruje klasyfikacje czułości (etykiety) rzeczywistych danych zwróconych przez zapytanie.

![Dziennik inspekcji](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Zarządzanie klasyfikacją danych przy użyciu języka T-SQL

Przy użyciu języka T-SQL można dodawać/usuwać klasyfikacje kolumn, a także pobierać wszystkie klasyfikacje dla całej bazy danych.

> [!NOTE]
> W przypadku zarządzania etykietami przy użyciu języka T-SQL nie istnieje weryfikacja, czy etykiety dodawane do kolumny istnieją w zasadach usługi Information Protection (zestaw etykiet, które są wyświetlane w zaleceniach portalu). W związku z tym można sprawdzić poprawność tego procesu.

- Dodaj/zaktualizuj klasyfikację jednej lub kilku kolumn: [DODAJ KLASYFIKACJĘ CZUŁOŚCI](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Usuń klasyfikację z jednej lub kilku kolumn: [KLASYFIKACJA USUWANIA CZUŁOŚCI](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Wyświetl wszystkie klasyfikacje w bazie danych: [sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="manage-classifications-using-rest-apis"></a>Zarządzanie klasyfikacjami przy użyciu interfejsów API REST

Za pomocą interfejsów API REST można także programowo zarządzać klasyfikacjami. Opublikowane interfejsy API REST obsługują następujące operacje:

- [Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) — tworzy lub aktualizuje etykietę czułości danej kolumny.
- [Usuń](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) — usuwa etykietę czułości danej kolumny.
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) -pobiera etykietę czułości danej kolumny
- [Lista bieżąca według bazy danych](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) — pobiera bieżące etykiety czułości danej bazy danych
- [Lista zalecana przez bazę danych](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) — pobiera zalecane etykiety czułości danej bazy danych.

## <a name="manage-data-discovery-and-classification-using-azure-powershell"></a>Zarządzanie odnajdywaniem i klasyfikacją danych przy użyciu Azure PowerShell

Program PowerShell umożliwia pobieranie wszystkich zalecanych kolumn w bazie danych SQL Azure i wystąpienia zarządzanego.

### <a name="powershell-cmdlets-for-azure-sql-database"></a>Polecenia cmdlet programu PowerShell dla usługi Azure SQL Database

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>Polecenia cmdlet programu PowerShell dla wystąpienia zarządzanego

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)

## <a name="permissions"></a>Uprawnienia

Następujące wbudowane `Owner`role mogą odczytywać klasyfikację danych usługi Azure SQL Database:, `Reader`, `Contributor` `SQL Security Manager` i `User Access Administrator`.

Następujące wbudowane role mogą modyfikować klasyfikację danych usługi Azure SQL Database: `Owner`, `Contributor`, `SQL Security Manager`.

Dowiedz się więcej o rolach [RBAC dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a id="subheading-5"></a>Następne kroki

- Dowiedz się więcej o [zaawansowanych zabezpieczeniach danych](sql-database-advanced-data-security.md).
- Należy rozważyć skonfigurowanie [inspekcji Azure SQL Database](sql-database-auditing.md) na potrzeby monitorowania i inspekcji dostępu do danych poufnych.

<!--Anchors-->
[SQL data discovery & classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
