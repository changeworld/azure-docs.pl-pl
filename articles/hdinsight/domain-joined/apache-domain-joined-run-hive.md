---
title: Konfigurowanie zasad Hive w usłudze HDInsight przy użyciu pakiet Enterprise Security platformy Azure
description: Dowiedz się, jak skonfigurować zasady Apache Ranger dla usługi Hive w usłudze Azure HDInsight przy użyciu pakiet Enterprise Security.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b0213fc1a96b38b615cbd8b7b6374a6716b9f840
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918196"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Konfigurowanie zasad technologii Apache Hive w usłudze HDInsight przy użyciu pakietu Enterprise Security
Dowiedz się, jak skonfigurować zasady Apache Ranger dla Apache Hive. Korzystając z tego artykułu, utworzysz dwie zasady platformy Ranger, aby ograniczyć dostęp do tabeli hivesampletable. Tabela hivesampletable jest dołączana do klastrów usługi HDInsight. Po skonfigurowaniu zasad łączysz się z tabelami usługi Hive w usłudze HDInsight przy użyciu sterownika ODBC i programu Excel.

## <a name="prerequisites"></a>Wymagania wstępne
* Klaster usługi HDInsight z pakiet Enterprise Security. Zobacz [Konfigurowanie klastrów usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-configure.md).
* Stacja robocza z pakietem Office 2016, pakietem Office 2013 Professional Plus, usługą Office 365 Pro Plus, programem Excel 2013 Standalone lub pakietem Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Łączenie z interfejsem użytkownika administratora platformy Apache Ranger
**Aby połączyć się z interfejsem użytkownika administratora platformy Ranger**

1. W przeglądarce połącz się z interfejsem użytkownika administratora platformy Ranger. Adres URL: https://&lt;nazwa_klastra>.azurehdinsight.net/Ranger/.

   > [!NOTE]  
   > Ranger używa innych poświadczeń niż Klaster Apache Hadoop. Aby uniemożliwić przeglądarkom korzystanie z zbuforowanych poświadczeń usługi Hadoop, Użyj nowego okna przeglądarki InPrivate do nawiązania połączenia z interfejsem użytkownika administratora Ranger.

