---
title: Zabezpieczanie usługi Azure SQL Database | Microsoft Docs
description: Dowiedz się więcej o technikach i funkcjach umożliwiających zabezpieczenie bazy danych Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: DRediske
ms.author: daredis
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 827b3b6776656619314af3053cb05f8cfc3754c0
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914400"
---
# <a name="tutorial-secure-your-azure-sql-database"></a>Samouczek: zabezpieczanie usługi Azure SQL Database

Usługa SQL Database zabezpiecza dane przez: 
- Ograniczanie dostępu do bazy danych przy użyciu reguł zapory 
- Korzystanie z mechanizmów uwierzytelniania wymagających od użytkowników potwierdzenia tożsamości
- Autoryzację do danych za pośrednictwem członkostw i uprawnień opartych na rolach 
- Zabezpieczenia na poziomie wiersza
- Dynamiczne maskowanie danych

Usługa SQL Database oferuje również zaawansowane monitorowanie, inspekcję i wykrywanie zagrożeń. 

W kilku prostych krokach możesz poprawić zabezpieczenia bazy danych, aby chronić ją przed złośliwymi użytkownikami i nieautoryzowanym dostępem. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Konfigurowanie reguły zapory serwera na poziomie serwera w witrynie Azure Portal
> * Konfigurowanie reguł zapory dla bazy danych na poziomie bazy danych przy użyciu programu SSMS
> * Łączenie z bazą danych przy użyciu bezpiecznych parametrów połączenia
> * Zarządzanie dostępem użytkowników
> * Ochrona danych za pomocą szyfrowania
> * Włączanie inspekcji usługi SQL Database
> * Włączanie wykrywania zagrożeń w usłudze SQL Database

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że dysponujesz następującymi elementami:

- Zainstalowanie najnowszej wersji programu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). 
- Zainstalowany program Microsoft Excel
- Utworzony serwer i baza danych usługi Azure SQL — zobacz [Tworzenie bazy danych usługi Azure SQL w witrynie Azure Portal](sql-database-get-started-portal.md), [Tworzenie pojedynczej bazy danych usługi Azure SQL przy użyciu interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md) oraz [Tworzenie pojedynczej bazy danych usługi Azure SQL przy użyciu programu PowerShell](sql-database-powershell-samples.md). 

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Tworzenie reguły zapory na poziomie serwera w witrynie Azure Portal

Bazy danych SQL są chronione przez zaporę na platformie Azure. Domyślnie wszystkie połączenia z serwerem i znajdującymi się na nim bazami danych są odrzucane, poza połączeniami z innych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure SQL Database server-level and database-level firewall rules (Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database)](sql-database-firewall-configure.md).

Najbezpieczniejsza konfiguracja to ustawienie opcji „Zezwalaj na dostęp do usług platformy Azure” na wartość WYŁ. Aby nawiązać połączenie z bazą danych z maszyny wirtualnej platformy Azure lub z usługi w chmurze, musisz utworzyć [zastrzeżony adres IP (wdrożenie klasyczne)](../virtual-network/virtual-networks-reserved-public-ip.md) i zezwolić na dostęp przez zaporę tylko zastrzeżonego adresu IP. Jeśli używasz modelu wdrażania usługi [Resource Manager](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm), do zasobu jest przypisywany dedykowany publiczny adres IP. Zezwól na ten adres IP przez zaporę.

Wykonaj następujące kroki, aby utworzyć [Regułę zapory na poziomie serwera usługi SQL Database](sql-database-firewall-configure.md) dla serwera i umożliwić połączenia z konkretnego adresu IP. 

> [!NOTE]
> Jeśli utworzono przykładową bazę danych na platformie Azure przy użyciu jednego z poprzednich samouczków lub samouczków Szybki start, a ten samouczek jest wykonywany na komputerze o tym samym adresie IP, który był wykorzystywany podczas realizowania tamtych samouczków, możesz pominąć ten krok, ponieważ masz już utworzoną regułę zapory na poziomie serwera.
>

