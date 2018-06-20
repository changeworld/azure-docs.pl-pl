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
ms.date: 06/18/2018
ms.author: jeffgilb
ms.openlocfilehash: 183d9479ae18e557b00d0867cad79600145da7bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265232"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Dodaj serwery hostingu dla dostawcy zasobów SQL

Wystąpienie serwera SQL na maszynie wirtualnej (VM) może obsługiwać w [stosu Azure](azure-stack-poc.md), lub na maszynie Wirtualnej poza środowiskiem Azure stosu, tak długo, jak dostawca zasobów SQL można połączyć się z wystąpieniem.

## <a name="overview"></a>Przegląd

Ogólne wymagania dotyczące serwerów hostingu SQL są:

* Wystąpienie programu SQL muszą być przeznaczone wyłącznie do użytku przez zasób obciążeń dostawcy i użytkownika. Nie można użyć wystąpienia SQL, który jest używany przez innego użytkownika. To ograniczenie ma również zastosowanie do usług aplikacji.
* Dostawca zasobów maszyny Wirtualnej SQL nie jest przyłączony do domeny i można połączyć wyłącznie przy użyciu uwierzytelniania programu SQL.
* Należy skonfigurować konto z odpowiednimi uprawnieniami do użytku przez dostawcę zasobów.
* Dostawcy zasobów i użytkowników, takich jak aplikacje sieci Web, należy używać sieci użytkownika, dlatego jest wymagana łączność z wystąpieniem serwera SQL w tej sieci. To wymaganie zwykle oznacza, że adres IP dla swoich wystąpień SQL musi być w sieci publicznej.
* Zarządzanie wystąpień programu SQL i ich hostów zależy od użytkownika. Na przykład dostawcę zasobów nie stosować aktualizacje, obsługi kopii zapasowych lub obsługi poświadczeń obrotu.
* Można użyć jednostki SKU obsługujących różnych klas SQL możliwości, takie jak wydajność i wysokiej dostępności przy użyciu funkcji AlwaysOn.

### <a name="sql-server-virtual-machine-images"></a>Obrazy maszyny wirtualnej programu SQL Server

Obrazy maszyny wirtualnej SQL IaaS są dostępne za pośrednictwem funkcji zarządzania Marketplace. Te obrazy są takie same jak SQL maszyn wirtualnych, które są dostępne w systemie Azure.

Upewnij się, że zawsze Pobierz najnowszą wersję **rozszerzenia IaaS SQL** przed wdrożeniem maszyny Wirtualnej przy użyciu elementu portalu Marketplace.  Rozszerzenia IaaS i odpowiednie portalu ulepszeń oferują dodatkowe funkcje, takie jak automatyczne stosowanie poprawek i kopii zapasowej.

