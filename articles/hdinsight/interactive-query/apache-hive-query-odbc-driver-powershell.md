---
title: Rozmedij gałąź apache ze sterownikiem ODBC & programu PowerShell — usługa Azure HDInsight
description: Użyj sterownika ODBC hive firmy Microsoft i programu PowerShell do wykonywania zapytań dotyczących klastrów gałęzi Apache w usłudze Azure HDInsight.
keywords: ul,ul odbc,powershell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.openlocfilehash: f6139bb98fa0272e43c8e180d4ec029f7a7538bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73494324"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Samouczek: Zapytanie Apache Hive z ODBC i PowerShell

Sterowniki ODBC firmy Microsoft zapewniają elastyczny sposób interakcji z różnymi rodzajami źródeł danych, w tym z gałęzią Apache. Można napisać kod w językach skryptów, takich jak PowerShell, które używają sterowników ODBC, aby otworzyć połączenie z klastrem gałęzi, przekazać zapytanie wybranej i wyświetlić wyniki.

W tym samouczku wykonaj następujące zadania:

> [!div class="checklist"]
> * Pobieranie i instalowanie sterownika Microsoft Hive ODBC
> * Tworzenie źródła danych OdBC gałęzi Apache połączonego z klastrem
> * Zapytanie przykładowych informacji z klastra przy użyciu programu PowerShell

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* Interaktywny klaster zapytań w programie HDInsight. Aby go utworzyć, zobacz [Wprowadzenie do usługi Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Wybierz **opcję Zapytanie interaktywne** jako typ klastra.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalowanie sterownika ODBC firmy Microsoft Hive

Pobierz i zainstaluj [sterownik OdBC firmy Microsoft Hive](https://www.microsoft.com/download/details.aspx?id=40886).

## <a name="create-apache-hive-odbc-data-source"></a>Tworzenie źródła danych Apache Hive ODBC

W poniższych krokach pokazano, jak utworzyć źródło danych OdBC gałęzi Apache.

1. W systemie Windows przejdź do **pozycji Uruchom** > **narzędzia** > administracyjne systemu Windows Źródła danych**ODBC (32-bitowe)/(64-bitowe)**.  Zostanie otwarte okno **Administrator źródła danych ODBC.**

    ![Administrator źródła danych OBDC](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "Konfigurowanie sieci DSN przy użyciu administratora źródła danych ODBC")

1. Na karcie **Nazwa DSN użytkownika** wybierz pozycję **Dodaj,** aby otworzyć okno **Utwórz nowe źródło danych.**

1. Wybierz pozycję **Sterownik ODBC gałęzi firmy Microsoft,** a następnie wybierz pozycję **Zakończ,** aby otworzyć okno **Instalatora dsn sterownika ODBC sterownika odbc firmy Microsoft.**

1. Wpisz lub wybierz poniższe wartości:

   | Właściwość | Opis |
   | --- | --- |
   |  Data Source Name (Nazwa źródła danych) |Nadaj nazwę źródła danych. |
   |  Hostami |Wprowadź polecenie `CLUSTERNAME.azurehdinsight.net`. Na przykład: `myHDICluster.azurehdinsight.net` |
   |  Port |Użyj portu **443**.|
   |  baza danych |Użyj **domyślnego**pliku . |
   |  Mechanism (Mechanizm) |Wybieranie **usługi HDInsight systemu Windows Azure** |
   |  Nazwa użytkownika |Wprowadź nazwę użytkownika HTTP klastra HDInsight. Domyślna nazwa użytkownika to **admin**. |
   |  Hasło |Wprowadź hasło użytkownika klastra HDInsight. Zaznacz pole wyboru **Zapisz hasło (zaszyfrowane).**|

1. Opcjonalnie: wybierz **opcję Opcje zaawansowane**.  

   | Parametr | Opis |
   | --- | --- |
   |  Użyj kwerendy natywnej |Po wybraniu sterownika ODBC NIE próbuje przekonwertować TSQL na HiveQL. Użyj tej opcji tylko wtedy, gdy masz 100% pewności, że przesyłasz czyste instrukcje HiveQL. Podczas łączenia się z programem SQL Server lub usługą Azure SQL Database należy pozostawić ją niezaznaczoną. |
   |  Wiersze pobrane na blok |Podczas pobierania dużej liczby rekordów, dostrajanie tego parametru może być wymagane w celu zapewnienia optymalnej wydajności. |
   |  Domyślna długość kolumny ciągu, Długość kolumny binarnej, Skala kolumny dziesiętnej |Długości i precyzje typu danych mogą mieć wpływ na sposób zwracania danych. Powodują one nieprawidłowe informacje do zwrotu z powodu utraty precyzji i obcinania. |

    ![Zaawansowane opcje konfiguracji DSN](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "Zaawansowane opcje konfiguracji DSN")

1. Wybierz **opcję Testuj,** aby przetestować źródło danych. Gdy źródło danych jest poprawnie skonfigurowane, wynik testu pokazuje **SUKCES**.  

1. Wybierz **przycisk OK,** aby zamknąć okno Test.  

1. Wybierz **przycisk OK,** aby zamknąć okno **Instalatora dsn sterownika ODBC sterownika odbc firmy Microsoft.**  

1. Wybierz **przycisk OK,** aby zamknąć okno **Administrator źródła danych ODBC.**  

## <a name="query-data-with-powershell"></a>Zapytanie o dane za pomocą programu PowerShell

Następujący skrypt programu PowerShell jest funkcją, którą ODBC wysyła do klastra hive.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

Poniższy fragment kodu używa powyższej funkcji do wykonania kwerendy w klastrze zapytań interaktywnych utworzonych na początku samouczka. Zastąp `DATASOURCENAME` **nazwą źródła danych określoną** na ekranie Instalator **dsn sterownika ODBC sterownika odbc** firmy Microsoft. Po wyświetleniu monitu o podanie poświadczeń wprowadź nazwę użytkownika i hasło wprowadzone w obszarze **Nazwa użytkownika logowania klastra** i **hasło logowania klastra** podczas tworzenia klastra.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebna, usuń grupę zasobów, klaster HDInsight i konto magazynu. Aby to zrobić, zaznacz grupę zasobów, w której został utworzony klaster, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak używać sterownika ODBC i programu PowerShell do pobierania danych z klastra zapytań interaktywnych usługi Azure HDInsight.

> [!div class="nextstepaction"]
> [Łączenie programu Excel z gałęzią Apache przy użyciu funkcji ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
