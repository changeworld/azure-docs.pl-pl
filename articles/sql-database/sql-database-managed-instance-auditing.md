---
title: Usługi Azure SQL Database Managed inspekcji wystąpienia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozpocząć pracę z platformy Azure zarządzanego wystąpienia inspekcji usługi SQL Database przy użyciu języka T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: vainolo
ms.author: vainolo
ms.reviewer: vanto
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 045314980d0051e8b5ef71bdf95023084eff1880
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063881"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Rozpoczynanie pracy z usługą Azure Managed wystąpienia inspekcji usługi SQL Database

[Wystąpienie usługi Azure SQL Database Managed](sql-database-managed-instance.md) inspekcji śledzi zdarzenia bazy danych i zapisuje je do inspekcji logowania na koncie magazynu platformy Azure. Inspekcja również:

- Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych i uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.
- Włącza i ułatwia zgodności ze standardami zgodności, ale nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat usługi Azure programy zgodność ze standardami tej pomocy technicznej, zobacz [Centrum zaufania systemu Azure](https://azure.microsoft.com/support/trust-center/compliance/).

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Inspekcja serwera do usługi Azure Storage 

W poniższej sekcji opisano konfigurację inspekcji dla wystąpienia zarządzanego.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Poniższe kroki umożliwiają utworzenie usługi Azure Storage **kontenera** przechowywania dzienników inspekcji.

   - Przejdź do usługi Azure Storage, w którym chcesz przechowywać dzienniki inspekcji.

     > [!IMPORTANT]
     > Użyj konta magazynu w tym samym regionie co serwer wystąpienia zarządzanego, aby uniknąć operacji odczytu/zapisu między regionami.

   - Na koncie magazynu, przejdź do **Przegląd** i kliknij przycisk **obiektów blob**.

     ![Okienko nawigacji][1]

   - W górnym menu, kliknij przycisk **+ kontener** do utworzenia nowego kontenera.

     ![Okienko nawigacji][2]

   - Podaj kontener **nazwa**, Ustaw poziom dostępu publicznego **prywatnej**, a następnie kliknij przycisk **OK**.

     ![Okienko nawigacji][3]

   - Na liście kontenerów kliknij nowo utworzony kontener, a następnie kliknij **właściwości kontenera**.

     ![Okienko nawigacji][4]

   - Skopiuj adres URL kontenera, klikając ikonę kopiowania i Zapisz adres URL (na przykład w Notatniku), do użytku w przyszłości. Powinna być w formacie adresu URL kontenera `https://<StorageName>.blob.core.windows.net/<ContainerName>`

     ![Okienko nawigacji][5]

3. Poniższe kroki Generowanie usługi Azure Storage **tokenu sygnatury dostępu Współdzielonego** używane do udzielania inspekcji wystąpienie zarządzane prawa dostępu do konta magazynu.

   - Przejdź do konta usługi Azure Storage, w której utworzono kontener w poprzednim kroku.

   - Kliknij pozycję **sygnatury dostępu współdzielonego** w menu Ustawienia magazynu.

     ![Okienko nawigacji][6]

   - Skonfiguruj sygnatury dostępu Współdzielonego w następujący sposób:
     - **Dozwolone usługi**: Obiekt blob
     - **Data rozpoczęcia**: Aby uniknąć problemów związanych z strefy czasowej, zaleca się używać Data przeszła.
     - **Data zakończenia**: Wybierz datę, na którym wygaśnięcia tego tokenu sygnatury dostępu Współdzielonego. 

       > [!NOTE]
       > Odnów token po upływie w celu uniknięcia niepowodzeń inspekcji.

     - Kliknij opcję **Generuj sygnaturę dostępu współdzielonego**.

       ![Okienko nawigacji][7]

   - Po kliknięciu przycisku na Generowanie sygnatury dostępu Współdzielonego, tokenu sygnatury dostępu Współdzielonego zostanie wyświetlony u dołu. Skopiuj token, klikając ikonę kopiowania, a następnie zapisz go (na przykład w Notatniku) do użytku w przyszłości.

     > [!IMPORTANT]
     > Usuń znak zapytania ("?") znaku od początku tokenu.

     ![Okienko nawigacji][8]

4. Połącz z wystąpieniem zarządzanym za pomocą programu SQL Server Management Studio (SSMS).

5. Wykonaj następującą instrukcję języka T-SQL, aby **utworzyć nowe poświadczenie** przy użyciu adresu URL kontenera i tokenu sygnatury dostępu Współdzielonego utworzony w poprzednich krokach:

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. Wykonaj następującą instrukcję języka T-SQL, aby utworzyć nowy Server Audit (Wybierz nazwę inspekcji, użyj adresu URL kontenera, który został utworzony w poprzednich krokach):

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    Jeśli nie zostanie określony, `RETENTION_DAYS` domyślna to 0 (nieograniczone przechowywanie).

    Dodatkowe informacje:
    - [Inspekcja różnice między wystąpienia zarządzanego, bazy danych SQL Azure i programu SQL Server](#auditing-differences-between-managed-instance-azure-sql-database-and-sql-server)
    - [TWORZENIE INSPEKCJI SERWERA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [INSTRUKCJA ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. Utwórz Specyfikacja inspekcji serwera lub specyfikacji inspekcji bazy danych, tak jak w przypadku programu SQL Server:
    - [Utwórz Podręcznik języka T-SQL Specyfikacja inspekcji serwera](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [Utwórz Podręcznik języka T-SQL w specyfikacji inspekcji bazy danych](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. Włączanie inspekcji serwera, który został utworzony w kroku 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="set-up-auditing-for-your-server-to-event-hub-or-log-analytics"></a>Inspekcja serwera do Centrum zdarzeń lub usługi Log Analytics

Dzienniki inspekcji z wystąpienia zarządzanego, mogą być wysyłane do nawet Hubs lub usługi Log Analytics przy użyciu usługi Azure Monitor. W tej sekcji opisano sposób konfigurowania to:

1. Nawigowanie w [witryny Azure Portal](https://portal.azure.com/) wystąpienie zarządzane SQL.

2. Kliknij pozycję **ustawień diagnostycznych**.

3. Kliknij pozycję **Włącz diagnostykę**. Jeśli już włączono diagnostyki *+ Dodaj ustawienie diagnostyczne* będzie wyświetlana w zamian.

4. Wybierz **SQLSecurityAuditEvents** na liście dzienniki.

5. Wybierz lokalizację docelową dla zdarzeń inspekcji — Centrum zdarzeń i/lub usługi Log Analytics. Skonfiguruj dla każdego obiektu docelowego wymaganych parametrów (np. obszaru roboczego usługi Log Analytics).

6. Kliknij pozycję **Zapisz**.

  ![Okienko nawigacji][9]

7. Łączenie do wystąpienia zarządzanego przy użyciu **programu SQL Server Management Studio (SSMS)** lub innych obsługiwanych klientów.

8. Wykonaj następującą instrukcję języka T-SQL, aby utworzyć inspekcji serwera:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Utwórz Specyfikacja inspekcji serwera lub specyfikacji inspekcji bazy danych, tak jak w przypadku programu SQL Server:

   - [Utwórz Podręcznik języka T-SQL Specyfikacja inspekcji serwera](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Utwórz Podręcznik języka T-SQL w specyfikacji inspekcji bazy danych](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Włączanie inspekcji serwera utworzonego w kroku 7:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Używanie dzienników inspekcji

### <a name="consume-logs-stored-in-azure-storage"></a>Korzystanie z dzienników przechowywanych w usłudze Azure Storage

Istnieje kilka metod, których można użyć, aby wyświetlić dzienniki inspekcji obiektów blob.

- Użyj funkcji systemowej `sys.fn_get_audit_file` (T-SQL), aby zwrócić dane z dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [dokumentacji sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Dzienniki inspekcji można eksplorować przy użyciu narzędzia, takiego jak Eksplorator usługi Azure Storage. W usłudze Azure storage dzienniki inspekcji są zapisywane jako kolekcja plików obiektów blob w kontenerze o nazwie sqldbauditlogs. Aby uzyskać więcej informacji na temat hierarchii folderu przechowywania konwencje nazewnictwa i format dziennika Zobacz dokumentacja Format dziennika inspekcji obiektów Blob.

- Aby uzyskać pełną listę metod użycie dziennika inspekcji, zobacz [Rozpoczynanie pracy z inspekcją bazy danych SQL](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> Wyświetlanie rekordów inspekcji w witrynie Azure portal (w okienku "Rekordy inspekcji") jest obecnie niedostępna dla wystąpienia zarządzanego.

### <a name="consume-logs-stored-in-event-hub"></a>Korzystanie z dzienników przechowywanych w Centrum zdarzeń

Korzystanie z danych dzienników inspekcji z Centrum zdarzeń, należy skonfigurować strumienia na korzystanie ze zdarzeń i zapisywać je do obiektu docelowego. Aby uzyskać więcej informacji zobacz dokumentacja usługi Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-log-analytics"></a>Używanie i analizowanie dzienników przechowywanych w usłudze Log Analytics

Jeśli dzienniki inspekcji są zapisywane do usługi Log Analytics, są one dostępne w obszarze roboczym usługi Log Analytics, gdzie wyszukiwania zaawansowanego można uruchomić na danych inspekcji. Jako punktu wyjścia, przejdź do usługi Log Analytics i w obszarze *ogólne* kliknij sekcję *dzienniki* i wprowadzać proste zapytanie, takie jak: `search "SQLSecurityAuditEvents"` Aby wyświetlić inspekcji logowania.  

Usługa log Analytics udostępnia w czasie rzeczywistym operational insights za pomocą zintegrowanej funkcji wyszukiwania i niestandardowym pulpitom nawigacyjnym umożliwiającym szybkie analizowanie milionów rekordów z wszystkich obciążeń i serwerów. Aby uzyskać dodatkowe przydatne informacje dotyczące języka wyszukiwania usługi Log Analytics i poleceń, zobacz [usługi Log Analytics Wyszukaj odwołanie](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Inspekcja różnice między wystąpienia zarządzanego usługi Azure SQL Database i programu SQL Server

Podstawowe różnice między inspekcji SQL w wystąpieniu zarządzanym, Azure SQL Database i SQL Server w środowisku lokalnym są:

- W wystąpieniu zarządzanym inspekcji SQL działa na poziomie serwera i magazyny `.xel` dzienniki na koncie magazynu obiektów blob platformy Azure.
- W usłudze Azure SQL Database inspekcji SQL, działa na poziomie bazy danych.
- W programie SQL Server w środowisku lokalnym / wirtualnych maszyn, działania inspekcji SQL na serwerze poziomu, ale zdarzenia magazyny plików system i windows dzienniki zdarzeń.

Inspekcji systemu XEvent, w wystąpieniu zarządzanym obsługuje obiekty docelowe magazynu obiektów blob platformy Azure. Dzienniki plików i systemu windows są **nieobsługiwane**.

Klucz różnice w `CREATE AUDIT` składnia dla inspekcji w usłudze Azure blob storage są:

- Nowa składnia `TO URL` jest dostarczany i umożliwia określenie adresu URL kontenera magazynu obiektów blob platformy Azure gdzie `.xel` pliki zostaną umieszczone.
- Nowa składnia `TO EXTERNAL MONITOR` jest dostarczana, aby umożliwić cele nawet Centrum i usługi Log Analytics.
- Składnia `TO FILE` jest **nieobsługiwane** ponieważ wystąpienia zarządzanego nie można uzyskać dostępu do udziałów plików w Windows.
- Opcja zamknięcie jest **nieobsługiwane**.
- `queue_delay` 0 jest **nieobsługiwane**.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać pełną listę metod użycie dziennika inspekcji, zobacz [Rozpoczynanie pracy z inspekcją bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).
- Aby uzyskać więcej informacji na temat usługi Azure programy zgodność ze standardami tej pomocy technicznej, zobacz [Centrum zaufania systemu Azure](https://azure.microsoft.com/support/trust-center/compliance/).

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
[9]: ./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png
