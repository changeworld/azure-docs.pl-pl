---
title: Hosting serwerów na stosie Azure SQL | Dokumentacja firmy Microsoft
description: Jak dodać wystąpienia programu SQL dla inicjowania obsługi administracyjnej za pośrednictwem dostawcy zasobów karty SQL.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeffgilb
ms.openlocfilehash: af820f90c5d8822dbdaa768b16360d534fd47828
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060046"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Dodaj serwery hostingu dla dostawcy zasobów SQL

Wystąpienie serwera SQL na maszynie wirtualnej (VM) może obsługiwać w [stosu Azure](azure-stack-poc.md), lub na maszynie Wirtualnej poza środowiskiem Azure stosu, tak długo, jak dostawca zasobów SQL można połączyć się z wystąpieniem.

## <a name="overview"></a>Przegląd

Przed dodaniem hostem serwera SQL, przejrzyj następujące obowiązkowe i ogólne wymagania.

**Obowiązkowe wymagania**

* Włącz uwierzytelnianie SQL w wystąpieniu programu SQL Server. Ponieważ dostawca zasobów SQL maszyny Wirtualnej nie jest przyłączony do domeny, można go łączyć się hostingu serwera przy użyciu uwierzytelniania programu SQL.
* Skonfiguruj adresy IP dla wystąpień programu SQL jako publiczne. Dostawca zasobów i użytkowników, takich jak aplikacje sieci Web komunikują się za pośrednictwem sieci przez użytkownika tak łączność do wystąpienia serwera SQL w tej sieci jest wymagana.

**Wymagania ogólne**

* Przeznaczyć wystąpienia SQL do użycia przez zasób obciążeń dostawcy i użytkownika. Nie można użyć wystąpienia SQL, który jest używany przez innego użytkownika. To ograniczenie ma również zastosowanie do usług aplikacji.
* Skonfiguruj konto z poziomami uprawnień właściwe dla dostawcy zasobów.
* Jesteś są odpowiedzialni za zarządzanie wystąpień programu SQL i ich hostów.  Na przykład dostawcę zasobów nie stosować aktualizacje, obsługi kopii zapasowych lub obsługi poświadczeń obrotu.

### <a name="sql-server-virtual-machine-images"></a>Obrazy maszyny wirtualnej programu SQL Server

Obrazy maszyny wirtualnej SQL IaaS są dostępne za pośrednictwem funkcji zarządzania Marketplace. Te obrazy są takie same jak SQL maszyn wirtualnych, które są dostępne w systemie Azure.

