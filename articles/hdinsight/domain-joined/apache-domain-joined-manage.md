---
title: Zarządzanie klastrami usługi HDInsight przy użyciu zabezpieczeń przedsiębiorstwa — Azure
description: Dowiedz się, jak zarządzać klastrami usługi Azure HDInsight za pomocą pakiet Enterprise Security.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: acf11b8107855c6f087f63f592cdbbad3766795e
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811022"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Zarządzanie klastrami usługi HDInsight przy użyciu pakiet Enterprise Security
Informacje o użytkownikach i rolach w usłudze HDInsight pakiet Enterprise Security (ESP) i sposobach zarządzania klastrami ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Łączenie z klastrem przyłączonym do domeny za pomocą programu VSCode

Można połączyć normalny klaster przy użyciu nazwy użytkownika zarządzanego przez Apache Ambari, a także połączyć Klaster Apache Hadoop zabezpieczeń przy użyciu nazwy użytkownika domeny (na przykład `user1@contoso.com`:).
1. Otwórz paletę poleceń, wybierając **klawisze Ctrl + Shift + P**, a następnie **wprowadź polecenie HDInsight: Połącz klaster**.

   ![Link klastra — polecenie](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Wprowadź adres URL klastra usługi HDInsight — > wejściowa nazwa użytkownika — > hasło wejściowe-> Wybierz typ klastra-> pokazuje informacje o powodzeniu, Jeśli weryfikacja przebiegła pomyślnie.
   
   ![okno dialogowe łączenia klastra](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > Połączona nazwa użytkownika i hasło są używane, Jeśli klaster jest zarejestrowany w ramach subskrypcji platformy Azure i połączony z klastrem. 
   
3. Można wyświetlić połączony klaster przy użyciu **listy poleceń klaster**. Teraz można przesłać skrypt do tego połączonego klastra.

   ![połączony klaster](./media/apache-domain-joined-manage/linked-cluster.png "połączony klaster")

4. Możesz również odłączyć klaster przez umieszczenie **usługi HDInsight: Odłącz klaster** od palety poleceń.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Łączenie z klastrem przyłączonym do domeny za pomocą środowiska IntelliJ

Normalny klaster można połączyć przy użyciu nazwy użytkownika Ambari Managed, a także połączyć klaster usługi Hadoop z zabezpieczeniami przy użyciu nazwy użytkownika domeny (na `user1@contoso.com`przykład:). 
1. Kliknij pozycję **Połącz klaster** z poziomu **Eksploratora platformy Azure**.

   ![menu kontekstowe linku klastra IntelliJ](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Wprowadź **nazwę klastra**, **nazwę użytkownika** i **hasło**. Jeśli wystąpił błąd uwierzytelniania, należy sprawdzić nazwę użytkownika i hasło. Opcjonalnie dodaj konto magazynu, klucz magazynu, a następnie wybierz kontener z kontenera magazynu. Informacje o magazynie są przeznaczone dla Eksploratora usługi Storage w lewym drzewie
   
   ![IntelliJ okna dialogowego klastra](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > Jeśli klaster jest zarejestrowany w ramach subskrypcji platformy Azure i połączony z klastrem, użyjemy podanego klucza magazynu, nazwy użytkownika i hasła.
   > ![Eksplorator magazynu w programie IntelliJ](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

   
3. Jeśli dane wejściowe są odpowiednie, w węźle usługi **HDInsight** można zobaczyć połączony klaster. Teraz można przesłać aplikację do tego połączonego klastra.

   ![IntelliJ połączonego klastra](./media/apache-domain-joined-manage/linked-cluster-intellij.png "połączony klaster IntelliJ]")

4. Możesz również odłączyć klaster od programu **Azure Explorer**.
   
   ![niepołączony klaster IntelliJ](./media/apache-domain-joined-manage/unlink.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Łączenie z klastrem przyłączonym do domeny za pomocą programu Eclipse

Normalny klaster można połączyć przy użyciu nazwy użytkownika Ambari Managed, a także połączyć klaster usługi Hadoop z zabezpieczeniami przy użyciu nazwy użytkownika domeny (na `user1@contoso.com`przykład:).
1. Kliknij pozycję **Połącz klaster** z poziomu **Eksploratora platformy Azure**.

   ![menu kontekstowe łączenia klastra — zaćmienie](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Wprowadź **nazwę klastra**, **nazwę użytkownika** i **hasło**, a następnie kliknij przycisk OK, aby połączyć klaster. Opcjonalnie wprowadź konto magazynu, klucz magazynu, a następnie wybierz pozycję kontener magazynu dla Eksploratora usługi Storage, aby rozpocząć działanie w widoku drzewa po lewej stronie
   
   ![okno dialogowe łączenia klastra](./media/apache-domain-joined-manage/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Jeśli klaster jest zarejestrowany w ramach subskrypcji platformy Azure i połączony z klastrem, użyjemy podanego klucza magazynu, nazwy użytkownika i hasła.
   > ![Eksplorator magazynu w programie zaćmienie](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Możesz zobaczyć połączony klaster w węźle usługi **HDInsight** po kliknięciu przycisku OK, jeśli dane wejściowe są prawidłowe. Teraz można przesłać aplikację do tego połączonego klastra.

   ![przezaćmienie połączonego klastra](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Możesz również odłączyć klaster od programu **Azure Explorer**.
   
   ![niepołączony klaster — przezaćmienie](./media/apache-domain-joined-manage/unlink.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Uzyskaj dostęp do klastrów przy użyciu pakiet Enterprise Security.

Pakiet Enterprise Security (wcześniej znany jako HDInsight Premium) zapewnia dostęp do klastra przez wiele użytkowników, w którym uwierzytelnianie jest wykonywane przez Active Directory i autoryzację przez usługi Apache Ranger i listy ACL magazynu (ADLS list ACL). Autoryzacja zapewnia bezpieczne granice między wieloma użytkownikami i zezwala na dostęp do danych tylko użytkownikom uprzywilejowanym na podstawie zasad autoryzacji.

Zabezpieczenia i izolacja użytkowników są ważne dla klastra usługi HDInsight z pakiet Enterprise Security. Aby spełnić te wymagania, dostęp SSH do klastra z pakiet Enterprise Security jest blokowany. W poniższej tabeli przedstawiono zalecane metody dostępu dla każdego typu klastra:

|Obciążenie|Scenariusz|Metoda dostępu|
|--------|--------|-------------|
|Apache Hadoop|Hive — zadania interaktywne/zapytania  |<ul><li>[Z usługi Beeline](#beeline)</li><li>[Widok Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC — Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Zadania interaktywne/zapytania, PySpark Interactive|<ul><li>[Z usługi Beeline](#beeline)</li><li>[Zeppelin z usługi Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Widok Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC — Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Scenariusze przetwarzania wsadowego — przesyłanie na platformie Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Zapytanie interaktywne (LLAP)|Interaktywne|<ul><li>[Z usługi Beeline](#beeline)</li><li>[Widok Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC — Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Any|Zainstaluj aplikację niestandardową|<ul><li>[Akcje skryptu](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter nie jest zainstalowana/obsługiwana w pakiet Enterprise Security.

Korzystanie z standardowych interfejsów API pomaga z perspektywą zabezpieczeń. Ponadto uzyskasz następujące korzyści:

1.  **Zarządzanie** — umożliwia zarządzanie kodem i Automatyzowanie zadań przy użyciu standardowych interfejsów API — usługi LIVY, HS2 itp.
2.  **Inspekcja** — za pomocą protokołu SSH nie ma żadnego sposobu na inspekcję, którzy użytkownicy są SSH do klastra. Nie będzie to miało miejsca w przypadku konstruowania zadań za pośrednictwem standardowych punktów końcowych, ponieważ będą one wykonywane w kontekście użytkownika. 



### <a name="beeline"></a>Użyj Z usługi Beeline 
Zainstaluj program Z usługi Beeline na maszynie i nawiąż połączenie za pośrednictwem publicznej sieci Internet, korzystając z następujących parametrów: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Jeśli zainstalowano program Z usługi Beeline lokalnie i nawiążesz połączenie za pośrednictwem Virtual Network platformy Azure, użyj następujących parametrów: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Aby znaleźć w pełni kwalifikowaną nazwę domeny węzła głównego, użyj informacji znajdujących się w dokumencie Zarządzanie usługą HDInsight przy użyciu Ambari interfejsu API REST.














## <a name="users-of-hdinsight-clusters-with-esp"></a>Użytkownicy klastrów usługi HDInsight z partycją ESP
Klaster usługi HDInsight inny niż ESP ma dwa konta użytkowników, które są tworzone podczas tworzenia klastra:

* **Ambari administrator**: To konto jest również znane jako *użytkownik usługi Hadoop* lub *użytkownik http*. Tego konta można użyć do zalogowania się do Ambari w https://&lt;ClusterName >. azurehdinsight. NET. Można go również użyć do uruchamiania zapytań w widokach Ambari, wykonywania zadań za pośrednictwem zewnętrznych narzędzi (na przykład PowerShell, Templeton, Visual Studio) i uwierzytelniania za pomocą sterownika programu Hive ODBC oraz narzędzi analizy biznesowej (na przykład Excel, Power BI lub Tableau).

Klaster usługi HDInsight z protokołem ESP ma trzech nowych użytkowników oprócz administratora Ambari.

* **Ranger administrator**:  To konto jest lokalnym kontem administratora Apache Ranger. Nie jest użytkownikiem domeny usługi Active Directory. Tego konta można użyć do skonfigurowania zasad i nawiązać innych administratorów lub administratorów delegowanych (tak, aby użytkownicy mogli zarządzać zasadami). Domyślnie nazwa użytkownika to *admin* , a hasło jest takie samo jak hasło administratora Ambari. Hasło można zaktualizować na stronie Ustawienia w Ranger.
* **Użytkownik domeny administratora klastra**: To konto jest użytkownikiem domeny usługi Active Directory wyznaczono jako administrator klastra Hadoop, w tym Ambari i Ranger. Należy podać poświadczenia tego użytkownika podczas tworzenia klastra. Ten użytkownik ma następujące uprawnienia:

  * Przyłączanie maszyn do domeny i umieszczanie ich w jednostce organizacyjnej określonej podczas tworzenia klastra.
  * Utwórz jednostki usługi w jednostce organizacyjnej, która została określona podczas tworzenia klastra.
  * Utwórz odwrotne wpisy DNS.

    Zwróć uwagę, że inni użytkownicy usługi AD mają również te uprawnienia.

    Istnieje kilka punktów końcowych w klastrze (na przykład Templeton), które nie są zarządzane przez Ranger i dlatego nie są bezpieczne. Te punkty końcowe są zablokowane dla wszystkich użytkowników z wyjątkiem użytkownika domeny administratora klastra.
* **Regularna**: Podczas tworzenia klastra można podać wiele grup usługi Active Directory. Użytkownicy w tych grupach są synchronizowani z Ranger i Ambari. Ci użytkownicy są użytkownikami domeny i mają dostęp tylko do Ranger punktów końcowych zarządzanych przez program (na przykład serwera hiveserver2). Wszystkie zasady i inspekcja RBAC będą stosowane dla tych użytkowników.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Role klastrów usługi HDInsight z partycją ESP
Usługa HDInsight pakiet Enterprise Security ma następujące role:

* Administrator klastra
* Operator klastra
* Administrator usługi
* Operator usługi
* Użytkownik klastra

**Aby wyświetlić uprawnienia tych ról**

1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. W menu po lewej stronie kliknij pozycję **role**.
3. Kliknij niebieski znak zapytania, aby wyświetlić uprawnienia:

    ![Uprawnienia ról usługi HDInsight ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Otwórz interfejs użytkownika zarządzania Ambari

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz klaster usługi HDInsight.
3. Kliknij pozycję **pulpit nawigacyjny** w górnym menu, aby otworzyć Ambari.
4. Zaloguj się do Ambari przy użyciu nazwy użytkownika i hasła administratora klastra.
5. Kliknij menu rozwijane **administrator** w prawym górnym rogu, a następnie kliknij pozycję **Zarządzaj Ambari**.

    ![Usługa ESP HDInsight — zarządzanie Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Interfejs użytkownika wygląda następująco:

    ![Interfejs użytkownika zarządzania Ambari usługi HDInsight ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Wyświetl listę użytkowników domeny synchronizowanych z Active Directory
1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. W menu po lewej stronie kliknij pozycję **Użytkownicy**. Zobaczysz wszystkich użytkowników synchronizowanych z Active Directory do klastra usługi HDInsight.

    ![Lista użytkowników listy UI usługi HDInsight Ambari Management](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Wyświetl listę grup domen synchronizowanych z Active Directory
1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. W menu po lewej stronie kliknij pozycję **grupy**. Zostaną wyświetlone wszystkie grupy synchronizowane z Active Directory do klastra usługi HDInsight.

    ![Grupy list UI usługi HDInsight Ambari Management](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurowanie uprawnień do widoków Hive
1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. W menu po lewej stronie kliknij pozycję **widoki**.
3. Kliknij pozycję **Hive** , aby wyświetlić szczegóły.

    ![Widoki programu Hive interfejsu użytkownika usługi HDInsight Ambari Management](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Kliknij link **Widok Hive** , aby skonfigurować widoki programu Hive.
5. Przewiń w dół do sekcji **uprawnienia** .

    ![Widoki programu Hive dla interfejsu użytkownika usługi HDInsight Ambari Management Konfigurowanie uprawnień](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Kliknij pozycję **Dodaj użytkownika** lub **Dodaj grupę**, a następnie określ użytkowników lub grupy, które mogą używać widoków Hive.

## <a name="configure-users-for-the-roles"></a>Konfigurowanie użytkowników dla ról
 Aby wyświetlić listę ról i ich uprawnień, zobacz Role klastrów usługi HDInsight z ESP.

1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. W menu po lewej stronie kliknij pozycję **role**.
3. Kliknij pozycję **Dodaj użytkownika** lub **Dodaj grupę** , aby przypisać użytkowników i grupy do różnych ról.

## <a name="next-steps"></a>Następne kroki
* Aby skonfigurować klaster usługi HDInsight z pakiet Enterprise Security, zobacz [Konfigurowanie klastrów usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-configure.md).
* Aby skonfigurować zasady Hive i uruchamiać zapytania programu Hive, zobacz [Konfigurowanie zasad Apache Hive dla klastrów usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-run-hive.md).
