---
title: Przeprowadzanie inspekcji w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Więcej informacji na temat inspekcji i sposób konfigurowania inspekcji w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/11/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: d9a911dccf3d59bf1159cf8576b95d86ef26657b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314249"
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Przeprowadzanie inspekcji w usłudze Azure SQL Data Warehouse

Więcej informacji na temat inspekcji i sposób konfigurowania inspekcji w usłudze Azure SQL Data Warehouse.

## <a name="what-is-auditing"></a>Co to jest inspekcja?
Inspekcja SQL Data Warehouse pozwala na rekord, który w bazie danych inspekcji w dzienniku na koncie usługi Azure Storage. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych i uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

Narzędzia inspekcji Włącz i ułatwienia zgodności ze standardami zgodności, ale nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat usługi Azure programy zgodność ze standardami tej pomocy technicznej, zobacz [Centrum zaufania systemu Azure](https://azure.microsoft.com/support/trust-center/compliance/).

## <a id="subheading-1"></a>Podstawowe informacje o inspekcji
Inspekcja bazy danych SQL Data Warehouse pozwala na:

* **Zachowaj** dziennik inspekcji wybranych zdarzeń. Można zdefiniować kategorie działań bazy danych powinien być poddany inspekcji.
* **Raport** aktywność bazy danych. Wstępnie skonfigurowane raporty i pulpit nawigacyjny umożliwia szybkie rozpoczynanie pracy z działaniem i raportowanie zdarzeń.
* **Analizowanie** raportów. Możesz znaleźć podejrzanych zdarzeń, nietypowej aktywności i trendów.

Dzienniki inspekcji są przechowywane na koncie magazynu platformy Azure. Można zdefiniować okresu przechowywania dziennika inspekcji.


## <a id="subheading-4"></a>Zdefiniuj poziom serwera, a zasady inspekcji na poziomie bazy danych

Można zdefiniować zasady inspekcji dla konkretnej bazy danych lub jako domyślne zasady serwera:

* Zasady serwera **ma zastosowanie do wszystkich istniejących i nowo utworzonej bazy danych** na serwerze.

* Jeśli *Inspekcja obiektów blob serwera jest włączona*, jego *zawsze ma zastosowanie do bazy danych*. Baza danych będzie monitorowane, niezależnie od ustawienia inspekcji bazy danych.

* Włączanie inspekcji bazy danych, oprócz Włączanie go na serwerze jest *nie* zastąpić lub zmienić ustawienia inspekcji obiektów blob serwera. Zarówno inspekcji będą istnieć obok siebie. Innymi słowy bazy danych jest różna dwa razy w sposób równoległy; raz przez zasady serwera banku i raz przez zasady bazy danych.

> [!NOTE]
> Zalecane jest, aby włączyć **inspekcji obiektów blob tylko poziom serwera** i pozostawić inspekcji poziomu bazy danych wyłączone dla wszystkich baz danych.
> Należy unikać włączenie inspekcji serwera i bazy danych inspekcji ze sobą, chyba że:
> * Aby użyć innego *konta magazynu* lub *okres przechowywania* dla konkretnej bazy danych.
> * Chcesz inspekcji zdarzeń typów lub kategorii dla konkretnej bazy danych, które różnią się od pozostałej części bazy danych na serwerze. Na przykład może być wstawia tabeli, które należy przeprowadzić inspekcję tylko dla konkretnej bazy danych.
> * Chcesz użyć wykrywania zagrożeń, która jest obecnie obsługiwane tylko w przypadku inspekcję na poziomie bazy danych.

> [!IMPORTANT]
>Włączanie inspekcji usługi Azure SQL Data Warehouse, lub na serwerze usługi Azure SQL Data Warehouse, **spowoduje w magazynie danych wznawiane**, nawet w przypadku, gdy wcześniej został wstrzymany. **Upewnij się, na wstrzymanie magazynu danych ponownie, po włączeniu inspekcji**.

## <a id="subheading-5"></a>Konfigurowanie serwera do poziomu inspekcji dla wszystkich baz danych

Zasady inspekcji serwera ma zastosowanie do **baz danych wszystkich istniejących i nowo utworzony** na serwerze.

W poniższej sekcji opisano konfigurację inspekcji przy użyciu witryny Azure portal.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do **programu SQL server** , którą chcesz inspekcji (ważny, upewnij się, że programu SQL server nie określonej bazy danych/magazyn danych). W **zabezpieczeń** menu, wybierz opcję **inspekcji i wykrywania zagrożeń**.

    ![Okienko nawigacji][6]
4. W *inspekcji i wykrywania zagrożeń* bloku dla **inspekcji** wybierz **ON**. Te zasady inspekcji będą dotyczyć wszystkich istniejących i nowo utworzonej bazy danych na tym serwerze.

    ![Okienko nawigacji][7]
5. Aby otworzyć **magazyn dzienników inspekcji** bloku wybierz **szczegóły magazynu**. Wybierz lub Utwórz konto magazynu platformy Azure, której będą zapisywane dzienniki, a następnie wybierz okres przechowywania (stare dzienniki zostaną usunięte). Następnie kliknij przycisk **OK**.

    ![Okienko nawigacji][8]

    > [!IMPORTANT]
    > Dzienniki inspekcji są zapisywane w **obiekty BLOB dołączania** w usłudze Azure Blob storage w ramach subskrypcji platformy Azure.
    >
    > - Wszelkiego rodzaju magazynu (v1, v2, obiektów blob) są obsługiwane.
    > - Obsługiwane są wszystkie konfiguracje replikacji magazynu.
    > - **Usługa Premium storage** jest obecnie **nieobsługiwane**.
    > - **Magazyn w sieci wirtualnej** jest obecnie **nieobsługiwane**.
    > - **Magazyn za zaporą** jest obecnie **nieobsługiwane**

8. Kliknij pozycję **Zapisz**.



## <a id="subheading-2"></a>Ustawienie poziomie bazy danych inspekcji dla pojedynczej bazy danych

Można zdefiniować zasady inspekcji dla konkretnej bazy danych lub jako domyślne zasady serwera.

Zdecydowanie zalecane jest użycie serwera inspekcji i przeprowadzanie inspekcji nie poziom bazy danych, zgodnie z opisem w [zdefiniuj poziom serwera, a zasady inspekcji na poziomie bazy danych](#subheading-4)

Przed rozpoczęciem konfigurowania inspekcji inspekcje wyboru, jeśli używasz ["Klientów niższych poziomów"](sql-data-warehouse-auditing-downlevel-clients.md).


1. Uruchom [witryny Azure portal](https://portal.azure.com).
2. Przejdź do **ustawienia** usługi SQL Data Warehouse, mają być poddane inspekcji. Wybierz **inspekcji i wykrywania zagrożeń**.

    ![][1]
3. Następnie włącz inspekcję, klikając **ON** przycisku.

    ![][3]
4. W panelu inspekcji konfiguracji wybierz **szczegóły MAGAZYNU** aby otworzyć panel magazyn dzienników inspekcji. Wybierz konto magazynu platformy Azure dla dzienników i okres przechowywania.
    >[!TIP]
    >Użyj tego samego konta magazynu dla wszystkich baz danych poddawanych inspekcji, aby uzyskać w pełni wykorzystać szablony raportów wstępnie skonfigurowane.

    ![][4]

5. Kliknij przycisk **OK** przycisk, aby zapisać konfigurację szczegółów magazynu.
6. W obszarze **rejestrowanie przez zdarzenie**, kliknij przycisk **Powodzenie** i **błąd** do rejestrowania wszystkich zdarzeń lub wybierz kategorie poszczególnych zdarzeń.
7. W przypadku konfigurowania inspekcji dla bazy danych, może być konieczne zmienić parametry połączenia klienta, aby upewnić się, że prawidłowo przechwyconych danych inspekcji. Sprawdź [zmodyfikować nazwę FQDN serwera w parametrach połączenia](sql-data-warehouse-auditing-downlevel-clients.md) tematu dla połączeń klientów niższych poziomów.
8. Kliknij przycisk **OK**.

## <a id="subheading-3"></a>Analizowanie dzienników inspekcji i raporty

### <a name="server-level-policy-audit-logs"></a>Dzienniki inspekcji zasad na poziomie serwera
Dzienniki inspekcji na poziomie serwera są zapisywane w **obiekty BLOB dołączania** w usłudze Azure Blob storage w ramach subskrypcji platformy Azure. Są one zapisywane jako zbiór plików obiektów blob w kontenerze o nazwie **sqldbauditlogs**.

Aby uzyskać więcej informacji o hierarchii folderu przechowywania konwencji nazewnictwa i format dziennika zobacz [odwołanie Format dziennika inspekcji obiektów Blob](https://go.microsoft.com/fwlink/?linkid=829599).

Istnieje kilka metod, których można użyć, aby wyświetlić dzienniki inspekcji obiektów blob:

* Użyj **scalania plików inspekcji** w SQL Server Management Studio (począwszy od SSMS 17):
    1. Wybierz z menu Narzędzia SSMS **pliku** > **Otwórz** > **scalania plików inspekcji**.

    2. **Dodaj pliki inspekcji** zostanie otwarte okno dialogowe. Wybierz jedną z **Dodaj** opcji do wyboru, czy chcesz scalić plików inspekcji z dysku lokalnego lub zaimportować je z usługi Azure Storage. Należy podać szczegóły dotyczące usługi Azure Storage i klucz konta.

    3. Po dodaniu wszystkich plików w celu scalenia kliknij **OK** można ukończyć operacji scalania.

    4. Scalono plik zostanie otwarty w programie SSMS, gdzie możesz można wyświetlać i analizować je, a także go wyeksportować w pliku XEL lub CSV lub tabeli.

* Użyj [synchronizacji aplikacji](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) który utworzyliśmy. Działa na platformie Azure i korzysta z usługi log analytics publicznych interfejsów API do wypychania SQL dzienniki inspekcji w usłudze Azure Monitor dzienniki. Synchronizowanie aplikacji wypycha dzienniki inspekcji SQL do dzienników usługi Azure Monitor do użycia, za pośrednictwem pulpitu nawigacyjnego analizy dziennika.

* Usługa Power BI. Można wyświetlać i analizować dane dzienników inspekcji w usłudze Power BI. Dowiedz się więcej o [usługi Power BI i dostęp do pobrania szablonu](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Pobierz pliki dziennika z kontenera obiektów blob usługi Azure Storage za pośrednictwem portalu lub przy użyciu narzędzia, takie jak [Eksploratora usługi Azure Storage](http://storageexplorer.com/).
    * Po pobraniu pliku dziennika, który jest lokalnie, można kliknąć dwukrotnie plik, Otwórz, wyświetlać i analizować dzienniki w programie SSMS.
    * Można również pobrać wielu plików jednocześnie, za pomocą Eksploratora usługi Azure Storage. Kliknij prawym przyciskiem myszy określony podfolder, a następnie wybierz pozycję **Zapisz jako** można zapisać w folderze lokalnym.

* Dodatkowe metody:
   * Po pobraniu kilka plików lub podfolder, który zawiera pliki dziennika, może scalić je lokalnie zgodnie z opisem w instrukcje plików inspekcji scalania SSMS wcześniejszym opisem.

   * Inspekcja obiektów blob widoku programowo dzienników:

     * Użyj [czytnika zdarzeń rozszerzonych](https://blogs.msdn.microsoft.com/extended_events/20../../introducing-the-extended-events-reader/) bibliotekę języka C#.
     * [Rozszerzone zdarzenia pliki zapytań](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) przy użyciu programu PowerShell.



<br>
### <a name="database-level-policy-audit-logs"></a>Dzienniki inspekcji zasad na poziomie bazy danych
Dzienniki inspekcji na poziomie bazy danych są agregowane w kolekcji Store tabel z **SQLDBAuditLogs** prefiksu w ramach konta usługi Azure storage, wybrana w Instalatorze. Możesz wyświetlić pliki dziennika przy użyciu narzędzia, takie jak [Eksploratora usługi Azure Storage](http://azurestorageexplorer.codeplex.com).

Szablon raportu wstępnie skonfigurowany pulpit nawigacyjny jest dostępny jako [arkusz kalkulacyjny programu Excel do pobrania](https://go.microsoft.com/fwlink/?LinkId=403540) ułatwiające szybsze analizowanie danych dzienników. Aby użyć szablonu w dziennikach inspekcji, potrzebujesz programu Excel 2013 lub nowszy i dodatku Power Query, który można [pobrać tutaj](https://www.microsoft.com/download/details.aspx?id=39379).

Szablon w nim fikcyjnej przykładowe dane, i możesz skonfigurować dodatku Power Query do importowania dziennika inspekcji bezpośrednio z konta usługi Azure storage.

## <a id="subheading-4"></a>Ponowne generowanie klucza magazynu
W środowisku produkcyjnym prawdopodobnie okresowo odświeżyć klucze magazynu. Podczas odświeżania klucze, należy zapisać zasady. Proces przebiega w następujący sposób:

1. W przypadku inspekcji panelu konfiguracji, które opisano w poprzedniej instalacji inspekcji sekcji, należy zmienić **klucz dostępu do magazynu** z *podstawowego* do *dodatkowej* i  **ZAPISZ**.

   ![][4]
2. Przejdź do panelu konfiguracji magazynu i **ponownie wygenerować** *podstawowy klucz dostępu*.
3. Wróć do panelu Konfiguracja inspekcji
4. Przełącz **klucz dostępu do magazynu** z *dodatkowej* do *podstawowego* i naciśnij klawisz **ZAPISZ**.
4. Wróć do magazynu interfejsu użytkownika i **ponownie wygenerować** *pomocniczy klucz dostępu* (jako przygotowania do następnej klucze odświeżania w tle.

## <a id="subheading-5"></a>Automatyzacja (PowerShell/interfejs API REST)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można również skonfigurować inspekcji w usłudze Azure SQL Data Warehouse przy użyciu następujących narzędzi do automatyzacji:

* **Polecenia cmdlet programu PowerShell**:

   * [Get-AzSqlDatabaseAuditingPolicy](/powershell/module/az.sql/get-azsqldatabaseauditingpolicy)
   * [Get-AzSqlServerAuditingPolicy](/powershell/module/az.sql/Get-azSqlServerAuditingPolicy)
   * [Remove-AzSqlDatabaseAuditing](/powershell/module/az.sql/Remove-azSqlDatabaseAuditing)
   * [Remove-AzSqlServerAuditing](/powershell/module/az.sql/Remove-azSqlServerAuditing)
   * [Set-AzSqlDatabaseAuditingPolicy](/powershell/module/az.sql/Set-azSqlDatabaseAuditingPolicy)
   * [Set-AzSqlServerAuditingPolicy](/powershell/module/az.sql/Set-azSqlServerAuditingPolicy)
   * [Use-AzSqlServerAuditingPolicy](/powershell/module/az.sql/Use-azSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Klienci z obniżonym poziomem Obsługa inspekcji i dynamicznego maskowania danych
Inspekcja współpracuje z klientami programu SQL, które obsługują przekierowanie TDS.

Dowolny klient, który implementuje TDS w wersji 7.4 powinien obsługiwać również przekierowania. Wyjątki od tej reguły obejmują JDBC 4.0, w którym funkcji przekierowania nie jest w pełni obsługiwana i Tedious dla środowiska Node.JS, w których przekierowania nie została zaimplementowana.

Aby uzyskać "Klienci z obniżonym poziomem", które obsługują TDS w wersji 7.3 i poniżej, zmodyfikuj nazwa FQDN serwera w ciągu połączenia w następujący sposób:

- Oryginalna nazwa FQDN serwera w ciągu połączenia: <*nazwy serwera*>. database.windows.net
- Nazwa FQDN serwera zmodyfikowane w ciągu połączenia: <*nazwy serwera*> .database. **bezpieczne**. windows.net

Częściowa lista "Klienci z obniżonym poziomem" obejmuje:

* Program .NET 4.0 i poniżej
* ODBC 10.0 i poniżej.
* JDBC (JDBC obsługują TDS w wersji 7.4, funkcja przekierowywania TDS nie jest w pełni obsługiwany)
* Tedious (dla środowiska Node.JS)

**Uwaga:** Poprzedni serwer modyfikacji w pełni kwalifikowaną nazwę domeny może być przydatne, również w odniesieniu do zasady inspekcji usługi SQL Server poziom bez potrzeby kroku konfiguracji, w każdej bazie danych (tymczasowe ograniczenie).     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Define server-level vs. database-level auditing policy]: #subheading-4
[Set up server-level auditing for all databases]: #subheading-5

<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png
[6]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_1_overview.png
[7]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_2_enable.png
[8]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_3_storage.png
