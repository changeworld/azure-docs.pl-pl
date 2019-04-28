---
title: Konfigurowanie Micro fokus CICS BankDemo Micro fokus Enterprise Developer 4.0 na maszynach wirtualnych platformy Azure
description: Uruchom aplikację BankDemo fokus Micro w usłudze Azure Virtual Machines (VMs) i Dowiedz się, jak używać Micro fokus Enterprise Server i Enterprise Developer.
author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: be94cf0367f93f14249239fce5e09c8635a01136
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125478"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Konfigurowanie Micro fokus CICS BankDemo Micro fokus Enterprise Developer 4.0 na platformie Azure

Po skonfigurowaniu Micro fokus Enterprise Server 4.0 i Enterprise Developer 4.0 na platformie Azure, możesz przetestować wdrożeń IBM z/OS obciążeń. W tym artykule pokazano, jak skonfigurować BankDemo CICS, przykładowej aplikacji, która jest dostarczany z Enterprise Developer.

CICs oznacza System kontroli informacje klienta, platforma transakcji używanych przez wiele aplikacji online mainframe. Aplikacja BankDemo nadaje się doskonale do nauki, jak działają Enterprise Server i Enterprise Developer i jak zarządzać i wdrożyć aplikację rzeczywiste wraz z terminale zielony ekran.

## <a name="prerequisites"></a>Wymagania wstępne

- Maszyna wirtualna o [Enterprise Developer](set-up-micro-focus-azure.md). Należy pamiętać o tym, czy Enterprise Developer ma pełne wystąpienie Enterprise Server go do celów tworzenia i testowania. To jest wystąpienie serwera przedsiębiorstwa używany udział w pokazie.

