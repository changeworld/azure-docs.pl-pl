---
title: Zapytanie o kontener platformy Docker systemu SQL Server Linux za pomocą usługi Azure Databricks
description: W tym artykule opisano sposób wdrażania usługi Azure Databricks w sieci wirtualnej, znany również jako iniekcji sieci wirtualnej.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747664"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Samouczek: Wykonywanie zapytań o kontener platformy Docker systemu SQL Server Linux w sieci wirtualnej z notesu usługi Azure Databricks

W tym samouczku opisano, jak zintegrować usługi Azure Databricks z kontenerem platformy Docker systemu SQL Server Linux w sieci wirtualnej. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie obszaru roboczego usługi Azure Databricks w sieci wirtualnej
> * Instalowanie maszyny wirtualnej systemu Linux w sieci publicznej
> * Zainstaluj platformę Docker
> * Instalowanie programu Microsoft SQL Server w kontenerze platformy Dokacyjnej systemu Linux
> * Zapytanie do programu SQL Server przy użyciu JDBC z notesu databricks

## <a name="prerequisites"></a>Wymagania wstępne

* Tworzenie [obszaru roboczego Databricks w sieci wirtualnej](quickstart-create-databricks-workspace-vnet-injection.md).

* Zainstaluj [Ubuntu dla Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Pobierz program [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Linux

1. W witrynie Azure portal wybierz ikonę maszyn **wirtualnych**. Następnie wybierz pozycję **+ Dodaj**.

    ![Dodawanie nowej maszyny wirtualnej platformy Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Na karcie **Podstawy** wybierz Ubuntu Server 18.04 LTS i zmień rozmiar maszyny Wirtualnej na B2s. Wybierz nazwę użytkownika i hasło administratora.

    ![Karta Podstawowe nowej konfiguracji maszyny wirtualnej](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Przejdź do karty **Sieć.** Wybierz sieć wirtualną i podsieć publiczną, która zawiera klaster usługi Azure Databricks. Wybierz **pozycję Przejrzyj + utwórz**, a następnie **utwórz,** aby wdrożyć maszynę wirtualną.

    ![Karta Sieć nowej konfiguracji maszyny wirtualnej](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Po zakończeniu wdrażania przejdź do maszyny wirtualnej. Zwróć uwagę na publiczny adres IP i wirtualną sieć/podsieć w **przeglądzie**. Wybierz **publiczny adres IP**

    ![Omówienie maszyny wirtualnej](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Zmień **przypisanie** na **Statyczne** i wprowadź **etykietę nazwy DNS**. Wybierz **pozycję Zapisz**i uruchom ponownie maszynę wirtualną.

    ![Konfiguracja publicznego adresu IP](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Wybierz kartę **Sieć** w obszarze **Ustawienia**. Należy zauważyć, że grupa zabezpieczeń sieci, który został utworzony podczas wdrażania usługi Azure Databricks jest skojarzony z maszyną wirtualną. Wybierz **pozycję Dodaj regułę portu przychodzącego**.

7. Dodaj regułę, aby otworzyć port 22 dla SSH. Użyj następujących ustawień:
    
    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Element źródłowy|Adresy IP|Adresy IP określają, że ruch przychodzący z określonego źródłowego adresu IP będzie dozwolony lub odrzucany przez tę regułę.|
    |Źródłowe adresy IP|<twoje publiczne ip\>|Wprowadź swój publiczny adres IP. Możesz znaleźć swój publiczny adres IP, odwiedzając [bing.com](https://www.bing.com/) i wyszukując **"mój ADRES IP".**|
    |Zakresy portów źródłowych|*|Zezwalaj na ruch z dowolnego portu.|
    |Element docelowy|Adresy IP|Adresy IP określają, że ruch wychodzący dla określonego źródłowego adresu IP będzie dozwolony lub odrzucany przez tę regułę.|
    |Docelowe adresy IP|<publicznego ip vm\>|Wprowadź publiczny adres IP maszyny wirtualnej. Można to znaleźć na stronie **Przegląd** maszyny wirtualnej.|
    |Zakresy portów docelowych|22|Otwarty port 22 dla SSH.|
    |Priorytet|290|Nadaj regułie pierwszeństwo.|
    |Nazwa|ssh-databricks-tutorial-vm|Nadaj regule nazwę.|


    ![Dodawanie reguły zabezpieczeń ruchu przychodzącego dla portu 22](./media/vnet-injection-sql-server/open-port.png)

8. Dodaj regułę, aby otworzyć port 1433 dla języka SQL z następującymi ustawieniami:

    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Element źródłowy|Dowolne|Źródło określa, że ruch przychodzący z określonego źródłowego adresu IP będzie dozwolony lub odrzucany przez tę regułę.|
    |Zakresy portów źródłowych|*|Zezwalaj na ruch z dowolnego portu.|
    |Element docelowy|Adresy IP|Adresy IP określają, że ruch wychodzący dla określonego źródłowego adresu IP będzie dozwolony lub odrzucany przez tę regułę.|
    |Docelowe adresy IP|<publicznego ip vm\>|Wprowadź publiczny adres IP maszyny wirtualnej. Można to znaleźć na stronie **Przegląd** maszyny wirtualnej.|
    |Zakresy portów docelowych|1433|Otwórz port 22 dla programu SQL Server.|
    |Priorytet|300|Nadaj regułie pierwszeństwo.|
    |Nazwa|sql-databricks-tutorial-vm|Nadaj regule nazwę.|

    ![Dodawanie reguły zabezpieczeń ruchu przychodzącego dla portu 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Uruchamianie programu SQL Server w kontenerze platformy Docker

1. Otwórz [Ubuntu dla Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)lub inne narzędzie, które pozwoli Ci na SSH do maszyny wirtualnej. Przejdź do maszyny wirtualnej w witrynie Azure portal i wybierz pozycję **Połącz,** aby uzyskać polecenie SSH, które należy połączyć.

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Wprowadź polecenie w terminalu Ubuntu i wprowadź hasło administratora utworzone podczas konfigurowania maszyny wirtualnej.

    ![Ubuntu terminal SSH zaloguj się](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Użyj następującego polecenia, aby zainstalować dok. na maszynie wirtualnej.

    ```bash
    sudo apt-get install docker.io
    ```

    Sprawdź instalację platformy Docker za pomocą następującego polecenia:

    ```bash
    sudo docker --version
    ```

4. Zainstaluj obraz.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Sprawdź zdjęcia.

    ```bash
    sudo docker images
    ```

5. Uruchom kontener z obrazu.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Sprawdź, czy kontener jest uruchomiony.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Tworzenie bazy danych SQL

1. Otwórz program SQL Server Management Studio i połącz się z serwerem przy użyciu nazwy serwera i uwierzytelniania SQL. Nazwa użytkownika logowania jest **SA** i hasło jest hasło ustawione w poleceniu platformy Docker. Hasło w przykładowym `Password1234`poleceniu to .

    ![Łączenie się z programem SQL Server przy użyciu programu SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Po pomyślnym nawiązaniu połączenia wybierz pozycję **Nowa kwerenda** i wprowadź poniższy fragment kodu, aby utworzyć bazę danych, tabelę i wstawić niektóre rekordy do tabeli.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![Kwerenda w celu utworzenia bazy danych programu SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Zapytanie o program SQL Server z usługi Azure Databricks

1. Przejdź do obszaru roboczego usługi Azure Databricks i sprawdź, czy został utworzony jako część wymagań wstępnych. Następnie wybierz pozycję **Utwórz notes**. Nadaj notesowi nazwę, wybierz *python* jako język i wybierz utworzony klaster.

    ![Nowe ustawienia notesu Databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Użyj następującego polecenia, aby pingować wewnętrzny adres IP maszyny wirtualnej programu SQL Server. Ten ping powinien się udać. Jeśli nie, sprawdź, czy kontener jest uruchomiony, i przejrzyj konfigurację sieciowej grupy zabezpieczeń (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    Można również użyć polecenia nslookup do sprawdzenia.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Po pomyślnym pingowaniu programu SQL Server można zbadać bazę danych i tabele. Uruchom następujący kod języka python:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, usuń grupę zasobów, obszar roboczy usługi Azure Databricks i wszystkie powiązane zasoby. Usunięcie zadania pozwala uniknąć niepotrzebnych rozliczeń. Jeśli planujesz używać obszaru roboczego usługi Azure Databricks w przyszłości, możesz zatrzymać klaster i ponownie uruchomić go później. Jeśli nie zamierzasz nadal korzystać z tego obszaru roboczego usługi Azure Databricks, usuń wszystkie zasoby utworzone w tym samouczku, wykonując następujące kroki:

1. Z menu po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów,** a następnie kliknij nazwę utworzonej grupy zasobów.

2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz nazwę zasobu do usunięcia w polu tekstowym, a następnie wybierz pozycję **Usuń** ponownie.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak wyodrębnić, przekształcić i załadować dane przy użyciu usługi Azure Databricks.
> [!div class="nextstepaction"]
> [Samouczek: wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
