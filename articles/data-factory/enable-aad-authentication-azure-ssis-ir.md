---
title: Włącz uwierzytelnianie usługi Azure Active Directory dla środowiska Azure-SSIS integration runtime | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania środowiska Azure-SSIS IR do połączeń, które korzystają z uwierzytelniania usługi Azure Active Directory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: douglasl
ms.openlocfilehash: aa06110a6f6fe668388c6aecd98c1ddeeae37edd
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576633"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Włącz uwierzytelnianie usługi Azure Active Directory dla środowiska Azure-SSIS integration runtime

W tym artykule pokazano, jak utworzyć środowisko IR Azure-SSIS przy użyciu tożsamości usługi Azure Data Factory. Uwierzytelnianie usługi Active Directory (Azure AD) systemu Azure za pomocą tożsamość usługi zarządzanej (MSI) dla środowiska Azure-SSIS integration runtime pozwala utworzyć środowisko Azure-SSIS integration runtime przy użyciu pliku MSI z fabryki danych zamiast uwierzytelniania SQL.

Aby uzyskać więcej informacji na temat MSI fabryki danych, zobacz [tożsamości usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Jeśli utworzono już środowiska Azure-SSIS integration runtime przy użyciu uwierzytelniania SQL, nie można ponownie skonfigurować środowiska IR do użycia uwierzytelnianie usługi Azure AD przy użyciu programu PowerShell w tej chwili.

## <a name="create-a-group-in-azure-ad-and-make-the-data-factory-msi-a-member-of-the-group"></a>Utwórz grupę w usłudze Azure AD i dołączyć MSI fabryki danych do grupy

Możesz użyć istniejącej grupy usługi Azure AD lub utworzyć nową grupę przy użyciu programu Azure AD PowerShell.

1.  Zainstaluj [usługi Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modułu.

2.  Zaloguj się przy użyciu `Connect-AzureAD`, uruchom następujące polecenie, aby utworzyć grupę i zapisz go w zmiennej:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Dane wyjściowe wyglądają jak w poniższym przykładzie, który również sprawdza, czy wartość zmiennej:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Dodaj plik MSI z fabryki danych do grupy. Możesz wykonać [tożsamości usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) można pobrać identyfikator podmiotu zabezpieczeń tożsamości usługi (na przykład 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, ale nie należy używać identyfikator aplikacji tożsamości usługi w tym celu).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Ponadto można sprawdzić członkostwa w grupie później.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

## <a name="enable-azure-ad-on-azure-sql-database"></a>Włączanie usługi Azure AD w usłudze Azure SQL Database

Usługa Azure SQL Database obsługuje tworzenie bazy danych za pomocą użytkownika usługi Azure AD. Co w efekcie można ustawić użytkownika usługi Azure AD jako administratora usługi Active Directory, a następnie zaloguj się do programu SSMS, za pomocą użytkownika usługi Azure AD. Następnie można utworzyć użytkownika zawartej grupy usługi Azure AD włączyć środowisko IR utworzenie wykazu usług SQL Server Integration Services (SSIS) na serwerze.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Włącz uwierzytelnianie usługi Azure AD dla usługi Azure SQL Database

Możesz [Konfigurowanie uwierzytelniania usługi Azure AD dla usługi SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) wykonując następujące czynności:

1.  W witrynie Azure portal wybierz **wszystkich usług** -> **serwerów SQL** nawigacji po lewej stronie.

2.  Wybierz bazę danych SQL do włączenia uwierzytelniania usługi Azure AD.

3.  W **ustawienia** części bloku wybierz **administratora usługi Active Directory**.

4.  Na pasku poleceń Wybierz **Ustaw administratora**.

5.  Wybierz konto użytkownika usługi Azure AD, można wprowadzić administrator serwera, a następnie wybierz pozycję **wybierz.**

6.  Na pasku poleceń Wybierz **Zapisz.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Tworzenie w bazie danych użytkownika zawartego, który będzie reprezentować grupę usługi Azure AD

W tym następnego kroku należy [programu Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Uruchom program SQL Server Management Studio.

2.  W **Połącz z serwerem** okno dialogowe, wprowadź nazwę serwera SQL w **nazwy serwera** pola.

3.  W polu **Uwierzytelnianie** wybierz opcję **Active Directory — uniwersalne z obsługą uwierzytelniania wieloskładnikowego**. (Możesz również użyć innych dwa typy uwierzytelniania usługi Active Directory. Zobacz [Konfigurowanie i zarządzanie uwierzytelnianiem usługi Azure Active Directory z usługą SQL Database, wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).)

4.  W **nazwa_użytkownika** wprowadź nazwę konta usługi Azure AD, który został ustawiony jako administrator serwera — na przykład testuser@xxxonline.com.

5.  Wybierz **połączyć**. Zakończ proces logowania.

6.  W **Eksplorator obiektów**, rozwiń węzeł **baz danych** -> folderów systemowych baz danych.

7.  Po prawej stronie wybierz pozycję na **wzorca** bazy danych, a następnie wybierz pozycję **nowe zapytanie**.

8.  W oknie kwerendy wpisz następujący wiersz, a następnie wybierz pozycję **Execute** na pasku narzędzi:

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Polecenie powinno zakończyć się pomyślnie, tworząc zawartego użytkownika grupy.

9.  Wyczyść okno zapytania, wprowadź następujące polecenie, a następnie wybierz **Execute** na pasku narzędzi:

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    Polecenie powinno zakończyć się pomyślnie, udzielania zawartej użytkownikowi możliwość tworzenia bazy danych.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Włączanie usługi Azure AD na wystąpienie zarządzane usługi Azure SQL Database

Wystąpienie usługi Azure SQL Database Managed nie obsługuje tworzenia bazy danych z żadnym użytkownikiem usługi Azure AD, innym niż administratora usługi AD. W rezultacie musisz ustawić grupę usługi Azure AD jako administrator usługi Active Directory. Nie należy do utworzenia zawartego użytkownika.

Możesz [Konfigurowanie uwierzytelniania usługi Azure AD dla serwera wystąpienia zarządzanego usługi SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) wykonując następujące czynności:

7.  W witrynie Azure portal wybierz **wszystkich usług** -> **serwerów SQL** nawigacji po lewej stronie.

8.  Wybierz program SQL server do włączenia uwierzytelniania usługi Azure AD.

9.  W **ustawienia** części bloku wybierz **administratora usługi Active Directory**.

10. Na pasku poleceń Wybierz **Ustaw administratora**.

11. Wyszukaj i wybierz grupę usługi Azure AD (na przykład SSISIrGroup), a następnie wybierz **wybierz.**

12. Na pasku poleceń Wybierz **Zapisz.**

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>Aprowizowanie środowiska Azure-SSIS IR w portalu

Przypadku aprowizowania środowiska IR Azure-SSIS przy użyciu witryny Azure portal na **ustawień serwera SQL** strony wyboru "Użyj usługi AAD uwierzytelniania za pomocą usługi ADF pliku MSI" opcji. (Poniższy zrzut ekranu przedstawia ustawienia dla środowiska IR za pomocą usługi Azure SQL Database. Środowisko IR za pomocą wystąpienia zarządzanego właściwość "Warstwie usługi bazy danych wykazu" nie jest dostępna; inne ustawienia są takie same).

Aby uzyskać więcej informacji na temat tworzenia środowiska Azure-SSIS integration runtime, zobacz [tworzenie środowiska Azure-SSIS integration runtime w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Ustawienia środowiska Azure-SSIS integration Runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>Aprowizowanie środowiska Azure-SSIS IR za pomocą programu PowerShell

Aby zainicjować obsługę środowiska IR Azure-SSIS przy użyciu programu PowerShell, wykonaj następujące czynności:

1.  Zainstaluj [programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modułu.

2.  W skrypcie, nie należy ustawiać *CatalogAdminCredential* parametru. Na przykład:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Type Managed `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -Description $AzureSSISDescription `
                                               -Edition $AzureSSISEdition `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
