---
title: Baza danych Azure SQL zarządzane wystąpienia inspekcji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozpocząć pracę z Azure SQL Database zarządzane wystąpienia Auditing za pomocą T-SQL
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: giladm
ms.openlocfilehash: 71929be456de4b798da48bb202969deb71e1c371
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648857"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Wprowadzenie do usługi Azure SQL Database zarządzane wystąpienia Auditing

[Azure wystąpienia bazy danych SQL zarządzane](sql-database-managed-instance.md) inspekcji śledzi zdarzenia bazy danych i zapisuje je inspekcji logowania na koncie magazynu Azure. Inspekcja również:
- Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.
- Włącza i ułatwia przestrzeganie standardów zgodności, mimo że nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat usługi Azure programy tego zgodność ze standardami pomocy technicznej, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a name="set-up-auditing-for-your-server"></a>Ustawienia inspekcji serwera

W poniższej sekcji opisano konfigurację inspekcji na wystąpienie zarządzane.
1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Poniższe kroki tworzenia usługi Azure Storage **kontenera** przechowywania dzienników inspekcji.

   - Przejdź do magazynu Azure, w którym chcesz przechowywać dzienników inspekcji.

     > [!IMPORTANT]
     > Użyj konta magazynu w tym samym regionie co zarządzane wystąpienia serwera, aby uniknąć region między odczyt/zapis.

   - W ramach konta magazynu, przejdź do **omówienie** i kliknij przycisk **obiekty BLOB**.

     ![Okienko nawigacji][1]

   - W menu górnym, kliknij przycisk **+ kontener** do utworzenia nowego kontenera.

     ![Okienko nawigacji][2]

   - Podaj kontener **nazwa**, Ustaw poziom dostępu publicznego **prywatnej**, a następnie kliknij przycisk **OK**.

     ![Okienko nawigacji][3]

   - Na liście kontenery kliknij kontener nowo utworzony, a następnie kliknij przycisk **właściwości kontenera**.

     ![Okienko nawigacji][4]

   - Skopiuj adres URL kontenera, klikając ikonę kopiowania i Zapisz adres URL (na przykład w Notatniku) do użycia w przyszłości. Powinna mieć ona format adresu URL kontenera `https://<StorageName>.blob.core.windows.net/<ContainerName>`

     ![Okienko nawigacji][5]

3. Poniższe kroki Generowanie usługi Azure Storage **tokenu sygnatury dostępu Współdzielonego** służy do udzielania zarządzane inspekcji wystąpienia praw dostępu do konta magazynu.

   - Przejdź do konta magazynu Azure, której utworzono kontener w poprzednim kroku.

   - Polecenie **współużytkowanego podpisu dostępu** w menu Ustawienia magazynu.

     ![Okienko nawigacji][6]

   - Skonfiguruj sygnatury dostępu Współdzielonego w następujący sposób:
     - **Dozwolone usług**: obiektów Blob
     - **Data rozpoczęcia**: Aby uniknąć problemów związanych z strefy czasowej, zaleca się użyć daty przestarzałe.
     - **Data zakończenia**: Wybierz datę tego tokenu sygnatury dostępu Współdzielonego wygaśnięcia. 

       > [!NOTE]
       > Odnów token po upływie w celu uniknięcia niepowodzeń inspekcji.

     - Kliknij opcję **Generuj sygnaturę dostępu współdzielonego**.

       ![Okienko nawigacji][7]

   - Po kliknięciu przycisku na Generowanie sygnatury dostępu Współdzielonego, SAS Token pojawia się na dole. Skopiuj ten token, klikając ikonę kopiowania i zapisz go (na przykład w Notatniku) do użycia w przyszłości.

     > [!IMPORTANT]
     > Usuń znak zapytania ("?") znaków od początku tokenu.

     ![Okienko nawigacji][8]

4. Nawiązać połączenia z wystąpieniem zarządzanych za pomocą programu SQL Server Management Studio (SSMS).