Upewnij się, że zawsze Pobierz najnowszą wersję **rozszerzenia IaaS SQL** przed wdrożeniem maszyny Wirtualnej SQL przy użyciu elementu portalu Marketplace. Rozszerzenia IaaS i odpowiednie portalu ulepszeń oferują dodatkowe funkcje, takie jak automatyczne stosowanie poprawek i kopii zapasowej. Aby uzyskać więcej informacji na temat tego rozszerzenia, zobacz [automatyzacji zadań zarządzania na maszynach wirtualnych Azure z rozszerzeniem agenta serwera SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Istnieją inne opcje wdrażania maszyn wirtualnych SQL, w tym szablony w [galerii Szybki Start Azure stosu](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Wszystkie serwery hostingu zainstalowane na stosie Azure wielowęzłowego musi zostać utworzona z subskrypcji użytkownika. Nie można ich utworzyć z subskrypcji domyślny dostawca. Musi zostać utworzony za pomocą portalu użytkownika lub w sesji programu PowerShell z odpowiednią nazwą logowania. Wszystkie serwery hostingu rozliczeniowy maszyn wirtualnych i musi mieć odpowiednie licencje SQL. Administrator usługi _można_ właściciela tej subskrypcji.

### <a name="required-privileges"></a>Wymagane uprawnienia

Można utworzyć użytkownika administracyjnego z uprawnieniami niższe niż SQL sysadmin. Użytkownik musi tylko uprawnienia dla następujących czynności:

* Baza danych: Utwórz, Alter, z zawierania (dla zawsze włączony tylko), porzucić, kopia zapasowa
* Grupy dostępności: Alter, Dołącz do dodawania i usuwania bazy danych
* Logowania: Tworzenie, wybierz, Alter, Drop, odwoływanie
* Wybierz operacje: \[wzorca\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), sys.availability_replicas (AlwaysOn), sys.databases, \[wzorca\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[wzorca\].\[ sys\].\[ availability_groups\] (AlwaysOn), widoku sys.master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Zapewniają pojemność, łącząc się z autonomicznego hostowany program SQL server

Można użyć autonomicznej (z systemem innym niż — HA) serwerów SQL przy użyciu dowolnej wersji programu SQL Server 2014 lub SQL Server 2016. Upewnij się, że masz poświadczenia dla konta z uprawnieniami administratora systemu.

Aby dodać hostingu serwer autonomiczny, który został już skonfigurowany, wykonaj następujące kroki:

1. Zaloguj się do portalu Azure stosu operatora jako administratora usługi.

2. Wybierz **Przeglądaj** &gt; **zasobów administracyjnych** &gt; **SQL serwerów hosta**.

   ![Hosting serwerów SQL](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   W obszarze **Hosting serwerów SQL**, możesz połączyć dostawcy zasobów SQL do wystąpień programu SQL Server, które służą jako wewnętrznej bazy danych dostawcy zasobów.

   ![Pulpit nawigacyjny karty SQL](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Na **dodawania serwera hostingu SQL**, podaj szczegóły połączenia dla wystąpienia programu SQL Server.

   ![Dodaj hostem serwera SQL](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Opcjonalnie podaj nazwę wystąpienia, a następnie określ numer portu, jeśli wystąpienie nie jest przypisana do domyślnego portu 1433.

   > [!NOTE]
   > Tak długo, jak przez użytkownika, a administrator usługi Azure Resource Manager można uzyskać dostępu do wystąpienia serwera SQL, mogą być umieszczone pod kontrolą dostawcy zasobów. Wystąpienie programu SQL __musi__ można przydzielić wyłącznie do dostawcy zasobów.

4. W miarę dodawania serwerów, należy przypisać je do istniejącej jednostki SKU albo utworzyć nowe jednostki SKU. W obszarze **dodawania serwera hostingu**, wybierz pozycję **jednostki SKU**.

   * Aby użyć istniejącej jednostki SKU, wybierz dostępne jednostki SKU, a następnie wybierz **Utwórz**.
   * Aby utworzyć jednostki SKU, wybierz **+ Utwórz nowe jednostki SKU**. W **utworzyć jednostki SKU**, wprowadź wymagane informacje, a następnie wybierz **OK**.

     > [!IMPORTANT]
     > Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **nazwa** pola. Przykładowych można używać w następujących Przechwytywanie ekranu, aby wprowadzić wartości **rodziny**, **warstwy**, i **wersji** pola.

     ![Utwórz jednostki SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

      Jednostki SKU może potrwać do godziny mają być wyświetlane w portalu. Użytkownicy nie można utworzyć bazy danych, dopóki nie zostanie całkowicie utworzona jednostka SKU.

### <a name="sku-notes"></a>Informacje o wersji

Jednostki SKU służy do rozróżnienia oferty usług. Na przykład można mieć wystąpienia programu SQL Enterprise, które ma następującą charakterystykę:
  
* Wysoka wydajność
* wysokiej wydajności
* Wysoka dostępność

Można utworzyć jednostki SKU dla poprzedniego przykładu, ograniczanie dostępu do określonych grup, które wymagają wysokiej wydajności bazy danych.

>[!TIP]
>Użyj nazwy jednostki SKU, która odzwierciedla opisano możliwości serwerów w jednostce SKU, takich jak pojemność i wydajność. Nazwa służy jako pomoc, aby ułatwić użytkownikom wdrażania ich baz danych na odpowiednie jednostki SKU.

Najlepszym rozwiązaniem serwerami hostingu w jednostce SKU powinny mieć te same właściwości zasobów i wydajności.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Zapewnianie wysokiej dostępności przy użyciu SQL zawsze włączone grupy dostępności

Konfigurowanie SQL zawsze włączone wystąpienia wymaga wykonania dodatkowych kroków i wymaga trzech maszyn wirtualnych (lub maszyn fizycznych.) W tym artykule przyjęto założenie, już pełny opis zawsze włączonych grup dostępności. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Wprowadzenie do programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Zawsze włączone grupy dostępności (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Dostawca zasobów SQL karty _tylko_ obsługuje Enterprise programu SQL 2016 z dodatkiem SP1 lub później wystąpień dla zawsze włączony. Nowe funkcje SQL, takie jak automatyczne wstępne wypełnianie w ramach tej konfiguracji karty.

Ponadto należy włączyć [automatyczne wstępne wypełnianie](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) w każdej grupie dostępności dla każdego wystąpienia programu SQL Server.

Aby włączyć automatyczne wstępne wypełnianie we wszystkich wystąpieniach, edytować, a następnie uruchom poniższe polecenie SQL dla poszczególnych wystąpień:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

W wystąpieniach dodatkowej edytować, a następnie uruchom poniższe polecenie SQL dla każdego wystąpienia:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Aby dodać SQL zawsze na serwerów hosta

1. Zaloguj się do portalu administracyjnego platformy Azure stosu jako administratora usługi.

2. Wybierz **Przeglądaj** &gt; **zasobów administracyjnych** &gt; **SQL serwerów hosta** &gt; **+ Dodaj**.

   W obszarze **Hosting serwerów SQL**, możesz połączyć dostawcy zasobów programu SQL Server do rzeczywistego wystąpień programu SQL Server, które służyć jako wewnętrznej bazy danych dostawcy zasobów.

3. Wypełnij formularz Szczegóły połączenia dla wystąpienia programu SQL Server. Upewnij się, że używasz nazwę FQDN adresu zawsze na odbiornika (i numer portu opcjonalne.) Podaj informacje dotyczące konta, które są skonfigurowane z uprawnieniami administratora systemu.

4. Zaznacz pole zawsze włączonej grupy dostępności, aby włączyć obsługę wystąpienia SQL zawsze włączonej grupy dostępności.

   ![Włącz zawsze](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Dodaj wystąpienie SQL AlwaysOn do jednostki SKU.

   > [!IMPORTANT]
   > Nie można mieszać serwerów autonomicznych z wystąpieniami zawsze włączone w tej samej jednostki SKU. Próba mieszać typów po dodaniu pierwszego hostingu wyników z serwera błąd.

## <a name="make-the-sql-databases-available-to-users"></a>Udostępnienie użytkownikom bazy danych SQL

Tworzenie planów i oferty, aby udostępnić baz danych SQL dla użytkowników. Dodaj **Microsoft.SqlAdapter** usługi do planu i Dodaj domyślny limit przydziału lub Utwórz nowy przydział.

![Tworzenie planów i ofert do dołączenia bazy danych](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>Kolejne kroki

[Dodawanie baz danych](azure-stack-sql-resource-provider-databases.md)
