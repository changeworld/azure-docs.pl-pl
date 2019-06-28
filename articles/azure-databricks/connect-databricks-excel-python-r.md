---
title: 'Nawiązywanie połączenia usługi Azure Databricks z programu Excel, Python lub R '
description: Dowiedz się, jak połączyć z usługą Azure Databricks do programu Excel, Python lub R. za pomocą sterownika firmą Simba
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: 1805f04d7833dea180847defadd865cb23e9df62
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340851"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Nawiązywanie połączenia usługi Azure Databricks z programu Excel, Python lub R

W tym artykule dowiesz się, jak połączyć z usługą Azure Databricks przy użyciu języka programu Microsoft Excel, Python lub R za pomocą sterownika ODBC usługi Databricks. Po ustanowieniu połączenia uzyskujesz dostęp do danych w usłudze Azure Databricks z klientów programu Excel, Python lub R. Klientów można również użyć w celu dalszej analizy danych. 

## <a name="prerequisites"></a>Wymagania wstępne

* Konieczne jest posiadanie obszar roboczy usługi Azure Databricks, klaster Spark i przykładowe dane skojarzone z klastrem. Jeśli nie masz już te wymagania wstępne, ukończenie tego przewodnika Szybki Start na [uruchamianie zadania Spark w usłudze Azure Databricks przy użyciu witryny Azure portal](quickstart-create-databricks-workspace-portal.md).

