---
title: Nawiązywanie połączenia z programu Excel, Python lub R do platformy Azure Databricks | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać ze sterownika Simba nawiązać Azure Databricks do programu Excel, Python lub R.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: bbf75a03fb771aa415a26e151614cecfaa14c485
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598882"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Nawiązywanie połączenia z programu Excel, Python lub R do Azure Databricks

W tym artykule Dowiedz się jak korzystać ze sterownika Databricks ODBC nawiązać połączenia z językiem programu Microsoft Excel, Python lub R Azure Databricks. Po ustanowieniu połączenia ma dostęp do danych w usłudze Azure Databricks, od klientów programu Excel, Python lub R. Umożliwia także klientów do dalszej analizy danych. 

## <a name="prerequisites"></a>Wymagania wstępne

* Musi mieć obszarem roboczym Azure Databricks, klastra Spark i przykładowe dane skojarzone z klastra. Nie masz już te wymagania wstępne, należy wykonać procedury szybkiego startu w [Uruchom zadanie Spark Databricks Azure przy użyciu portalu Azure](quickstart-create-databricks-workspace-portal.md).

* Pobierz sterownik Databricks ODBC z [stronę pobierania sterownika Databricks](https://databricks.com/spark/odbc-driver-download). Zainstaluj wersję 32-bitowy lub 64-bitowych zależnie od aplikacji, z którym chcesz nawiązać połączenie Azure Databricks. Na przykład aby połączyć się z programu Excel, należy zainstalować 32-bitowej wersji sterownika. Aby połączyć się z języków R i Python, zainstaluj 64-bitowej wersji sterownika.

* Konfigurowanie osobisty token dostępu w Databricks. Aby uzyskać instrukcje, zobacz [Token zarządzania](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Konfigurowanie nazwy DSN

Nazwa źródła danych (DSN) zawiera informacje dotyczące określonego źródła danych. Sterownik ODBC wymaga tego DSN nawiązywania połączenia ze źródłem danych. W tej sekcji ustawić DSN, który może służyć ze sterownikiem Databricks ODBC do nawiązania połączenia od klientów, takich jak program Microsoft Excel, Python lub R. Azure Databricks

1. W obszarze roboczym Azure Databricks przejdź do klastra Databricks.

    ![Otwórz klaster Databricks](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Databricks Otwórz klastra")

2. W obszarze **konfiguracji** , kliknij pozycję **JDBC/ODBC** karcie i skopiuj wartości **nazwy hosta serwera** i **ścieżki HTTP**. Należy wykonać czynności opisane w tym artykule tych wartości.

    ![Uzyskiwanie konfiguracji Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Databricks pobrać konfiguracji")

3. Na komputerze, należy uruchomić **źródeł danych ODBC** aplikacji (32-bitowy lub 64-bitowy), w zależności od aplikacji. Aby połączyć się z programu Excel, użyj wersji 32-bitowej. Aby połączyć się z języków R i Python, użyj wersji 64-bitowej.

    ![Uruchamianie ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Uruchom aplikację ODBC")

4. W obszarze **DSN użytkownika** , kliknij pozycję **Dodaj**. W **Utwórz nowe źródło danych** okno dialogowe, wybierz opcję **sterownika ODBC Spark Simba**, a następnie kliknij przycisk **Zakończ**.

    ![Uruchamianie ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Uruchom aplikację ODBC")

5. W **sterownika ODBC Spark Simba** okna dialogowego wprowadź następujące wartości:

    ![Konfigurowanie nazwy DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "skonfigurować DSN")

    Poniższa tabela zawiera informacje o wartości w oknie dialogowym.
    
    |Pole  | Wartość  |
    |---------|---------|
    |**Nazwa źródła danych**     | Podaj nazwę dla źródła danych.        |
    |**Hostami**     | Podaj wartość skopiowany z obszaru roboczego Databricks dla *nazwy hosta serwera*.        |
    |**Port**     | Wprowadź *443*.        |
    |**Uwierzytelnianie** > **mechanizmu**     | Wybierz *nazwy użytkownika i hasła*.        |
    |**Nazwa użytkownika**     | Wprowadź *tokenu*.        |
    |**Hasło**     | Wprowadź wartość tokenu, które zostały skopiowane z obszaru roboczego Databricks. |
    
    W oknie dialogowym Ustawienia DSN, należy wykonać następujące dodatkowe czynności.
    
    * Kliknij przycisk **Options protokołu HTTP**. W otwartym oknie dialogowym Wklej wartość *ścieżki HTTP* skopiowany z Databricks obszaru roboczego. Kliknij przycisk **OK**.
    * Kliknij przycisk **opcji SSL**. W otwartym oknie dialogowym wybierz **Włącz SSL** pole wyboru. Kliknij przycisk **OK**.
    * Kliknij przycisk **Test** do testowania połączenia Azure Databricks. Kliknij przycisk **OK** Aby zapisać konfigurację.
    * W **Administrator źródła danych ODBC** okno dialogowe, kliknij przycisk **OK**.

Masz teraz Twojego DSN Konfigurowanie. W kolejnych sekcjach Użyj tej nazwy DSN do nawiązania połączenia z programu Excel, Python lub R. Azure Databricks

## <a name="connect-from-microsoft-excel"></a>Nawiązywanie połączenia z programu Microsoft Excel

W tej sekcji możesz pobierają dane z Azure Databricks do programu Microsoft Excel za pomocą nazwy DSN, utworzony wcześniej. Przed rozpoczęciem upewnij się, że masz zainstalowany na tym komputerze program Microsoft Excel. Można użyć programu Excel z wersji próbnej [łącze wersji próbnej programu Microsoft Excel](https://products.office.com/excel).

1. Otwórz pusty skoroszyt w programie Microsoft Excel. Z **danych** wstążki, kliknij przycisk **Pobierz dane**. Kliknij przycisk **z innych źródeł** , a następnie kliknij przycisk **z ODBC**.

    ![Uruchamianie ODBC z programu Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "uruchamianie ODBC z programu Excel")

2. W **z ODBC** okno dialogowe DSN, który został utworzony wcześniej, a następnie kliknij przycisk **OK**.

    ![Wybierz DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "wybierz DSN")

3. W przypadku wyświetlenia monitu o poświadczenia dla nazwy użytkownika wprowadź **tokenu**. Hasła Podaj wartość tokenu, którego został pobrany z obszaru roboczego Databricks.

    ![Podaj poświadczenia dla Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "wybierz DSN")

4. W oknie Nawigator wybierz tabelę w Databricks, który chcesz załadować do programu Excel, a następnie kliknij przycisk **obciążenia**. 

    ![Ładowanie dta do programu Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "dta obciążenia do programu Excel")

Po utworzeniu danych w skoroszycie programu Excel, można wykonać na nim operacji analitycznych.

## <a name="connect-from-r"></a>Nawiązywanie połączenia z języka R

W tej sekcji służy IDE języka R odwoływanie się do danych dostępne w Azure Databricks. Przed rozpoczęciem, musi mieć następujące zainstalowane na komputerze.

* IDE języka R. W tym artykule używa programu RStudio pulpitu. Możesz zainstalować ją z [pobieranie R Studio](https://www.rstudio.com/products/rstudio/download/).
* Jeśli używasz programu RStudio pulpitu jako środowiskiem IDE także zainstalować Microsoft R Client [ http://aka.ms/rclient/ ](http://aka.ms/rclient/). 

Otwórz programu RStudio i wykonaj następujące czynności:

- Odwołanie `RODBC` pakietu. Dzięki temu można nawiązać połączenia z Databricks Azure przy użyciu nazwy DSN utworzone wcześniej.
- Nawiąż połączenie przy użyciu nazwy DSN.
- Uruchom kwerendę SQL w danych w Azure Databricks. W poniższy fragment *radio_sample_data* jest tabela, która już istnieje w Azure Databricks.
- Wykonanie pewnych operacji w zapytaniu, aby zweryfikować dane wyjściowe. 

Poniższy fragment kodu wykonuje te zadania:

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

## <a name="connect-from-python"></a>Nawiązywanie połączenia z języka Python

W tej sekcji służy IDE języka Python (na przykład BEZCZYNNY) do dostępnych w usłudze Azure Databricks danych referencyjnych. Przed rozpoczęciem należy wykonać następujące wymagania wstępne:

* Zainstaluj środowisko Python z [tutaj](https://www.python.org/downloads/). Instalowanie języka Python z tego łącza instaluje BEZCZYNNY.

* W wierszu polecenia na komputerze, należy zainstalować `pyodbc` pakietu. Uruchom następujące polecenie:

      pip install pyodbc

Otwórz BEZCZYNNY i wykonaj następujące czynności:

- Importuj `pyodbc` pakietu. Dzięki temu można nawiązać połączenia z Databricks Azure przy użyciu nazwy DSN utworzone wcześniej.
- Nawiąż połączenie przy użyciu nazwy DSN utworzony wcześniej.
-  Uruchom zapytanie SQL przy użyciu połączenia utworzone. W poniższy fragment *radio_sample_data* jest tabela, która już istnieje w Azure Databricks.
- Wykonywanie operacji w zapytaniu, aby zweryfikować dane wyjściowe.

Poniższy fragment kodu wykonuje te zadania:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit = True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)

```

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać informacje dotyczące źródła, z których możesz zaimportować dane Azure Databricks, zobacz [źródła danych dla Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)


