---
title: Konfigurowanie micro focus CICS BankDemo dla micro Focus Enterprise Developer 4.0 na maszynach wirtualnych platformy Azure
description: Uruchom aplikację Micro Focus BankDemo na maszynach wirtualnych platformy Azure (VM), aby nauczyć się korzystać z programu Micro Focus Enterprise Server i enterprise developer.
author: sread
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 4491fc137c2c85e2be605f5e58fde6fd422efbbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67621332"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Konfigurowanie programu Micro Focus CICS BankDemo dla programu Micro Focus Enterprise Developer 4.0 na platformie Azure

Po skonfigurowaniu programu Micro Focus Enterprise Server 4.0 i Enterprise Developer 4.0 na platformie Azure można przetestować wdrożenia obciążeń IBM z/OS. W tym artykule pokazano, jak skonfigurować CICS BankDemo, przykładową aplikację, która jest do pochodzą z Enterprise Developer.

CiCs oznacza System Kontroli Informacji o Kliencie, platformę transakcyjną używaną przez wiele aplikacji mainframe online. Aplikacja BankDemo doskonale nadaje się do nauki działania enterprise server i enterprise developer oraz zarządzania rzeczywistą aplikacją i wdrażania jej z terminalami na zielonym ekranie.

## <a name="prerequisites"></a>Wymagania wstępne

- Maszyna wirtualna z [deweloperem korporacyjnym](set-up-micro-focus-azure.md). Należy pamiętać, że enterprise developer ma pełne wystąpienie Enterprise Server na nim do celów programistycznych i testowych. To wystąpienie jest wystąpieniem enterprise server używane dla demo.

- [SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Pobierz i zainstaluj go na maszynie Wirtualnej dla deweloperów dla przedsiębiorstw. Enterprise Server wymaga bazy danych do zarządzania regionami CICS, a aplikacja BankDemo używa również bazy danych programu SQL Server o nazwie BANKDEMO. W tym pokazie przyjęto założenie, że używasz programu SQL Server Express dla obu baz danych. Podczas instalacji wybierz instalację podstawową.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). Usługa SSMS służy do zarządzania bazami danych i uruchamiania skryptu T-SQL. Pobierz i zainstaluj go na maszynie Wirtualnej dla deweloperów dla przedsiębiorstw.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) z najnowszym dodatkiem Service Pack lub [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), które można pobrać za darmo.

- Rumba Desktop lub inny emulator 3270.

## <a name="configure-the-windows-environment"></a>Konfigurowanie środowiska systemu Windows

Po zainstalowaniu programu Enterprise Developer 4.0 na maszynie wirtualnej należy skonfigurować wystąpienie serwera Enterprise Server, które jest z nią do niej przypisane. Aby to zrobić, musisz zainstalować kilka dodatkowych funkcji systemu Windows w następujący sposób.

1. Użyj protokołu RDP, aby zalogować się do utworzonej maszyny Wirtualnej enterprise server 4.0.

2. Kliknij ikonę **Wyszukaj** obok przycisku **Start** i wpisz **polecenie Funkcje systemu Windows**. Zostanie otwarty Kreator dodawania ról i funkcji Menedżera serwera.

3. Wybierz pozycję **Rola serwera sieci Web (IIS),** a następnie sprawdź następujące opcje:

    - Narzędzia do zarządzania siecią Web
    - Zgodność zarządzania usługami IIS 6 (wybierz wszystkie dostępne funkcje)
    - Konsola zarządzania usługami IIS
    - Skrypty i narzędzia zarządzania usługami IIS
    - Usługa zarządzania usługami IIS

4. Wybierz pozycję **Usługi sieci World Wide Web**i sprawdź następujące opcje:

     Funkcje tworzenia aplikacji:
    - Rozszerzenia architektury .NET
    - ASP.NET
    - Typowe funkcje HTTP: dodaj wszystkie dostępne funkcje
    - Zdrowie i diagnostyka: Dodaj wszystkie dostępne funkcje
    - Zabezpieczeń:
        - Uwierzytelnianie podstawowe
        - Uwierzytelnianie systemu Windows

5. Wybierz **pozycję Usługa aktywacji procesów systemu Windows** i wszystkie jej dzieci.

6. W przypadku **funkcji**sprawdź program **Microsoft .NET framework 3.5.1**i sprawdź następujące opcje:

    - Aktywacja HTTP programu Windows Communication Foundation
    - Aktywacja programu Windows Communication Foundation non-HTTP