- [Wersja programu SQL Server Express 2017](https://www.microsoft.com/sql-server/sql-server-editions-express). Pobierz i zainstaluj go na maszynie Wirtualnej Developer Enterprise. Enterprise Server wymaga bazy danych zarządzania CICS regionów, a aplikacja BankDemo również używa bazy danych SQL Server o nazwie BANKDEMO. Ten pokaz przyjęto założenie, że używasz programu SQL Server Express dla obu baz danych. Podczas instalacji, wybierz opcję instalacji podstawowej.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). Program SSMS jest używany do zarządzania bazami danych i uruchamiania skryptu T-SQL. Pobierz i zainstaluj go na maszynie Wirtualnej Developer Enterprise.

- [Program Visual Studio 2017](https://azure.microsoft.com/downloads/) za pomocą najnowszego dodatku service pack lub [programu Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), który można pobrać bezpłatnie.

- Rumba pulpit lub inną 3270 emulatora.

## <a name="configure-the-windows-environment"></a>Konfigurowanie środowiska Windows

Po zainstalowaniu Enterprise Developer 4.0 na maszynie Wirtualnej, należy skonfigurować wystąpienie serwera przedsiębiorstwa, który jest dostarczany z nim. Aby to zrobić, należy zainstalować kilka dodatkowych funkcji Windows w następujący sposób.

1. Użyj protokołu RDP do logowania się do przedsiębiorstwa Server 4.0 utworzonej maszyny Wirtualnej.

2. Kliknij przycisk **wyszukiwania** ikona obok pozycji **Start** przycisk i typ **funkcji Windows**. Zostanie otwarte okno Menedżera serwera Dodaj role i funkcje kreatora.

3. Wybierz **roli Serwer sieci Web (IIS)**, a następnie sprawdź następujące czynności:

    - Narzędzia zarządzania siecią Web
    - Zgodność z narzędziami zarządzania usług IIS 6 (Zaznacz wszystkie dostępne funkcje)
    - Konsola zarządzania usługami IIS
    - Narzędzia i skrypty zarządzania usługami IIS
    - Usługa zarządzania usługami IIS

4. Wybierz **usługi World Wide Web**i sprawdź następujące:

     Funkcje tworzenia aplikacji:
    - Rozszerzalność architektury .NET
    - ASP.NET
    - Wspólne funkcje HTTP: Dodaj wszystkie dostępne funkcje
    - Kondycja i Diagnostyka: Dodaj wszystkie dostępne funkcje
    - Zabezpieczenia:
        - Uwierzytelnianie podstawowe
        - Uwierzytelnianie systemu Windows

5. Wybierz **usługi Windows Process Activation Service** i wszystkie jego elementy podrzędne.

6. Aby uzyskać **funkcji**, sprawdź **programu Microsoft .NET framework 3.5.1**i sprawdź następujące:

    - Windows Communication Foundation HTTP aktywacji
    - Aktywacja bez HTTP programu Windows Communication Foundation

7. Aby uzyskać **funkcji**, sprawdź **programu Microsoft .NET framework 4.6**i sprawdź następujące:

   - Aktywacja potoku nazwanego
   - Aktywacja TCP
   - Udostępnianie portów TCP

     ![Kreatora dodawania ról i funkcji: Usługi ról](media/01-demo-roles.png)

8. Po wybraniu wszystkich opcji kliknij przycisk **dalej** do zainstalowania.

9. Po funkcji Windows przejdź do **Panelu sterowania \> System i zabezpieczenia \> narzędzia administracyjne**i wybierz **usług**. Przewiń w dół i upewnij się, że następujące usługi są uruchomione i ustaw **automatyczne**:

    - **NetTcpPortSharing**
    - **Adapter odbiornika Net.Pipe**
    - **Karta NET.TCP odbiornika**

10. Aby skonfigurować obsługę usług IIS i WAS, z menu zlokalizuj **wiersz polecenia dla deweloperów Enterprise Micro fokus (64-bitowy)** i Uruchom jako **administratora**.

11. Typ **wassetup – i** i naciśnij klawisz **Enter**.

12. Po uruchomieniu skryptu, możesz zamknąć okno.

## <a name="configure-the-local-system-account-for-sql-server"></a>Skonfiguruj lokalne konto systemowe dla programu SQL Server

Niektóre procesy Enterprise Server muszą być w stanie zalogować się do programu SQL Server i tworzyć bazy danych i innych obiektów. Te procesy używają konta systemu lokalnego, więc należy przyznać uprawnienia administratora systemu do tego konta.

1. Uruchom **SSMS** i kliknij przycisk **Connect** do łączenia się z lokalnym serwerem SQLEXPRESS przy użyciu uwierzytelniania Windows. Powinny być dostępne w **nazwy serwera** listy.

2. Po lewej stronie, rozwiń węzeł **zabezpieczeń** i wybierz polecenie **logowania**.

3. Wybierz **urzędu NT\\systemu** i wybierz **właściwości**.

4. Wybierz **ról serwera** i sprawdź **sysadmin**.

     ![Okno Eksplorator obiektów programu SSMS: Właściwości identyfikatora logowania](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Tworzenie bazy danych BankDemo i wszystkich jego obiektów

1. Otwórz **Eksplorator Windows** i przejdź do **C:\\użytkowników\\publicznych\\dokumenty\\Micro Focus\\Enterprise Developer\\ Przykłady\\Mainframe\\CICS\\DotNet\\BankDemo\\SQL**.

2. Skopiuj zawartość **BankDemoCreateAll.SQL** pliku do Schowka.

3. Otwórz **SSMS**. Po prawej stronie, kliknij przycisk **serwera** i wybierz **nowe zapytanie**.

4. Wklej zawartość Schowka do **nowe zapytanie** pole.

5. Wykonaj instrukcję SQL, klikając **Execute** z **polecenia** kartę powyżej zapytania.

Zapytanie powinno być uruchomione z bez błędów. Po zakończeniu będziesz mieć przykładowej bazy danych dla aplikacji BankDemo.

![SQLQuery1.sql output](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Sprawdź, czy zostały utworzone tabele bazy danych i obiektów

1. Kliknij prawym przyciskiem myszy **BANKDEMO** bazy danych, a następnie wybierz pozycję **Odśwież**.

2. Rozwiń **bazy danych** i wybierz **tabel**. Powinien zostać wyświetlony podobny do poniższego.

     ![Tabela BANKDEMO rozwinięty w Eksploratorze obiektów](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Tworzenie aplikacji w Enterprise Developer

1. Otwórz program Visual Studio i zaloguj się.

2. W obszarze **pliku** opcji menu, wybierz opcję **Otwórz projekt/rozwiązanie**, przejdź do **C:\\użytkowników\\publicznych\\dokumenty\\Micro Fokus\\Enterprise Developer\\przykłady\\Mainframe\\CICS\\DotNet\\BankDemo**i wybierz **sln**pliku.

3. Zająć trochę czasu, aby zbadać obiekty. Programy COBOL są wyświetlane w Eksploratorze rozwiązań przy użyciu rozszerzenia CBL, a także kod źródłowy języka (CPY) i JCL.

4. Kliknij prawym przyciskiem myszy **BankDemo2** projektu i wybierz pozycję **Ustaw jako projekt startowy**.

    > [!NOTE]
    > Projekt BankDemo korzysta z HCOSS (Host opcję zgodności dla programu SQL Server), który nie jest używany dla tej wersji demonstracyjnej.

5. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **BankDemo2** projektu i wybierz pozycję **kompilacji**.

    > [!NOTE]
    > Kompilowanie na poziomie rozwiązania powoduje błędy, ponieważ nie skonfigurowano HCOSS.

6. W przypadku kompilowania projektu sprawdzić **dane wyjściowe** okna. Powinno to wyglądać podobnie jak na poniższej ilustracji.

     ![Okno danych wyjściowych przedstawiający pomyślnej kompilacji](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Wdrażanie aplikacji BankDemo do bazy danych w regionach

1. Otwórz wiersz polecenia programisty Enterprise (64-bitowy) jako Administrator.

2. Przejdź do **publicznych %\\dokumenty\\fokus Micro\\Enterprise Developer\\przykłady\\Mainframe\\CICS\\DotNet\\ BankDemo**.

3. W wierszu polecenia wykonaj **bankdemodbdeploy** i określić parametr dla bazy danych do wdrożenia, na przykład:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Upewnij się, że używasz ukośnika (/) nie ukośnika odwrotnego (\\). Ten skrypt jest uruchamiany przez jakiś czas.

![Administracja: Okno Wiersz polecenia programisty dla przedsiębiorstw](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Tworzenie regionu BankDemo w administratora przedsiębiorstwa dla platformy .NET

1. Otwórz **Server Enterprise do administrowania .NET** interfejsu użytkownika.

2. Aby uruchomić przystawkę MMC z Windows **Start** menu, wybierz **Micro fokus Enterprise Developer \> konfiguracji \> Enterprise Server dla platformy .NET administratora**. (Dla systemu Windows Server, wybierz **Micro fokus Enterprise Developer \> Enterprise Server dla platformy .NET administratora**).

3. Rozwiń **regionów** kontenera w lewym okienku, a następnie kliknij prawym przyciskiem myszy **CICS**.

4. Wybierz **zdefiniować Region** do utworzenia nowego regionu CICS o nazwie **BANKDEMO**hostowanych w bazie danych (lokalny).

5. Podaj wystąpieniem serwera bazy danych, kliknij przycisk **dalej**, a następnie wprowadź nazwę regionu **BANKDEMO**.

     ![Definiowanie Region, okno dialogowe](media/07-demo-cics.png)

6. Aby wybrać region plik definicji dla bazy danych między regionami, zlokalizuj **region\_bankdemo\_db.config** w **C:\\użytkowników\\publicznych\\ Dokumenty\\Micro Focus\\Enterprise Developer\\przykłady\\Mainframe\\CICS\\DotNet\\BankDemo**.

     ![Zdefiniuj Region — nazwa regionu: BANKDEMO](media/08-demo-cics.png)

7. Kliknij przycisk **Zakończ**.

## <a name="create-xa-resource-definitions"></a>Tworzenie definicji zasobów XA

1. W okienku po lewej stronie **Enterprise Server dla platformy .NET administracji** interfejsu użytkownika, rozwiń węzeł **systemu**, a następnie **definicji zasobów XA**. To ustawienie określa, jak Region współdziała z Enterprise Server i baz danych aplikacji.

2. Kliknij prawym przyciskiem myszy **definicji zasobów XA** i wybierz **Dodaj wystąpienie serwera**.

3. W polu listy rozwijanej wybierz **wystąpienie usługi Database**. Konieczne będzie SQLEXPRESS komputera lokalnego.

4. Wybierz wystąpienie z pod **definicji zasobów XA (machinename\\sqlexpress)** kontenera i kliknij przycisk **Dodaj**.

5. Wybierz **definicji zasobu XA bazy danych** , a następnie wpisz **BANKDEMO** dla **nazwa** i **Region**.

     ![Nowy ekran definicji zasobu XA bazy danych](media/09-demo-xa.png)

6. Kliknij przycisk z wielokropkiem (**...** ) aby wywołać Kreatora parametry połączenia. Aby uzyskać **nazwy serwera**, typ **(local)\\SQLEXPRESS**. Aby uzyskać **logowania**, wybierz opcję **uwierzytelniania Windows**. Nazwa bazy danych, wpisz **BANKDEMO**

     ![Edytuj parametry połączenia ekranu](media/10-demo-string.png)

7. Testuj połączenie.

## <a name="start-the-bankdemo-region"></a>Rozpocznij BANKDEMO Region

> [!NOTE]
> Pierwszym krokiem jest ważne. Należy ustawić Region do użycia XA definicji zasobu został utworzony.

1. Przejdź do **BANDEMO CICS Region** w obszarze **kontenera regionów**, a następnie wybierz **Edytuj plik startowy Region** z **akcje** okienko. Przewiń w dół do właściwości SQL, a następnie wprowadź **bankdemo** dla **nazwy zasobów XA** , lub użyj wielokropka, aby go zaznaczyć.

2. Kliknij przycisk **Zapisz** ikonę, aby zapisać zmiany.

3. Kliknij prawym przyciskiem myszy **BANKDEMO CICS Region** w **konsoli** okienka, a następnie wybierz **Region uruchomień/zatrzymań**.

4. W dolnej części **Region uruchomień/zatrzymań** wyświetlonym w środkowym okienku wybierz **Start**. Po kilku sekundach rozpoczyna się region.

     ![Pole SQL uruchomień/zatrzymań](/media/11-demo-sql.png)

     ![Region CICS BANKDEMO — wprowadzenie ekranu](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Utwórz odbiornik

Należy utworzyć odbiornik, sesje TN3270, które uzyskiwać dostęp do aplikacji BankDemo.

1. W okienku po lewej stronie rozwiń **edytorów konfiguracji** i wybierz **odbiornika**.

2. Kliknij przycisk **Otwórz plik** ikonę i wybierz pozycję **seelistener.exe.config** pliku. Ten plik będzie można edytować i jest ładowana zawsze podczas uruchamiania serwera przedsiębiorstwa.

3. Zwróć uwagę, że dwa regiony wcześniej zdefiniowane (ESDEMO i JCLDEMO).

4. Aby utworzyć nowy Region dla BANKDEMO, kliknij prawym przyciskiem myszy **regionów**i wybierz **Dodaj Region**.

5. Wybierz **BANKDEMO Region**.

6. Dodaj kanał TN3270, klikając prawym przyciskiem myszy **BANKDEMO Region** i wybierając polecenie **Dodaj kanał**.

7. Aby uzyskać **nazwa**, wprowadź **TN3270**. Aby uzyskać **portu**, wprowadź **9024**. (Zwróć uwagę, że aplikacja ESDEMO używa portu 9230, warto użyć innego portu).

8. Aby zapisać plik, kliknij przycisk **Zapisz** ikonę lub wybierz **pliku** \> **Zapisz**.

9. Aby uruchomić odbiornik, kliknij przycisk **uruchomić odbiornika** ikonę lub wybierz **opcje** \> **uruchomić odbiornika**.

     ![Okna Edytora konfiguracji odbiornika](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Konfigurowanie Rumba dostępu do aplikacji BankDemo

Końcowe rzeczą, jaką należy wykonać to skonfigurować sesję 3270 przy użyciu Rumba, 3270 emulator. Ten krok umożliwia dostęp do aplikacji BankDemo przez odbiornik, który został utworzony.

1. Od Windows **Start** menu, uruchom Rumba pulpitu.

2. W obszarze **połączeń** elementu menu, wybierz opcję **TN3270**.

3. Kliknij przycisk **Wstaw** i typ **127.0.0.1** dla adresu IP i **9024** za pośrednictwem portu usługi zdefiniowane przez użytkownika.

4. W dolnej części okna dialogowego kliknij **Connect**. Pojawia się czarny ekran CICS.

5. Typ **bank** Aby wyświetlić ekran początkowy 3270 BankDemo aplikacji.

6. Identyfikator użytkownika, wpisz **B0001** i hasła, należy wpisać cokolwiek. Zostanie otwarty pierwszy ekran BANK20.

![Ekran powitalny wyświetlania mainframe](media/14-demo.png)
![ekran wyświetlania Mainframe - Rumba - podsystemu pokaz](media/15-demo.png)

Gratulacje! Teraz uruchamiasz CICS aplikację na platformie Azure przy użyciu Micro fokus Enterprise Server.

## <a name="next-steps"></a>Kolejne kroki

- [Uruchamianie serwera przedsiębiorstwa w kontenerach platformy Docker na platformie Azure](run-enterprise-server-container.md)
- [Migracja komputera mainframe — Portal](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Rozwiązywanie problemów](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demystifying mainframe do migracji na platformę Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
