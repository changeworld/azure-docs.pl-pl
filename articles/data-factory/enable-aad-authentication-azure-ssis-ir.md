---
title: Włącz uwierzytelnianie usługi Azure Active Directory dla środowiska uruchomieniowego integracji usług SSIS Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania środowiska uruchomieniowego integracji usług SSIS Azure umożliwiających połączenia, które korzystają z uwierzytelniania usługi Azure Active Directory.
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
ms.openlocfilehash: 93d3e25957fb1f04400fa78423a5658d32f7d5fd
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749722"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Włącz uwierzytelnianie usługi Azure Active Directory dla środowiska uruchomieniowego integracji usług SSIS Azure

W tym artykule przedstawiono sposób tworzenia IR Azure SSIS z tożsamości usługi fabryka danych Azure. Azure uwierzytelniania usługi Active Directory (Azure AD) z zarządzanych usługi tożsamości (MSI) środowiska uruchomieniowego integracji usług SSIS Azure umożliwia używanie MSI fabryki danych zamiast uwierzytelniania SQL do tworzenia środowiska uruchomieniowego integracji usług SSIS Azure.

Aby uzyskać więcej informacji na temat MSI fabryki danych zobacz [tożsamości usługi fabryka danych Azure](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Jeśli utworzono już środowiska uruchomieniowego integracji usług SSIS Azure z uwierzytelnianiem SQL, nie można ponownie skonfigurować IR, aby używać uwierzytelniania usługi Azure AD przy użyciu programu PowerShell w tym momencie.

## <a name="create-a-group-in-azure-ad-and-make-the-data-factory-msi-a-member-of-the-group"></a>Utwórz grupę w usłudze Azure AD i dołącz je MSI fabryki danych grupy

Można użyć istniejącej grupy usługi Azure AD, lub Utwórz nową przy użyciu programu Azure AD PowerShell.

1.  Zainstaluj [programu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modułu.

2.  Zaloguj się przy użyciu `Connect-AzureAD`, uruchom następujące polecenie, aby utworzyć grupę i zapisz go w zmiennej:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Dane wyjściowe wyglądają jak w następującym przykładzie, który sprawdza również wartość zmiennej:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Dodaj MSI fabryki danych do grupy. Możesz wykonać [tożsamości usługi fabryka danych Azure](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-service-identity) można pobrać Identyfikatora podmiotu zabezpieczeń tożsamości usługi (na przykład 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, ale w tym celu nie należy używać Identyfikatora aplikacji tożsamości usługi).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Ponadto można sprawdzić członkostwa w grupie później.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

## <a name="enable-azure-ad-on-azure-sql-database"></a>Włączanie usługi Azure AD w bazie danych Azure SQL

Baza danych SQL Azure obsługuje tworzenie bazy danych z użytkownikiem usługi Azure AD. W związku z tym można ustawić użytkownika usługi Azure AD jako administratora usługi Active Directory, a następnie zaloguj się do narzędzia SSMS przy użyciu użytkownika usługi Azure AD. Następnie można utworzyć zawartego użytkownika dla grupy usługi Azure AD umożliwia IR, można utworzyć katalogu programu SQL Server Integration Services (SSIS) na serwerze.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Włącz uwierzytelnianie usługi Azure AD dla bazy danych SQL Azure

Możesz [Konfigurowanie uwierzytelniania usługi Azure AD dla bazy danych SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) wykonując następujące czynności:

1.  W portalu Azure wybierz **wszystkie usługi** -> **serwerów SQL** z nawigacji po lewej stronie.

2.  Wybierz bazę danych SQL dla uwierzytelniania usługi Azure AD.

3.  W **ustawienia** bloku, wybierz opcję **administratora usługi Active Directory**.

4.  Na pasku poleceń Wybierz **ustawić administratora**.

5.  Wybierz konto użytkownika usługi Azure AD można wprowadzać administrator serwera, a następnie wybierz **wybierz.**

6.  Na pasku poleceń Wybierz **zapisać.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Utworzenia zawartego użytkownika w bazie danych, który reprezentuje grupę usługi Azure AD

W tym kroku dalej należy [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Uruchom program SQL Server Management Studio.

2.  W **Połącz z serwerem** okna dialogowego, wprowadź nazwę serwera SQL w **nazwy serwera** pola.

3.  W **uwierzytelniania** pól, zaznacz **usługi Active Directory - uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego**. (Można również używać innych dwóch typów uwierzytelniania usługi Active Directory. Zobacz [Konfigurowanie i zarządzanie nimi uwierzytelniania usługi Azure Active Directory z bazą danych SQL, zarządzane wystąpienia](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure).)

4.  W **nazwy użytkownika** wprowadź nazwę konta usługi Azure AD, które można ustawić jako administratora — na przykład testuser@xxxonline.com.

5.  Wybierz **Connect**. Zakończenie procesu logowania.

6.  W **Eksplorator obiektów**, rozwiń węzeł **baz danych** -> folderów systemowych baz danych.

7.  Zaznacz prawej na **wzorca** bazy danych, a następnie wybierz **nowe zapytanie**.

8.  W oknie zapytania, wprowadź następujące polecenie, a następnie wybierz **Execute** na pasku narzędzi:

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Polecenie powinno zakończyć się pomyślnie, utworzenia zawartego użytkownika w grupie.

9.  Wyczyść okno kwerendy, wprowadź następujące polecenie, a następnie wybierz **Execute** na pasku narzędzi:

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    Polecenie powinno zakończyć się pomyślnie, umożliwianie zawartego użytkownika do tworzenia bazy danych.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Włączanie usługi Azure AD zarządzanego wystąpienia bazy danych Azure SQL

Azure wystąpienia bazy danych SQL zarządzane nie obsługuje tworzenia bazy danych z żadnym użytkownikiem usługi Azure AD, inne niż AD administratora. W związku z tym należy ustawić grupę usługi Azure AD jako administratora usługi Active Directory. Nie należy do utworzenia zawartego użytkownika.

Możesz [Konfigurowanie uwierzytelniania usługi Azure AD dla serwera zarządzanego wystąpienia bazy danych SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) wykonując następujące czynności:

7.  W portalu Azure wybierz **wszystkie usługi** -> **serwerów SQL** z nawigacji po lewej stronie.

8.  Wybierz program SQL server należy włączyć funkcję uwierzytelniania usługi Azure AD.

9.  W **ustawienia** bloku, wybierz opcję **administratora usługi Active Directory**.

10. Na pasku poleceń Wybierz **ustawić administratora**.

11. Wyszukaj i wybierz grupę usługi Azure AD (na przykład SSISIrGroup), a następnie wybierz **wybierz.**

12. Na pasku poleceń Wybierz **zapisać.**

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>Zainicjuj obsługę IR Azure SSIS w portalu

Podczas udostępniania sieci IR Azure SSIS z portalu Azure na **ustawienia programu SQL** strony, sprawdź "Użyj AAD uwierzytelniania za pomocą programu MSI ADF" opcji. (Poniższy zrzut ekranu przedstawia ustawienia IR z bazy danych SQL Azure. IR z wystąpieniem zarządzane właściwość "Warstwy usługi bazy danych katalogu" nie jest dostępna; inne ustawienia są takie same.)

Aby uzyskać więcej informacji o sposobie tworzenia środowiska uruchomieniowego integracji usług SSIS Azure, zobacz [utworzyć środowiska uruchomieniowego integracji usług SSIS Azure w fabryce danych Azure](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime).

![Ustawienia środowiska uruchomieniowego integracji usług SSIS Azure](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>Zainicjuj obsługę IR Azure SSIS przy użyciu programu PowerShell

Aby zainicjować obsługę sieci IR Azure SSIS przy użyciu programu PowerShell, wykonaj następujące czynności:

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
