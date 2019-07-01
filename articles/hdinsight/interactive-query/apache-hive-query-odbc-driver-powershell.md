---
title: Zapytanie Apache Hive za pomocą sterownika ODBC i program PowerShell — usługa Azure HDInsight
description: Użyj sterownika Microsoft Hive ODBC i programu PowerShell do wykonywania zapytań, klastry Apache Hive w usłudze Azure HDInsight.
keywords: hive, hive odbc i programu powershell
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: hrasheed
ms.openlocfilehash: b02c865e953861b5ac396538fdd0f0623b0e5428
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486102"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Samouczek: Zapytanie Apache Hive ODBC i programu PowerShell

Sterowniki ODBC firmy Microsoft zapewniają elastyczny sposób interakcji z różnych rodzajów źródeł danych, w tym Apache Hive. Pisanie kodu w językach skryptów, jak program PowerShell, które Użyj sterowników ODBC, aby nawiązać połączenie z klastrem Hive, przekazać wybrane zapytanie i wyświetlić wyniki.

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Pobieranie i instalowanie sterownika Hive ODBC firmy Microsoft
> * Tworzenie źródła danych programu Apache Hive ODBC, połączony z klastrem
> * Zapytanie przykładowe informacje z klastrem przy użyciu programu PowerShell

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* Klaser zapytania interaktywnego HDInsight. Aby go utworzyć, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Wybierz **Interactive Query** jako typ klastra.

## <a name="install-microsoft-hive-odbc-driver"></a>Zainstaluj sterownik Hive ODBC firmy Microsoft

Pobierz i zainstaluj [sterownika Microsoft Hive ODBC](https://go.microsoft.com/fwlink/?LinkID=286698).

## <a name="create-apache-hive-odbc-data-source"></a>Utwórz źródło danych Apache Hive ODBC

Poniższe kroki pokazują sposób tworzenia źródła danych ODBC usługi Apache Hive.

1. W Windows, przejdź do **Start** > **narzędzia administracyjne Windows** > **źródeł danych ODBC (32-bit)/(64-bit)** .  **Administrator źródła danych ODBC** zostanie otwarte okno.

    ![Administrator źródła danych OBDC](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "skonfigurować przy użyciu Administratora źródła danych ODBC DSN")

1. Z **DSN użytkownika** zaznacz **Dodaj** otworzyć **Utwórz nowe źródło danych** okna.

1. Wybierz **sterownika Microsoft Hive ODBC**, a następnie wybierz pozycję **Zakończ** otworzyć **Hive ODBC Driver DSN Instalator programu Microsoft** okna.

1. Wpisz lub wybierz poniższe wartości:

   | Właściwość | Opis |
   | --- | --- |
   |  Data Source Name (Nazwa źródła danych) |Nadaj nazwę źródła danych. |
   |  Hostami |Wprowadź polecenie `CLUSTERNAME.azurehdinsight.net`. Na przykład: `myHDICluster.azurehdinsight.net` |
   |  Port |Użyj portu **443**.|
   |  Database (Baza danych) |Użyj **domyślne**. |
   |  Mechanism (Mechanizm) |Wybierz **Windows, usługa Azure HDInsight** |
   |  Nazwa użytkownika |Wprowadź username użytkownika HTTP klastra HDInsight. Domyślna nazwa użytkownika to **admin**. |
   |  Hasło |Wprowadź hasło użytkownika klastra HDInsight. Zaznacz pole wyboru **Zapisz hasła (zaszyfrowane)** .|

1. Opcjonalnie: Wybierz **zaawansowane opcje**.  

   | Parametr | Opis |
   | --- | --- |
   |  Użyj zapytania natywne |Jeśli wybrano, sterownik ODBC nie podejmuje próby konwertować TSQL HiveQL. Użyj tej opcji tylko wtedy, gdy masz 100% się upewnić, że wysyłasz czystego instrukcje HiveQL. Podczas nawiązywania połączenia programu SQL Server lub usługi Azure SQL Database, należy pozostawić nie zaznaczone. |
   |  Pobrano następującą liczbę na blok wierszy |Podczas pobierania dużą liczbę rekordów, dostrajanie ten parametr może być konieczne zapewnienia optymalnej wydajności. |
   |  Domyślna długość kolumny ciąg, długość kolumny binarne, skala dziesiętną kolumny |Typ danych długości i opisie może mieć wpływ na sposób dane są zwracane. Spowodują niepoprawne informacje, które mają zostać zwrócone ze względu na utratę dokładności i obcięcie. |

    ![Zaawansowane opcje](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "DSN zaawansowane opcje konfiguracji")

1. Wybierz **Test** do testowania źródła danych. Gdy źródło danych jest skonfigurowany poprawnie, wynik testu pokazuje **Powodzenie**.  

1. Wybierz **OK** zamknięcie okna testów.  

1. Wybierz **OK** zamknąć **Hive ODBC Driver DSN Instalator programu Microsoft** okna.  

1. Wybierz **OK** zamknąć **Administrator źródła danych ODBC** okna.  

## <a name="query-data-with-powershell"></a>Wykonywanie zapytań o dane przy użyciu programu PowerShell

Poniższy skrypt programu PowerShell jest funkcją przez ODBC do wykonywania zapytań Hive klastra.

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

Poniższy fragment kodu używa funkcji powyżej, można wykonać zapytania na to klastra zapytania interakcyjnego, który został utworzony na początku tego samouczka. Zastąp `DATASOURCENAME` z **nazwa źródła danych** , którą podano na **Hive ODBC Driver DSN Instalator programu Microsoft** ekranu. Po wyświetleniu monitu o poświadczenia, wprowadź nazwę użytkownika i hasło wprowadzone w ramach **nazwa użytkownika logowania klastra** i **hasło logowania klastra** podczas tworzenia klastra.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, klaster HDInsight i konta magazynu. Aby to zrobić, wybierz grupę zasobów, w której został utworzony klaster, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób używania sterownika Microsoft Hive ODBC i programu PowerShell do pobierania danych z klastra usługi Azure HDInsight interakcyjnych zapytań.

> [!div class="nextstepaction"]
> [Łączenie programu Excel z Apache Hive przy użyciu interfejsu ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
