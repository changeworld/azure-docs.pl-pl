---
title: Usługa Azure SQL Database managed inspekcji wystąpienia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozpocząć pracę z usługi Azure SQL Database, zarządzanego wystąpienia inspekcji przy użyciu języka T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: fc8b300cea714ee44f826a78ce8c7a10c1443414
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282121"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Rozpoczynanie pracy z inspekcją wystąpienie zarządzane bazy danych SQL Azure

[Wystąpienie zarządzane](sql-database-managed-instance.md) inspekcji śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie magazynu platformy Azure. Inspekcja również:

- Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych i uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.
- Włącza i ułatwia zgodności ze standardami zgodności, ale nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat usługi Azure programy zgodność ze standardami tej pomocy technicznej, zobacz [Centrum zaufania systemu Azure](https://azure.microsoft.com/support/trust-center/compliance/) gdzie można znaleźć najbardziej aktualną listą ze zgodnością bazy danych SQL.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Inspekcja serwera do usługi Azure storage

W poniższej sekcji opisano konfigurację inspekcji w ramach wystąpienia zarządzanego.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Tworzenie usługi Azure Storage **kontenera** przechowywania dzienników inspekcji.

   1. Przejdź do usługi Azure Storage, w którym chcesz przechowywać dzienniki inspekcji.

      > [!IMPORTANT]
      > Użyj konta magazynu w tym samym regionie co zarządzane wystąpienie, aby uniknąć operacji odczytu/zapisu między regionami.

   1. Na koncie magazynu, przejdź do **Przegląd** i kliknij przycisk **obiektów blob**.

      ![Usługa Azure Blob widżetu](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. W górnym menu, kliknij przycisk **+ kontener** do utworzenia nowego kontenera.

      ![Tworzenie ikony kontenera obiektów blob](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Podaj kontener **nazwa**, Ustaw poziom dostępu publicznego **prywatnej**, a następnie kliknij przycisk **OK**.

      ![Utwórz konfigurację kontenera obiektów blob](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. Po utworzeniu kontenera dla inspekcji dzienniki są dwa sposoby konfigurowania go jako element docelowy dla dzienników inspekcji: [przy użyciu języka T-SQL](#blobtsql) lub [przy użyciu programu SQL Server Management Studio (SSMS) interfejsu użytkownika](#blobssms):

   - <a id="blobtsql"></a>Skonfiguruj magazyn obiektów blob dla dzienników inspekcji przy użyciu języka T-SQL:

     1. Na liście kontenerów kliknij nowo utworzony kontener, a następnie kliknij **właściwości kontenera**.

        ![Przycisk Właściwości kontenera obiektów blob](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Skopiuj adres URL kontenera, klikając ikonę kopiowania i Zapisz adres URL (na przykład w Notatniku), do użytku w przyszłości. Powinna być w formacie adresu URL kontenera `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Adres URL kopiowania kontenera obiektów blob](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Generowanie usługi Azure Storage **tokenu sygnatury dostępu Współdzielonego** udzielenia wystąpienia zarządzanego inspekcji prawa dostępu do konta magazynu:

        - Przejdź do konta usługi Azure Storage, w której utworzono kontener w poprzednim kroku.

        - Kliknij pozycję **sygnatury dostępu współdzielonego** w menu Ustawienia magazynu.

          ![Ikona podpis dostępu w menu Ustawienia magazynu udostępnionego](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Skonfiguruj sygnatury dostępu Współdzielonego w następujący sposób:

          - **Dozwolone usługi**: Obiekt blob

          - **Data rozpoczęcia**: Aby uniknąć problemów związanych z strefy czasowej, zaleca się używać Data przeszła

          - **Data zakończenia**: Wybierz datę, na którym wygaśnięcia tego tokenu sygnatury dostępu Współdzielonego

            > [!NOTE]
            > Odnów token po upływie w celu uniknięcia niepowodzeń inspekcji.

          - Kliknij opcję **Generuj sygnaturę dostępu współdzielonego**.
            
            ![Konfiguracji sygnatury dostępu Współdzielonego](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Po kliknięciu przycisku na Generowanie sygnatury dostępu Współdzielonego, tokenu sygnatury dostępu Współdzielonego zostanie wyświetlony u dołu. Skopiuj token, klikając ikonę kopiowania, a następnie zapisz go (na przykład w Notatniku) do użytku w przyszłości.

          ![Skopiuj token sygnatury dostępu Współdzielonego](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Usuń znak zapytania ("?") znaku od początku tokenu.

     1. Podłącz do wystąpienia zarządzanego przy użyciu programu SQL Server Management Studio (SSMS) lub innemu narzędziu obsługiwane.

     1. Wykonaj następującą instrukcję języka T-SQL, aby **utworzyć nowe poświadczenie** przy użyciu adresu URL kontenera i tokenu sygnatury dostępu Współdzielonego utworzony w poprzednich krokach:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Wykonaj następującą instrukcję języka T-SQL, aby utworzyć nowy Server Audit (Wybierz nazwę inspekcji, użyj adresu URL kontenera, który został utworzony w poprzednich krokach). Jeśli nie zostanie określony, `RETENTION_DAYS` domyślna to 0 (nieograniczone przechowywanie):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Kontynuuj, [tworzenie Specyfikacja inspekcji serwera lub specyfikacji inspekcji bazy danych](#createspec)

   - <a id="blobssms"></a>Konfigurowanie magazynu obiektów blob dla dzienników inspekcji przy użyciu programu SQL Server Management Studio (SSMS) 18 (wersja zapoznawcza):

     1. Podłącz do wystąpienia zarządzanego przy użyciu programu SQL Server Management Studio (SSMS) interfejsu użytkownika.

     1. Rozwiń węzeł główny Zanotuj Eksplorator obiektów.

     1. Rozwiń **zabezpieczeń** węzła, kliknij prawym przyciskiem myszy **inspekcje** węzłem, a następnie kliknij przycisk "Nowy inspekcji":

        ![Rozwiń pozycję Zabezpieczenia i inspekcja węzła](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Upewnij się, że "URL" wybrano **docelowego inspekcji** i kliknij pozycję **Przeglądaj**:

        ![Przejdź do usługi Azure Storage](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Opcjonalnie) Zaloguj się do konta platformy Azure:

        ![Logowanie do platformy Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Wybierz subskrypcję, konto magazynu i kontener obiektów Blob z list rozwijanych, lub utworzyć własnego kontenera, klikając **Utwórz**. Po zakończeniu kliknij przycisk **OK**:

        ![Wybierz subskrypcję platformy Azure, konta magazynu i kontener obiektów blob](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Kliknij przycisk **OK** w oknie dialogowym "Create Audit".

1. <a id="createspec"></a>Po skonfigurowaniu kontenera obiektów Blob jako cel dla dzienników inspekcji Utwórz Specyfikacja inspekcji serwera lub specyfikacji inspekcji bazy danych, tak jak w przypadku programu SQL Server:

   - [Utwórz Podręcznik języka T-SQL Specyfikacja inspekcji serwera](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Utwórz Podręcznik języka T-SQL w specyfikacji inspekcji bazy danych](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. Włączanie inspekcji serwera, który został utworzony w kroku 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Dodatkowe informacje:

- [Inspekcja różnice między pojedynczych baz danych, pul elastycznych, s i wystąpienia zarządzanego Azure SQL Database i baz danych programu SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [TWORZENIE INSPEKCJI SERWERA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [INSTRUKCJA ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Inspekcja serwera, aby dzienniki Centrum zdarzeń lub usługi Azure Monitor

Dzienniki inspekcji z wystąpienia zarządzanego, mogą być wysyłane do dzienniki usługi Azure Monitor lub nawet koncentratorów. W tej sekcji opisano sposób konfigurowania to:

1. Nawigowanie w [witryny Azure Portal](https://portal.azure.com/) do wystąpienia zarządzanego.

2. Kliknij pozycję **ustawień diagnostycznych**.

3. Kliknij pozycję **Włącz diagnostykę**. Jeśli już włączono diagnostyki *+ Dodaj ustawienie diagnostyczne* będzie wyświetlana w zamian.

4. Wybierz **SQLSecurityAuditEvents** na liście dzienniki.

5. Wybierz lokalizację docelową dla zdarzeń inspekcji — Centrum zdarzeń i/lub dzienniki usługi Azure Monitor. Skonfiguruj dla każdego obiektu docelowego wymaganych parametrów (np. obszaru roboczego usługi Log Analytics).

6. Kliknij pozycję **Zapisz**.

    ![Konfigurowanie ustawień diagnostycznych](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Łączenie do wystąpienia zarządzanego przy użyciu **programu SQL Server Management Studio (SSMS)** lub innych obsługiwanych klientów.

8. Wykonaj następującą instrukcję języka T-SQL, aby utworzyć inspekcji serwera:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Utwórz Specyfikacja inspekcji serwera lub specyfikacji inspekcji bazy danych, tak jak w przypadku programu SQL Server:

   - [Utwórz Podręcznik języka T-SQL Specyfikacja inspekcji serwera](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Utwórz Podręcznik języka T-SQL w specyfikacji inspekcji bazy danych](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Włączanie inspekcji serwera utworzonego w kroku 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Używanie dzienników inspekcji

### <a name="consume-logs-stored-in-azure-storage"></a>Korzystanie z dzienników przechowywanych w usłudze Azure Storage

Istnieje kilka metod, których można użyć, aby wyświetlić dzienniki inspekcji obiektów blob.

- Użyj funkcji systemowej `sys.fn_get_audit_file` (T-SQL), aby zwrócić dane z dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [dokumentacji sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Możesz zapoznać się z dziennikami inspekcji przy użyciu narzędzia, takie jak [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/). W usłudze Azure storage dzienniki inspekcji są zapisywane jako kolekcja plików obiektów blob w kontenerze, który został określony do przechowywania dzienników inspekcji. Aby uzyskać więcej informacji o hierarchii folderu przechowywania konwencji nazewnictwa i format dziennika zobacz [odwołanie Format dziennika inspekcji obiektów Blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Aby uzyskać pełną listę metod użycie dziennika inspekcji, zobacz [Rozpoczynanie pracy z inspekcją bazy danych SQL](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Korzystanie z dzienników przechowywanych w Centrum zdarzeń

Korzystanie z danych dzienników inspekcji z Centrum zdarzeń, należy skonfigurować strumienia na korzystanie ze zdarzeń i zapisywać je do obiektu docelowego. Aby uzyskać więcej informacji zobacz dokumentacja usługi Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Używanie i analizowanie dzienników przechowywanych w dziennikach w usłudze Azure Monitor

Jeśli dzienniki inspekcji są zapisywane do dzienników usługi Azure Monitor, są one dostępne w obszarze roboczym usługi Log Analytics, gdzie wyszukiwania zaawansowanego można uruchomić na danych inspekcji. Jako punktu wyjścia, przejdź do obszaru roboczego usługi Log Analytics i w obszarze *ogólne* kliknij sekcję *dzienniki* i wprowadzać proste zapytanie, takie jak: `search "SQLSecurityAuditEvents"` Aby wyświetlić inspekcji logowania.  

Dzienniki platformy Azure Monitor udostępnia w czasie rzeczywistym operational insights za pomocą zintegrowanej funkcji wyszukiwania i niestandardowym pulpitom nawigacyjnym umożliwiającym szybkie analizowanie milionów rekordów z wszystkich obciążeń i serwerów. Aby uzyskać dodatkowe przydatne informacje dotyczące języka wyszukiwania dzienników usługi Azure Monitor i poleceń, zobacz [usługi Azure Monitor rejestruje dokumentacja wyszukiwania](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Inspekcja różnice między bazami danych w usłudze Azure SQL Database i baz danych programu SQL Server

Podstawowe różnice między inspekcji w bazach danych Azure SQL Database i baz danych programu SQL Server są:

- Za pomocą opcji wdrożenia wystąpienia zarządzanego usługi Azure SQL Database, inspekcja działa na poziomie serwera i magazyny `.xel` pliki dziennika w usłudze Azure Blob storage.
- Korzystając z pojedynczą bazę danych i pul elastycznych opcji wdrażania w usłudze Azure SQL Database inspekcja działa na poziomie bazy danych.
- W programie SQL Server w środowisku lokalnym / wirtualnej maszyny, inspekcji działa na serwerze poziomu, ale przechowuje zdarzenia w plikach systemu/dzienniki zdarzeń systemu windows.

Wystąpienie zarządzane inspekcji systemu XEvent obsługuje cele usługi Azure Blob storage. Dzienniki plików i systemu windows są **nieobsługiwane**.

Klucz różnice w `CREATE AUDIT` składnia inspekcji w usłudze Azure Blob storage są:

- Nowa składnia `TO URL` jest dostarczany i umożliwia określenie adresu URL kontenera magazynu obiektów blob platformy Azure gdzie `.xel` pliki zostaną umieszczone.
- Nowa składnia `TO EXTERNAL MONITOR` jest dostarczana, aby umożliwić cele dzienniki nawet Centrum i usługi Azure Monitor.
- Składnia `TO FILE` jest **nieobsługiwane** ponieważ bazy danych SQL nie może uzyskać dostępu do udziałów plików w Windows.
- Opcja zamknięcie jest **nieobsługiwane**.
- `queue_delay` 0 jest **nieobsługiwane**.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać pełną listę metod użycie dziennika inspekcji, zobacz [Rozpoczynanie pracy z inspekcją bazy danych SQL](sql-database-auditing.md).
- Aby uzyskać więcej informacji na temat usługi Azure programy zgodność ze standardami tej pomocy technicznej, zobacz [Centrum zaufania systemu Azure](https://azure.microsoft.com/support/trust-center/compliance/) gdzie można znaleźć najbardziej aktualną listą ze zgodnością bazy danych SQL.

<!--Image references-->









