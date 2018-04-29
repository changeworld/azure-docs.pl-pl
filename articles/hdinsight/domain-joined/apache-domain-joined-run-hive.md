---
title: Konfigurowanie zasad Hive w usłudze HDInsight przyłączonych do domeny - Azure
description: Dowiedz się...
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3fade1e5-c2e1-4ad5-b371-f95caea23f6d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: bd99e5fda80663b37c60d972742b16c27b92cf55
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="configure-hive-policies-in-domain-joined-hdinsight"></a>Konfigurowanie zasad Hive w usłudze HDInsight z przyłączonych do domeny
Dowiedz się, jak skonfigurować zasady platformy Apache Ranger dla usługi Hive. Korzystając z tego artykułu, utworzysz dwie zasady platformy Ranger, aby ograniczyć dostęp do tabeli hivesampletable. Tabela hivesampletable jest dołączana do klastrów usługi HDInsight. Po skonfigurowaniu zasad łączysz się z tabelami usługi Hive w usłudze HDInsight przy użyciu sterownika ODBC i programu Excel.

## <a name="prerequisites"></a>Wymagania wstępne
* Przyłączony do domeny klaster usługi HDInsight. Zobacz [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Konfigurowanie przyłączonych do domeny klastrów usługi HDInsight).
* Stacja robocza z pakietem Office 2016, pakietem Office 2013 Professional Plus, usługą Office 365 Pro Plus, programem Excel 2013 Standalone lub pakietem Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Łączenie z interfejsem użytkownika administratora platformy Apache Ranger
**Aby połączyć się z interfejsem użytkownika administratora platformy Ranger**

1. W przeglądarce połącz się z interfejsem użytkownika administratora platformy Ranger. Adres URL: https://&lt;nazwa_klastra>.azurehdinsight.net/Ranger/.

   > [!NOTE]
   > Poświadczenia używane na platformie Ranger są inne niż w klastrze usługi Hadoop. Aby zapobiec przeglądarki przy użyciu buforowanych poświadczeń Hadoop, należy użyć nowe okno przeglądarki InPrivate do nawiązania połączenia interfejsu użytkownika administratora zakres.
   >
   >
