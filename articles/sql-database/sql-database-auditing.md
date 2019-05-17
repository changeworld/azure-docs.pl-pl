---
title: Rozpoczynanie pracy z inspekcją bazy danych Azure SQL | Dokumentacja firmy Microsoft
description: Użyj opcji inspekcji usługi Azure SQL database, aby śledzić zdarzenia bazy danych w dzienniku inspekcji.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 3efdf5c256a22529c9d19e9ae1dce5d2db9516a5
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827769"
---
# <a name="get-started-with-sql-database-auditing"></a>Rozpoczynanie pracy z inspekcją bazy danych SQL

Przeprowadzanie inspekcji na platformie Azure [bazy danych SQL](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na swoje konto usługi Azure storage, obszar roboczy pakietu OMS lub centrów zdarzeń. Inspekcja również:

- Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych i uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

- Włącza i ułatwia zgodności ze standardami zgodności, ale nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat usługi Azure programy zgodność ze standardami tej pomocy technicznej, zobacz [Centrum zaufania systemu Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) gdzie można znaleźć najbardziej aktualną listą ze zgodnością bazy danych SQL.


> [!NOTE] 
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Omówienie inspekcji usługi Azure SQL database

Można użyć inspekcji usługi SQL database do:

- **Zachowaj** dziennik inspekcji wybranych zdarzeń. Można zdefiniować kategorie działań bazy danych powinien być poddany inspekcji.
- **Raport** aktywność bazy danych. Wstępnie skonfigurowane raporty i pulpit nawigacyjny umożliwia szybkie rozpoczynanie pracy z działaniem i raportowanie zdarzeń.
- **Analizowanie** raportów. Możesz znaleźć podejrzanych zdarzeń, nietypowej aktywności i trendów.

> [!IMPORTANT]
> Dzienniki inspekcji są zapisywane w **obiekty BLOB dołączania** w usłudze Azure Blob storage w ramach subskrypcji platformy Azure.
>
> - Wszelkiego rodzaju magazynu (v1, v2, obiektów blob) są obsługiwane.
> - Obsługiwane są wszystkie konfiguracje replikacji magazynu.
> - **Usługa Premium storage** jest obecnie **nieobsługiwane**.
> - **Magazyn w sieci wirtualnej** jest obecnie **nieobsługiwane**.
> - **Magazyn za zaporą** jest obecnie **nieobsługiwane**

## <a id="subheading-8"></a>Zdefiniuj poziom serwera, a zasady inspekcji na poziomie bazy danych

Można zdefiniować zasady inspekcji dla konkretnej bazy danych lub jako domyślne zasady serwera:

- Zasady serwera ma zastosowanie do wszystkich istniejących i nowo utworzony baz danych na serwerze.

- Jeśli *Inspekcja obiektów blob serwera jest włączona*, jego *zawsze ma zastosowanie do bazy danych*. Baza danych będzie monitorowane, niezależnie od ustawienia inspekcji bazy danych.

- Włączanie inspekcji dla bazy danych lub dane magazynu obiektów blob, oprócz Włączanie go na serwerze jest *nie* zastąpić lub zmienić ustawienia inspekcji obiektów blob serwera. Zarówno inspekcji będą istnieć obok siebie. Innymi słowy bazy danych jest różna dwa razy w sposób równoległy; raz przez zasady serwera banku i raz przez zasady bazy danych.

   > [!NOTE]
   > Należy unikać włączenie inspekcji obiektów blob serwera i inspekcji obiektów blob dla bazy danych ze sobą, chyba że:
    > - Aby użyć innego *konta magazynu* lub *okres przechowywania* dla konkretnej bazy danych.
    > - Chcesz inspekcji zdarzeń typów lub kategorii dla konkretnej bazy danych, które różnią się od pozostałej części bazy danych na serwerze. Na przykład może być wstawia tabeli, które należy przeprowadzić inspekcję tylko dla konkretnej bazy danych.
   >
   > W przeciwnym razie zalecane jest włączanie inspekcji obiektów blob tylko poziom serwera i pozostawić inspekcji poziomu bazy danych wyłączone dla wszystkich baz danych.

## <a id="subheading-2"></a>Inspekcja bazy danych

W poniższej sekcji opisano konfigurację inspekcji przy użyciu witryny Azure portal.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do **inspekcji** w pozycji zabezpieczeń, w okienku serwera/bazy danych SQL.

    <a id="auditing-screenshot"></a> ![Okienko nawigacji][1]

3. Jeśli wolisz skonfigurować zasady inspekcji serwera, możesz wybrać **Wyświetl ustawienia serwera** łącze na stronie inspekcji bazy danych. Można następnie wyświetlić lub zmodyfikować ustawień inspekcji serwera. Zasady inspekcji serwera mają zastosowanie do wszystkich istniejących i nowo utworzonej bazy danych na tym serwerze.

    ![Okienko nawigacji][2]

4. Jeśli wolisz włączyć inspekcję na poziomie bazy danych, Przełącz **inspekcji** do **ON**.

    Jeśli inspekcja serwera jest włączona, skonfigurowana bazy danych inspekcji będzie istnieć side-by-side przy użyciu inspekcji serwera.

    ![Okienko nawigacji][3]

5. **Nowe** — masz teraz wiele opcji dotyczących konfigurowania której będą zapisywane dzienniki inspekcji. Konto usługi Azure storage, obszar roboczy usługi Log Analytics do użycia przez dzienniki usługi Azure Monitor lub Centrum zdarzeń do użycia z Centrum zdarzeń, można napisać dzienników. Można skonfigurować dowolną kombinację tych opcji, a do każdego będą zapisywane w dziennikach inspekcji.

   > [!WARNING]
   > Włączenie inspekcji w usłudze Log Analytics spowoduje naliczenie opłat zgodnie z szybkości pozyskiwania. Należy mieć świadomość kosztów skojarzonych z za pomocą tego [opcji](https://azure.microsoft.com/pricing/details/monitor/), lub rozważ przechowywanie inspekcji logowania na koncie usługi Azure storage.

    ![Opcje magazynu](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Aby skonfigurować zapisywanie inspekcji dzienniki na koncie magazynu, wybierz opcję **magazynu** , a następnie otwórz **szczegóły magazynu**. Wybierz konto magazynu platformy Azure, której będą zapisywane dzienniki, a następnie wybierz okres przechowywania. Stare dzienniki zostaną usunięte. Następnie kliknij przycisk **OK**.

    ![konto magazynu](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Aby skonfigurować zapisywanie inspekcji dzienniki do obszaru roboczego usługi Log Analytics, wybierz opcję **usługi Log Analytics (wersja zapoznawcza)** , a następnie otwórz **szczegóły usługi Log Analytics**. Wybierz lub Utwórz obszar roboczy usługi Log Analytics, której będą zapisywane dzienniki, a następnie kliknij przycisk **OK**.

    ![Obszar roboczy usługi Log Analytics](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Aby skonfigurować zapisywanie inspekcji dzienników do Centrum zdarzeń, wybierz opcję **Centrum zdarzeń (wersja zapoznawcza)** , a następnie otwórz **szczegóły Centrum zdarzeń**. Wybierz Centrum zdarzeń, w którym będą zapisywane dzienniki, a następnie kliknij przycisk **OK**. Pamiętaj, że Centrum zdarzeń znajduje się w tym samym regionie, co bazy danych i serwera.

    ![Centrum zdarzeń](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Kliknij pozycję **Zapisz**.
10. Jeśli chcesz dostosować zdarzenia poddawane inspekcji, to zrobić za pomocą [poleceń cmdlet programu PowerShell](#subheading-7) lub [interfejsu API REST](#subheading-9).
11. Po skonfigurowaniu ustawień inspekcji można włączyć funkcji wykrywania zagrożeń i skonfigurować wiadomości e-mail, aby otrzymywać alerty zabezpieczeń. Korzystając z wykrywania zagrożeń, otrzymujesz alerty proaktywne na nietypowe działania bazy danych, które mogą wskazywać potencjalne zagrożenia bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [wprowadzenie do wykrywania zagrożeń](sql-database-threat-detection-get-started.md).

> [!IMPORTANT]
> Włączenie inspekcji na wstrzymanie usługi Azure SQL Data Warehouse nie jest możliwe. Aby ją włączyć, anulować wstrzymanie magazynu danych.

> [!WARNING]
> Włączenie inspekcji na serwerze usługi Azure SQL Data Warehouse na nim **spowoduje on hurtowni danych wznowić i ponownie wstrzymane ponownie** który może być naliczane opłaty.

## <a id="subheading-3"></a>Analizowanie dzienników inspekcji i raporty

Jeśli została wybrana opcja zapisywania dzienników inspekcji usługi Azure Monitor dzienników:

- Użyj [witryny Azure portal](https://portal.azure.com).  Otwórz odpowiedniej bazy danych. W górnej części bazy danych **inspekcji** kliknij **Wyświetl dzienniki inspekcji**.

    ![Wyświetl dzienniki inspekcji](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

- Następnie, klikając **Otwórz w usłudze OMS** w górnej części **rekordy inspekcji** zostanie otwarta strona Widok dzienników w usłudze Log Analytics, w którym można dostosować zakres czasu i zapytania wyszukiwania.

    ![Otwórz w usłudze Log Analytics](./media/sql-database-auditing-get-started/auditing_open_in_oms.png)

- Alternatywnie można także przejść dzienniki inspekcji w bloku usługi Log Analytics. Otwórz obszar roboczy usługi Log Analytics i w obszarze **ogólne** kliknij **dzienniki**. Można zacząć od prostego zapytania, takie jak: *wyszukiwanie "SQLSecurityAuditEvents"* Aby wyświetlić inspekcji logowania.
    W tym miejscu możesz również użyć [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-log-search.md) przeprowadzić zaawansowane wyszukiwanie na dane dziennika inspekcji. Dzienniki platformy Azure Monitor udostępnia w czasie rzeczywistym operational insights za pomocą zintegrowanej funkcji wyszukiwania i niestandardowym pulpitom nawigacyjnym umożliwiającym szybkie analizowanie milionów rekordów z wszystkich obciążeń i serwerów. Aby uzyskać dodatkowe przydatne informacje dotyczące języka wyszukiwania dzienników usługi Azure Monitor i poleceń, zobacz [usługi Azure Monitor rejestruje dokumentacja wyszukiwania](../log-analytics/log-analytics-log-search.md).

W przypadku wybrania tylko zapisywanie dzienników inspekcji w Centrum zdarzeń:

- Korzystanie z danych dzienników inspekcji z Centrum zdarzeń, należy skonfigurować strumienia na korzystanie ze zdarzeń i zapisywać je do obiektu docelowego. Aby uzyskać więcej informacji, zobacz [dokumentacja usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
- Dzienniki inspekcji w Centrum zdarzeń są przechwytywane w treści [Apache Avro](https://avro.apache.org/) zdarzeń i zmagazynowane przy użyciu formatu JSON formatowanie przy użyciu kodowania UTF-8. Aby odczytać dzienniki inspekcji, możesz użyć [narzędzia Avro](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) lub podobne narzędzia, które przetwarzają tego formatu.

Jeśli wybrano zapisywanie dzienników inspekcji na koncie usługi Azure storage, istnieje kilka metod, których można użyć, aby wyświetlić dzienniki:

- Dzienniki inspekcji są agregowane w ramach konta, który został wybrany podczas instalacji. Możesz zapoznać się z dziennikami inspekcji przy użyciu narzędzia, takie jak [Eksploratora usługi Azure Storage](https://storageexplorer.com/). W usłudze Azure storage, dzienniki inspekcji są zapisywane jako kolekcja plików obiektów blob w kontenerze o nazwie **sqldbauditlogs**. Aby uzyskać więcej informacji o hierarchii folderu przechowywania konwencji nazewnictwa i format dziennika zobacz [odwołanie Format dziennika inspekcji obiektów Blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Użyj [witryny Azure portal](https://portal.azure.com).  Otwórz odpowiedniej bazy danych. W górnej części bazy danych **inspekcji** kliknij **Wyświetl dzienniki inspekcji**.

    ![Okienko nawigacji][7]

    **Rekordy inspekcji** zostanie otwarta, z których będziesz mieć możliwość wyświetlenia dzienników.

  - Określone daty można wyświetlić, klikając **filtru** w górnej części **rekordy inspekcji** strony.
  - Możesz przełączać się między rekordy inspekcji, które zostały utworzone przez *zasady inspekcji serwera* i *bazy danych zasady inspekcji* przełączając **źródło inspekcji**.
  - Można wyświetlić tylko wstrzyknięcie kodu SQL powiązane rekordy inspekcji, sprawdzając **Pokaż tylko inspekcji rekordów dla wstrzyknięć kodu SQL** pola wyboru.

       ![Okienko nawigacji][8]

- Użyj funkcji systemowej **sys.fn_get_audit_file** (T-SQL), aby zwrócić dane z dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Użyj **scalania plików inspekcji** w SQL Server Management Studio (począwszy od SSMS 17):
    1. Wybierz z menu Narzędzia SSMS **pliku** > **Otwórz** > **scalania plików inspekcji**.

        ![Okienko nawigacji][9]
    2. **Dodaj pliki inspekcji** zostanie otwarte okno dialogowe. Wybierz jedną z **Dodaj** opcji do wyboru, czy chcesz scalić plików inspekcji z dysku lokalnego lub zaimportować je z usługi Azure Storage. Należy podać szczegóły dotyczące usługi Azure Storage i klucz konta.

    3. Po dodaniu wszystkich plików w celu scalenia kliknij **OK** można ukończyć operacji scalania.

    4. Scalono plik zostanie otwarty w programie SSMS, gdzie możesz można wyświetlać i analizować je, a także go wyeksportować w pliku XEL lub CSV lub tabeli.

- Usługa Power BI. Można wyświetlać i analizować dane dzienników inspekcji w usłudze Power BI. Aby uzyskać więcej informacji i uzyskać dostęp do pobrania szablonu, zobacz [Analizuj dane z dziennika inspekcji w usłudze Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Pobierz pliki dziennika z kontenera obiektów blob usługi Azure Storage za pośrednictwem portalu lub przy użyciu narzędzia, takie jak [Eksploratora usługi Azure Storage](https://storageexplorer.com/).
  - Po pobraniu pliku dziennika, który jest lokalnie, kliknij dwukrotnie plik, Otwórz, wyświetlać i analizować dzienniki w programie SSMS.
  - Można również pobrać wielu plików jednocześnie, za pomocą Eksploratora usługi Azure Storage. Aby to zrobić, kliknij prawym przyciskiem myszy określony podfolder, a następnie wybierz **Zapisz jako** można zapisać w folderze lokalnym.

- Dodatkowe metody:

  - Po pobraniu kilka plików lub podfolder, który zawiera pliki dziennika, może scalić je lokalnie zgodnie z opisem w instrukcje plików inspekcji scalania SSMS opisanych powyżej.
  - Inspekcja obiektów blob widoku programowo dzienników:

    - [Rozszerzone zdarzenia pliki zapytań](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) przy użyciu programu PowerShell.

## <a id="subheading-5"></a>Rozwiązania w środowisku produkcyjnym

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Inspekcja bazy danych replikowanej geograficznie</a>

Bazy danych replikowanej geograficznie po włączeniu inspekcji podstawowej bazy danych pomocniczej bazy danych mają identyczne zasady inspekcji. Istnieje również możliwość skonfigurowania inspekcji w pomocniczej bazie danych przez włączenie inspekcji na **serwer pomocniczy**, niezależnie od podstawowej bazy danych.

- Poziom serwera (**zalecane**): Włącz inspekcję na obu **serwera podstawowego** , jak również **serwer pomocniczy** -podstawowych i pomocniczych baz danych będą każdego inspekcji niezależnie zależnie od ich odpowiednich zasad na poziomie serwera.
- Poziom bazy danych: Poziom bazy danych inspekcji dla pomocniczych baz danych można skonfigurować tylko z podstawowej bazy danych, ustawienia inspekcji.
  - Inspekcja musi być włączona na *podstawowa baza danych sam*, nie na serwerze.
  - Po włączeniu inspekcji podstawowej bazy danych, będzie również stać się włączone w pomocniczej bazie danych.

    >[!IMPORTANT]
    >Za pomocą inspekcji poziomu bazy danych, ustawienia magazynu dla pomocniczej bazy danych będzie identyczne z podstawowej bazy danych, co spowoduje niepowodzenie ruch między regionami. Zaleca się włączania inspekcji tylko poziomu serwera i pozostaw inspekcji poziomu bazy danych wyłączone dla wszystkich baz danych.
    > [!WARNING]
    > Za pomocą Centrum zdarzeń i dzienników usługi Azure Monitor jako elementy docelowe dla dzienników inspekcji na poziomie serwera nie jest obecnie obsługiwane dla pomocniczych baz danych, które są replikowane geograficznie.

### <a id="subheading-6">Ponowne generowanie klucza magazynu</a>

W środowisku produkcyjnym prawdopodobnie okresowo odświeżyć klucze magazynu. Podczas zapisywania dzienników inspekcji usługi Azure storage, musisz ponownie zapisać zasady inspekcji podczas odświeżania klucze. Proces przebiega w następujący sposób:

1. Otwórz **szczegóły magazynu**. W **klucz dostępu do magazynu** wybierz opcję **dodatkowej**i kliknij przycisk **OK**. Następnie kliknij przycisk **Zapisz** w górnej części strony Konfiguracja inspekcji.

    ![Okienko nawigacji][5]
2. Przejdź do strony konfiguracji magazynu, a następnie ponownie wygenerować podstawowy klucz dostępu.

    ![Okienko nawigacji][6]
3. Przejdź z powrotem na stronie Konfiguracja inspekcji przełączyć klucz dostępu do magazynu z dodatkowej do głównej, a następnie kliknij przycisk **OK**. Następnie kliknij przycisk **Zapisz** w górnej części strony Konfiguracja inspekcji.
4. Wróć do strony konfiguracji magazynu i ponownie wygenerować pomocniczy klucz dostępu (w celu przygotowania następny klawisz cyklu odświeżania).

## <a name="additional-information"></a>Dodatkowe informacje

- Aby uzyskać szczegółowe informacje o dzienniku formatowania hierarchii folderu przechowywania i konwencje nazewnictwa, zobacz [odwołanie Format dziennika inspekcji obiektów Blob](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Inspekcji platformy Azure SQL Database przechowuje 4000 znaków dane dla znaków pola rekordu inspekcji. Gdy **instrukcji** lub **data_sensitivity_information** wartości zwrócone z akcji podlegających inspekcji zawiera więcej niż 4000 znaków, będzie można odświeżyć żadnych danych poza najpierw 4000 znaków  **obcięty i nie inspekcji**.

- Dzienniki inspekcji są zapisywane w **obiekty BLOB dołączania** w usłudze Azure Blob storage w ramach subskrypcji platformy Azure:
  - **Usługa Premium Storage** jest obecnie **nieobsługiwane** przez obiekty BLOB dołączania.
  - **Magazyn w sieci wirtualnej** jest obecnie **nieobsługiwane**.

- Domyślne zasady inspekcji zawiera wszystkie akcje i następujący zestaw grup akcji, które zostanie przeprowadzona inspekcja wszystkie zapytania i procedur składowanych wykonywanych względem bazy danych, a także udane i nieudane logowania:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Można skonfigurować inspekcję różnego rodzaju akcje i grup akcji przy użyciu programu PowerShell, zgodnie z opisem w [Zarządzanie inspekcji usługi SQL database przy użyciu programu Azure PowerShell](#subheading-7) sekcji.

- Podczas uwierzytelniania usługi AAD nie powiodło się będą rekordy logowania *nie* pojawiają się w dzienniku inspekcji SQL. Aby wyświetlić rekordy inspekcji logowania nie powiodło się, należy odwiedzić [portalu Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), który rejestruje szczegółowe informacje o tych zdarzeń.


## <a id="subheading-7"></a>Zarządzanie inspekcji usługi SQL database przy użyciu programu Azure PowerShell

**Polecenia cmdlet programu PowerShell (w tym obsługa klauzuli WHERE filtrowania dodatkowych)**:

- [Tworzenie lub aktualizowanie bazy danych inspekcji zasad (zestaw AzSqlDatabaseAuditing)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseauditing)
- [Utwórz lub zaktualizuj zasady inspekcji serwera (zestaw AzSqlServerAuditing)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserverauditing)
- [Pobierz zasady inspekcji bazy danych (Get-AzSqlDatabaseAuditing)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseauditing)
- [Pobieranie serwera zasad dotyczących inspekcji (Get-AzSqlServerAuditing)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverauditing)

Aby uzyskać przykładowy skrypt, zobacz [skonfigurować inspekcję i wykrywanie zagrożeń za pomocą programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Zarządzanie inspekcji usługi SQL database przy użyciu interfejsu API REST

**REST API**:

- [Utwórz lub zaktualizuj zasady inspekcji bazy danych](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Utwórz lub zaktualizuj zasady inspekcji serwera](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Pobierz zasady inspekcji bazy danych](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Pobieranie serwera zasad inspekcji](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

Rozszerzone zasady, z którym klauzuli pomocy technicznej w celu filtrowania dodatkowych:

- [Utwórz lub zaktualizuj bazę danych *rozszerzone* zasady inspekcji](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Utwórz lub zaktualizuj serwer *rozszerzone* zasady inspekcji](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Pobierz bazę danych *rozszerzone* zasady inspekcji](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Pobierz serwer *rozszerzone* zasady inspekcji](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-10"></a>Zarządzanie inspekcji usługi SQL database przy użyciu szablonów ARM

Można zarządzać za pomocą inspekcja bazy danych Azure SQL [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) szablonów, jak pokazano w tych przykładach:

- [Wdrażanie serwera SQL Azure za pomocą inspekcji włączone zapisywanie dzienników inspekcji do konta magazynu obiektów Blob platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Wdrażanie serwera SQL Azure za pomocą inspekcji włączone zapisywanie dzienników inspekcji usługi Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Wdrażanie serwera SQL Azure za pomocą inspekcji włączone zapisywanie dzienników inspekcji usługi Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Połączone przykłady znajdują się na publiczne repozytorium zewnętrznego, a podano "jak jest" bez gwarancji i nie są obsługiwane w jakiejkolwiek usługi firmy Microsoft pomocy technicznej programu /.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9
[Manage SQL database auditing using ARM templates]: #subheading-10

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