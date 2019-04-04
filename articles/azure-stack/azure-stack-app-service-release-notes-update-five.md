---
title: Usługa App Service w usłudze Azure Stack update 5 informacje o wersji | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o nowościach w aktualizacji 5 dla usługi App Service w usłudze Azure Stack, znanych problemów i umożliwiające pobranie aktualizacji.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 851673de4a711f5eb64228233ea7703ef86bfa7b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500206"
---
# <a name="app-service-on-azure-stack-update-5-release-notes"></a>Usługa App Service w usłudze Azure Stack update 5 — informacje o wersji

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Te informacje o wersji opisano ulepszeń i poprawek w usłudze Azure App Service na usługi Azure Stack aktualizacja Update 5 i znanych problemach. Znane problemy są podzielone na problemy z bezpośrednio do wdrożenia, proces aktualizacji i problemy z kompilacją (po instalacji).

> [!IMPORTANT]
> Dotyczą aktualizacji 1901 system zintegrowany z usługi Azure Stack można też wdrażać najnowszy zestaw Azure Stack development kit przed wdrożeniem Azure App Service w wersji 1.5.


## <a name="build-reference"></a>Dokumentacja kompilowania

App Service na numer kompilacji usługi Azure Stack aktualizacji 5 to **80.0.2.15**

### <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj się [dokumentacji przed rozpoczęciem](azure-stack-app-service-before-you-get-started.md) przed rozpoczęciem wdrażania.

Przed rozpoczęciem uaktualniania usługi Azure App Service w usłudze Azure Stack do wersji 1.5:

- Upewnij się, wszystkie role są gotowe w administracji usługi Azure App Service w portalu administracyjnym usługi Azure Stack

- Tworzyć kopie zapasowe usługi App Service i bazy danych Master:
  - AppService_Hosting;
  - AppService_Metering;
  - Główny

- Tworzenie kopii zapasowej udziału plików zawartości aplikacji dzierżawy

- Konsorcjum **rozszerzenia niestandardowego skryptu** wersji **1.9.1** z witryny Marketplace

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki

Usługa Azure App Service w usłudze Azure Stack aktualizacji 5 obejmuje następujące ulepszenia i poprawki:

- Aktualizacje **dzierżawy usługi aplikacji, administrator, portali funkcje i narzędzia Kudu**. Zgodne z wersją zestawu SDK usługi Azure Stack Portal.

- Aktualizacje **środowisko uruchomieniowe usługi Azure Functions** do **v1.0.12205**.

- Aktualizacje **narzędzia Kudu** Aby rozwiązać problemy z stylu i funkcje dla klientów działających **odłączony** usługi Azure Stack. 

- Aktualizacje do podstawowej usługi, aby zwiększyć niezawodność i komunikatów o włączenie łatwiejsze Diagnostyka typowe problemy.

- **Aktualizacje do narzędzi i struktur aplikacji na następujących**:
  - Dodano platformy ASP.NET Core 2.1.6 i 2.2.0
  - Dodano NodeJS 10.14.1
  - Dodano NPM 6.4.1
  - Zaktualizowano Kudu do 79.20129.3767
  
- **Aktualizacje do zasadniczego systemu operacyjnego wszystkich ról**:
  - [Aktualizacja zbiorcza 2019-02 dla systemu Windows Server 2016 systemów x64 64 (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

### <a name="post-deployment-steps"></a>Czynności po wdrożeniu

> [!IMPORTANT]  
> Jeśli podano jednostki Uzależnionej usługi aplikacji przy użyciu zawsze w wystąpieniu SQL musi [Dodawanie appservice_hosting i appservice_metering baz danych do grupy dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) i synchronizowanie baz danych w celu zapobieżenia utracie pracy w zdarzenia przejścia w tryb failover bazy danych.

### <a name="post-update-steps"></a>Kroki po aktualizacji

Klienci chcą migrować do zawartej bazy danych dla istniejącej usługi Azure App Service w wdrożenia usługi Azure Stack wykonaj następujące kroki, pod warunkiem została zakończona w usłudze Azure App Service w usłudze Azure Stack w wersji 1.5 update:

> [!IMPORTANT]
> Procedury migracji trwa około 5 – 10 minut.  Procedura obejmuje zabijanie istniejących sesji logowania bazy danych.  Zaplanuj czas przestoju do migracji i weryfikowanie usługi Azure App Service w usłudze Azure Stack po migracji.  Jeśli wykonano tych kroków po aktualizacji do usługi Azure App Service w usłudze Azure Stack 1.3 te kroki nie są wymagane.
>
>

1. Dodaj [AppService baz danych (appservice_hosting i appservice_metering) do grupy dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. Włącz zawarte bazy danych
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Konwertowanie bazy danych zawarty w częściowo, konwersja zostaną naliczone przestoju zgodnie z wszystkich aktywnych sesji musi być zakończony

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

Walidacja

1. Sprawdź, czy program SQL Server ma włączone zawierania

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Sprawdź istniejące zachowanie zawarte
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

- Pracownicy są nie można nawiązać połączenia z serwerem plików po wdrożeniu usługi App Service w istniejącej sieci wirtualnej i serwer plików jest dostępna tylko w sieci prywatnej, jak w usłudze Azure App Service w dokumentacji wdrażania usługi Azure Stack.

Jeśli wybierzesz do wdrożenia w istniejącej sieci wirtualnej i wewnętrzny adres IP, aby nawiązać połączenie z serwerem plików, należy dodać regułę zabezpieczeń dla ruchu wychodzącego włączanie ruchu SMB między podsieci procesów roboczych i serwera plików. Przejdź do WorkersNsg w portalu administracyjnym i dodawanie reguły zabezpieczeń dla ruchu wychodzącego z następującymi właściwościami:
 * Źródło: Dowolne
 * Zakres portów źródłowych: *
 * Miejsce docelowe: Adresy IP
 * Docelowy zakres adresów IP: Zakres adresów IP dla serwera plików
 * Zakres portów docelowych: 445
 * Protokół: TCP
 * Akcja: Zezwalaj
 * Priorytet: 700
 * Nazwa: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Znane problemy dotyczące administratorów chmury, działania usługi Azure App Service w usłudze Azure Stack

Zapoznaj się z dokumentacją w [informacje o wersji usługi Azure Stack 1809](azure-stack-update-1809.md)

## <a name="next-steps"></a>Kolejne kroki

- Omówienie usługi Azure App Service, zobacz [usługi Azure App Service w usłudze Azure Stack — omówienie](azure-stack-app-service-overview.md).
- Aby uzyskać więcej informacji na temat przygotowywania do wdrożenia usługi App Service w usłudze Azure Stack, zobacz [przed rozpoczęciem pracy z usługą App Service w usłudze Azure Stack](azure-stack-app-service-before-you-get-started.md).