2. Zaloguj się za pomocą nazwy i hasła użytkownika domeny administratora klastra:

    ![Strona główna platformy Ranger z przyłączonym do domeny klastrem usługi HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Obecnie platforma Ranger współpracuje tylko z usługami Yarn i Hive.

## <a name="create-domain-users"></a>Tworzenie użytkowników domeny
Zobacz [utworzyć klaster HDInsight przyłączonych do domeny](apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster), aby uzyskać informacje dotyczące sposobu tworzenia hiveruser1 i hiveuser2. Używasz dwóch kont w tym samouczku.

## <a name="create-ranger-policies"></a>Tworzenie zasad platformy Ranger
W tej sekcji utworzysz dwie zasady zakres do uzyskiwania dostępu do hivesampletable. Możesz przydzielić uprawnienie select (wybór) do innego zestawu kolumn. Użytkownicy zostały utworzone przy użyciu [utworzyć klaster HDInsight przyłączonych do domeny](apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster). W następnej sekcji przetestujesz dwie zasady w programie Excel.

**Aby utworzyć zasady platformy Ranger**

1. Otwórz interfejs użytkownika administratora platformy Ranger. Zobacz sekcję [Łączenie z interfejsem użytkownika administratora platformy Apache Ranger](#connect-to-apache-ranager-admin-ui).
2. Kliknij pozycję **&lt;nazwa_klastra>_hive** w obszarze **Hive**. Zostaną wyświetlone dwie wstępnie skonfigurowane zasady.
3. Kliknij pozycję **Add New Policy** (Dodaj nowe zasady), a następnie wprowadź następujące wartości:

   * Policy Name (Nazwa zasad): read-hivesampletable-all
   * Hive Database (Baza danych Hive): default
   * table (tabela): hivesampletable
   * Hive Column (Kolumna usługi Hive): *
   * Select User (Wybierz użytkownika): hiveuser1
   * Permissions (Uprawnienia): select

     ![Konfigurowanie zasad usługi Hive na platformie Ranger z przyłączonym do domeny klastrem usługi HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]
     > Jeśli pole Select User (Wybierz użytkownika) nie zostało wypełnione przy użyciu danych użytkownika domeny, zaczekaj kilka minut na zsynchronizowanie platformy Ranger z usługą AAD.
     >
     >
4. Kliknij pozycję **Add** (Dodaj), aby zapisać zasady.
5. Powtórz dwa ostatnie kroki, aby utworzyć inne zasady o następujących właściwościach:

   * Policy Name (Nazwa zasad): read-hivesampletable-devicemake
   * Hive Database (Baza danych Hive): default
   * table (tabela): hivesampletable
   * Hive Column (Kolumna usługi Hive): clientid, devicemake
   * Select User (Wybierz użytkownika): hiveuser2
   * Permissions (Uprawnienia): select

## <a name="create-hive-odbc-data-source"></a>Tworzenie źródła danych ODBC usługi Hive
Instrukcje można znaleźć w sekcji [Create Hive ODBC data source](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (Tworzenie źródła danych ODBC usługi Hive).  

 | Właściwość  |Opis |
 | --- | --- |
 | Data Source Name (Nazwa źródła danych) | Nadaj nazwę źródła danych. |
 | Host | Wprowadź ciąg &lt;nazwa_klastra_usługi_HDInsight>.azurehdinsight.net, np. myHDICluster.azurehdinsight.net. |
 | Port | Użyj portu **443**. (Ten port został zmieniony z 563 na 443). |
 | Database (Baza danych) | Użyj wartości **Default** (Domyślna). |
 | Hive Server Type (Typ serwera Hive) | Wybierz wartość **Hive Server 2**. |
 | Mechanism (Mechanizm) | Wybierz wartość **Azure HDInsight Service** (Usługa Azure HDInsight). |
 | HTTP Path (Ścieżka HTTP) | Pozostaw to pole puste. |
 | Nazwa użytkownika | Wprowadź hiveuser1@contoso158.onmicrosoft.com. Zaktualizuj nazwę domeny, jeśli jest inny. |
 | Hasło | Wprowadź hasło użytkownika hiveuser1. |

Pamiętaj o kliknięciu przycisku **Test** (Testuj) przed zapisaniem źródła danych.

## <a name="import-data-into-excel-from-hdinsight"></a>Importowanie danych do programu Excel z usługi HDInsight
W ostatniej sekcji zostały skonfigurowane dwie zasady.  Użytkownik hiveuser1 ma uprawnienia select (wybór) we wszystkich kolumnach, a użytkownik hiveuser2 ma uprawnienia select w dwóch kolumnach. W tej sekcji będziesz personifikować dwóch użytkowników w celu zaimportowania danych do programu Excel.

1. Otwórz nowy lub istniejący skoroszyt w programie Excel.
2. Na karcie **Dane** kliknij pozycję **Z innych źródeł danych**, a następnie kliknij pozycję **Z Kreatora połączenia danych**, aby uruchomić **Kreatora połączenia danych**.

    ![Otwieranie Kreatora połączenia danych][img-hdi-simbahiveodbc.excel.dataconnection]
3. Wybierz źródło danych **ODBC DSN**, a następnie kliknij przycisk **Dalej**.
4. Spośród źródeł danych ODBC wybierz nazwę źródła danych utworzonego w poprzednim kroku, a następnie kliknij przycisk **Dalej**.
5. Wprowadź ponownie hasło do klastra w kreatorze, a następnie kliknij przycisk **OK**. Zaczekaj na otwarcie okna dialogowego **Wybieranie bazy danych i tabeli**. Może to potrwać kilka sekund.
6. Wybierz pozycję **hivesampletable**, a następnie kliknij przycisk **Dalej**.
7. Kliknij przycisk **Finish** (Zakończ).
8. W oknie dialogowym **Importowanie danych** możesz zmienić lub określić zapytanie. W tym celu kliknij pozycję **Właściwości**. Może to potrwać kilka sekund.
9. Kliknij kartę **Definicje**. Tekst polecenia to:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Zgodnie ze zdefiniowanymi zasadami platformy Ranger użytkownik hiveuser1 ma uprawnienia select (wybór) we wszystkich kolumnach.  Tak to zapytanie działa przy użyciu poświadczeń firmy hiveuser1, ale to zapytanie nie działa z poświadczeniami hiveuser2 firmy.

   ![Właściwości połączenia][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Kliknij przycisk **OK**, aby zamknąć okno dialogowe Właściwości połączenia.
11. Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Importowanie danych**.  
12. Wprowadź ponownie hasło użytkownika hiveuser1, a następnie kliknij przycisk **OK**. Importowanie danych do programu Excel może potrwać kilka sekund. Po zakończeniu zobaczysz 11 kolumn danych.

Aby przetestować drugim zasad (odczyt hivesampletable-devicemake), został utworzony w ostatniej sekcji

1. Dodaj nowy arkusz w programie Excel.
2. Wykonaj kroki ostatniej procedury, aby zaimportować dane.  Jest tylko zmiany wprowadzone na używanie poświadczeń w hiveuser2 zamiast hiveuser1 firmy. To nie powiedzie się, ponieważ hiveuser2 ma tylko uprawnienia do wyświetlania dwóch kolumn. Wystąpi następujący błąd:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Wykonaj kroki tej samej procedury, aby zaimportować dane. Tym razem użyj poświadczeń użytkownika hiveuser2 i zmień instrukcję select z:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    na:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Po zakończeniu zobaczysz dwie kolumny zaimportowanych danych.

## <a name="next-steps"></a>Kolejne kroki
* Aby skonfigurować przyłączony do domeny klaster usługi HDInsight, zobacz [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Konfigurowanie przyłączonych do domeny klastrów usługi HDInsight).
* Aby zarządzanie klastrem HDInsight przyłączonych do domeny, zobacz [przyłączonych do domeny zarządzania w usłudze hdinsight](apache-domain-joined-manage.md).
* Do uruchamiania zapytań Hive przy użyciu protokołu SSH w klastrach HDInsight przyłączonych do domeny, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Aby połączyć się z usługą Hive za pomocą interfejsu JDBC usługi Hive, zobacz [Connect to Hive on Azure HDInsight using the Hive JDBC driver](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md) (Łączenie z usługą Hive w usłudze Azure HDInsight przy użyciu sterownika JDBC usługi Hive).
* Aby połączyć program Excel z usługą Hadoop przy użyciu interfejsu ODBC usługi Hive, zobacz [Connect Excel to Hadoop with the Microsoft Hive ODBC drive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (Łączenie programu Excel z usługą Hadoop przy użyciu sterownika Microsoft Hive ODBC).
* Aby połączyć program Excel z usługą Hadoop przy użyciu dodatku Power Query, zobacz [Connect Excel to Hadoop by using Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md) (Łączenie programu Excel z usługą Hadoop przy użyciu dodatku Power Query).
