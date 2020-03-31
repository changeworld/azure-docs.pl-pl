---
title: Zasady dotyczące gałęzi Apache w Apache Ranger — usługa Azure HDInsight
description: Dowiedz się, jak skonfigurować zasady Apache Ranger dla gałęzi w usłudze Azure HDInsight z pakietem zabezpieczeń przedsiębiorstwa.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 90d7da9c8ddd8c9c595f2209dcc34e2f595acfd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196930"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Konfigurowanie zasad technologii Apache Hive w usłudze HDInsight przy użyciu pakietu Enterprise Security

Dowiedz się, jak skonfigurować zasady Apache Ranger dla gałęzi Apache. Korzystając z tego artykułu, utworzysz dwie zasady platformy Ranger, aby ograniczyć dostęp do tabeli hivesampletable. Tabela hivesampletable jest dołączana do klastrów usługi HDInsight. Po skonfigurowaniu zasad można połączyć się ze tabelami hive w programie HDInsight za pomocą sterownika Excel i ODBC.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster HDInsight z pakietem zabezpieczeń przedsiębiorstwa. Zobacz [Konfigurowanie klastrów HDInsight za pomocą pliku ESP](apache-domain-joined-configure.md).
* Stacja robocza z pakietem Office 2016, pakietem Office 2013 Professional Plus, usługą Office 365 Pro Plus, programem Excel 2013 Standalone lub pakietem Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Łączenie z interfejsem użytkownika administratora platformy Apache Ranger
**Aby połączyć się z interfejsem użytkownika administratora platformy Ranger**

1. Z przeglądarki przejdź do interfejsu użytkownika administratora rangera, w `https://CLUSTERNAME.azurehdinsight.net/Ranger/` którym CLUSTERNAME jest nazwą klastra.

   > [!NOTE]  
   > Ranger używa innych poświadczeń niż apache Hadoop klastra. Aby zapobiec przeglądarkom korzystającym z buforowanych poświadczeń Hadoop, użyj nowego okna przeglądarki InPrivate, aby połączyć się z interfejsem użytkownika administratora rangera.

