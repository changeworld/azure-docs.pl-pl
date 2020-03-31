---
title: 'Łączenie się z usługą Azure Databricks z programu Excel, Python lub R '
description: Dowiedz się, jak połączyć usługę Azure Databricks z programem Excel, Python lub R za pomocą sterownika Simba.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: f7494d36cf9b16ac6c7a1287a6ff96dd2285c6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73601943"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Łączenie się z usługą Azure Databricks z programu Excel, Python lub R

W tym artykule dowiesz się, jak używać sterownika Databricks ODBC do łączenia usługi Azure Databricks z językiem Microsoft Excel, Python lub Języka R. Po ustanowieniu połączenia można uzyskać dostęp do danych w usłudze Azure Databricks z klientów programu Excel, Python lub R. Można również użyć klientów do dalszej analizy danych. 

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć obszar roboczy usługi Azure Databricks, klaster platformy Spark i przykładowe dane skojarzone z klastrem. Jeśli nie masz jeszcze tych wymagań wstępnych, ukończ przewodnik Szybki start w [obszarze Uruchom zadanie platformy Spark na platformie Azure Databricks przy użyciu witryny Azure portal](quickstart-create-databricks-workspace-portal.md).

* Pobierz sterownik Databricks ODBC ze [strony pobierania sterownika Databricks](https://databricks.com/spark/odbc-driver-download). Zainstaluj wersję 32-bitową lub 64-bitową w zależności od aplikacji, z której chcesz połączyć się z usługą Azure Databricks. Na przykład, aby połączyć się z programem Excel, zainstaluj 32-bitową wersję sterownika. Aby połączyć się z języka R i Python, zainstaluj 64-bitową wersję sterownika.

* Konfigurowanie tokenu dostępu osobistego w uchwytów danych. Aby uzyskać instrukcje, zobacz [Zarządzanie tokenami](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="set-up-a-dsn"></a>Konfigurowanie dsn

Nazwa źródła danych (DSN) zawiera informacje o określonym źródle danych. Sterownik ODBC potrzebuje tej łączy DSN, aby połączyć się ze źródłem danych. W tej sekcji skonfigurowano DSN, który może być używany ze sterownikiem Databricks ODBC, aby połączyć się z usługą Azure Databricks od klientów, takich jak Microsoft Excel, Python lub R.

1. Z obszaru roboczego usługi Azure Databricks przejdź do klastra Databricks.

    ![Otwarty klaster Databricks](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Otwarty klaster Databricks")

2. Na karcie **Konfiguracja** kliknij kartę **JDBC/ODBC** i skopiuj wartości dla **nazwy hosta serwera** i **ścieżki HTTP**. Te wartości są potrzebne do wykonania kroków w tym artykule.

    ![Pobierz konfigurację Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Pobierz konfigurację Databricks")

3. Na komputerze uruchom aplikację **ODBC Data Sources** (32-bitową lub 64-bitową) w zależności od aplikacji. Aby połączyć się z programem Excel, użyj wersji 32-bitowej. Aby połączyć się z języka R i Python, użyj wersji 64-bitowej.

    ![Uruchom ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Uruchom aplikację ODBC")

4. Na karcie **Nazwa DSN użytkownika** kliknij pozycję **Dodaj**. W oknie dialogowym **Tworzenie nowego źródła danych** wybierz sterownik **SIMBA Spark ODBC,** a następnie kliknij przycisk **Zakończ**.

    ![Uruchom ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Uruchom aplikację ODBC")

5. W oknie dialogowym **Sterownik SIMBA Spark ODBC** podaj następujące wartości:

    ![Konfigurowanie dsn](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Konfigurowanie dsn")

    Poniższa tabela zawiera informacje na temat wartości, które należy podać w oknie dialogowym.
    
    |Pole  | Wartość  |
    |---------|---------|
    |**Data Source Name (Nazwa źródła danych)**     | Podaj nazwę źródła danych.        |
    |**Hostami**     | Podaj wartość skopiowaną z obszaru roboczego Databricks dla *nazwy hosta serwera*.        |
    |**Portu**     | Wprowadź *443*.        |
    |**Mechanizm uwierzytelniania** > **Mechanism**     | Wybierz *nazwę użytkownika i hasło*.        |
    |**Nazwa użytkownika**     | Wprowadź *token*.        |
    |**Hasło**     | Wprowadź wartość tokenu skopiowaną z obszaru roboczego Databricks. |
    
    Wykonaj następujące dodatkowe kroki w oknie dialogowym ustawienia DSN.
    
    * Kliknij pozycję **Opcje HTTP**. W oknie dialogowym, które się otwiera, wklej wartość *ścieżki HTTP* skopiowanej z obszaru roboczego Databricks. Kliknij przycisk **OK**.
    * Kliknij pozycję **Opcje SSL**. W oknie dialogowym, które się otwiera, zaznacz pole wyboru **Włącz SSL.** Kliknij przycisk **OK**.
    * Kliknij **przycisk Testuj,** aby przetestować połączenie z usługą Azure Databricks. Kliknij **przycisk OK,** aby zapisać konfigurację.
    * W oknie dialogowym **Administrator źródła danych ODBC** kliknij przycisk **OK**.

Masz teraz skonfigurowany DSN. W następnych sekcjach użyjesz tej dsn do łączenia się z usługą Azure Databricks z programu Excel, Python lub R.

## <a name="connect-from-microsoft-excel"></a>Łączenie z programu Microsoft Excel

W tej sekcji pobierasz dane z usługi Azure Databricks do programu Microsoft Excel przy użyciu dsn utworzonego wcześniej. Przed rozpoczęciem upewnij się, że na komputerze jest zainstalowany program Microsoft Excel. Można użyć wersji próbnej programu Excel z [łącza próbnego programu Microsoft Excel](https://products.office.com/excel).

1. Otwórz pusty skoroszyt w programie Microsoft Excel. Na wstążce **Dane** kliknij pozycję **Pobierz dane**. Kliknij **pozycję Z innych źródeł,** a następnie kliknij przycisk **OdBC**.

    ![Uruchamianie odbc z programu Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Uruchamianie odbc z programu Excel")

2. W oknie dialogowym **Od ODBC** zaznacz nazwę DSN utworzoną wcześniej, a następnie kliknij przycisk **OK**.

    ![Wybierz DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Wybierz DSN")

3. Jeśli zostanie wyświetlony monit o podanie poświadczeń, nazwa użytkownika wprowadź **token**. Hasło podaj wartość tokenu pobraną z obszaru roboczego Databricks.

    ![Podaj poświadczenia dla databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Wybierz DSN")

4. W oknie nawigatora zaznacz tabelę w programie Databricks, którą chcesz załadować do programu Excel, a następnie kliknij pozycję **Załaduj**. 

    ![Ładowanie dta do programu Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Ładowanie dta do programu Excel")

Po uzyskaniu danych w skoroszycie programu Excel można wykonywać na nim operacje analityczne.

## <a name="connect-from-r"></a>Połącz z R

> [!NOTE]
> Ta sekcja zawiera informacje na temat integracji klienta usługi R Studio działającego na pulpicie z usługą Azure Databricks. Aby uzyskać instrukcje dotyczące używania programu R Studio w samym klastrze usługi Azure Databricks, zobacz [R Studio na platformie Azure Databricks.](/azure/databricks/spark/latest/sparkr/rstudio)

In this section, you use an R language IDE to reference data available in Azure Databricks. Przed rozpoczęciem na komputerze muszą być zainstalowane następujące elementy.

* IDE dla języka R. W tym artykule użyto funkcji RStudio for Desktop. Można go zainstalować z [R Studio download](https://www.rstudio.com/products/rstudio/download/).
* Jeśli używasz RStudio for Desktop jako ide, [https://aka.ms/rclient/](https://aka.ms/rclient/)należy również zainstalować klienta Microsoft R z . 

Otwórz rStudio i wykonaj następujące czynności:

- Odwołaj `RODBC` się do pakietu. Dzięki temu można połączyć się z usługi Azure Databricks przy użyciu dsn utworzone wcześniej.
- Nawiążej połączenie przy użyciu sieci DSN.
- Uruchom kwerendę SQL na dane w usłudze Azure Databricks. W poniższym urywek *radio_sample_data* jest tabela, która już istnieje w usłudze Azure Databricks.
- Wykonaj niektóre operacje na kwerendzie, aby sprawdzić dane wyjściowe. 

Następujące fragment kodu wykonuje następujące zadania:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Połącz z Pythona

W tej sekcji używasz IDE języka Python (takich jak IDLE) do odwoływania się do danych dostępnych w usłudze Azure Databricks. Przed rozpoczęciem należy wykonać następujące wymagania wstępne:

* Zainstaluj [Pythona stąd](https://www.python.org/downloads/). Instalowanie Pythona z tego łącza instaluje również IDLE.

* W wierszu polecenia na komputerze `pyodbc` zainstaluj pakiet. Uruchom następujące polecenie:

      pip install pyodbc

Otwórz iDLE i wykonaj następujące czynności:

- Zaimportuj `pyodbc` pakiet. Dzięki temu można połączyć się z usługi Azure Databricks przy użyciu dsn utworzone wcześniej.
- Nawiążej połączenie przy użyciu sieci DSN utworzonej wcześniej.
-  Uruchom kwerendę SQL przy użyciu utworzonego połączenia. W poniższym urywek *radio_sample_data* jest tabela, która już istnieje w usłudze Azure Databricks.
- Wykonaj operacje na kwerendzie, aby sprawdzić dane wyjściowe.

Następujące fragment kodu wykonuje następujące zadania:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit=True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o źródłach, z których można importować dane do usługi Azure Databricks, zobacz [Źródła danych dla usługi Azure Databricks](/azure/databricks/data/data-sources/index)


