---
title: 'Szybki start: tworzenie wystąpienia trybu hybrydowego za pomocą witryny Azure Portal'
titleSuffix: Azure Database Migration Service
description: Użyj witryny Azure Portal, aby utworzyć wystąpienie usługi migracji bazy danych platformy Azure w trybie hybrydowym.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 03/13/2020
ms.openlocfilehash: dd3e77610749eb5d146b0c0b7cf9d307fba0dd83
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370240"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Szybki start: tworzenie wystąpienia trybu hybrydowego za pomocą usługi Azure portal & usługi migracji bazy danych Azure

Tryb hybrydowy usługi azure database migration service zarządza migracjami bazy danych przy użyciu procesu roboczego migracji, który jest hostowany lokalnie wraz z wystąpieniem usługi migracji bazy danych platformy Azure uruchomionej w chmurze. Tryb hybrydowy jest szczególnie przydatny w scenariuszach, w których brak jest łączności lokacja-lokacja między siecią lokalną a platformą Azure lub jeśli przepustowość łączności między lokacjami a lokacjami jest ograniczona.

>[!NOTE]
>Obecnie usługa migracji bazy danych Platformy Azure uruchomiona w trybie hybrydowym obsługuje migracje programu SQL Server do:
>
>- Wystąpienie zarządzanej usługi Azure SQL Database z niemal zerowym przestojem (online).
>- Pojedyncza baza danych usługi Azure SQL Database z pewnymi przestojami (w trybie offline).
>- MongoDb do usługi Azure CosmosDB z niemal zerowym przestojem (online).
>- MongoDb do usługi Azure CosmosDB z pewnym przestojem (w trybie offline).

W tym przewodniku Szybki start można użyć witryny Azure Portal, aby utworzyć wystąpienie usługi migracji bazy danych platformy Azure w trybie hybrydowym. Następnie pobierasz, instalujesz i skonfiguruj hybrydowego pracownika w sieci lokalnej. W wersji zapoznawczej można użyć trybu hybrydowego usługi azure database migracji do migracji danych z lokalnego wystąpienia programu SQL Server do bazy danych SQL Azure.

> [!NOTE]
> Instalator hybrydowy usługi migracji bazy danych Azure działa w systemach Microsoft Windows Server 2012 R2, Window Server 2016, Windows Server 2019 i Windows 10.