1. Kliknij pozycję **Bazy danych SQL** w menu po lewej stronie, a następnie kliknij bazę danych, dla której chcesz skonfigurować regułę zapory na stronie **Bazy danych SQL**. Zostanie otwarta strona przeglądu bazy danych zawierająca w pełni kwalifikowaną nazwę serwera (na przykład **mynewserver-20170313.database.windows.net**) i opcje dalszej konfiguracji.

      ![reguła zapory serwera](./media/sql-database-security-tutorial/server-firewall-rule.png) 

2. Kliknij pozycję **Ustaw zaporę serwera** na pasku narzędzi, tak jak pokazano to na wcześniejszej ilustracji. Zostanie otwarta strona **Ustawienia zapory** dla serwera SQL Database. 

3. Kliknij pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać publiczny adres IP komputera, z którego nawiązano połączenie z portalem lub wprowadzono regułę zapory ręcznie, a następnie kliknij przycisk **Zapisz**.

      ![ustawianie reguły zapory serwera](./media/sql-database-security-tutorial/server-firewall-rule-set.png) 

4. Kliknij przycisk **OK**, a następnie kliknij przycisk **X**, aby zamknąć stronę **Ustawienia zapory**.

Teraz możesz nawiązać połączenie z dowolną bazą danych na serwerze przy użyciu określonego adresu IP lub zakresu adresów IP.

> [!NOTE]
> Usługa SQL Database nawiązuje komunikację na porcie 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez firmową zaporę. Jeśli zachodzi taka sytuacja, nie będzie można nawiązać połączenia z serwerem Azure SQL Database, chyba że dział IT otworzy port 1433.
>

## <a name="create-a-database-level-firewall-rule-using-ssms"></a>Tworzenie reguły zapory na poziomie bazy danych za pomocą programu SSMS

Reguły zapory na poziomie bazy danych umożliwiają tworzenie różnych ustawień zapory dla różnych baz danych w ramach tego samego serwera logicznego oraz tworzenie przenośnych reguł zapory — oznacza to, że będą przenoszone razem z bazą danych podczas [pracy awaryjnej](sql-database-geo-replication-overview.md), a nie będą przechowywane na serwerze SQL. Reguły zapory na poziomie bazy danych można skonfigurować wyłącznie za pomocą instrukcji Transact-SQL oraz wyłącznie po skonfigurowaniu reguły zapory na poziomie serwera. Aby uzyskać więcej informacji, zobacz [Azure SQL Database server-level and database-level firewall rules (Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database)](sql-database-firewall-configure.md).

Wykonaj poniższe kroki, aby utworzyć regułę zapory właściwą dla bazy danych.

