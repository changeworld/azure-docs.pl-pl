---
title: Usługa App Service w usłudze Azure Stack aktualizacji 3 — informacje o wersji | Dokumentacja firmy Microsoft
description: Poznaj nowości w aktualizacji 3 dla usługi App Service w usłudze Azure Stack, znanych problemów i umożliwiające pobranie aktualizacji.
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
ms.reviewer: sethm
ms.lastreviewed: 08/20/2018
ms.openlocfilehash: 5ea711d3d4ffff72279e745290c1c8d9d854298e
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447491"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>Usługa App Service w usłudze Azure Stack update 3 — informacje o wersji

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Te informacje o wersji opisano ulepszeń i poprawek w usłudze Azure App Service w usłudze Azure Stack Update 3 i znanych problemach. Znane problemy są podzielone na problemy z bezpośrednio do wdrożenia, proces aktualizacji i problemy z kompilacją (po instalacji).

> [!IMPORTANT]
> Dotyczą aktualizacji 1807 system zintegrowany z usługi Azure Stack można też wdrażać najnowszy zestaw Azure Stack development kit przed wdrożeniem usługi Azure App Service 1.3.
>
>

## <a name="build-reference"></a>Dokumentacja kompilowania

App Service na numer kompilacji programu Azure Stack Update 3 to **74.0.13698.31**

### <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj się [dokumentacji przed rozpoczęciem](azure-stack-app-service-before-you-get-started.md) przed rozpoczęciem wdrażania.

Przed rozpoczęciem uaktualniania usługi Azure App Service w usłudze Azure Stack 1.3, upewnij się, wszystkie role są gotowe w administracji usługi Azure App Service w portalu administracyjnym usługi Azure Stack

![Stan roli w ramach usługi App Service](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki

Usługa Azure App Service w usłudze Azure Stack Update 3 obejmuje następujące ulepszenia i poprawki:

- Obsługa na użytek programu SQL Server Always On bazy danych dostawcy zasobów usługi Azure App Service.

- Dodano nowy parametr środowiska do skryptu pomocnika AADIdentityApp Utwórz w celu ułatwienia określania wartości docelowej różnych regionach usługi AAD.

- Aktualizacje **dzierżawy usługi aplikacji, administrator, portali funkcje i narzędzia Kudu**. Zgodne z wersją zestawu SDK usługi Azure Stack Portal.

- Aktualizacje **środowisko uruchomieniowe usługi Azure Functions** do **v1.0.11820**.

- Aktualizacje do podstawowej usługi, aby zwiększyć niezawodność i komunikatów o włączenie łatwiejsze Diagnostyka typowe problemy.

- **Aktualizacje do narzędzi i struktur aplikacji na następujących**:
  - Added ASP.NET Core 2.1.2
  - Dodano NodeJS 10.0.0
  - Added Zulu OpenJDK 8.30.0.1
  - Dodano Tomcat 8.5.31 i 9.0.8
  - Dodano PHP wersje:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Dodano Wincache 2.0.0.8
  - Zaktualizowano Git dla Windows, aby v 2.17.1.2
  - Zaktualizowano Kudu do 74.10611.3437
  
- **Aktualizacje do zasadniczego systemu operacyjnego wszystkich ról**:
  - [Aktualizacja stosu systemu Windows Server 2016 systemów x64 64 (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [Aktualizacja zbiorcza 2018-07 dla systemu Windows Server 2016 systemów x64 64 (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Opublikuj aktualizację kroki (opcjonalnie)

Klienci chcą migrować do zawartej bazy danych dla istniejącej usługi Azure App Service w wdrożenia usługi Azure Stack wykonaj następujące kroki, pod warunkiem została zakończona w usłudze Azure App Service w usłudze Azure Stack 1.3 update:

> [!IMPORTANT]
> Ta procedura trwa około 5 – 10 minut.  Ta procedura obejmuje zabijanie istniejących sesji logowania bazy danych.  Zaplanuj czas przestoju do migracji i weryfikowanie usługi Azure App Service w usłudze Azure Stack po migracji
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

1. Konwertowanie bazy danych zawarty w częściowo.  Ten krok spowoduje naliczenie Przestój, ponieważ wszystkich aktywnych sesji muszą być zakończony

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

- Pracownicy są nie można nawiązać połączenia z serwerem plików po wdrożeniu usługi App Service w istniejącej sieci wirtualnej i serwer plików jest dostępna tylko w sieci prywatnej.  Jest to również nazywane w usłudze Azure App Service w dokumentacji wdrażania usługi Azure Stack.

Jeśli wybierzesz do wdrożenia w istniejącej sieci wirtualnej i wewnętrzny adres IP, aby nawiązać połączenie z serwerem plików, należy dodać regułę zabezpieczeń dla ruchu wychodzącego włączanie ruchu SMB między podsieci procesów roboczych i serwera plików. Aby to zrobić, przejdź do WorkersNsg w portalu administracyjnym i dodawanie reguły zabezpieczeń dla ruchu wychodzącego z następującymi właściwościami:
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

Zapoznaj się z dokumentacją w informacjach o wersji usługi Azure Stack 1807.

## <a name="next-steps"></a>Kolejne kroki

- Omówienie usługi Azure App Service, zobacz [usługi Azure App Service w usłudze Azure Stack — omówienie](azure-stack-app-service-overview.md).
- Aby uzyskać więcej informacji na temat przygotowywania do wdrożenia usługi App Service w usłudze Azure Stack, zobacz [przed rozpoczęciem pracy z usługą App Service w usłudze Azure Stack](azure-stack-app-service-before-you-get-started.md).
