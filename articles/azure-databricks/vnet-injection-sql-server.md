---
title: Zapytanie SQL Server Linux narzędziem w sieci wirtualnej z notesu usługi Azure Databricks
description: W tym artykule opisano sposób wdrażania usługi Azure Databricks z siecią wirtualną, znany także jako iniekcji sieci wirtualnej.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288954"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Samouczek: Zapytanie SQL Server Linux narzędziem w sieci wirtualnej z notesu usługi Azure Databricks

Ten samouczek omawia sposób integracji usługi Azure Databricks przy użyciu kontenera platformy Docker do programu SQL Server systemu Linux w sieci wirtualnej. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie obszaru roboczego usługi Azure Databricks z siecią wirtualną
> * Zainstaluj maszynę wirtualną systemu Linux w sieci publicznej
> * Zainstaluj platformę Docker
> * Zainstaluj program Microsoft SQL Server w kontenerze platformy docker dla systemu Linux
> * Zapytania programu SQL Server przy użyciu sterownika JDBC z notesu usługi Databricks

## <a name="prerequisites"></a>Wymagania wstępne

* Tworzenie [obszaru roboczego usługi Databricks w sieci wirtualnej](quickstart-create-databricks-workspace-vnet-injection.md).

* Zainstaluj [Ubuntu dla Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Pobierz program [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Linux

1. W witrynie Azure portal wybierz ikonę **maszyn wirtualnych**. Następnie wybierz **+ Dodaj**.

    ![Dodaj nową maszynę wirtualną platformy Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Na **podstawy** karcie Wybierz Ubuntu Server 16.04 LTS. Zmień rozmiar maszyny Wirtualnej na B1ms, która ma jeden procesorów VCPU i 2 GB pamięci RAM. Minimalne wymagania dla kontenera systemu Linux platformy Docker z serwerem SQL wynosi 2 GB. Wybierz administratora, nazwa użytkownika i hasło.

    ![Podstawowe informacje o karcie nową konfigurację maszyny wirtualnej](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Przejdź do **sieć** kartę. Wybierz sieć wirtualną i podsieci publicznej, która obejmuje usługi Azure Databricks w klastrze. Wybierz **przeglądu + Utwórz**, następnie **Utwórz** wdrożyć maszynę wirtualną.

    ![Karta Sieć w nowej konfiguracji maszyny wirtualnej](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Po zakończeniu wdrożenia przejdź do maszyny wirtualnej. Zwróć uwagę, publiczny adres IP i sieci wirtualnej/podsieci **Przegląd**. Wybierz **publiczny adres IP**

    ![Omówienie maszyny wirtualnej](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Zmiana **przypisania** do **statyczne** i wprowadź **etykiety nazwy DNS**. Wybierz **Zapisz**i uruchom ponownie maszynę wirtualną.

    ![Publiczna konfiguracja adresu IP](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Wybierz **sieć** karcie **ustawienia**. Należy zauważyć, że sieciowej grupy zabezpieczeń, który został utworzony podczas wdrażania usługi Azure Databricks jest skojarzony z maszyną wirtualną. Wybierz **Dodaj regułę portu wejściowego**.

7. Dodaj zasadę, aby otworzyć port 22 dla protokołu SSH. Użyj następujących ustawień:
    
    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Element źródłowy|Adresy IP|Adresy IP określa, że ruch przychodzący z określonego źródła, adres IP będzie dozwolony lub odrzucany przez tę regułę.|
    |Źródłowe adresy IP|< adres ip\>|Wprowadź swój publiczny adres IP. Swój publiczny adres IP można znaleźć, odwiedzając [bing.com](https://www.bing.com/) i wyszukując **"Mój adres IP"**.|
    |Zakresy portów źródłowych|*|Zezwalać na ruch z dowolnego portu.|
    |Element docelowy|Adresy IP|Adresy IP określa, że ruch wychodzący do określonego źródłowego adresu IP będzie dozwolony lub odrzucany przez tę regułę.|
    |Docelowe adresy IP|< publiczny adres ip swojej maszyny wirtualnej\>|Wprowadź publiczny adres IP maszyny wirtualnej. Można je znaleźć na **Przegląd** strony maszyny wirtualnej.|
    |Zakresy portów docelowych|22|Otwórz port 22 dla protokołu SSH.|
    |Priorytet|290|Nadaj regule priorytet.|
    |Name (Nazwa)|ssh-databricks-tutorial-vm|Nadaj regule nazwę.|


    ![Dodawanie reguły zabezpieczeń dla ruchu przychodzącego dla portu 22](./media/vnet-injection-sql-server/open-port.png)

8. Dodaj zasadę, aby otworzyć port 1433 dla programu SQL z następującymi ustawieniami:

    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Element źródłowy|Adresy IP|Adresy IP określa, że ruch przychodzący z określonego źródła, adres IP będzie dozwolony lub odrzucany przez tę regułę.|
    |Źródłowe adresy IP|10.179.0.0/16|Wprowadź zakres adresów sieci wirtualnej.|
    |Zakresy portów źródłowych|*|Zezwalać na ruch z dowolnego portu.|
    |Element docelowy|Adresy IP|Adresy IP określa, że ruch wychodzący do określonego źródłowego adresu IP będzie dozwolony lub odrzucany przez tę regułę.|
    |Docelowe adresy IP|< publiczny adres ip swojej maszyny wirtualnej\>|Wprowadź publiczny adres IP maszyny wirtualnej. Można je znaleźć na **Przegląd** strony maszyny wirtualnej.|
    |Zakresy portów docelowych|1433|Otwórz port 22 dla programu SQL Server.|
    |Priorytet|300|Nadaj regule priorytet.|
    |Name (Nazwa)|sql-databricks-tutorial-vm|Nadaj regule nazwę.|

    ![Dodaj regułę zabezpieczeń dla ruchu przychodzącego dla portu 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Uruchom program SQL Server w kontenerze platformy Docker

1. Otwórz [Ubuntu dla Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), lub innego narzędzia, która umożliwi Ci SSH z maszyną wirtualną. Przejdź do maszyny wirtualnej w witrynie Azure portal i wybierz pozycję **Connect** można pobrać polecenia SSH, musisz połączyć.

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Wprowadź polecenie w terminalu systemu Ubuntu, a następnie wprowadź hasło administratora, utworzonej podczas konfigurowania maszyny wirtualnej.

    ![Ubuntu terminala protokołu SSH logowania](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Użyj następującego polecenia, aby zainstalować platformę Docker na maszynie wirtualnej.

    ```bash
    sudo apt-get install docker.io
    ```

    Sprawdź instalację platformy docker za pomocą następującego polecenia:

    ```bash
    sudo docker --version
    ```

4. Zainstaluj obraz.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Sprawdź obrazów.

    ```bash
    sudo docker images
    ```

5. Uruchom obraz kontenera.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Sprawdź, czy kontener jest uruchomiony.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Tworzenie bazy danych SQL

1. Otwórz program SQL Server Management Studio i Połącz z serwerem przy użyciu nazwy serwera i uwierzytelnianie SQL. Logowanie username jest **SA** , a hasło to hasło w poleceniu platformy Docker. To hasło w przykładzie polecenia `Password1234`.

    ![Połączenia z serwerem SQL przy użyciu programu SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Po pomyślnym nawiązaniu połączenia, wybierz **nowe zapytanie** wprowadź poniższy fragment kodu do tworzenia bazy danych, tabeli i wstawianie niektóre rekordy w tabeli.

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

    ![Zapytanie, aby utworzyć bazę danych programu SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Zapytanie SQL Server z usługi Azure Databricks

1. Przejdź do obszaru roboczego usługi Azure Databricks i sprawdź, czy klaster jest utworzony jako część wymagań wstępnych. Następnie wybierz **tworzenie notesu**. Nadaj nazwę, wybierz opcję Notes *Python* jako język, a następnie wybierz utworzony klaster.

    ![Nowe ustawienia notesu usługi Databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Użyj następującego polecenia, aby wykonać polecenie ping wewnętrzny adres IP maszyny wirtualnej programu SQL Server. Polecenie ping zostanie pomyślnie zakończona. Jeśli nie, sprawdź, czy kontener jest uruchomiony i przejrzyj konfiguracji Sieciowej grupy zabezpieczeń sieci.

    ```python
    %sh
    ping 10.179.64.4
    ```

    Można również użyć polecenia nslookup, aby zapoznać się z.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Po użytkownik został pomyślnie za pomocą polecenia ping programu SQL Server, można tworzyć zapytania bazy danych i tabel. Uruchom python następujący kod:

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

Gdy nie są już potrzebne, Usuń grupę zasobów, obszar roboczy usługi Azure Databricks i wszystkie powiązane zasoby. Usunięcie zadania pozwala uniknąć niepotrzebnych rozliczeń. Jeśli planujesz używać obszaru roboczego usługi Azure Databricks w przyszłości, możesz zatrzymać klaster i uruchomić ponownie później. Jeśli nie zamierzasz nadal korzystać z tego obszaru roboczego usługi Azure Databricks, należy usunąć wszystkie zasoby utworzone w ramach tego samouczka wykonując następujące kroki:

1. W menu po lewej stronie w witrynie Azure portal, kliknij **grup zasobów** a następnie kliknij nazwę utworzoną grupę zasobów.

2. Na stronie grupy zasobów, wybierz **Usuń**, wpisz nazwę zasobu do usunięcia w polu tekstowym, a następnie wybierz pozycję **Usuń** ponownie.

## <a name="next-steps"></a>Kolejne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak wyodrębnianie, przekształcanie i ładowanie danych za pomocą usługi Azure Databricks.
> [!div class="nextstepaction"]
> [Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
