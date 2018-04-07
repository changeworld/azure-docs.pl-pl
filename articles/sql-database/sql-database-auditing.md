---
title: Rozpoczynanie pracy z inspekcji bazy danych Azure SQL | Dokumentacja firmy Microsoft
description: Użyj inspekcji bazy danych Azure SQL, aby śledzić zdarzenia bazy danych w dzienniku inspekcji.
services: sql-database
author: giladm
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: giladm
ms.openlocfilehash: 54cd9864f6ff4bd8234e8ec55e158f4213f9f11b
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="get-started-with-sql-database-auditing"></a>Rozpoczynanie pracy z inspekcją bazy danych SQL
Usługa Azure SQL database auditing śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania na koncie magazynu Azure. Inspekcja również:

* Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

* Włącza i ułatwia przestrzeganie standardów zgodności, mimo że nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat usługi Azure programy tego zgodność ze standardami pomocy technicznej, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a id="subheading-1"></a>Omówienie inspekcji Azure bazy danych SQL
Program SQL inspekcji bazy danych:


* **Zachowaj** dziennik inspekcji wybranych zdarzeń. Można zdefiniować kategorie działań przeprowadzać inspekcję bazy danych.
* **Raport** na działanie bazy danych. Wstępnie skonfigurowane raporty i pulpit nawigacyjny umożliwia szybkie rozpoczęcie pracy z aktywności i raportowanie zdarzeń.
* **Analizowanie** raportów. Można znaleźć podejrzane zdarzenia, nietypowe działania i trendów.