2. Zaloguj się za pomocą nazwy i hasła użytkownika domeny administratora klastra:

    ![Strona główna usługi HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Obecnie platforma Ranger współpracuje tylko z usługami Yarn i Hive.

## <a name="create-domain-users"></a>Tworzenie użytkowników domeny
Więcej informacji na temat tworzenia hiveruser1 i hiveuser2 można znaleźć w temacie [Tworzenie klastra usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp). Te dwa konta użytkowników są używane w tym artykule.

## <a name="create-ranger-policies"></a>Tworzenie zasad platformy Ranger
W tej sekcji utworzysz dwie zasady Ranger na potrzeby uzyskiwania dostępu do hivesampletable. Możesz przydzielić uprawnienie select (wybór) do innego zestawu kolumn. Dwóch użytkowników zostało utworzonych przy użyciu polecenia [Utwórz klaster usługi HDInsight z partycją ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp). W następnej sekcji przetestujesz dwie zasady w programie Excel.

**Aby utworzyć zasady platformy Ranger**

1. Otwórz interfejs użytkownika administratora platformy Ranger. Zobacz Nawiązywanie połączenia z interfejsem użytkownika programu Apache Ranger admin.
2. Kliknij pozycję **&lt;nazwa_klastra>_hive** w obszarze **Hive**. Zostaną wyświetlone dwie wstępnie skonfigurowane zasady.
3. Kliknij pozycję **Add New Policy** (Dodaj nowe zasady), a następnie wprowadź następujące wartości:

   * Policy Name (Nazwa zasad): read-hivesampletable-all
   * Hive Database (Baza danych Hive): default
   * table (tabela): hivesampletable
   * Hive Column (Kolumna usługi Hive): *
   * Select User (Wybierz użytkownika): hiveuser1
   * Permissions (Uprawnienia): select

     ![Konfiguracja zasad Hive ESP Ranger usługi HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

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
 | Nazwa użytkownika | Wprowadź polecenie hiveuser1@contoso158.onmicrosoft.com. Zaktualizuj nazwę domeny, jeśli jest inna. |
 | Hasło | Wprowadź hasło użytkownika hiveuser1. |

Pamiętaj o kliknięciu przycisku **Test** (Testuj) przed zapisaniem źródła danych.

## <a name="import-data-into-excel-from-hdinsight"></a>Importowanie danych do programu Excel z usługi HDInsight
W ostatniej sekcji zostały skonfigurowane dwie zasady.  Użytkownik hiveuser1 ma uprawnienia select (wybór) we wszystkich kolumnach, a użytkownik hiveuser2 ma uprawnienia select w dwóch kolumnach. W tej sekcji będziesz personifikować dwóch użytkowników w celu zaimportowania danych do programu Excel.

1. Otwórz nowy lub istniejący skoroszyt w programie Excel.
2. Na karcie **Dane** kliknij pozycję **Z innych źródeł danych**, a następnie kliknij pozycję **Z Kreatora połączenia danych**, aby uruchomić **Kreatora połączenia danych**.

    ![Otwieranie Kreatora połączenia danych][img-hdi-simbahiveodbc.excel.dataconnection]
3. Wybierz źródło danych **ODBC DSN**, a następnie kliknij przycisk **Dalej**.
4. Spośród źródeł danych ODBC wybierz nazwę źródła danych utworzonego w poprzednim kroku, a następnie kliknij przycisk **Dalej**.
5. Wprowadź ponownie hasło klastra w kreatorze, a następnie kliknij przycisk **OK**. Zaczekaj na otwarcie okna dialogowego **Wybieranie bazy danych i tabeli**. Może to potrwać kilka sekund.
6. Wybierz pozycję **hivesampletable**, a następnie kliknij przycisk **Dalej**.
7. Kliknij przycisk **Finish** (Zakończ).
8. W oknie dialogowym **Importowanie danych** możesz zmienić lub określić zapytanie. W tym celu kliknij pozycję **Właściwości**. Może to potrwać kilka sekund.
9. Kliknij kartę **Definicje**. Tekst polecenia to:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Zgodnie ze zdefiniowanymi zasadami platformy Ranger użytkownik hiveuser1 ma uprawnienia select (wybór) we wszystkich kolumnach.  To zapytanie działa z poświadczeniami hiveuser1's, ale to zapytanie nie działa z poświadczeniami użytkownika hiveuser2.

   ![Właściwości połączenia][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Kliknij przycisk **OK**, aby zamknąć okno dialogowe Właściwości połączenia.
11. Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Importowanie danych**.  
12. Wprowadź ponownie hasło użytkownika hiveuser1, a następnie kliknij przycisk **OK**. Importowanie danych do programu Excel może potrwać kilka sekund. Po zakończeniu zobaczysz 11 kolumn danych.

Aby przetestować drugie zasady (Read-hivesampletable-devicemake), utworzone w ostatniej sekcji

1. Dodaj nowy arkusz w programie Excel.
2. Wykonaj kroki ostatniej procedury, aby zaimportować dane.  Jedyną zmianą jest użycie poświadczeń użytkownika hiveuser2 zamiast hiveuser1's. To nie powiedzie się, ponieważ tylko hiveuser2 ma uprawnienia do wyświetlania dwóch kolumn. Wystąpi następujący błąd:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Wykonaj kroki tej samej procedury, aby zaimportować dane. Tym razem użyj poświadczeń użytkownika hiveuser2 i zmień instrukcję select z:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    na:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Po zakończeniu zobaczysz dwie kolumny zaimportowanych danych.

## <a name="next-steps"></a>Następne kroki
* Aby skonfigurować klaster usługi HDInsight z pakiet Enterprise Security, zobacz [Konfigurowanie klastrów usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-configure.md).
* Aby zarządzać klastrem usługi HDInsight przy użyciu protokołu ESP, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-manage.md).
* Aby uruchamiać zapytania programu Hive przy użyciu protokołu SSH w klastrach usługi HDInsight z protokołem ESP, zobacz [Używanie protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Aby nawiązać połączenie z usługą Hive przy użyciu usługi Hive JDBC, zobacz [nawiązywanie połączenia z Apache Hive w usłudze Azure HDInsight przy użyciu sterownika JDBC Hive](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Aby połączyć program Excel z usługą Hadoop przy użyciu interfejsu ODBC platformy Hive, zobacz [Connect Excel to Apache Hadoop z dyskiem ODBC Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Aby połączyć program Excel z usługą Hadoop przy użyciu Power Query, zobacz [Connect Excel to Apache Hadoop przy użyciu Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