7. W obszarze **Funkcje**należy zapoznać się z programem **Microsoft .NET framework 4.6**i sprawdź następujące opcje:

   - Aktywacja nazwanego potoku
   - Aktywacja TCP
   - Udostępnianie portów TCP

     ![Kreator dodawania ról i funkcji: usługi ról](media/01-demo-roles.png)

8. Po wybraniu wszystkich opcji kliknij przycisk **Dalej,** aby zainstalować.

9. Po zakończeniu funkcji systemu Windows przejdź do pozycji **System i narzędzia administracyjne zabezpieczeń \> \> paneli**sterowania i wybierz pozycję **Usługi**. Przewiń w dół i upewnij się, że następujące usługi są uruchomione i ustawione na **Automatyczne:**

    - **Udostępnianie sieci NetTcpPortSharing**
    - **Adapter odbiornika net.pipe**
    - **Karta odbiornika net.tcp**

10. Aby skonfigurować obsługę usług IIS i WAS, z menu znajdź **wiersz polecenia dewelopera programu Micro Focus Enterprise Developer (64 bit)** i uruchom jako **administrator**.

11. Wpisz **wassetup –i** i naciśnij **klawisz Enter**.

12. Po uruchomieniu skryptu można zamknąć okno.

## <a name="configure-the-local-system-account-for-sql-server"></a>Konfigurowanie lokalnego konta systemowego dla programu SQL Server

Niektóre procesy programu Enterprise Server muszą mieć możliwość logowania się w programie SQL Server i tworzenia baz danych i innych obiektów. Procesy te używają lokalnego konta systemowego, więc należy dać sysadmin uprawnienia do tego konta.

1. Uruchom **usługę SSMS** i kliknij przycisk **Połącz,** aby połączyć się z lokalnym serwerem SQLEXPRESS Server przy użyciu uwierzytelniania systemu Windows. Powinien być dostępny na liście **Nazwa serwera.**

2. Po lewej stronie rozwiń folder **Zabezpieczenia** i wybierz pozycję **Loginy**.

3. Wybierz **nt\\system authority** i wybierz **właściwości**.

