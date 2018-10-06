---
title: Usługi Azure SQL Database Managed inspekcji wystąpienia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozpocząć pracę z platformy Azure zarządzanego wystąpienia inspekcji usługi SQL Database przy użyciu języka T-SQL
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
ms.date: 09/20/2018
ms.openlocfilehash: 1ff0df958e541d8dd4b70827ea8e064cd3100e50
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815090"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Rozpoczynanie pracy z usługą Azure Managed wystąpienia inspekcji usługi SQL Database

[Wystąpienie usługi Azure SQL Database Managed](sql-database-managed-instance.md) inspekcji śledzi zdarzenia bazy danych i zapisuje je do inspekcji logowania na koncie magazynu platformy Azure. Inspekcja również:
- Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych i uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.
- Włącza i ułatwia zgodności ze standardami zgodności, ale nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat usługi Azure programy zgodność ze standardami tej pomocy technicznej, zobacz [Centrum zaufania systemu Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a name="set-up-auditing-for-your-server"></a>Inspekcja serwera

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
     - **Dozwolone usługi**: obiektów Blob
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

## <a name="analyze-audit-logs"></a>Analizowanie dzienników inspekcji
Istnieje kilka metod, których można użyć, aby wyświetlić dzienniki inspekcji obiektów blob.

- Użyj funkcji systemowej `sys.fn_get_audit_file` (T-SQL), aby zwrócić dane z dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [dokumentacji sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Aby uzyskać pełną listę metod użycie dziennika inspekcji, zobacz [Rozpoczynanie pracy z inspekcją bazy danych SQL](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> Metoda wyświetlanie rekordów inspekcji w witrynie Azure portal (w okienku "Rekordy inspekcji") jest obecnie niedostępna dla wystąpienia zarządzanego.

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Inspekcja różnice między wystąpienia zarządzanego usługi Azure SQL Database i programu SQL Server

Podstawowe różnice między inspekcji SQL w wystąpieniu zarządzanym, Azure SQL Database i SQL Server w środowisku lokalnym są:

- W wystąpieniu zarządzanym inspekcji SQL działa na poziomie serwera i magazyny `.xel` dzienniki na koncie magazynu obiektów blob platformy Azure.
- W usłudze Azure SQL Database inspekcji SQL, działa na poziomie bazy danych.
- W programie SQL Server w środowisku lokalnym / wirtualnych maszyn, działania inspekcji SQL na serwerze poziomu, ale zdarzenia magazyny plików system i windows dzienniki zdarzeń.

Inspekcji systemu XEvent, w wystąpieniu zarządzanym obsługuje obiekty docelowe magazynu obiektów blob platformy Azure. Dzienniki plików i systemu windows są **nieobsługiwane**.

Klucz różnice w `CREATE AUDIT` składnia dla inspekcji w usłudze Azure blob storage są:
- Nowa składnia `TO URL` jest dostarczany i umożliwia określenie adresu URL kontenera magazynu obiektów blob platformy Azure gdzie `.xel` pliki zostaną umieszczone.
- Składnia `TO FILE` jest **nieobsługiwane** ponieważ wystąpienia zarządzanego nie można uzyskać dostępu do udziałów plików w Windows.
- Opcja zamknięcie jest **nieobsługiwane**.
- `queue_delay` 0 jest **nieobsługiwane**.


## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać pełną listę metod użycie dziennika inspekcji, zobacz [Rozpoczynanie pracy z inspekcją bazy danych SQL](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).
- Aby uzyskać więcej informacji na temat usługi Azure programy zgodność ze standardami tej pomocy technicznej, zobacz [Centrum zaufania systemu Azure](https://azure.microsoft.com/support/trust-center/compliance/).


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