Istnieją inne opcje wdrażania maszyn wirtualnych SQL, w tym szablony w [galerii Szybki Start Azure stosu](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Wszystkie serwery hostingu zainstalowane na stosie Azure wielowęzłowego musi zostać utworzona z subskrypcji użytkownika. Nie można ich utworzyć z subskrypcji domyślny dostawca. Musi zostać utworzony za pomocą portalu użytkownika lub w sesji programu PowerShell z odpowiednią nazwą logowania. Wszystkie serwery hostingu rozliczeniowy maszyn wirtualnych i musi mieć odpowiednie licencje SQL. Administrator usługi _można_ właściciela tej subskrypcji.

### <a name="required-privileges"></a>Wymagane uprawnienia

Można utworzyć użytkownika administracyjnego z niższe uprawnienia który SQL sysadmin. Użytkownik musi tylko uprawnienia dla następujących czynności:

- Baza danych: Utwórz, Alter, z zawierania (dla zawsze włączony tylko), porzucić, kopia zapasowa
- Grupy dostępności: Alter, Dołącz do dodawania i usuwania bazy danych
- Logowania: Tworzenie, wybierz, Alter, Drop, odwoływanie
- Wybierz operacje: \[wzorca\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), sys.availability_replicas (AlwaysOn), sys.databases, \[wzorca\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[wzorca\].\[ sys\].\[ availability_groups\] (AlwaysOn), widoku sys.master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Zapewniają pojemność, łącząc się z autonomicznego hostowany program SQL server

Można użyć autonomicznej (z systemem innym niż — HA) serwerów SQL przy użyciu dowolnej wersji programu SQL Server 2014 lub SQL Server 2016. Upewnij się, że masz poświadczenia dla konta z uprawnieniami administratora systemu.

Aby dodać hostingu serwer autonomiczny, który został już skonfigurowany, wykonaj następujące kroki:

1. Zaloguj się do portalu Azure stosu operatora jako administratora usługi.

2. Wybierz **Przeglądaj** &gt; **zasobów administracyjnych** &gt; **SQL serwerów hosta**.

   ![Hosting serwerów SQL](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   W obszarze **Hosting serwerów SQL**, możesz połączyć dostawcy zasobów SQL do wystąpień programu SQL Server, które służą jako wewnętrznej bazy danych dostawcy zasobów.

   ![Pulpit nawigacyjny karty SQL](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Wypełnienie formularza Szczegóły połączenia z wystąpieniem programu SQL Server.

   ![Dodaj hostem serwera SQL](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Można opcjonalnie dołączyć nazwę wystąpienia i określ numer portu, jeśli wystąpienie nie jest przypisana do domyślnego portu 1433.

   > [!NOTE]
   > Tak długo, jak przez użytkownika, a administrator usługi Azure Resource Manager można uzyskać dostępu do wystąpienia serwera SQL, mogą być umieszczone pod kontrolą dostawcy zasobów. Wystąpienie programu SQL __musi__ można przydzielić wyłącznie do dostawcy zasobów.

4. W miarę dodawania serwerów, należy je przypisać do nowej lub istniejącej jednostki SKU rozróżnianie oferty usług. Na przykład można mieć wystąpienia programu SQL Enterprise, które zawiera:
  
   - wydajność bazy danych
   - Automatyczne kopie zapasowe
   - Zarezerwuj serwerów o wysokiej wydajności dla poszczególnych działów

   Serwerami hostingu w jednostce SKU powinny mieć takie same możliwości. **Nazwa** powinien odzwierciedlać właściwości jednostki SKU, dlatego użytkownicy będą mogli wdrażać swoje bazy danych do odpowiednich jednostki SKU.

   > [!IMPORTANT]
   > Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** lub **warstwy** nazwy podczas tworzenia jednostki SKU dla dostawców zasobów SQL i MySQL.

   Na przykład:

   ![Utwórz jednostki SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

   >[!NOTE]
   > Jednostki SKU może potrwać do godziny mają być wyświetlane w portalu. Użytkownicy nie można utworzyć bazy danych, dopóki nie zostanie całkowicie utworzona jednostka SKU.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Zapewnianie wysokiej dostępności przy użyciu SQL zawsze włączone grupy dostępności

Konfigurowanie SQL zawsze włączone wystąpienia wymaga wykonania dodatkowych kroków i wymaga co najmniej trzech maszyn wirtualnych (lub maszyn fizycznych.) W tym artykule przyjęto założenie, już pełny opis zawsze włączonych grup dostępności. Aby uzyskać więcej informacji, zobacz:

* [Wprowadzenie do programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Zawsze włączone grupy dostępności (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Dostawca zasobów SQL karty _tylko_ obsługuje Enterprise programu SQL 2016 z dodatkiem SP1 lub później wystąpień dla zawsze włączony. Nowe funkcje SQL, takie jak automatyczne wstępne wypełnianie w ramach tej konfiguracji karty.

Oprócz powyższych listę wymagań należy włączyć [automatyczne wstępne wypełnianie](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) w każdej grupie dostępności dla każdego wystąpienia programu SQL Server.

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

   W obszarze **Hosting serwerów SQL** możesz połączyć dostawcy zasobów programu SQL Server do rzeczywistego wystąpień programu SQL Server, które służyć jako wewnętrznej bazy danych dostawcy zasobów.

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