4. Wybierz **pozycję Role serwera** i sprawdź **sysadmin**.

     ![Okno Eksploratora obiektów SSMS: Właściwości logowania](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Tworzenie bazy danych BankDemo i wszystkich jej obiektów

1. Otwórz **Eksploratora Windows** i przejdź do **C:\\Użytkownicy\\Dokumenty\\\\\\publiczne Micro Focus Enterprise Developer\\Przykłady\\Mainframe\\CICS\\\\BankDemo\\SQL**.

2. Skopiuj zawartość pliku **BankDemoCreate.SQL** do schowka.

3. Otwórz **SSMs**. Po prawej stronie kliknij pozycję **Serwer** i wybierz pozycję **Nowa kwerenda**.

4. Wklej zawartość schowka do pola **Nowe zapytanie.**

5. Wykonaj sql, klikając **przycisk Wykonaj** z karty **Polecenia** nad kwerendą.

Kwerenda powinna być uruchamiana bez błędów. Po zakończeniu masz przykładową bazę danych dla aplikacji BankDemo.

![Dane wyjściowe SQLQuery1.sql](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Sprawdzanie, czy utworzono tabele i obiekty bazy danych

1. Kliknij prawym przyciskiem myszy bazę danych **BANKDEMO** i wybierz polecenie **Odśwież**.

2. Rozwiń **bazę danych** i wybierz pozycję **Tabele**. Powinieneś zobaczyć coś podobnego.

     ![Tabela BANKDEMO rozwinięta w Eksploratorze obiektów](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Tworzenie aplikacji w u dewelopera korporacyjnego

1. Otwórz program Visual Studio i zaloguj się.

2. W menu **Plik** wybierz polecenie **Otwórz projekt/rozwiązanie**, przejdź do **pozycji C:\\Users\\Public\\\\Documents\\Micro Focus Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo**i wybierz plik **sln.**

3. Poświęć trochę czasu, aby zbadać obiekty. Programy COBOL są wyświetlane w Eksploratorze rozwiązań z rozszerzeniem CBL wraz z CopyBooks (CPY) i JCL.

4. Kliknij prawym przyciskiem myszy projekt **BankDemo2** i wybierz polecenie **Ustaw jako projekt startowy**.

    > [!NOTE]
    > Projekt BankDemo korzysta z HCOSS (Opcja zgodności hosta dla programu SQL Server), który nie jest używany do tego demo.

5. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **BankDemo2** i wybierz polecenie **Zbuduj**.

    > [!NOTE]
    > Tworzenie na poziomie rozwiązania powoduje błędy, ponieważ HCOSS nie został skonfigurowany.

6. Podczas budowy projektu sprawdź **output** okna. Powinna ona teraz wyglądać podobnie jak na poniższym obrazie.

     ![Okno danych wyjściowych z pomyślną kompilacją](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Wdrażanie aplikacji BankDemo w bazie danych Region

1. Otwórz wiersz polecenia dewelopera przedsiębiorstwa (64 bit) jako administrator.

2. Przejdź do **przykładów\\\\\\%PUBLIC% Documents Micro\\Focus\\\\Enterprise Developer\\\\Mainframe CICS DotNet BankDemo**.

3. W wierszu polecenia wykonaj **bankdemodbdeploy** i dołącz parametr bazy danych do wdrożenia, na przykład:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Upewnij się, że używane jest ukośnik\\do przodu (/) a nie ukośnik do tyłu( ). Ten skrypt jest uruchamiany przez jakiś czas.

![Administracja: okno wiersza polecenia dewelopera dla przedsiębiorstw](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Tworzenie regionu BankDemo w administratorze przedsiębiorstwa dla platformy .NET

1. Otwórz interfejs użytkownika **administracji programu .NET dla serwera enterprise server.**

2. Aby uruchomić przystawkę programu MMC, z menu **Start** systemu Windows wybierz polecenie **Micro Focus Enterprise Developer \> Configuration \> Enterprise Server for .NET Admin**. (W systemie Windows Server wybierz pozycję **Micro Focus Enterprise \> Developer Enterprise Server for .NET Admin**).

3. Rozwiń kontener **Regiony** w lewym okienku, a następnie kliknij prawym przyciskiem myszy **cics**.

4. Wybierz **pozycję Zdefiniuj region,** aby utworzyć nowy region CICS o nazwie **BANKDEMO**, hostowany w (lokalnej) bazie danych.

5. Podaj wystąpienie serwera bazy danych, kliknij przycisk **Dalej**, a następnie wprowadź nazwę regionu **BANKDEMO**.

     ![Okno dialogowe Definiowanie regionu](media/07-demo-cics.png)

6. Aby wybrać plik definicji regionu dla bazy danych między regionami, znajdź **\_region bankdemo\_db.config** w języku **C:\\Users\\Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo**.

     ![Definiuj region — nazwa regionu: BANKDEMO](media/08-demo-cics.png)

7. Kliknij przycisk **Zakończ**.

## <a name="create-xa-resource-definitions"></a>Tworzenie definicji zasobów XA

1. W lewym okienku interfejsu użytkownika **programu Enterprise Server dla systemu .NET Administration** UI rozwiń węzeł **System**, a następnie **definicje zasobów XA**. To ustawienie definiuje sposób, w jaki region współdziała z serwerem Enterprise Server i bazami danych aplikacji.

2. Kliknij prawym przyciskiem myszy **definicje zasobów XA** i wybierz polecenie **Dodaj wystąpienie serwera**.

3. W polu rozwijany wybierz pozycję **Wystąpienie usługi bazy danych**. Będzie to maszyna lokalna SQLEXPRESS.

4. Wybierz wystąpienie w kontenerze **XA Resource\\Definitions (nazwa komputera sqlexpress)** i kliknij przycisk **Dodaj**.

5. Wybierz **opcję Definicja zasobu XA bazy danych,** a następnie wpisz **POLECENIE BANKDEMO** dla **nazwy** i **regionu**.

     ![Ekran Nowa definicja zasobu XA bazy danych](media/09-demo-xa.png)

6. Kliknij wielokropek (**...**), aby przywołać kreatora parametry połączenia. W przypadku **nazwy serwera**wpisz **(lokalny)\\SQLEXPRESS**. W obszarze **Logowanie**wybierz pozycję **Uwierzytelnianie systemu Windows**. W przypadku nazwy bazy danych wpisz **POLECENIE BANKDEMO**

     ![Ekran Edytuj ciąg połączenia](media/10-demo-string.png)

7. Przetestuj połączenie.

## <a name="start-the-bankdemo-region"></a>Uruchamianie regionu BANKDEMO

> [!NOTE]
> Pierwszy krok jest ważny: należy ustawić region, aby używał właśnie utworzonej definicji zasobu XA.

1. Przejdź do **regionu CICS BANDEMO** w **kontenerze Regiony**, a następnie wybierz polecenie **Edytuj plik startowy regionu** z okienka **Akcje.** Przewiń w dół do właściwości SQL i wprowadź **bankdemo** dla **nazwy zasobu XA**lub użyj wielokropka, aby go zaznaczyć.

2. Kliknij ikonę **Zapisz,** aby zapisać zmiany.

3. Kliknij prawym przyciskiem myszy **pozycję REGION CICS BANKDEMO** w okienku **Konsola,** a następnie wybierz polecenie **Region startu/zatrzymania**.

4. W dolnej części pola **Region rozpoczęcia/zatrzymania,** które pojawi się w środkowym okienku, wybierz przycisk **Start**. Po kilku sekundach region zaczyna się.

     ![Pole Start/Stop języka SQL](media/11-demo-sql.png)

     ![CICS Region BANKDEMO - Ekran uruchomiono](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Tworzenie odbiornika

Utwórz odbiornik dla sesji TN3270, które uzyskują dostęp do aplikacji BankDemo.

1. W lewym okienku rozwiń pozycję **Edytory konfiguracji** i wybierz pozycję **Odbiornik**.

2. Kliknij ikonę **Otwórz plik** i wybierz plik **seelistener.exe.config.** Ten plik będzie edytowany i jest ładowany przy każdym uruchomieniu programu Enterprise Server.

3. Zwróć uwagę na dwa regiony wcześniej zdefiniowane (ESDEMO i JCLDEMO).

4. Aby utworzyć nowy region dla bankdemo, kliknij prawym przyciskiem myszy pozycję **Regiony**i wybierz polecenie **Dodaj region**.

5. Wybierz **region BANKDEMO**.

6. Dodaj kanał TN3270, klikając prawym przyciskiem myszy **region BANKDEMO** i wybierając pozycję **Dodaj kanał**.

7. W **ybranej na**serwer ie wpisz **TN3270**. Dla **portu**wprowadź **9024**. Aplikacja ESDEMO używa portu 9230, więc musisz użyć innego portu.

8. Aby zapisać plik, kliknij ikonę **Zapisz** lub wybierz pozycję **Zapisz** **plik** \> .

9. Aby uruchomić odbiornik, kliknij ikonę **Rozpocznij odbiornik** lub wybierz pozycję **Opcje** \> **uruchamiania odbiornika**.

     ![Edytor konfiguracji odbiornika windows](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Konfigurowanie Rumba, aby uzyskać dostęp do aplikacji BankDemo

Ostatnią rzeczą, którą musisz zrobić, to skonfigurować sesję 3270 za pomocą Rumba, emulatora 3270. Ten krok umożliwia dostęp do aplikacji BankDemo za pośrednictwem odbiornika, który został utworzony.

1. Z menu **Start** systemu Windows uruchom Rumba Desktop.

2. W obszarze menu **Połączenia** wybierz pozycję **TN3270**.

3. Kliknij **pozycję Wstaw** i wpisz **127.0.0.1** dla adresu IP i **9024** dla portu zdefiniowanego przez użytkownika.

4. U dołu okna dialogowego kliknij pozycję **Połącz**. Pojawi się czarny ekran CICS.

5. Wpisz **bank,** aby wyświetlić początkowy ekran 3270 dla aplikacji BankDemo.

6. W przypadku nazwy użytkownika wpisz **B0001** i hasło wpisz wszystko. Zostanie otwarty pierwszy ekran BANK20.

![Mainframe Display](media/14-demo.png)
![Ekran powitalny Mainframe Display - Rumba - Podsystem Ekran demonstracyjny](media/15-demo.png)

Gratulacje! Obecnie używasz aplikacji CICS na platformie Azure przy użyciu programu Micro Focus Enterprise Server.

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie serwera enterprise server w kontenerach platformy Docker na platformie Azure](run-enterprise-server-container.md)
- [Migracja mainframe - Portal](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Maszyny wirtualne](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Rozwiązywanie problemów](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demistyfikacja elementu mainframe z migracją na platformę Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
