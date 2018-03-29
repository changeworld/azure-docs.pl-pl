---
title: Zarządzanie przyłączonych do domeny w usłudze hdinsight - Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie zarządzania klastrami HDInsight przyłączonych do domeny
services: hdinsight
documentationcenter: ''
author: bprakash
manager: jhubbard
editor: cgronlun
tags: ''
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2018
ms.author: bhanupr
ms.openlocfilehash: 44202541557a7513e0068f52289a637f6e48f43f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="manage-domain-joined-hdinsight-clusters"></a>Zarządzanie klastrami HDInsight przyłączonych do domeny
Dowiedz się, użytkownicy i role w HDInsight przyłączonych do domeny oraz sposób zarządzania klastrami HDInsight przyłączonych do domeny.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Dostęp do klastrów z pakietu zabezpieczeń organizacji.

Pakiet zabezpieczeń przedsiębiorstwa (wcześniej znane jako HDInsight Premium) zapewnia dostęp wielu użytkowników do klastra, gdy uwierzytelnianie jest wykonywane przez usługę Active Directory i autoryzacji przez zakres Apache i przechowywania listy kontroli dostępu (ACL ADLS). Autoryzacji zapewnia bezpieczny granice przez wielu użytkowników oraz umożliwia tylko użytkownicy o odpowiednich uprawnieniach dostępu do danych na podstawie zasad autoryzacji.

Izolacji zabezpieczeń i użytkowników są ważne w przypadku klastra HDInsight z pakietu zabezpieczeń organizacji. Aby spełnić te wymagania, SSH dostęp do klastra z pakietem zabezpieczeń przedsiębiorstwa jest blokowany. W poniższej tabeli przedstawiono metody dostępu zalecane dla każdego typu klastra:

