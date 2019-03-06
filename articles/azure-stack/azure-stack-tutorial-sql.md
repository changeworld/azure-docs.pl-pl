---
title: Oferowanie baz danych SQL o wysokiej dostępności w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć dostawcę zasobów programu SQL Server komputer-host i o wysokiej dostępności funkcji SQL AlwaysOn baz danych przy użyciu usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: ed149398a1244ad0cb09c3e0fe128973c6caa00d
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408007"
---
# <a name="tutorial-offer-highly-available-sql-databases"></a>Samouczek: Oferowanie baz danych SQL o wysokiej dostępności

Operator usługi Azure Stack można skonfigurować maszyny wirtualne server do hostowania baz danych z programu SQL Server. SQL server hostingu po pomyślnie tworzone i zarządzane przez usługę Azure Stack, użytkowników, którzy subskrybowany przez usługi SQL services można łatwo utworzyć bazy danych SQL.

W tym samouczku pokazano, jak używać szablonu szybkiego startu usługi Azure Stack w celu utworzenia [grupy dostępności AlwaysOn programu SQL Server](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), dodaj go jako serwera hostingu SQL stosu Azure, a następnie utwórz bazę danych SQL o wysokiej dostępności.

Co dowiesz się:

> [!div class="checklist"]
> * Tworzenie grupy dostępności AlwaysOn programu SQL Server na podstawie szablonu
> * Tworzenie serwera hostingu SQL usługi Azure Stack
> * Utwórz bazę danych SQL o wysokiej dostępności

W tym samouczku dwie grupy dostępności AlwaysOn programu SQL Server VM zostanie utworzony i skonfigurowany przy użyciu dostępnych elementów portalu marketplace usługi Azure Stack. 

Przed rozpoczęciem wykonywania tych kroków w ramach tego samouczka, upewnij się, że [dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md) został pomyślnie zainstalowany i następujące elementy w witrynie marketplace usługi Azure Stack:

> [!IMPORTANT]
> Wszystkie z następujących czynności są wymagane dla szablonu szybkiego startu usługi Azure Stack, który ma być używany.