* Pobierz sterownik ODBC usługi Databricks z [strony pobierania sterownika usługi Databricks](https://databricks.com/spark/odbc-driver-download). Zainstaluj wersję 32-bitową lub 64-bitowych w zależności od aplikacji, z którym chcesz nawiązać połączenie z usługi Azure Databricks. Na przykład aby połączyć się z programu Excel, należy zainstalować 32-bitowej wersji sterownika. Aby nawiązać połączenie z języków R i Python, należy zainstalować wersję 64-bitowego sterownika.

* Skonfiguruj osobistego tokenu dostępu w usłudze Databricks. Aby uzyskać instrukcje, zobacz [Token zarządzania](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Konfigurowanie nazwy DSN

Nazwa źródła danych (DSN) zawiera informacje na temat z określonym źródłem danych. Sterownik ODBC wymaga tego DSN do połączenia ze źródłem danych. W tej sekcji można skonfigurować DSN, który może służyć za pomocą sterownika ODBC usługi Databricks nawiązać połączenia z usługi Azure Databricks z klientów takich jak program Microsoft Excel, Python lub R.

1. W obszarze roboczym usługi Azure Databricks przejdź do klastra usługi Databricks.

    ![Otwórz klastra usługi Databricks](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Otwórz Databricks w klastrze")

2. W obszarze **konfiguracji** kliknij pozycję **JDBC/ODBC** kartę i skopiuj wartości pól **nazwy hosta serwera** i **ścieżki HTTP**. Te wartości, wykonanie czynności opisanych w tym artykule będą potrzebne.

    ![Uzyskiwanie konfiguracji usługi Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "konfiguracji pobrać usługi Databricks")

3. Na komputerze, należy uruchomić **źródeł danych ODBC** aplikacji (32-bitowy lub 64-bitowy), w zależności od aplikacji. Aby nawiązać połączenie z programu Excel, należy użyć 32-bitowej wersji. Aby nawiązać połączenie z języków R i Python, należy użyć 64-bitowej wersji.

    ![Uruchom ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Uruchom aplikację ODBC")

4. W obszarze **DSN użytkownika** kliknij pozycję **Dodaj**. W **Utwórz nowe źródło danych** okno dialogowe, wybierz opcję **sterownik ODBC platformy Spark firmą Simba**, a następnie kliknij przycisk **Zakończ**.

    ![Uruchom ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Uruchom aplikację ODBC")

5. W **sterownik ODBC platformy Spark firmą Simba** okna dialogowego wprowadź następujące wartości:

    ![Konfigurowanie nazwy DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "skonfigurować DSN")

    Poniższa tabela zawiera informacje na podstawie wartości w celu zapewnienia w oknie dialogowym.
    
    |Pole  | Value  |
    |---------|---------|
    |**Nazwa źródła danych**     | Podaj nazwę dla źródła danych.        |
    |**Hostami**     | Podaj wartość, który został skopiowany z obszaru roboczego usługi Databricks *nazwy hosta serwera*.        |
    |**Port**     | Wprowadź *443*.        |
    |**Uwierzytelnianie** > **mechanizm**     | Wybierz *nazwy użytkownika i hasła*.        |
    |**Nazwa użytkownika**     | Wprowadź *tokenu*.        |
    |**Hasło**     | Wprowadź wartość tokenu, który został skopiowany z obszaru roboczego usługi Databricks. |
    
    W oknie dialogowym Ustawienia DSN, należy wykonać następujące dodatkowe czynności.
    
    * Kliknij przycisk **Options protokołu HTTP**. W otwartym oknie dialogowym Wklej wartość *ścieżki HTTP* skopiowany z obszaru roboczego usługi Databricks. Kliknij przycisk **OK**.
    * Kliknij przycisk **opcje protokołu SSL**. W otwartym oknie dialogowym wybierz **Włącz SSL** pole wyboru. Kliknij przycisk **OK**.
    * Kliknij przycisk **Test** Aby przetestować połączenie z usługą Azure Databricks. Kliknij pozycję **OK**, aby zapisać konfigurację.
    * W **Administrator źródła danych ODBC** okno dialogowe, kliknij przycisk **OK**.

Masz teraz Twoje DSN Konfigurowanie. W kolejnych sekcjach Użyj tej nazwy DSN nawiązać połączenia z usługi Azure Databricks z programu Excel, Python lub R.

## <a name="connect-from-microsoft-excel"></a>Łączenie z programu Microsoft Excel

W tej sekcji możesz ściągnąć dane z usługi Azure Databricks do programu Microsoft Excel przy użyciu utworzonej wcześniej nazwy DSN. Przed rozpoczęciem upewnij się, że masz zainstalowany na tym komputerze program Microsoft Excel. Można używać wersji próbnej wersji programu Excel z [łącze wersji próbnej programu Microsoft Excel](https://products.office.com/excel).

1. Otwórz pusty skoroszyt w programie Microsoft Excel. Z **danych** wstążki, kliknij przycisk **Pobierz dane**. Kliknij przycisk **z innych źródeł** a następnie kliknij przycisk **z ODBC**.

    ![Uruchom ODBC z programu Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Uruchom ODBC z programu Excel")

2. W **z ODBC** okno dialogowe, wybierz DSN, który został utworzony wcześniej, a następnie kliknij przycisk **OK**.

    ![Select DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Select DSN")

3. Jeśli zostanie wyświetlony monit o poświadczenia, aby uzyskać nazwę użytkownika wprowadź **tokenu**. Hasła Podaj wartość tokenu, który pobierane obszar roboczy usługi Databricks.

    ![Podaj poświadczenia dla usługi Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "wybierz DSN")

4. W oknie Nawigator wybierz tabelę w usłudze Databricks, który chcesz załadować do programu Excel, a następnie kliknij przycisk **obciążenia**. 

    ![Ładowanie dta do programu Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "dta obciążenia do programu Excel")

Po umieszczeniu danych w skoroszycie programu Excel, można wykonać operacji analitycznych na nim.

## <a name="connect-from-r"></a>Łączenie z języka R

> [!NOTE]
> Ta sekcja zawiera informacje na temat klienta programu R Studio uruchomiony na komputerze z usługą Azure Databricks. Aby uzyskać instrukcje dotyczące sposobu używania programu R Studio na sam klaster usługi Azure Databricks, zobacz [programu R Studio w usłudze Azure Databricks](https://docs.azuredatabricks.net/spark/latest/sparkr/rstudio.html).

W tej sekcji użyjesz środowiska IDE języka R z danymi referencyjnymi dostępne w usłudze Azure Databricks. Przed przystąpieniem do wykonywania, musi mieć następujące zainstalowane na komputerze.

* Środowisko IDE dla języka R. W tym artykule korzysta z programu RStudio Desktop. Można zainstalować go z [pobierania programu R Studio](https://www.rstudio.com/products/rstudio/download/).
* Jeśli używasz programu RStudio for Desktop jako zintegrowanego środowiska Projektowego także zainstalować program Microsoft R Client z [ https://aka.ms/rclient/ ](https://aka.ms/rclient/). 

Otwórz program RStudio i wykonaj następujące czynności:

- Odwołanie `RODBC` pakietu. Dzięki temu można nawiązać połączenia z usługi Azure Databricks przy użyciu utworzonej wcześniej nazwy DSN.
- Nawiąż połączenie przy użyciu nazwy DSN.
- Uruchom kwerendę SQL na tych danych w usłudze Azure Databricks. W poniższym fragmencie kodu *radio_sample_data* jest tabela, która już istnieje w usłudze Azure Databricks.
- Wykonywanie operacji na zapytanie, aby sprawdzić dane wyjściowe. 

Poniższy fragment kodu wykonuje następujące zadania:

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

## <a name="connect-from-python"></a>Połącz za pomocą języka Python

W tej sekcji użyjesz środowiskiem Python IDE (takich jak BEZCZYNNY) z danymi referencyjnymi dostępne w usłudze Azure Databricks. Przed rozpoczęciem należy spełnić następujące wymagania wstępne:

* Zainstaluj język Python z [tutaj](https://www.python.org/downloads/). Instalowanie języka Python z tego linku instaluje BEZCZYNNY.

* W wierszu polecenia na komputerze zainstalować `pyodbc` pakietu. Uruchom następujące polecenie:

      pip install pyodbc

Otwórz BEZCZYNNY i wykonaj następujące czynności:

- Importuj `pyodbc` pakietu. Dzięki temu można nawiązać połączenia z usługi Azure Databricks przy użyciu utworzonej wcześniej nazwy DSN.
- Nawiąż połączenie przy użyciu utworzonej wcześniej nazwy DSN.
-  Uruchom kwerendę SQL przy użyciu połączenia utworzone. W poniższym fragmencie kodu *radio_sample_data* jest tabela, która już istnieje w usłudze Azure Databricks.
- Wykonywać operacje na zapytanie, aby sprawdzić dane wyjściowe.

Poniższy fragment kodu wykonuje następujące zadania:

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

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat źródeł, z których możesz zaimportować dane do usługi Azure Databricks, zobacz [źródeł danych dla usługi Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)