Inspekcję można skonfigurować dla różnych kategorii, zgodnie z objaśnieniem w [inspekcja bazy danych](#subheading-2) sekcji.

> [!IMPORTANT]
> Dzienniki inspekcji są zapisywane w **Dołącz obiekty BLOB** w magazynie obiektów Blob platformy Azure w ramach subskrypcji platformy Azure.
>
> * **Magazyn w warstwie Premium** jest obecnie **nieobsługiwane** przez Dołącz obiektów blob.
> * **Magazyn w sieci wirtualnej** jest obecnie **nieobsługiwane**.

## <a id="subheading-8"></a>Zdefiniuj na poziomie serwera, a zasady inspekcji na poziomie bazy danych

Zasady inspekcji mogą być definiowane dla określonej bazy danych lub jako domyślne zasady serwera:

* Zasady serwera ma zastosowanie do wszystkich istniejących i nowo utworzone bazy danych na serwerze.

* Jeśli *Inspekcja obiektów blob serwera jest włączone*, on *zawsze ma zastosowanie do bazy danych*. Bazy danych zostanie przeprowadzona, niezależnie od ustawień inspekcji bazy danych.

* Włączanie Inspekcja obiektów blob w bazie danych, oprócz włączenia go na serwerze, jest *nie* zastąpić lub zmienić dowolne z ustawień inspekcji serwera obiektu blob. Zarówno inspekcji będą istniały obok siebie. Innymi słowy bazy danych jest różna dwukrotnie równolegle; raz przez serwer zasad i raz przez zasady bazy danych.

   > [!NOTE]
   > Należy unikać włączania inspekcji obiektu blob serwera i inspekcja obiektów blob dla bazy danych, chyba że:
    > * Aby użyć innego *konta magazynu* lub *okres przechowywania* dla określonej bazy danych.
    > * Chcesz inspekcji typów zdarzeń lub kategorii dla określonej bazy danych, które różnią się od pozostałej części bazy danych na serwerze. Na przykład może być wstawia tabeli, które muszą być sprawdzona tylko dla określonej bazy danych.
   >
   > W przeciwnym razie zaleca się włączania inspekcji obiektu blob tylko poziom serwera i pozostaw inspekcji bazy danych na poziomie wyłączona dla wszystkich baz danych.


## <a id="subheading-2"></a>Inspekcja bazy danych
W poniższej sekcji opisano konfigurację inspekcji przy użyciu portalu Azure.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do **ustawienia** bloku programu SQL server bazy danych/SQL chcesz inspekcji. W **ustawienia** bloku, wybierz opcję **Inspekcja i wykrywanie zagrożeń**.

    <a id="auditing-screenshot"></a> ![Okienko nawigacji][1]
3. Jeśli wolisz skonfigurować zasady inspekcji serwera, możesz wybrać **wyświetlić ustawienia serwera** łącze w bloku inspekcji bazy danych. Można następnie wyświetlić lub zmodyfikować ustawienia inspekcji serwera. Zasady inspekcji serwera dotyczą wszystkich istniejących i nowo utworzone bazy danych na tym serwerze.

    ![Okienko nawigacji][2]
4. Jeśli wolisz włączyć inspekcję obiektu blob na poziomie bazy danych **inspekcji**, wybierz pozycję **ON**oraz **inspekcji typu**, wybierz pozycję **obiektu Blob**.

    Jeśli inspekcja obiektów blob serwera jest włączone, skonfigurować bazy danych inspekcji będą istniały równolegle z obiektu blob inspekcji serwera.

    ![Okienko nawigacji][3]
5. Aby otworzyć **magazyn dzienników inspekcji** bloku, wybierz opcję **szczegóły magazynu**. Wybierz konto magazynu Azure, w którym zostaną zapisane dzienniki, a następnie wybierz okres przechowywania. Stare dzienniki zostaną usunięte. Następnie kliknij przycisk **OK**.
   >[!TIP]
   >Aby uzyskać maksymalne wykorzystanie inspekcji szablonów raportów, należy użyć tego samego konta magazynu dla wszystkich baz danych inspekcji.

    <a id="storage-screenshot"></a> ![Okienko nawigacji][4]
6. Jeśli chcesz dostosować zdarzeń inspekcji, można to zrobić za pomocą programu PowerShell lub interfejsu API REST.
7. Po skonfigurowaniu ustawień inspekcji można włączyć funkcji wykrywania zagrożeń i skonfigurować wiadomości e-mail w celu otrzymywania alertów zabezpieczeń. Gdy używasz wykrywanie zagrożeń, pojawi się aktywne alerty w przypadku nietypowe działania bazy danych, które mogą wskazywać możliwe zagrożenia bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [wprowadzenie wykrywanie zagrożeń](sql-database-threat-detection-get-started.md).
8. Kliknij pozycję **Zapisz**.





## <a id="subheading-3"></a>Analizowanie dzienników inspekcji i raportów
Dzienniki inspekcji są agregowane w wybranym podczas instalacji konto magazynu Azure. Dzienniki inspekcji można sprawdzić za pomocą narzędzia, takie jak [Eksploratora usługi Storage Azure](http://storageexplorer.com/).

Dzienniki inspekcji obiektów blob są zapisywane jako kolekcja plików obiektów blob w kontenerze o nazwie **sqldbauditlogs**.

Dodatkowe szczegóły dotyczące hierarchii folderu przechowywania konwencje nazewnictwa i format dziennika, zobacz [odwołanie Format dziennika inspekcji obiektu Blob](https://go.microsoft.com/fwlink/?linkid=829599).

Istnieje kilka metod, których można użyć do wyświetlenia obiektu blob, dzienniki inspekcji:

* Użyj [portalu Azure](https://portal.azure.com).  Otwórz odpowiednie bazy danych. W górnej części bazy danych **Inspekcja i wykrywanie zagrożeń** bloku, kliknij przycisk **Przeglądanie dzienników inspekcji**.

    ![Okienko nawigacji][7]

    **Rekordów inspekcji** zostanie otwarty blok, z których będziesz mieć możliwość wyświetlania w dziennikach.

    - Konkretne daty można wyświetlić, klikając **filtru** w górnej części **rekordów inspekcji** bloku.
    - Można przełączać się między rekordów inspekcji, które zostały utworzone przez serwer zasad lub bazy danych zasad kontroli.

       ![Okienko nawigacji][8]

* Użyj funkcji systemowej **sys.fn_get_audit_file** (T-SQL), aby przywrócić dane z dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat używania tej funkcji, zobacz [dokumentacji sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).


* Użyj **scalania plików inspekcji** w programu SQL Server Management Studio (począwszy od SSMS 17):
    1. Wybierz z menu Narzędzia SSMS **pliku** > **Otwórz** > **scalania plików inspekcji**.

        ![Okienko nawigacji][9]
    2. **Dodaj pliki inspekcji** zostanie otwarte okno dialogowe. Wybierz jedną z **Dodaj** opcji, aby zdecydować się na scalanie plików inspekcji na dysku lokalnym lub zaimportować je z usługi Azure Storage. Należy podać szczegóły magazynu Azure i klucza konta.

    3. Po dodaniu wszystkich plików do scalenia kliknij **OK** można ukończyć operacji scalania.

    4. Scalony plik zostanie otwarty w programie SSMS, gdzie możesz można wyświetlać i analizować je, a także go wyeksportować do pliku w pliku XEL lub CSV lub tabeli.

* Użyj [synchronizacji aplikacji](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) , który został utworzony. Działa na platformie Azure, a używa analizy dzienników publiczne interfejsy API, aby wypchnąć dzienników inspekcji SQL do analizy dzienników. Synchronizowanie aplikacji wypchnięcia dzienników inspekcji SQL do analizy dzienników zużycia za pośrednictwem pulpitu nawigacyjnego Analytics dziennika.

* Use Power BI. Można wyświetlać i analizować dane dzienników inspekcji w usłudze Power BI. Dowiedz się więcej o [usługi Power BI i dostęp do pobrania szablonu](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Pobierz pliki dziennika z kontenera obiektów blob magazynu Azure w portalu lub przy użyciu narzędzia, takie jak [Eksploratora usługi Storage Azure](http://storageexplorer.com/).
    * Po pobraniu pliku dziennika, który jest lokalnie, możesz kliknąć dwukrotnie plik, który będzie otwierać, przeglądać i analizować dzienniki w programie SSMS.
    * Możesz również pobrać wielu plików jednocześnie za pomocą Eksploratora usługi Storage platformy Azure. Kliknij prawym przyciskiem myszy określony podfolder, a następnie wybierz **Zapisz jako** można zapisać w folderze lokalnym.

* Dodatkowe metody:
   * Po pobraniu kilka plików lub podfolder, który zawiera pliki dziennika, może scalić je lokalnie zgodnie z opisem w instrukcjach plików inspekcji scalania SSMS opisany wcześniej.

   * Inspekcja obiektów blob widoku programowo dzienników:

     * Użyj [czytnika zdarzeń rozszerzonych](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) biblioteki C#.
     * [Zapytanie rozszerzonych plików zdarzenia](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) przy użyciu programu PowerShell.




## <a id="subheading-5"></a>Wskazówki produkcji
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Inspekcja bazy danych z replikacją geograficzną</a>
W przypadku baz danych z replikacją geograficzną po włączeniu inspekcji w głównej bazie danych dodatkowej bazy danych mają identyczne zasady inspekcji. Istnieje również możliwość skonfigurowania inspekcji w pomocniczej bazie danych przez włączenie inspekcji na **serwer pomocniczy**, niezależnie od podstawowej bazy danych.

* Poziomu serwera (**zalecane**): Włączanie inspekcji zarówno **serwera podstawowego** , jak również **serwer pomocniczy** — podstawowych i pomocniczych baz danych zostanie każdego przeprowadzać inspekcję niezależnie od siebie na podstawie ich odpowiednich zasad poziomu serwera.

* Poziom bazy danych: Poziom bazy danych inspekcji dla pomocniczej bazy danych można skonfigurować tylko z podstawowej bazy danych, ustawienia inspekcji.
   * Inspekcja obiektów blob musi być włączona na *podstawowa baza danych sam*, nie na serwerze.
   * Po włączeniu inspekcji obiektu blob na podstawowej bazy danych, również będą stają się włączone w pomocniczej bazie danych.

     >[!IMPORTANT]
     >Z poziomu bazy danych inspekcji, ustawienia magazynu dla pomocniczej bazy danych będzie identyczne z podstawowej bazy danych, powodując ruchu między regionalne. Zaleca się włączania inspekcji tylko poziomu serwera i pozostawić inspekcji bazy danych na poziomie wyłączona dla wszystkich baz danych.
<br>

### <a id="subheading-6">Ponowne generowanie klucza magazynu</a>
W środowisku produkcyjnym najprawdopodobniej będzie okresowo Odśwież kluczy magazynu. Podczas odświeżania klucze, należy ponownie zapisać zasady inspekcji. Proces przebiega w następujący sposób:

1. Otwórz **szczegóły magazynu** bloku. W **klucz dostępu do magazynu** wybierz opcję **dodatkowej**i kliknij przycisk **OK**. Następnie kliknij przycisk **zapisać** w górnej części bloku konfiguracji inspekcji.

    ![Okienko nawigacji][5]
2. Przejdź do bloku konfiguracji magazynu i ponownie wygenerować podstawowy klucz dostępu.

    ![Okienko nawigacji][6]
3. Przejdź wstecz do inspekcji bloku konfiguracji przełącznika klucz dostępu do magazynu z podstawowym dodatkowej, a następnie kliknij przycisk **OK**. Następnie kliknij przycisk **zapisać** w górnej części bloku konfiguracji inspekcji.
4. Wróć do bloku konfiguracji magazynu, a następnie ponownie wygenerować pomocniczy klucz dostępu (w ramach przygotowania do klucza następnego cyklu odświeżania).

## <a name="manage-sql-database-auditing-using-azure-powershell"></a>Zarządzanie za pomocą programu Azure PowerShell inspekcja bazy danych SQL


* **Polecenia cmdlet programu PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditing][101]
   * [Get-AzureRMSqlServerAuditing][102]
   * [Set-AzureRMSqlDatabaseAuditing][105]
   * [Set-AzureRMSqlServerAuditing][106]

   Na przykład skryptu, zobacz [konfigurowania inspekcji i wykrywania zagrożeń przy użyciu programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="manage-sql-database-auditing-using-rest-api"></a>Zarządzanie za pomocą interfejsu API REST inspekcja bazy danych SQL

* **Interfejs API REST - Inspekcja obiektów Blob**:

   * [Utwórz lub zaktualizuj bazę danych obiektów Blob zasady inspekcji](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [Utwórz lub zaktualizuj Blob serwera zasady inspekcji](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [Pobierz bazy danych obiektów Blob zasady inspekcji](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [Pobierz obiekt Blob serwera zasady inspekcji](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [Pobierz Blob serwera inspekcji wynik operacji](https://msdn.microsoft.com/library/azure/mt771862.aspx)


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditing
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditing
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditing
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditing
