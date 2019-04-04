---
title: SQL hostowania serwerów w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jak dodać wystąpień SQL na potrzeby aprowizacji za pośrednictwem dostawcy zasobu karty bazy danych SQL.
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
ms.date: 03/26/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 1b9c7f00c8ec8408547620111634470d455334c8
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499237"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Dodawanie serwerów hostingu dla dostawcy zasobów bazy danych SQL

Możesz utworzyć bazę danych SQL Server, serwerów, na maszynie wirtualnej (VM) hosta w [usługi Azure Stack](azure-stack-poc.md), lub na maszynie Wirtualnej poza środowiskiem usługi Azure Stack, tak długo, jak dostawcy zasobów bazy danych SQL można połączyć się z wystąpieniem.

> [!NOTE]
> Dostawcy zasobów bazy danych SQL, powinny zostać utworzone w domyślnej subskrypcji dostawcy, a serwerami hostingu SQL powinny zostać utworzone w płatnych, subskrypcja użytkownika. Serwer dostawcy zasobów nie powinny służyć do baz danych użytkownika hosta.

## <a name="overview"></a>Przegląd

Przed dodaniem jest hostem serwera SQL, sprawdź następujące wymagania obowiązkowe i ogólne.

### <a name="mandatory-requirements"></a>Obowiązkowe wymagania

* Włącz uwierzytelnianie SQL w wystąpieniu programu SQL Server. Dostawcy zasobów bazy danych SQL, maszyna wirtualna nie jest przyłączony do domeny, dlatego jego można połączyć tylko do obsługi serwera przy użyciu uwierzytelniania SQL.
* Skonfiguruj adresy IP dla wystąpienia programu SQL jako publiczne, podczas instalowania w usłudze Azure Stack. Dostawcy zasobów i użytkowników, takich jak aplikacje sieci Web, komunikują się za pośrednictwem sieci przez użytkownika, dzięki czemu jest wymagana łączność z wystąpieniem programu SQL w tej sieci.

### <a name="general-requirements"></a>Wymagania ogólne

* Przydzielenie dedykowanego wystąpienia SQL do użycia przez zasób obciążeń dostawcy oraz użytkownika. Nie można użyć wystąpienia SQL, który jest używany przez innego konsumenta. To ograniczenie dotyczy także App Services.
* Konto można skonfigurować z poziomami odpowiednich uprawnień dla dostawcy zasobów (opisanych poniżej).
* Odpowiedzialność za zarządzanie wystąpienia programu SQL i ich hostów.  Na przykład dostawca zasobów nie zastosowania aktualizacji, obsługi kopii zapasowych lub obsługi poświadczeń obrotu.

### <a name="sql-server-virtual-machine-images"></a>Obrazy maszyn wirtualnych programu SQL Server

Obrazy maszyn wirtualnych SQL IaaS są dostępne za pośrednictwem funkcji zarządzania w portalu Marketplace. Te obrazy są takie same jak maszyn wirtualnych SQL, które są dostępne na platformie Azure.

Upewnij się, że zawsze pobrać najnowszą wersję **SQL IaaS rozszerzenia** przed wdrożeniem maszyny Wirtualnej SQL przy użyciu elementu portalu Marketplace. Rozszerzenie IaaS i odpowiedniego portalu ulepszeń zapewniają dodatkowe funkcje, takie jak automatyczne stosowanie poprawek i tworzenia kopii zapasowych. Aby uzyskać więcej informacji na temat tego rozszerzenia, zobacz [automatyzacji zadań zarządzania na maszynach wirtualnych platformy Azure za pomocą rozszerzenia agenta programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

> [!NOTE]
> Rozszerzenie SQL IaaS jest _wymagane_ SQL wszystkie Windows obrazów w portalu marketplace; na maszynie Wirtualnej zakończy się niepowodzeniem do wdrożenia, jeśli rozszerzenie nie został pobrany. Nie jest używana z obrazów maszyn wirtualnych SQL opartych na systemie Linux.

Istnieją inne opcje wdrażania maszyn wirtualnych SQL, w tym szablony w [galerii Szybki Start usługi Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Serwery hostingu zainstalowana na wielu węzłach usługi Azure Stack musi zostać utworzona subskrypcja użytkownika i nie domyślne dostawcy subskrypcji. Musi zostać utworzona z poziomu portalu użytkownika lub w sesji programu PowerShell, za pomocą identyfikatora logowania odpowiednie. Wszystkie serwery hostingu są płatne maszyn wirtualnych i musi mieć odpowiednie licencje programu SQL. Administrator usługi _można_ być właścicielem tej subskrypcji.

### <a name="required-privileges"></a>Wymagane uprawnienia

Użytkownik administracyjny możesz utworzyć przy użyciu niższe uprawnienia niż aktualnie SQL sysadmin. Użytkownik musi jedynie uprawnień w przypadku następujących operacji:

* Baza danych: Tworzenie, Alter, przy użyciu zawierania (dla zawsze włączonych tylko), porzucić, utworzyć kopię zapasową
* Grupy dostępności: Instrukcja ALTER, dołączenia, dodawać i usuwać bazy danych
* Logowanie: Tworzenie, wybierz, Alter, Drop, odwoływanie
* Wybierz operacje: \[wzorca\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn) sys.availability_replicas (AlwaysOn), sys.databases, \[wzorca\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[wzorca\].\[ sys\].\[ availability_groups\] (AlwaysOn) sys.master_files

### <a name="additional-security-information"></a>Informacje dodatkowe zabezpieczenia

Poniżej znajdują się wskazówki dotyczące zabezpieczeń:

* Cały magazyn usługi Azure Stack, są szyfrowane za pomocą funkcji BitLocker, więc dowolnego wystąpienia SQL w usłudze Azure Stack będą używać magazynu obiektów blob zaszyfrowany.
* Dostawcy zasobów bazy danych SQL w pełni obsługuje protokół TLS 1.2. Upewnij się, że wszelkie SQL Server, które odbywa się za pośrednictwem SQL RP jest skonfigurowany dla protokołu TLS 1.2 _tylko_ i domyślnie do tej jednostki Uzależnionej. Zobacz wszystkich obsługiwanych wersjach programu SQL Server Obsługa protokołu TLS 1.2 [Obsługa protokołu TLS 1.2 dla programu Microsoft SQL Server](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server).
* Użyj SQL Server Configuration Manager, aby ustawić **ForceEncryption** opcję, aby cała komunikacja z programem SQL server są zawsze szyfrowane. Zobacz [do skonfigurowania serwera, aby wymusić połączeń szyfrowanych](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#ConfigureServerConnections).
* Upewnij się, że każda aplikacja kliencka komunikuje się również za pośrednictwem szyfrowanego połączenia.
* Jednostki Uzależnionej jest skonfigurowana do ufania certyfikatów używanych przez wystąpienia programu SQL Server.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Zapewniają pojemność, łącząc się z autonomicznym hostowany program SQL server

Można użyć autonomicznego (inne niż-HA) serwerami SQL korzystającymi z dowolnej wersji programu SQL Server 2014 lub SQL Server 2016. Upewnij się, że masz poświadczenia dla konta z uprawnieniami administratora systemu.

Aby dodać autonomicznego serwera hostingu, który został już skonfigurowany, wykonaj następujące kroki:

1. Zaloguj się do portalu usługi Azure Stack operatora jako administrator usługi.

2. Wybierz **wszystkich usług** &gt; **zasoby administracyjne** &gt; **serwerów do hostingu SQL**.

   ![SQL Hosting Servers](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   W obszarze **serwerów do hostingu SQL**, łączysz dostawcy zasobów bazy danych SQL do wystąpienia programu SQL Server, która będzie służyć jako dostawca zasobów wewnętrznej bazy danych.

   ![Karta SQL pulpitu nawigacyjnego](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. Kliknij przycisk **Dodaj** , a następnie wprowadź szczegóły połączenia dla wystąpienia programu SQL Server na **dodawania serwera hostingu SQL** bloku.

   ![Dodaj jest hostem serwera SQL](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Opcjonalnie podaj nazwę wystąpienia, a następnie podaj numer portu, jeśli wystąpienie nie jest przypisany do domyślnego portu 1433.

   > [!NOTE]
   > Tak długo, jak wystąpieniem serwera SQL są dostępne dla użytkowników i administratorów usługi Azure Resource Manager, mogą być umieszczone pod kontrolą dostawcy zasobów. Wystąpienie SQL __musi__ przydzielenia wyłącznie na potrzeby dostawcy zasobów.

4. Podczas dodawania serwerów, należy przypisać je do istniejących jednostek SKU lub tworzenie nowej jednostki SKU. W obszarze **dodawania serwera hostingu**, wybierz opcję **jednostki SKU**.

   * Aby korzystać z istniejących jednostek SKU, wybierz dostępną jednostką SKU, a następnie wybierz **Utwórz**.
   * Aby utworzyć jednostkę SKU, wybierz **+ Tworzenie nowej jednostki SKU**. W **Tworzenie jednostki SKU**, wprowadź wymagane informacje, a następnie wybierz **OK**.

     ![Tworzenie jednostki SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Zapewnianie wysokiej dostępności przy użyciu programu SQL zawsze włączone grupy dostępności

Konfigurowanie wystąpienia SQL Always On wymaga dodatkowych kroków, a trzy maszyny wirtualne (lub maszyn fizycznych). W tym artykule przyjęto założenie, iż już pełny opis zawsze włączonych grup dostępności. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Wprowadzenie do programu SQL Server zawsze włączonych grup dostępności na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Zawsze włączone grupy dostępności (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Dostawcy zasobów bazy danych SQL karty _tylko_ obsługuje SQL 2016 SP1 Enterprise lub później wystąpień dla zawsze włączonych grup dostępności. Ta konfiguracja karty wymaga nowych funkcji programu SQL, takich jak automatyczne wstępne wypełnianie.

### <a name="automatic-seeding"></a>Automatyczne wstępne wypełnianie

Należy włączyć [automatyczne wstępne wypełnianie](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) dla każdej grupy dostępności dla każdego wystąpienia programu SQL Server.

Aby włączyć automatyczne wstępne wypełnianie we wszystkich wystąpieniach, edycji, a następnie uruchom poniższe polecenie SQL, w replice podstawowej dla każdego wystąpienia dodatkowej:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Grupa dostępności muszą być ujęte w nawiasy kwadratowe.

Dodatkowych węzłów uruchom następujące polecenie SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Konfigurowanie uwierzytelniania zawartej bazy danych

Przed dodaniem zawartej bazy danych do grupy dostępności, upewnij się, czy opcja serwer uwierzytelniania zawartej bazy danych jest ustawiona na 1 na każde wystąpienie serwera, który jest hostem repliki dostępności dla grupy dostępności. Aby uzyskać więcej informacji, zobacz [zawarte uwierzytelnianie bazy danych opcji konfiguracji serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Aby ustawić opcję zawartej bazy danych uwierzytelniania serwera dla każdego wystąpienia, należy użyć tych poleceń:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Aby dodać SQL Always On serwerów hosta

1. Zaloguj się do portalu usługi Azure Stack administratora jako administratora usługi.

2. Wybierz **Przeglądaj** &gt; **zasoby administracyjne** &gt; **hostowania serwerów SQL** &gt; **+ Dodaj**.

   W obszarze **serwerów do hostingu SQL**, łączysz dostawcy zasobów programu SQL Server do rzeczywistych wystąpień programu SQL Server, które służą jako dostawca zasobów wewnętrznej bazy danych.

3. Wypełnij formularz z szczegóły połączenia dla wystąpienia programu SQL Server. Należy upewnić się, że adres FQDN zawsze odbiornik (i opcjonalnie port numer i nazwę wystąpienia). Podaj informacje dla konta, które zostały skonfigurowane z uprawnieniami administratora systemu.

4. Zaznacz pole zawsze włączonej grupy dostępności, aby włączyć obsługę wystąpień zawsze włączonej grupy dostępności SQL.

   ![Włącz zawsze](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Dodaj wystąpienie programu SQL Always On do jednostki SKU.

   > [!IMPORTANT]
   > Nie można mieszać serwerów autonomicznych z wystąpieniami Always On w tej samej jednostki SKU. Podjęto próbę mieszać typy po dodaniu pierwszego wyników z serwera hostingu wystąpienie błędu.

## <a name="sku-notes"></a>Informacje o jednostce SKU
Użyj nazwy jednostek SKU, która zawiera opis możliwości serwerów w ramach jednostki SKU, takich jak pojemność i wydajność. Nazwa służy jako pomocy, aby ułatwić użytkownikom wdrożyć swoje bazy danych do odpowiedniej jednostki SKU. Na przykład służy nazwy jednostki SKU do odróżnienia oferty usługi przez następujące cechy:
  
* Wysoka pojemność
* o wysokiej wydajności
* Wysoka dostępność

Najlepszym rozwiązaniem jest serwerami hostingu w jednostce SKU powinny mieć takie same charakterystyki zasobów i wydajności.

Nie można przypisać jednostki SKU do określonych użytkowników lub grup.

Jednostki SKU może potrwać do godziny mają być wyświetlane w portalu. Użytkownicy nie mogą tworzyć bazy danych, dopóki nie jest w pełni utworzona jednostka SKU.

Aby edytować jednostki SKU, przejdź do **wszystkich usług** > **SQL Adapter** > **jednostki SKU**. Wybierz jednostkę SKU, aby zmodyfikować, wprowadź niezbędne zmiany i kliknij przycisk **Zapisz** można zapisać zmian. 

Aby usunąć jednostkę SKU, która nie jest już potrzebny, przejdź do **wszystkich usług** > **SQL Adapter** > **jednostki SKU**. Kliknij prawym przyciskiem myszy nazwę jednostki SKU, a następnie wybierz pozycję **Usuń** go usunąć.

> [!IMPORTANT]
> Może potrwać do godziny nowych jednostek SKU mają być dostępne w portalu użytkowników.

## <a name="make-sql-databases-available-to-users"></a>Udostępnić użytkownikom baz danych SQL

Utwórz plany i oferty, aby udostępnić użytkownikom baz danych SQL. Dodaj **Microsoft.SqlAdapter** usługi zgodnie z planem i tworzenie nowego limitu przydziału.

> [!IMPORTANT]
> Może upłynąć do dwóch godzin nowe przydziały, które mają być dostępne w portalu użytkowników, lub przed zmienionych limitu przydziału jest wymuszany.

## <a name="next-steps"></a>Kolejne kroki

[Dodawanie baz danych](azure-stack-sql-resource-provider-databases.md)
