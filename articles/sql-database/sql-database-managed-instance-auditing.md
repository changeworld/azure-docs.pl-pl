---
title: Inspekcja wystąpienia zarządzanego
description: Dowiedz się, jak rozpocząć inspekcję wystąpienia zarządzanego usługi Azure SQL Database przy użyciu języka T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.openlocfilehash: 405ac27fad3c24d3064f11476f452ad00abb9b02
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387771"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Rozpoczynanie pracy z inspekcją wystąpienia zarządzanego usługi Azure SQL Database

[Inspekcja wystąpienia zarządzanego](sql-database-managed-instance.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie magazynu platformy Azure. Ponadto inspekcja:

- Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.
- Umożliwia i ułatwia przestrzeganie standardów zgodności, chociaż nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat programów platformy Azure, które obsługują zgodność ze standardami, zobacz [Centrum zaufania platformy Azure,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) gdzie można znaleźć najnowszą listę certyfikatów zgodności bazy danych SQL.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Konfigurowanie inspekcji serwera w magazynie platformy Azure

W poniższej sekcji opisano konfigurację inspekcji w wystąpieniu zarządzanym.

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Utwórz **kontener** usługi Azure Storage, w którym przechowywane są dzienniki inspekcji.

   1. Przejdź do usługi Azure Storage, gdzie chcesz przechowywać dzienniki inspekcji.

      > [!IMPORTANT]
      > Użyj konta magazynu w tym samym regionie co wystąpienie zarządzane, aby uniknąć odczytów/zapisów między regionami.

   1. Na koncie magazynu przejdź do **przeglądu** i kliknij pozycję **Obiekty Blobs**.

      ![Widżet obiektów blob platformy Azure](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. W górnym menu kliknij przycisk **+ Kontener,** aby utworzyć nowy kontener.

      ![Tworzenie ikony kontenera obiektów blob](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Podaj **nazwę**kontenera , ustaw poziom dostępu publicznego na **Prywatny,** a następnie kliknij przycisk **OK**.

      ![Tworzenie konfiguracji kontenera obiektów blob](./media/sql-managed-instance-auditing/3_create_container_config.png)
  > [!IMPORTANT]
  > Klient, który chce skonfigurować niezmienny magazyn dzienników dla zdarzeń inspekcji na poziomie serwera lub bazy danych, powinien postępować zgodnie z [instrukcjami dostarczonymi przez usługę Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (Upewnij się, że podczas konfigurowania niezmiennego magazynu obiektów blob wybrano opcję **Zezwalaj na dodatkowe dodatki)**
  
3. Po utworzeniu kontenera dla dzienników inspekcji istnieją dwa sposoby skonfigurowania go jako miejsca docelowego dla dzienników inspekcji: [przy użyciu T-SQL](#blobtsql) lub [przy użyciu interfejsu użytkownika PROGRAMU SQL Server Management Studio (SSMS):](#blobssms)

   - <a id="blobtsql"></a>Konfigurowanie magazynu blogów dla dzienników inspekcji przy użyciu języka T-SQL:

     1. Na liście kontenerów kliknij nowo utworzony kontener, a następnie kliknij pozycję **Właściwości kontenera**.

        ![Przycisk właściwości kontenera obiektów blob](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Skopiuj adres URL kontenera, klikając ikonę kopiowania i zapisując adres URL (na przykład w Notatniku) do wykorzystania w przyszłości. Format adresu URL kontenera powinien być`https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Adres URL kopiowania kontenera obiektu Blob](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Generowanie **tokenu SYGNATUR DOSTĘPU WSPÓŁDZIELONEGO** usługi Azure Storage w celu przyznania zarządzanych wystąpień inspekcji praw dostępu do konta magazynu:

        - Przejdź do konta usługi Azure Storage, na którym utworzono kontener w poprzednim kroku.

        - Kliknij **podpis dostępu współdzielonego** w menu Ustawienia magazynu.

          ![Ikona podpisu dostępu współdzielonego w menu ustawień pamięci](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Skonfiguruj sygnaturę dostępu Współdzielonego w następujący sposób:

          - **Dozwolone usługi**: Blob

          - **Data rozpoczęcia:** aby uniknąć problemów związanych ze strefą czasową, zaleca się stosowanie wczorajszej daty

          - **Data zakończenia:** wybierz datę wygaśnięcia tego Tokenu Sygnatury Dostępu Współdzielonego

            > [!NOTE]
            > Odnów token po wygaśnięciu, aby uniknąć błędów inspekcji.

          - Kliknij opcję **Generuj sygnaturę dostępu współdzielonego**.
            
            ![Konfiguracja sas](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Po kliknięciu przycisku Generuj sygnatury dostępu Współdzielonego token sygnatury dostępu Współdzielonego pojawia się u dołu. Skopiuj token, klikając ikonę kopiowania i zapisz go (na przykład w Notatniku) do wykorzystania w przyszłości.

          ![Kopiowanie tokenu Sygnatury Dostępu Współ](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Usuń znak zapytania ("?") znak z początku tokenu.

     1. Połącz się z wystąpieniem zarządzanym za pośrednictwem programu SQL Server Management Studio (SSMS) lub innego obsługiwanego narzędzia.

     1. Wykonaj następującą **instrukcję** T-SQL, aby utworzyć nowe poświadczenia przy użyciu adresu URL kontenera i tokenu sygnatury dostępu Współdzielonego utworzonego w poprzednich krokach:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Wykonaj następującą instrukcję T-SQL, aby utworzyć nową inspekcję serwera (wybierz własną nazwę inspekcji, użyj adresu URL kontenera utworzonego w poprzednich krokach). Jeśli nie `RETENTION_DAYS` zostanie określony, wartość domyślna to 0 (nieograniczona retencja):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Kontynuuj, [tworząc specyfikację inspekcji serwera lub specyfikację inspekcji bazy danych](#createspec)

   - <a id="blobssms"></a>Konfigurowanie magazynu obiektów blob dla dzienników inspekcji przy użyciu programu SQL Server Management Studio (SSMS) 18 (Wersja zapoznawcza):

     1. Połącz się z wystąpieniem zarządzanym przy użyciu interfejsu użytkownika programu SQL Server Management Studio (SSMS).

     1. Rozwiń notatkę główną Eksploratora obiektów.

     1. Rozwiń węzeł **Zabezpieczenia,** kliknij prawym przyciskiem myszy węzeł **Inspekcje** i kliknij polecenie "Nowy audyt":

        ![Rozwiń węzeł zabezpieczeń i inspekcji](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Upewnij się, że w **audycie docelowym wybrano** opcję "URL", a następnie kliknij **pozycję Przeglądaj:**

        ![Przeglądanie usługi Azure Storage](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Opcjonalnie) Zaloguj się do konta platformy Azure:

        ![Logowanie do platformy Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Wybierz subskrypcję, konto magazynu i kontener obiektów Blob z listy rozwijanej lub utwórz własny kontener, klikając pozycję **Utwórz**. Po zakończeniu kliknij przycisk **OK:**

        ![Wybieranie subskrypcji platformy Azure, konta magazynu i kontenera obiektów blob](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Kliknij **przycisk OK** w oknie dialogowym "Utwórz inspekcję".

4. <a id="createspec"></a>Po skonfigurowaniu kontenera obiektów blob jako obiektu docelowego dla dzienników inspekcji należy utworzyć i włączyć specyfikację inspekcji serwera lub specyfikację inspekcji bazy danych, tak jak w przypadku programu SQL Server:

   - [Przewodnik T-SQL specyfikacji inspekcji serwera](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Przewodnik T-SQL specyfikacji inspekcji bazy danych](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Włącz inspekcję serwera utworzoną w kroku 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Dodatkowe informacje:

- [Inspekcja różnic między pojedynczymi bazami danych, pulami elastycznymi i wystąpieniami zarządzanymi w bazie danych SQL Azure i bazach danych w programie SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [TWORZENIE INSPEKCJI SERWERA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [KONTROLA SERWERA ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Konfigurowanie inspekcji serwera w usłudze Event Hub lub dziennikach usługi Azure Monitor

Dzienniki inspekcji z wystąpienia zarządzanego mogą być wysyłane do dzienników parzyste lub usługi Azure Monitor. W tej sekcji opisano sposób konfigurowania tego:

1. Przejdź w [witrynie Azure Portal](https://portal.azure.com/) do wystąpienia zarządzanego.

2. Kliknij **ustawienia diagnostyczne**.

3. Kliknij **włącz diagnostykę**. Jeśli diagnostyka jest już włączona, zamiast tego zostanie wyświetlone *ustawienie diagnostyki +Dodaj.*

4. Wybierz **SQLSecurityAuditEvents** na liście dzienników.

5. Wybierz miejsce docelowe dla zdarzeń inspekcji — Centrum zdarzeń, dzienniki usługi Azure Monitor lub oba te elementy. Skonfiguruj dla każdego obiektu docelowego wymagane parametry (np. obszar roboczy usługi Log Analytics).

6. Kliknij przycisk **Zapisz**.

    ![Konfigurowanie ustawień diagnostycznych](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Połącz się z wystąpieniem zarządzanym przy użyciu **programu SQL Server Management Studio (SSMS)** lub innego obsługiwanego klienta.

8. Wykonaj następującą instrukcję T-SQL, aby utworzyć inspekcję serwera:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Tworzenie i włączanie specyfikacji inspekcji serwera lub specyfikacji inspekcji bazy danych, tak jak w przypadku programu SQL Server:

   - [Przewodnik T-SQL specyfikacji inspekcji serwera](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Przewodnik T-SQL specyfikacji inspekcji bazy danych](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Włącz inspekcję serwera utworzoną w kroku 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Korzystanie z dzienników inspekcji

### <a name="consume-logs-stored-in-azure-storage"></a>Korzystanie z dzienników przechowywanych w usłudze Azure Storage

Istnieje kilka metod, których można użyć do wyświetlania dzienników inspekcji obiektów blob.

- Użyj funkcji `sys.fn_get_audit_file` systemowej (T-SQL), aby zwrócić dane dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [sys.fn_get_audit_file dokumentacji](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Dzienniki inspekcji można eksplorować za pomocą narzędzia, takiego jak [Eksplorator usługi Azure Storage.](https://azure.microsoft.com/features/storage-explorer/) W usłudze Azure Storage dzienniki inspekcji są zapisywane jako zbiór plików obiektów blob w kontenerze, który został zdefiniowany do przechowywania dzienników inspekcji. Aby uzyskać więcej informacji na temat hierarchii folderu magazynu, konwencji nazewnictwa i formatu dziennika, zobacz [odwołanie do formatu dziennika inspekcji obiektów blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Aby uzyskać pełną listę metod zużycia dziennika inspekcji, zobacz [Wprowadzenie do inspekcji bazy danych SQL](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Korzystanie z dzienników przechowywanych w Centrum zdarzeń

Aby korzystać z danych dzienników inspekcji z Usługi Event Hub, należy skonfigurować strumień, aby korzystać ze zdarzeń i zapisać je do obiektu docelowego. Aby uzyskać więcej informacji, zobacz Dokumentacja usługi Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Korzystanie z dzienników i analizowania przechowywanych w dziennikach usługi Azure Monitor

Jeśli dzienniki inspekcji są zapisywane w dziennikach usługi Azure Monitor, są one dostępne w obszarze roboczym usługi Log Analytics, gdzie można uruchomić zaawansowane wyszukiwania na dane inspekcji. Punktem wyjścia przejdź do obszaru roboczego usługi Log Analytics i w sekcji *Ogólne* `search "SQLSecurityAuditEvents"` kliknij pozycję *Dzienniki* i wprowadź proste zapytanie, takie jak: aby wyświetlić dzienniki inspekcji.  

Dzienniki usługi Azure Monitor zapewniają szczegółowe informacje operacyjne w czasie rzeczywistym przy użyciu zintegrowanych systemów wyszukiwania i niestandardowych pulpitów nawigacyjnych, aby łatwo analizować miliony rekordów we wszystkich obciążeniach i serwerach. Aby uzyskać dodatkowe przydatne informacje na temat języka wyszukiwania i poleceń dziennika usługi Azure Monitor, zobacz [Odwołanie do wyszukiwania dzienników usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Inspekcja różnic między bazami danych w bazie danych SQL Azure i bazami danych w programie SQL Server

Kluczowe różnice między inspekcją w bazach danych w usłudze Azure SQL Database i baz danych w programie SQL Server to:

- Dzięki opcji wdrażania wystąpienia zarządzanego w usłudze Azure SQL `.xel` Database inspekcja działa na poziomie serwera i przechowuje pliki dziennika w magazynie obiektów Blob platformy Azure.
- W sql server na maszynach lokalnych / wirtualnych inspekcji działa na poziomie serwera, ale przechowuje zdarzenia w dziennikach zdarzeń system/windows plików.

Inspekcja XEvent w wystąpieniu zarządzanym obsługuje obiekty docelowe magazynu obiektów Blob platformy Azure. Dzienniki plików i okien nie są **obsługiwane**.

Kluczowe różnice w `CREATE AUDIT` składni do inspekcji do magazynu obiektów Blob platformy Azure są następujące:

- Nowa składnia `TO URL` jest dostarczana i umożliwia określenie adresu URL kontenera `.xel` usługi Azure blob Storage, w którym są umieszczane pliki.
- Nowa składnia `TO EXTERNAL MONITOR` jest dostępna, aby włączyć centrum parzyste i usługi Azure Monitor dzienniki obiektów docelowych.
- Składnia nie `TO FILE` jest **obsługiwana,** ponieważ baza danych SQL nie może uzyskać dostępu do udziałów plików systemu Windows.
- Opcja zamykania nie jest **obsługiwana**.
- `queue_delay`0 nie jest **obsługiwany**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać pełną listę metod zużycia dziennika inspekcji, zobacz [Wprowadzenie do inspekcji bazy danych SQL](sql-database-auditing.md).
- Aby uzyskać więcej informacji na temat programów platformy Azure, które obsługują zgodność ze standardami, zobacz [Centrum zaufania platformy Azure,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) gdzie można znaleźć najnowszą listę certyfikatów zgodności bazy danych SQL.

<!--Image references-->