- [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) obrazu z witryny marketplace.
- SQL Server 2016 z dodatkiem SP1 lub z dodatkiem SP2 (Enterprise, Standard lub deweloper) w systemie Windows Server 2016 server obrazu. W tym samouczku [programu SQL Server 2016 z dodatkiem SP2 Enterprise w systemie Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) obrazu z witryny marketplace.
- [Rozszerzenie programu SQL Server IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) wersji 1.2.30 lub nowszej. Rozszerzenie SQL IaaS instaluje wymagane składniki, które są wymagane przez elementy programu SQL Server w portalu Marketplace, wszystkie wersje Windows. Dzięki temu ustawienia specyficzne dla SQL do skonfigurowania na maszynach wirtualnych programu SQL. Jeśli rozszerzenie nie jest zainstalowany na rynku lokalnym, inicjowania obsługi programu SQL Server zakończy się niepowodzeniem.
- [Rozszerzenie niestandardowego skryptu dla Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) wersji 1.9.1 lub nowszej. Rozszerzenie niestandardowego skryptu jest narzędziem, które może służyć do automatycznego uruchomienia zadań dostosowywania maszyny Wirtualnej po wdrożeniu.
- [Program PowerShell Desired State Configuration (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) wersji 2.76.0.0 lub nowszej. DSC to platforma zarządzania w programie Windows PowerShell, umożliwiająca wdrażanie i zarządzanie danymi konfiguracji usług oprogramowania i zarządzanie środowiskiem, w którym są uruchomione te usługi.

Aby dowiedzieć się więcej na temat dodawania elementów portalu Marketplace usługi Azure Stack, zobacz [Omówienie usługi Azure Stack w portalu Marketplace](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Tworzenie grupy dostępności AlwaysOn programu SQL Server
Wykonaj kroki w tej sekcji, aby wdrożyć grupy dostępności AlwaysOn programu SQL Server przy użyciu [szablon szybkiego startu usługi Azure Stack funkcji sql alwayson na 2016](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Ten szablon umożliwia wdrożenie dwóch wystąpień programu SQL Server Enterprise, Standard i Developer w zawsze włączonej grupy dostępności. Umożliwia on utworzenie następujących zasobów:

- Sieciowa grupa zabezpieczeń
- Sieć wirtualna
- Cztery konta magazynu (jeden dla Active Directory (AD), jeden dla programu SQL, jeden dla monitora udostępniania plików i jeden dla diagnostyki maszyny Wirtualnej)
- Cztery publiczne adresy IP (po jednym dla usługi AD, dwa dla każdej maszyny Wirtualnej SQL i jeden dla publicznego modułu równoważenia obciążenia powiązany z funkcji SQL AlwaysOn odbiornika)
- Jeden zewnętrzny moduł równoważenia obciążenia dla maszyn wirtualnych SQL z publicznym adresem IP, powiązany z odbiornikiem funkcji SQL AlwaysOn
- Jedna maszyna wirtualna (system Windows Server 2016), skonfigurowany jako kontroler domeny dla nowego lasu z jedną domeną
- Dwie maszyny wirtualne (system Windows Server 2016) skonfigurowany za pomocą programu SQL Server 2016 z dodatkiem SP1 lub SP2 Enterprise, Standard lub Developer Edition i klastra. Musi to być obrazów z witryny marketplace.
- Jedna maszyna wirtualna (system Windows Server 2016), skonfigurowany jako monitor udziału plików dla klastra
- Dostępność jeden zestaw zawierający Monitor udziału plików języków SQL i maszyn wirtualnych  

1. 
[!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Wybierz **\+** **Utwórz zasób** > **niestandardowe**, a następnie **wdrożenie szablonu**.

   ![Wdrożenie szablonu niestandardowego](media/azure-stack-tutorial-sqlrp/1.png)


3. Na **wdrożenie niestandardowe** bloku wybierz **Edytuj szablon** > **szablon szybkiego startu** , a następnie użyj listy rozwijanej dostępnych szablonów niestandardowych, aby Wybierz **funkcji sql alwayson na 2016** szablonu, kliknij przycisk **OK**, a następnie **Zapisz**.

   [![](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Wybierz szablon szybkiego startu")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. Na **wdrożenie niestandardowe** bloku wybierz **Edytuj parametry** i przejrzyj wartości domyślne. Zmodyfikuj wartości, co jest niezbędne do zapewnienia wszystkie wymagane parametry, a następnie kliknij przycisk **OK**.<br><br> Co najmniej:

    - Podaj parametry ADMINPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD i SQLAUTHPASSWORD złożone hasła.
    - Wprowadź sufiks DNS dla wyszukiwania wstecznego w małych liter parametru elementu DNSSUFFIX (**azurestack.external** dla instalacji ASDK).
    
   [![](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Edytuj parametry wdrożenia niestandardowego")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. Na **wdrożenie niestandardowe** bloku, wybierz subskrypcję i Utwórz nową grupę zasobów lub wybierz istniejącą grupę zasobów dla wdrożenia niestandardowego.<br><br> Następnie wybierz lokalizację grupy zasobów (**lokalnego** dla instalacji ASDK) a następnie kliknij przycisk **Utwórz**. Niestandardowe ustawienia wdrażania zostaną sprawdzone, a następnie rozpoczęcia wdrożenia.

    [![](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Tworzenie wdrożenia niestandardowego")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)


6. W portalu administracyjnym wybierz **grup zasobów** i następnie nazwę grupy zasobów utworzoną w ramach wdrożenia niestandardowego (**grupy zasobów** w tym przykładzie). Wyświetl stan wdrożenia, aby upewnić się, że wszystkie wdrożenia zostały ukończone pomyślnie.<br><br>Następnie przejrzyj elementy grupy zasobów, a następnie wybierz **SQLPIPsql\<nazwy grupy zasobów\>**  element adres publiczny adres IP. Zapisz publiczny adres IP i pełną nazwę FQDN IP publicznego modułu równoważenia obciążenia. Konieczne będzie podanie tego operatora usługi Azure Stack, dzięki czemu mogą utworzyć serwera hostingu SQL, korzystając z tej grupy dostępności funkcji SQL AlwaysOn.

   > [!NOTE]
   > Wdrożenie szablonu potrwa kilka godzin.

   ![Zakończono wdrożenie niestandardowe](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Włącz automatyczne wstępne wypełnianie
Po szablonie pomyślnie wdrożone i skonfigurowane grupy dostępności funkcji SQL AlwaysON, należy włączyć [automatyczne wstępne wypełnianie](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) w każdym wystąpieniu programu SQL Server w grupie dostępności. 

Po utworzeniu grupy dostępności przy użyciu automatycznego rozmieszczania programu SQL Server automatycznie tworzy repliki pomocnicze dla każdej bazy danych w grupie bez ręcznej interwencji niezbędne w celu zapewnienia wysokiej dostępności (AlwaysOn) baz danych.

Aby skonfigurować automatyczne wstępne wypełnianie dla grupy dostępności AlwaysOn, należy użyć tych poleceń SQL. Zastąp \<InstanceName\> przy użyciu podstawowego wystąpienia programu SQL Server, nazwę i < availability_group_name > nazwą grupy dostępności (AlwaysOn) zgodnie z potrzebami. 

Na podstawowe wystąpienie serwera SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Podstawowy skrypt wystąpienia SQL](./media/azure-stack-tutorial-sqlrp/sql1.png)

W dodatkowej wystąpieniach programu SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Pomocniczy skrypt wystąpienia SQL](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Konfigurowanie uwierzytelniania zawartej bazy danych
Przed dodaniem zawartej bazy danych do grupy dostępności, upewnij się, czy opcja serwer uwierzytelniania zawartej bazy danych jest ustawiona na 1 na każde wystąpienie serwera, który jest hostem repliki dostępności dla grupy dostępności. Aby uzyskać więcej informacji, zobacz [uwierzytelniania zawartej bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Aby ustawić opcję zawartej bazy danych uwierzytelniania serwera dla każdego wystąpienia programu SQL Server w grupie dostępności, należy użyć tych poleceń:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Uwierzytelnianie bazy danych zawiera zestaw](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Tworzenie serwera hostingu SQL usługi Azure Stack
Po grupy dostępności programu SQL Server AlwayOn utworzono i poprawnie skonfigurowane, Operator usługi Azure Stack, należy utworzyć serwera usługi Azure Stack SQL hostingu do udostępnienia dodatkowej pojemności dla użytkowników do tworzenia baz danych. 

Koniecznie Użyj publicznego adresu IP lub pełną nazwę FQDN dla publicznego adresu IP modułu równoważenia obciążenia programu SQL wcześniej zarejestrowane po utworzeniu grupy dostępności funkcji SQL AlwaysOn grupy zasobów (**SQLPIPsql\<nazwy grupy zasobów\>** ). Ponadto musisz znać poświadczenia uwierzytelniania umożliwiają dostęp do wystąpienia SQL w grupie dostępności AlwaysOn w programie SQL Server.

> [!NOTE]
> Ten krok należy uruchomić z portalu administratora usługi Azure Stack przez operatora usługi Azure Stack.

Z grupy dostępności funkcji SQL AlwaysOn odbiornika modułu równoważenia obciążenia — informacje logowania uwierzytelniania SQL i publiczny adres IP, Operator usługi Azure Stack można teraz [Tworzenie serwera hostingu SQL przy użyciu grupy dostępności funkcji SQL AlwaysOn](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Upewnij się również w utworzonych planów i oferuje udostępnić użytkownikom tworzenie bazy danych SQL AlwaysOn. Operator konieczne będzie dodanie **Microsoft.SqlAdapter** usługę do planu i tworzenie nowego limitu przydziału dla baz danych o wysokiej dostępności. Aby uzyskać więcej informacji na temat tworzenia planów, zobacz [omówienie planu, oferty, przydziału i subskrypcji](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> **Microsoft.SqlAdapter** usługa nie będzie można dodawać do planów do momentu [dostawcy zasobów programu SQL Server został wdrożony](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Utwórz bazę danych SQL o wysokiej dostępności
Po SQL zawsze włączone grupy dostępności zostały utworzone, skonfigurowane i dodany serwer usługi Azure Stack SQL hostingu przez operatora usługi Azure Stack użytkownika dzierżawy z subskrypcją, w tym możliwości bazy danych programu SQL Server można utworzyć pomocniczych baz danych SQL Funkcję AlwaysOn, wykonując kroki opisane w tej sekcji. 

> [!NOTE]
> Uruchom następujące kroki, za pomocą portalu użytkownika usługi Azure Stack jako użytkownik dzierżawy z subskrypcją, zapewniając możliwości programu SQL Server (usługa Microsoft.SQLAdapter).

1. 
[!INCLUDE [azs-user-portal](../../includes/azs-user-portal.md)]

2. Wybierz **\+** **Utwórz zasób** > **danych \+ magazynu**, a następnie **bazy danych SQL**.<br><br>Podaj informacje właściwości wymaganej bazy danych, w tym nazwy, sortowanie, maksymalny rozmiar i subskrypcji, grupy zasobów i lokalizacji na potrzeby wdrożenia. 

   ![Tworzenie bazy danych SQL](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Wybierz **jednostki SKU** , a następnie wybierz odpowiednią SQL hostingu serwera jednostkę SKU do użycia. W tym przykładzie został utworzony — Azure Stack Operator **Enterprise-HA** jednostki SKU, aby zapewnić wysoką dostępność dla grup dostępności funkcji SQL AlwaysOn.

   ![Wybierz jednostkę SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Wybierz **logowania** > **Utwórz nowy identyfikator logowania** a następnie wprowadź poświadczenia uwierzytelniania SQL, który ma być używany dla nowej bazy danych. Po zakończeniu kliknij przycisk **OK** i następnie **Utwórz** do rozpoczęcia procesu wdrażania bazy danych.

   ![Utwórz nazwę logowania](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Po pomyślnym zakończeniu wdrażania bazy danych SQL, przejrzyj właściwości bazy danych, aby dowiedzieć się, parametry połączenia służące do łączenia się z nową bazę danych o wysokiej dostępności. 

   ![Wyświetl parametry połączenia](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy dostępności AlwaysOn programu SQL Server na podstawie szablonu
> * Tworzenie serwera hostingu SQL usługi Azure Stack
> * Utwórz bazę danych SQL o wysokiej dostępności

Przejdź do następnego samouczka, aby dowiedzieć się, jak:
> [!div class="nextstepaction"]
> [Tworzenie baz danych MySQL o wysokiej dostępności](azure-stack-tutorial-mysql.md)