5. Wykonaj następującą instrukcję T-SQL do **utworzyć nowe poświadczenie** przy użyciu adresu URL kontenera i sygnatury dostępu Współdzielonego Token utworzony w poprzednich krokach:

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. Wykonaj następującą instrukcję T-SQL, aby utworzyć nowy Server Audit (Wybierz nazwę inspekcji, użyj adresu URL kontenera, który został utworzony w poprzednich krokach):

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    Jeśli nie zostanie określony, `RETENTION_DAYS` domyślna to 0 (nieograniczone przechowywania).

    Dodatkowe informacje:
    - [Inspekcja różnice między zarządzane wystąpienia, bazy danych SQL Azure i SQL Server](#subheading-3)
    - [TWORZENIE INSPEKCJI SERWERA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [INSTRUKCJA ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. Utwórz specyfikacji inspekcji serwera lub specyfikacji inspekcji bazy danych, jak w przypadku programu SQL Server:
    - [Utwórz przewodnik T-SQL specyfikacji inspekcji serwera](https://docs.microsoft.com/ sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [Utwórz Podręcznik T-SQL specyfikacji inspekcji bazy danych](https://docs.microsoft.com/ sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. Włączanie inspekcji serwera, który został utworzony w kroku 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="analyze-audit-logs"></a>Analizowanie dzienników inspekcji
Istnieje kilka metod, których można użyć do wyświetlenia obiektu blob, dzienniki inspekcji.

- Użyj funkcji systemowej `sys.fn_get_audit_file` (T-SQL), aby przywrócić dane z dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat używania tej funkcji, zobacz [dokumentacji sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Aby uzyskać pełną listę metod użycie dziennika inspekcji, zapoznaj się [wprowadzenie inspekcji bazy danych SQL](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> Metoda wyświetlanie rekordów inspekcji z portalu Azure (w okienku "Rekordów inspekcji") jest obecnie niedostępna dla wystąpienia zarządzane.

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Inspekcja różnice między zarządzane wystąpienia bazy danych SQL Azure i SQL Server

Podstawowe różnice między inspekcji SQL zarządzane wystąpienia bazy danych SQL Azure i SQL Server w lokalnym programie są:

- W przypadku zarządzanych inspekcji SQL działa na poziomie serwera i magazyny `.xel` dzienniki na koncie magazynu obiektów blob platformy Azure.
- W bazie danych SQL Azure inspekcji SQL działa na poziomie bazy danych.
- W lokalnej instalacji programu SQL Server / wirtualnej maszyny, inspekcji SQL działa na serwerze poziomu, ale zdarzenia magazynów na pliki system i windows dzienników zdarzeń.

Zarządzane wystąpienia inspekcji systemu XEvent obsługuje obiekty docelowe magazynu obiektów blob platformy Azure. Dzienniki plików i systemu windows są **nieobsługiwane**.

Klucz różnice w `CREATE AUDIT` składnię inspekcji do magazynu obiektów blob platformy Azure są:
- Nowej składni `TO URL` podano i umożliwia określenie adresu URL kontenera magazynu obiektów blob platformy Azure gdzie `.xel` pliki zostaną umieszczone.
- Składnia `TO FILE` jest **nieobsługiwane** ponieważ zarządzane wystąpienia nie ma dostępu do udziałów plików systemu Windows.
- Opcja zamknięcia jest **nieobsługiwane**.
- `queue_delay` 0 jest **nieobsługiwane**.


## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać pełną listę metod użycie dziennika inspekcji, zapoznaj się [wprowadzenie inspekcji bazy danych SQL](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).
- Aby uzyskać więcej informacji na temat usługi Azure programy tego zgodność ze standardami pomocy technicznej, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/support/trust-center/compliance/).


<!--Anchors-->
[Set up auditing for your server]: #subheading-1
[Analyze audit logs]: #subheading-2
[Auditing differences between Managed Instance, Azure SQL DB and SQL Server]: #subheading-3

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