1. Nawiąż połączenie z bazą danych, np. przy użyciu programu [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

2. W Eksploratorze obiektów kliknij prawym przyciskiem myszy bazę danych, dla której chcesz dodać reguły zapory, a następnie kliknij pozycję **Nowe zapytanie**. Zostanie otwarte puste okno zapytania, które jest połączone z Twoją bazą danych.

3. W oknie zapytania zmień adres IP na swój publiczny adres IP, a następnie wykonaj następujące zapytanie:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. Na pasku narzędzi kliknij opcję **Wykonaj**, aby utworzyć regułę zapory.

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>Zobacz, jak połączyć aplikację z bazą danych przy użyciu bezpiecznych parametrów połączenia

W celu zapewnienia bezpiecznego, szyfrowanego połączenia pomiędzy aplikacją kliencką i usługą SQL Database, należy skonfigurować parametry połączenia, aby:

- żądać połączenia szyfrowanego; oraz
- nie ufać certyfikatowi serwera. 

W ten sposób nawiązuje się połączenie przy użyciu zabezpieczeń Transport Layer Security (TLS) i redukuje ryzyko ataków typu „man-in-the-middle” (człowiek pośrodku). Możesz uzyskać prawidłowo skonfigurowane parametry połączenia dla usługi SQL Database i obsługiwanych sterowników klienta z witryny Azure Portal, tak jak pokazano na poniższym zrzucie ekranu (w tym przypadku ADO.net). Aby uzyskać informacje dotyczące zabezpieczeń TLS i łączności, zobacz [Zagadnienia dotyczące protokołu TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

1. Wybierz pozycję **Bazy danych SQL** z menu po lewej stronie, a następnie kliknij bazę danych na stronie **Bazy danych SQL**.

2. Na stronie **Omówienie** bazy danych kliknij pozycję **Pokaż parametry połączenia bazy danych**.

3. Sprawdź pełne parametry połączenia sterownika **ADO.NET**.

    ![Parametry połączenia sterownika ADO.NET](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="creating-database-users"></a>Tworzenie użytkowników bazy danych

Przed utworzeniem jakichkolwiek użytkowników musisz wybrać jeden z dwóch typów uwierzytelniania obsługiwany przez usługę Azure SQL Database: 

**Uwierzytelnianie SQL** używające nazwy użytkownika i hasła do logowania użytkowników, którzy są prawidłowi tylko w kontekście konkretnej bazy danych w ramach serwera logicznego. 

**Uwierzytelnianie usługi Azure Active Directory** korzystające z tożsamości zarządzanej przez usługę Azure Active Directory. 

Jeśli chcesz użyć usługi [Azure Active Directory](./sql-database-aad-authentication.md) do uwierzytelniania w usłudze SQL Database, zanim przejdziesz dalej, musisz mieć wypełnioną usługę Azure Active Directory.

Wykonaj następujące kroki, aby utworzyć użytkownika przy użyciu uwierzytelniania SQL:

1. Nawiąż połączenie z bazą danych, np. przy użyciu programu [SQL Server Management Studio](./sql-database-connect-query-ssms.md), korzystając z poświadczeń administratora serwera.

2. W Eksploratorze obiektów kliknij prawym przyciskiem myszy bazę danych, dla której chcesz dodać nowego użytkownika, a następnie kliknij pozycję **Nowe zapytanie**. Zostanie otwarte puste okno zapytania, które jest połączone z wybraną bazą danych.

3. W oknie zapytania wprowadź następujące zapytanie:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

4. Na pasku narzędzi kliknij opcję **Wykonaj**, aby utworzyć użytkownika.

5. Domyślnie użytkownik może łączyć się z bazą danych, ale nie ma uprawnień do odczytywania i zapisywania danych. Aby przyznać te uprawnienia nowo utworzonemu użytkownikowi, wykonaj poniższe dwa polecenia w oknie nowego zapytania

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

Najlepszym rozwiązaniem jest utworzenie tych kont użytkowników innych niż administrator na poziomie bazy danych, aby nawiązywać połączenie z bazą danych, chyba że potrzebujesz możliwości wykonywania zadań administratora, np. tworzenia nowych użytkowników. Zapoznaj się z [samouczkiem usługi Azure Active Directory](./sql-database-aad-authentication-configure.md) dotyczącym sposobów uwierzytelniania przy użyciu usługi Azure Active Directory.


## <a name="protect-your-data-with-encryption"></a>Ochrona danych za pomocą szyfrowania

Przezroczyste szyfrowanie danych (TDE) usługi Azure SQL Database automatycznie szyfruje dane w spoczynku, bez potrzeby wprowadzania jakichkolwiek zmian w aplikacji uzyskującej dostęp do szyfrowanej bazy danych. W przypadku nowo utworzonych baz danych funkcja TDE jest domyślnie włączona. Aby włączyć funkcję TDE dla bazy danych lub sprawdzić, czy funkcja TDE jest włączona, wykonaj następujące kroki:

1. Wybierz pozycję **Bazy danych SQL** z menu po lewej stronie, a następnie kliknij bazę danych na stronie **Bazy danych SQL**. 

2. Kliknij opcję **Niewidoczne szyfrowanie danych**, aby otworzyć stronę konfiguracji dla funkcji TDE.

    ![Niewidoczne szyfrowanie danych](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. W razie potrzeby ustaw opcję **Szyfrowanie danych** na WŁ., a następnie kliknij opcję **Zapisz**.

Proces szyfrowania zostanie uruchomiony w tle. Możesz monitorować postępy, łącząc się z usługą SQL Database przy użyciu programu [SQL Server Management Studio](./sql-database-connect-query-ssms.md) i wysyłając zapytania do kolumny encryption_state widoku [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql?view=sql-server-2017). Stan 3 oznacza, że baza danych jest szyfrowana. 

## <a name="enable-sql-database-auditing-if-necessary"></a>Włączanie inspekcji usługi SQL Database, jeżeli jest potrzebna

Inspekcja usługi Azure SQL Database śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać potencjalne naruszenia zabezpieczeń. Wykonaj następujące kroki, aby utworzyć domyślne zasady inspekcji dla bazy danych SQL:

1. Wybierz pozycję **Bazy danych SQL** z menu po lewej stronie, a następnie kliknij bazę danych na stronie **Bazy danych SQL**. 

2. W bloku Ustawienia wybierz opcję **Inspekcja i wykrywanie zagrożeń**. Zauważ, że inspekcja na poziomie serwera jest wyłączona. Jest dostępny link **Wyświetl ustawienia serwera**, który umożliwia wyświetlenie lub zmodyfikowanie ustawień inspekcji serwera z tego kontekstu.

    ![Blok Inspekcja](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. Jeśli wolisz włączyć typ inspekcji (lub lokalizację) inny od określonego na poziomie serwera, **WŁĄCZ** inspekcję, a następnie wybierz typ inspekcji **Obiekt blob**. Jeśli inspekcja obiektów blob serwera jest włączona, skonfigurowana inspekcja bazy danych będzie istnieć równolegle z inspekcją obiektów blob serwera.

    ![Włączanie inspekcji](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. Wybierz opcję **Szczegóły magazynu**, aby otworzyć blok Magazyn dzienników inspekcji. Wybierz konto magazynu platformy Azure, na którym będą zapisywane dzienniki, a także okres przechowywania, po którym stare dzienniki będą usuwane, a następnie kliknij przycisk **OK** u dołu. 

   > [!TIP]
   > Użyj tego samego konta magazynu dla wszystkich baz danych poddawanych inspekcji, aby możliwie najlepiej wykorzystać szablony raportów inspekcji.
   > 

5. Kliknij pozycję **Zapisz**.

> [!IMPORTANT]
> Jeśli chcesz dostosować zdarzenia poddawane inspekcji, możesz to zrobić za pośrednictwem programu PowerShell lub interfejsu API REST — zobacz [Inspekcja bazy danych SQL](sql-database-auditing.md), aby uzyskać więcej informacji.
>

## <a name="enable-sql-database-threat-detection"></a>Włączanie wykrywania zagrożeń w usłudze SQL Database

Funkcja wykrywania zagrożeń stanowi nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie na bieżąco, zapewniając alerty zabezpieczeń w przypadku wystąpienia nietypowych działań. Użytkownicy mogą eksplorować podejrzane zdarzenia przy użyciu inspekcji usługi SQL Database, aby określić, czy są one skutkiem próby uzyskania dostępu, naruszenia zabezpieczeń lub wykorzystania danych w bazie danych. Wykrywanie zagrożeń upraszcza reagowanie na potencjalne zagrożenia bazy danych bez konieczności dysponowania wiedzą na poziomie eksperta zabezpieczeń lub zarządzania zaawansowanymi systemami monitorowania bezpieczeństwa.
Na przykład funkcja wykrywania zagrożeń wykrywa niektóre nietypowe działania bazy danych, wskazują na potencjalne próby iniekcji kodu SQL. Iniekcja kodu SQL jest jednym z typowych problemów z zabezpieczeniami aplikacji internetowych w Internecie. Metody tej używa się do atakowania aplikacji opartych na danych. Osoby atakujące wykorzystują luki w zabezpieczeniach aplikacji, aby przeprowadzać iniekcję złośliwych instrukcji SQL do pól wejścia aplikacji, co umożliwia naruszenie lub zmodyfikowanie danych w bazie danych.

1. Przejdź do bloku konfiguracji bazy danych SQL, którą chcesz monitorować. W bloku Ustawienia wybierz opcję **Inspekcja i wykrywanie zagrożeń**.

    ![Okienko nawigacji](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. W bloku konfiguracji **Inspekcja i wykrywanie zagrożeń** **WŁĄCZ** inspekcję, co spowoduje wyświetlenie ustawień wykrywania zagrożeń.

3. **WŁĄCZ** wykrywanie zagrożeń.

4. Skonfiguruj listę adresów e-mail, które będą otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.

5. Kliknij przycisk **Zapisz** w bloku **Inspekcja i wykrywanie zagrożeń**, aby zapisać nowe lub zaktualizowane zasady inspekcji i wykrywania zagrożeń.

    ![Okienko nawigacji](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    W przypadku wykrycia nietypowych działań bazy danych otrzymasz powiadomienie e-mail zaraz po takim wykryciu. Wiadomość e-mail będzie zapewniać informacje o podejrzanych zdarzeniach dotyczących zabezpieczeń, w tym o naturze nietypowych działań, nazwie bazy danych, nazwie serwera oraz czasie trwania zdarzenia. Ponadto będzie zapewniać informacje o możliwych przyczynach i rekomendowanych działań umożliwiających sprawdzenie i ograniczenie potencjalnych zagrożeń dla bazy danych. Następne kroki opisują czynności, które należy wykonać, aby odebrać taką wiadomość e-mail:

    ![Wiadomość e-mail dotycząca wykrywania zagrożeń](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. W wiadomości e-mail kliknij link **Dziennik inspekcji usługi Azure SQL**, który spowoduje uruchomienie witryny Azure Portal i wyświetlenie odpowiednich rekordów inspekcji dotyczących czasu wystąpienia podejrzanego zdarzenia.

    ![Rekordy inspekcji](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. Kliknij rekordy inspekcji, aby wyświetlić więcej informacji o podejrzanych działaniach bazy danych, np. instrukcji SQL, powodzie awarii oraz adresie IP klienta.

    ![Szczegóły rekordu](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. W bloku Rekordy inspekcji kliknij opcję **Otwórz w programie Excel**, aby otworzyć wstępnie skonfigurowany szablon programu Excel i zaimportować oraz uruchomić dogłębną analizę dziennika inspekcji dla przybliżonego czasu wystąpienia podejrzanego zdarzenia.

    > [!NOTE]
    > W programie Excel 2010 lub nowszym wymagana jest funkcja Power Query oraz ustawienie **Szybkie łączenie**.

    ![Rekordy otwarte w programie Excel](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. Aby skonfigurować ustawienie **Szybkie łączenie** — na karcie wstążki **POWER QUERY** wybierz pozycję **Opcje**, aby wyświetlić okno dialogowe Opcje. Wybierz sekcję prywatności i wybierz drugą opcję — „Ignorowanie poziomów prywatności i potencjalne poprawianie wydajności”:

    ![Funkcja szybkiego łączenia programu Excel](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. Aby załadować dzienniki inspekcji SQL, upewnij się, że parametry na karcie ustawień są skonfigurowane prawidłowo, a następnie wybierz wstążkę „Dane” i kliknij przycisk „Odśwież wszystko”.

    ![Parametry programu Excel](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. Wyniki zostaną wyświetlone w arkuszu **Dzienniki inspekcji SQL**, co umożliwia uruchomienie dogłębnej analizy wykrytych nietypowych działań, a także ograniczenie wpływu zdarzenia dotyczącego zabezpieczeń na Twoją aplikację.


## <a name="next-steps"></a>Następne kroki
W tym samouczku dowiedzieliśmy się, jak w kilku prostych krokach poprawić zabezpieczenia bazy danych, aby chronić ją przed złośliwymi użytkownikami i nieautoryzowanym dostępem.  W tym samouczku omówiono: 

> [!div class="checklist"]
> * Konfigurowanie reguł zapory dla serwera lub bazy danych
> * Łączenie z bazą danych przy użyciu bezpiecznych parametrów połączenia
> * Zarządzanie dostępem użytkowników
> * Ochrona danych za pomocą szyfrowania
> * Włączanie inspekcji usługi SQL Database
> * Włączanie wykrywania zagrożeń w usłudze SQL Database

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć bazę danych rozproszoną geograficznie.

> [!div class="nextstepaction"]
>[Implementowanie rozproszonej geograficznie bazy danych](sql-database-implement-geo-distributed-database.md)