|Obciążenie|Scenariusz|Metody dostępu|
|--------|--------|-------------|
|Hadoop|Gałąź — interakcyjnych zadania/zapytań |<ul><li>[Beeline](#beeline)</li><li>[Widok gałęzi](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|platforma Spark|Zadania interakcyjnych/zapytań, PySpark interakcyjne|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin z Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Widok gałęzi](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|platforma Spark|Scenariusze partii — przesyłania Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Zapytanie interakcyjne (LLAP)|Interaktywne|<ul><li>[Beeline](#beeline)</li><li>[Widok gałęzi](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Dowolne|Instalowanie niestandardowych aplikacji|<ul><li>[Akcje skryptu](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|


Pomaga przy użyciu standardowych interfejsów API z punktu widzenia zabezpieczeń. Ponadto można uzyskać następujące korzyści:

1.  **Zarządzanie** — możesz zarządzać kodem i automatyzowania zadań przy użyciu standardowych interfejsów API — Livy, HS2 itd.
2.  **Inspekcja** — przy użyciu protokołu SSH, nie istnieje sposób inspekcji, które użytkownicy SSH miał do klastra. Nie będą sytuacji, gdy zadania są utworzony za pośrednictwem standardowych punktów końcowych, ponieważ może być wykonywane w kontekście użytkownika. 



### <a name="beeline"></a>Użyj Beeline 
Zainstaluj Beeline na tym komputerze i nawiązywać połączeń za pośrednictwem publicznej sieci internet, użyj następujących parametrów: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Jeśli masz Beeline zainstalowane lokalnie, a połączenie za pośrednictwem sieci wirtualnej platformy Azure, należy użyć poniższych parametrów: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Aby znaleźć nazwę FQDN headnode, skorzystaj z informacji w usłudze HDInsight zarządzania, przy użyciu interfejsu API REST Ambari dokumentu.














## <a name="users-of-domain-joined-hdinsight-clusters"></a>Użytkownicy domeny w usłudze hdinsight
Klaster usługi HDInsight, który nie jest przyłączony do domeny ma dwa konta użytkownika, które są tworzone podczas tworzenia klastra:

* **Administrator Ambari**: to konto jest także znana jako *użytkownika Hadoop* lub *użytkowników HTTP*. To konto może służyć do logowania się do narzędzia Ambari w https://&lt;clustername >. azurehdinsight.net. Można go również służyć do uruchamiać zapytania dotyczące widoków Ambari, wykonaj zadania za pomocą narzędzi zewnętrznych (na przykład programu PowerShell, Templeton, Visual Studio) i uwierzytelniania za pomocą sterownika ODBC programu Hive i narzędzi do analizy Biznesowej (na przykład Excel usługi Power Bi i Tableau).

Klaster HDInsight przyłączonych do domeny ma trzy nowi użytkownicy oprócz Ambari administratora.

* **Zakres admin**: to konto jest Apache zakres konta administratora lokalnego. Nie jest użytkownikiem domeny usługi active directory. To konto można skonfigurować zasady i innych użytkowników Administratorzy lub delegowani administratorzy (tak aby tym użytkownikom można zarządzać zasadami). Domyślnie nazwa użytkownika jest *admin* i hasło jest taka sama jak Ambari hasło administratora. Hasło może zostać zaktualizowana ze strony ustawień w zakres.
* **Użytkownik domeny administratora klastra**: jest to konto użytkownika domeny usługi active directory, wyznaczony jako administratora klastra usługi Hadoop w tym Ambari i zakres. Musisz podać poświadczenia użytkownika podczas tworzenia klastra. Ten użytkownik ma następujące uprawnienia:

  * Przyłączanie komputerów do domeny i umieścić je w jednostce Organizacyjnej, który jest określany podczas tworzenia klastra.
  * Utwórz jednostki usługi w jednostce Organizacyjnej, który jest określany podczas tworzenia klastra.
  * Utworzenie wpisów DNS odwrotnej.

    Należy pamiętać, że inni użytkownicy AD również ma tych uprawnień.

    Brak niektórych punktów końcowych w klastrze (na przykład Templeton), które nie są zarządzane przez zakres i dlatego nie jest bezpieczna. Te punkty końcowe są zablokowany dla wszystkich użytkowników z wyjątkiem użytkownika domeny administratora klastra.
* **Regularne**: podczas tworzenia klastra, możesz podać wiele grup usługi active directory. Użytkownicy w tych grupach są zsynchronizowane z zakres i narzędzia Ambari. Ci użytkownicy są użytkownikami domeny i mają dostęp do tylko zarządzane zakres punktów końcowych (na przykład serwera Hiveserver2). Wszystkie zasady RBAC i inspekcji będą mieć zastosowanie do tych użytkowników.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>Role z klastrów HDInsight przyłączonych do domeny
HDInsight przyłączonych do domeny mają następujące role:

* Administrator klastra
* Operator klastra
* Administrator usługi
* Operator usługi
* Użytkownika klastra

**Aby sprawdzić uprawnienia tych ról**

1. Otwórz przystawkę Zarządzanie Ambari interfejsu użytkownika.  Zobacz [otworzyć Interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. Z menu po lewej stronie kliknij **ról**.
3. Kliknij niebieski znak zapytania, aby sprawdzić uprawnienia:

    ![Przyłączonych do domeny uprawnienia ról usługi HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Otwórz przystawkę Zarządzanie Ambari interfejsu użytkownika

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz z klastrem usługi HDInsight. Zobacz [klastrów listy i Pokaż](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Kliknij przycisk **pulpitu nawigacyjnego** z górnego menu, aby otworzyć narzędzie Ambari.
4. Zaloguj się przy użyciu nazwy użytkownika domeny administratora klastra i hasła narzędzia Ambari.
5. Kliknij przycisk **Admin** menu rozwijane w górnym prawym rogu, a następnie kliknij **Zarządzanie Ambari**.

    ![HDInsight przyłączonych do domeny zarządzania Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Interfejs użytkownika wygląda następująco:

    ![Przyłączonych do domeny zarządzania HDInsight Ambari interfejsu użytkownika](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Lista użytkowników domeny synchronizowane z usługi Active Directory
1. Otwórz przystawkę Zarządzanie Ambari interfejsu użytkownika.  Zobacz [otworzyć Interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. Z menu po lewej stronie kliknij **użytkowników**. Powinna wyświetlić wszystkich użytkowników, które są synchronizowane z usługi Active Directory do klastra usługi HDInsight.

    ![Przyłączonych do domeny HDInsight Ambari zarządzania interfejsu użytkownika lista użytkowników](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Lista grup w domenie synchronizowane z usługi Active Directory
1. Otwórz przystawkę Zarządzanie Ambari interfejsu użytkownika.  Zobacz [otworzyć Interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. Z menu po lewej stronie kliknij **grup**. Zostanie wyświetlona wszystkich grup, które są synchronizowane z usługi Active Directory do klastra usługi HDInsight.

    ![Przyłączonych do domeny HDInsight Ambari zarządzania interfejsu użytkownika listy grup](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurowanie uprawnień widoki Hive
1. Otwórz przystawkę Zarządzanie Ambari interfejsu użytkownika.  Zobacz [otworzyć Interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. Z menu po lewej stronie kliknij **widoków**.
3. Kliknij przycisk **HIVE** Aby wyświetlić szczegóły.

    ![Przyłączonych do domeny zarządzania HDInsight Ambari widoki Hive interfejsu użytkownika](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Kliknij przycisk **Hive View** link do skonfigurowania widoki Hive.
5. Przewiń w dół do **uprawnienia** sekcji.

    ![Przyłączonych do domeny zarządzania HDInsight Ambari widoki Hive interfejsu użytkownika konfigurowania uprawnień](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Kliknij przycisk **Dodaj użytkownika** lub **Dodaj grupę**, a następnie określ użytkowników lub grupy, które mogą używać widoki Hive.

## <a name="configure-users-for-the-roles"></a>Konfigurowanie użytkowników dla ról
 Aby wyświetlić listę ról i uprawnień, zobacz [role domeny w usłudze hdinsight](#roles-of-domain---joined-hdinsight-clusters).

1. Otwórz przystawkę Zarządzanie Ambari interfejsu użytkownika.  Zobacz [otworzyć Interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. Z menu po lewej stronie kliknij **ról**.
3. Kliknij przycisk **Dodaj użytkownika** lub **Dodaj grupę** można przypisać użytkowników i grup dla różnych ról.

## <a name="next-steps"></a>Kolejne kroki
* Aby skonfigurować przyłączony do domeny klaster usługi HDInsight, zobacz [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Konfigurowanie przyłączonych do domeny klastrów usługi HDInsight).
* Aby znaleźć informacje na temat konfigurowania zasad Hive i uruchamiania kwerend Hive, zobacz [Konfigurowanie zasad usługi Hive dla przyłączonych do domeny klastrów usługi HDInsight](apache-domain-joined-run-hive.md).