2. Zaloguj się za pomocą nazwy i hasła użytkownika domeny administratora klastra:

    ![Strona główna HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Obecnie platforma Ranger współpracuje tylko z usługami Yarn i Hive.

## <a name="create-domain-users"></a>Tworzenie użytkowników domeny

Zobacz [Tworzenie klastra HDInsight z ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp), aby uzyskać informacje na temat tworzenia hiveruser1 i hiveuser2. Użyj dwóch kont użytkowników w tym artykule.

## <a name="create-ranger-policies"></a>Tworzenie zasad platformy Ranger

W tej sekcji utworzysz dwie zasady Ranger dostępu do pouci. Możesz przydzielić uprawnienie select (wybór) do innego zestawu kolumn. Obaj użytkownicy zostali stworzeni przy użyciu [programu Utwórz klaster HDInsight z esp](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). W następnej sekcji przetestujesz dwie zasady w programie Excel.

**Aby utworzyć zasady platformy Ranger**

1. Otwórz interfejs użytkownika administratora platformy Ranger. Zobacz sekcję Łączenie z interfejsem użytkownika administratora platformy Apache Ranger.
2. Wybierz **CLUSTERNAME_Hive**, w obszarze **Gałąź**. Zostaną wyświetlone dwie wstępnie skonfigurowane zasady.
3. Wybierz **pozycję Dodaj nową zasadę,** a następnie wprowadź następujące wartości:

    |Właściwość |Wartość |
    |---|---|
    |Policy Name (Nazwa zasad)|odczyt-ulamożerne-wszystko|
    |Baza danych gałęzi|default|
    |tabela|rozkropażalne pokrzyżowe|
    |Kolumna gałęzi|*|
    |Select User (Wybierz użytkownika)|uleuser1|
    |Uprawnienia|Wybierz|

    ![Konfiguracja zasad hive programu HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > Jeśli pole Select User (Wybierz użytkownika) nie zostało wypełnione przy użyciu danych użytkownika domeny, zaczekaj kilka minut na zsynchronizowanie platformy Ranger z usługą AAD.

4. Wybierz **pozycję Dodaj,** aby zapisać zasady.

5. Powtórz dwa ostatnie kroki, aby utworzyć inne zasady o następujących właściwościach:

    |Właściwość |Wartość |
    |---|---|
    |Policy Name (Nazwa zasad)|odczyt-hivesampletable-devicemake|
    |Baza danych gałęzi|default|
    |tabela|rozkropażalne pokrzyżowe|
    |Kolumna Gałąź|clientid, devicemake|
    |Select User (Wybierz użytkownika)|uleuser2|
    |Uprawnienia|Wybierz|

## <a name="create-hive-odbc-data-source"></a>Tworzenie źródła danych ODBC usługi Hive

Instrukcje można znaleźć w sekcji [Create Hive ODBC data source](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (Tworzenie źródła danych ODBC usługi Hive).  

 | Właściwość  |Opis |
 | --- | --- |
 | Data Source Name (Nazwa źródła danych) | Nadaj nazwę źródła danych. |
 | Host | Wprowadź CLUSTERNAME.azurehdinsight.net. np. myHDICluster.azurehdinsight.net. |
 | Port | Użyj portu **443**. (Ten port został zmieniony z 563 na 443). |
 | baza danych | Użyj wartości **Default** (Domyślna). |
 | Hive Server Type (Typ serwera Hive) | Wybierz wartość **Hive Server 2**. |
 | Mechanism (Mechanizm) | Wybierz wartość **Azure HDInsight Service** (Usługa Azure HDInsight). |
 | HTTP Path (Ścieżka HTTP) | Pozostaw to pole puste. |
 | Nazwa użytkownika | Wprowadź polecenie hiveuser1@contoso158.onmicrosoft.com. Zaktualizuj nazwę domeny, jeśli jest inna. |
 | Hasło | Wprowadź hasło użytkownika hiveuser1. |

Pamiętaj o kliknięciu przycisku **Test** (Testuj) przed zapisaniem źródła danych.

## <a name="import-data-into-excel-from-hdinsight"></a>Importowanie danych do programu Excel z usługi HDInsight

W ostatniej sekcji skonfigurowano dwie zasady.  Użytkownik hiveuser1 ma uprawnienia select (wybór) we wszystkich kolumnach, a użytkownik hiveuser2 ma uprawnienia select w dwóch kolumnach. W tej sekcji będziesz personifikować dwóch użytkowników w celu zaimportowania danych do programu Excel.

1. Otwórz nowy lub istniejący skoroszyt w programie Excel.

1. Na karcie **Dane** przejdź do pozycji **Pobierz dane** > **z innych źródeł** > **z odbc,** aby uruchomić okno **OdBC.**

    ![Kreator otwierania połączenia danych](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. Z listy rozwijanej wybierz nazwę źródła danych utworzoną w ostatniej sekcji, a następnie wybierz przycisk **OK**.

1. Przy pierwszym użyciu zostanie otwarte okno dialogowe **sterownika ODBC.** Wybierz **pozycję Windows** z lewego menu. Następnie wybierz pozycję **Połącz,** aby otworzyć okno **Nawigator.**

1. Zaczekaj na otwarcie okna dialogowego **Wybieranie bazy danych i tabeli**. Może to potrwać kilka sekund.

1. Wybierz **opcję rozjemczalna**, a następnie wybierz pozycję **Dalej**.

1. Wybierz **pozycję Zakończ**.

1. W oknie dialogowym **Importowanie danych** możesz zmienić lub określić zapytanie. Aby to zrobić, wybierz polecenie **Właściwości**. Może to potrwać kilka sekund.

1. Wybierz kartę **Definicja.** Tekst polecenia to:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Zgodnie ze zdefiniowanymi zasadami platformy Ranger użytkownik hiveuser1 ma uprawnienia select (wybór) we wszystkich kolumnach.  Więc ta kwerenda działa z poświadczeniami hiveuser1, ale ta kwerenda nie działa z poświadczeniami hiveuser2.

1. Wybierz **przycisk OK,** aby zamknąć okno dialogowe Właściwości połączenia.

1. Wybierz **przycisk OK,** aby zamknąć okno dialogowe **Importowanie danych.**  

1. Wprowadź ponownie hasło użytkownika hiveuser1, a następnie kliknij przycisk **OK**. Importowanie danych do programu Excel może potrwać kilka sekund. Po zakończeniu zobaczysz 11 kolumn danych.

Aby przetestować drugą zasadę (odczyt-pouplepianie-devicemake), utworzono w ostatniej sekcji

1. Dodaj nowy arkusz w programie Excel.
2. Wykonaj kroki ostatniej procedury, aby zaimportować dane.  Jedyną zmianą, którą wprowadzasz, jest użycie poświadczeń hiveuser2 zamiast pounień1. To nie powiedzie się, ponieważ hiveuser2 ma tylko uprawnienia, aby zobaczyć dwie kolumny. Wystąpi następujący błąd:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
        
3. Wykonaj kroki tej samej procedury, aby zaimportować dane. Tym razem użyj poświadczeń użytkownika hiveuser2 i zmień instrukcję select z:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    na:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Po zakończeniu zobaczysz dwie kolumny importowanych danych.

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować klaster HDInsight z pakietem zabezpieczeń przedsiębiorstwa, zobacz [Konfigurowanie klastrów HDInsight za pomocą pliku ESP](apache-domain-joined-configure.md).
* Aby zarządzać klastrem HDInsight za pomocą esp, zobacz [Zarządzanie klastrami HDInsight za pomocą esp](apache-domain-joined-manage.md).
* Aby uzyskać uruchamianie zapytań hive przy użyciu SSH w klastrach HDInsight z ESP, zobacz [Używanie SSH z HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
* Aby połączyć gałąź przy użyciu hive JDBC, zobacz [Łączenie się z gałęzią apache na platformie Azure HDInsight przy użyciu sterownika JDBC hive](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Aby połączyć program Excel z programem Hadoop przy użyciu funkcji Hive ODBC, zobacz [Łączenie programu Excel z apache Hadoop za pomocą dysku Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Aby połączyć program Excel z programem Hadoop za pomocą dodatku Power Query, zobacz [Łączenie programu Excel z programem Apache Hadoop przy użyciu dodatku Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