> [!IMPORTANT]
> Instalator hybrydowy usługi migracji bazy danych Azure wymaga platformy .NET 4.7.2 lub nowszej. Aby znaleźć najnowsze wersje platformy .NET, zobacz stronę [Pobierz program .NET Framework.](https://dotnet.microsoft.com/download/dotnet-framework)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Otwórz przeglądarkę internetową, przejdź do witryny [Microsoft Azure Portal](https://portal.azure.com/), a następnie wprowadź swoje poświadczenia, aby zalogować się w portalu.

Widok domyślny to pulpit nawigacyjny usług.

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

Przed utworzeniem pierwszego wystąpienia usługi migracji bazy danych platformy Azure należy zarejestrować dostawcę zasobów Microsoft.DataMigration.

1. W witrynie Azure portal wybierz **pozycję Subskrypcje**, wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi migracji bazy danych platformy Azure, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Dostawca zasobów wyszukiwania](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Wyszukaj migrację, a następnie po prawej stronie **microsoft.DataMigration**, wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Tworzenie wystąpienia usługi

1. Wybierz +**Utwórz zasób,** aby utworzyć wystąpienie usługi migracji bazy danych platformy Azure.

2. Wyszukaj w portalu Marketplace hasło "migracja", wybierz pozycję **Usługa migracji bazy danych platformy Azure,** a następnie na ekranie Usługi migracji bazy danych platformy **Azure** wybierz pozycję **Utwórz**.

3. Na ekranie **Tworzenie usługi migracji**:

    - Wybierz **nazwę usługi,** która jest zapadawająca zapamiętające i unikatowa, aby zidentyfikować wystąpienie usługi migracji bazy danych platformy Azure.
    - Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć wystąpienie.
    - Wybierz istniejącą **grupę zasobów** lub utwórz nową.
    - Wybierz pozycję **Lokalizacja** położoną najbliżej Twojego serwera źródłowego lub docelowego.
    - W **trybie serwisowym**wybierz opcję **Hybrydowy (Wersja zapoznawcza)**.

         ![Tworzenie usługi migracji — podstawy](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Wybierz pozycję **Przegląd + utwórz**.

5. Na karcie **Recenzja + utwórz** przejrzyj warunki, sprawdź inne podane informacje, a następnie wybierz pozycję **Utwórz**.

    ![Tworzenie usługi migracji — recenzja + tworzenie](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Po kilku chwilach twoje wystąpienie usługi migracji bazy danych Azure w trybie hybrydowym jest tworzone i gotowe do skonfigurowania. Wystąpienie usługi migracji bazy danych Azure jest wyświetlane w sposób pokazany na poniższej ilustracji:

    ![Wystąpienie trybu hybrydowego usługi migracji bazy danych Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. Po utworzeniu usługi wybierz pozycję **Właściwości**, a następnie skopiuj wartość wyświetlaną w polu **Identyfikator zasobu,** która będzie używana do zainstalowania procesu roboczego hybrydowego usługi azure database.

    ![Właściwości trybu hybrydowego usługi migracji bazy danych Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Tworzenie identyfikatora rejestracji aplikacji platformy Azure

Należy utworzyć identyfikator rejestracji aplikacji platformy Azure, którego lokalny hybrydowy proces roboczy może używać do komunikowania się z usługą migracji usługi Azure Database w chmurze.

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Rejestracje aplikacji**, a następnie wybierz pozycję Nowa **rejestracja**.
2. Określ nazwę aplikacji, a następnie w obszarze **Obsługiwane typy kont**wybierz typ kont do obsługi, aby określić, kto może korzystać z aplikacji.

    ![Aplikacja rejestru trybu migracji usługi azure database](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Użyj wartości domyślnych dla pól **Przekierowanie identyfikatora URI (opcjonalnie),** a następnie wybierz pozycję **Zarejestruj**.

4. Po zakończeniu rejestracji identyfikatora aplikacji zanotuj **identyfikator aplikacji (klienta),** którego użyjesz podczas instalowania hybrydowego procesu roboczego.

5. W witrynie Azure portal przejdź do usługi migracji bazy danych azure, wybierz **pozycję Kontrola dostępu (IAM),** a następnie wybierz pozycję **Dodaj przypisanie roli,** aby przypisać dostęp współautora do identyfikatora aplikacji.

    ![Tryb hybrydowy usługi migracji bazy danych platformy Azure przypisz rolę współautora](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Wybierz **opcję Współautor** jako rolę, przypisz dostęp do użytkownika usługi Azure AD lub **jednostki usługi,** a następnie wybierz nazwę identyfikatora aplikacji.

    ![Tryb hybrydowy usługi migracji bazy danych azure przypisywać szczegóły roli współautora](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Wybierz **pozycję Zapisz,** aby zapisać przypisanie roli dla identyfikatora aplikacji w zasobie usługi migracji bazy danych Azure.

## <a name="download-and-install-the-hybrid-worker"></a>Pobieranie i instalowanie hybrydowego pracownika

1. W witrynie Azure portal przejdź do wystąpienia usługi migracji bazy danych platformy Azure.

2. W obszarze **Ustawienia**wybierz pozycję **Hybrydowy**, a następnie wybierz pozycję **Pobierz instalatora,** aby pobrać proces pracy hybrydowej.

    ![Pobieranie hybrydowego pracownika usługi Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Wyodrębnij plik ZIP na serwerze, który będzie hostować hybrydowy proces pracy usługi Azure Database Migration Service.

    > [!IMPORTANT]
    > Instalator hybrydowy usługi migracji bazy danych Azure wymaga platformy .NET 4.7.2 lub nowszej. Aby znaleźć najnowsze wersje platformy .NET, zobacz stronę [Pobierz program .NET Framework.](https://dotnet.microsoft.com/download/dotnet-framework)

4. W folderze instalacji znajdź i otwórz plik **dmsSettings.json,** określ **identyfikator aplikacji** i **identyfikator zasobu**, a następnie zapisz plik.

    ![Ustawienia hybrydowego procesu migracji usługi azure database](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. Generowanie certyfikatu, którego usługa migracji bazy danych Azure może używać do uwierzytelniania komunikacji z hybrydowym pracownikiem roboczym przy użyciu następującego polecenia.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Certyfikat jest generowany w folderze Zainstaluj.

    ![Hybrydowy certyfikat procesu roboczego usługi migracji bazy danych Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. W witrynie Azure portal przejdź do identyfikatora aplikacji w obszarze **Zarządzaj**, wybierz pozycję **Certyfikat & wpisy tajne**, a następnie wybierz pozycję Przekaż **certyfikat,** aby wybrać wygenerowany certyfikat publiczny.

    ![Przekazywanie certyfikatu hybrydowego usługi migracji usługi azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Zainstaluj proces roboczy hybrydowy usługi azure database na serwerze lokalnym, uruchamiając następujące polecenie:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms -d
    ```

    > [!NOTE]
    > Podczas uruchamiania polecenia install można również użyć następujących parametrów:
    >
    > - **-TelemetryOptOut** — zatrzymuje pracownika od wysyłania danych telemetrycznych, ale nadal logować lokalnie minimalnie.  Instalator nadal wysyła dane telemetryczne.
    > - **-p {InstallLocation}**. Umożliwia zmianę ścieżki instalacji, która domyślnie to "C:\Program Files\DatabaseMigrationServiceHybrid".

8. Jeśli instalator działa bez błędu, usługa wyświetli stan online w usłudze migracji usługi Azure Database i będzie gotowy do migracji baz danych.

    ![Usługa migracji bazy danych platformy Azure w trybie online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Odinstalowywanie trybu hybrydowego usługi migracji bazy danych azure

Obecnie odinstalowywanie trybu hybrydowego usługi azure database migration service jest obsługiwane tylko za pośrednictwem hybrydowego instalatora procesu roboczego usługi azure database migration service na serwerze lokalnym, za pomocą następującego polecenia:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> Podczas uruchamiania polecenia odinstalowywania można również użyć parametru "-ReuseCert", który utrzymuje certyfikat AdApp generowany przez przepływ pracy generateCert.  Umożliwia to korzystanie z tego samego certyfikatu, który został wcześniej wygenerowany i przekazany.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>Konfigurowanie procesu roboczego hybrydowego usługi azure database przy użyciu programu PowerShell

Oprócz instalowania hybrydowego procesu roboczego usługi migracji usługi azure database za pośrednictwem portalu Azure udostępniamy [skrypt programu PowerShell,](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) którego można użyć do automatyzacji kroków instalacji procesu roboczego po utworzeniu nowego wystąpienia usługi migracji bazy danych platformy Azure w trybie hybrydowym. Skrypt:

1. Tworzy nową AdApp.
2. Pobiera instalatora.
3. Uruchamia przepływ pracy generateCert.
4. Przesyła certyfikat.
5. Dodaje AdApp jako współautora do wystąpienia usługi migracji bazy danych platformy Azure.
6. Uruchamia przepływ pracy instalacji.

Ten skrypt jest przeznaczony do szybkiego prototypowania, gdy użytkownik ma już wszystkie niezbędne uprawnienia w środowisku. Należy pamiętać, że w środowisku produkcyjnym AdApp i Cert mogą mieć różne wymagania, więc skrypt może zakończyć się niepowodzeniem.

> [!IMPORTANT]
> Ten skrypt zakłada, że istnieje istniejące wystąpienie usługi migracji bazy danych Azure w trybie hybrydowym i że konto platformy Azure używane ma uprawnienia do tworzenia Aplikacji AdApps w dzierżawie i modyfikowania rbac w ramach subskrypcji.

Wypełnij parametry w górnej części skryptu, a następnie uruchom skrypt z wystąpienia programu Administrator programu PowerShell.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database w trybie online](tutorial-sql-server-managed-instance-online.md)
> [Migrowanie programu SQL Server do pojedynczej bazy danych lub puli bazy danych w bazie danych SQL w trybie offline](tutorial-sql-server-to-azure-sql.md)
