---
title: Zarządzanie klastrami pakietów zabezpieczeń w przedsiębiorstwie — Usługa Azure HDInsight
description: Dowiedz się, jak zarządzać klastrami usługi Azure HDInsight za pomocą pakietu zabezpieczeń przedsiębiorstwa.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 93880269edd72477f3aa85b2dbdc9d9f3ec8ef25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435951"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Zarządzanie klastrami HDInsight za pomocą pakietu zabezpieczeń przedsiębiorstwa

Dowiedz się więcej o użytkownikach i rolach w pakiecie zabezpieczeń dla przedsiębiorstw HDInsight (ESP) oraz o tym, jak zarządzać klastrami ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Łączenie z klastrem przyłączonym do domeny za pomocą programu VSCode

Możesz połączyć normalny klaster przy użyciu zarządzanej nazwy użytkownika Apache Ambari, a także połączyć `user1@contoso.com`klaster Apache Hadoop za pomocą nazwy użytkownika domeny (np. ).

1. Otwórz [program Visual Studio Code](https://code.visualstudio.com/). Upewnij się, że jest zainstalowane rozszerzenie [Spark & Hive Tools.](../hdinsight-for-vscode.md)

1. Wykonaj kroki z [Łącze klastra](../hdinsight-for-vscode.md#link-a-cluster) dla programu Visual Studio Code.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Łączenie z klastrem przyłączonym do domeny za pomocą środowiska IntelliJ

Możesz połączyć normalny klaster przy użyciu zarządzanej nazwy użytkownika Ambari, a także `user1@contoso.com`połączyć klaster zabezpieczeń hadoop przy użyciu nazwy użytkownika domeny (np. ).

1. Otwórz środowisko IntelliJ IDEA. Upewnij [się,](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) że wszystkie wymagania wstępne są spełnione.

1. Postępuj zgodnie z instrukcjami z [Link klastra](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) dla IntelliJ.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Łączenie z klastrem przyłączonym do domeny za pomocą programu Eclipse

Możesz połączyć normalny klaster przy użyciu zarządzanej nazwy użytkownika Ambari, a także `user1@contoso.com`połączyć klaster zabezpieczeń hadoop przy użyciu nazwy użytkownika domeny (np. ).

1. Otwórz program Eclipse. Upewnij [się,](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) że wszystkie wymagania wstępne są spełnione.

1. Postępuj zgodnie z instrukcjami z [link klastra](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) dla programu Eclipse.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Dostęp do klastrów za pomocą pakietu zabezpieczeń przedsiębiorstwa

Pakiet zabezpieczeń przedsiębiorstwa (wcześniej znany jako HDInsight Premium) zapewnia dostęp wielu użytkowników do klastra, gdzie uwierzytelnianie odbywa się za pomocą usługi Active Directory i autoryzacji przez apache Ranger and Storage ALs (ADLS). Autoryzacja zapewnia bezpieczne granice między wieloma użytkownikami i umożliwia tylko uprzywilejowanym użytkownikom dostęp do danych na podstawie zasad autoryzacji.

Zabezpieczenia i izolacja użytkowników są ważne dla klastra HDInsight z pakietem zabezpieczeń przedsiębiorstwa. Aby spełnić te wymagania, dostęp SSH do klastra z pakietem zabezpieczeń przedsiębiorstwa jest zablokowany. W poniższej tabeli przedstawiono zalecane metody dostępu dla każdego typu klastra:

|Obciążenie|Scenariusz|Metoda dostępu|
|--------|--------|-------------|
|Apache Hadoop|Gałąź — interaktywne zadania/zapytania  |<ul><li>[Beeline](#beeline)</li><li>[widok programu Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Narzędzia programu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interaktywne zadania/zapytania, PySpark interaktywne|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin z Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[widok programu Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Narzędzia programu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Scenariusze wsadowe — przesyłanie platformy Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Zapytanie interaktywne (LLAP)|Interaktywne|<ul><li>[Beeline](#beeline)</li><li>[widok programu Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Narzędzia programu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Dowolne|Instalowanie aplikacji niestandardowej|<ul><li>[Akcje skryptu](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter nie jest zainstalowany/obsługiwany w pakiecie zabezpieczeń przedsiębiorstwa.

Korzystanie ze standardowych interfejsów API pomaga z punktu widzenia zabezpieczeń. Otrzymasz również następujące korzyści:

- **Zarządzanie** — można zarządzać kodem i zautomatyzować zadania przy użyciu standardowych interfejsów API — Livy, HS2 itp.
- **Inspekcja** — z SSH, nie ma sposobu, aby inspekcji, które użytkownicy SSH'd do klastra. Nie byłoby to w przypadku, gdy zadania są konstruowane za pośrednictwem standardowych punktów końcowych, ponieważ będą one wykonywane w kontekście użytkownika.

### <a name="use-beeline"></a><a name="beeline"></a>Korzystanie z usługi Beeline

Zainstaluj Beeline na swoim komputerze i połącz się przez publiczny Internet, użyj następujących parametrów:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Jeśli beeline jest instalowany lokalnie i łączysz się za pośrednictwem sieci wirtualnej platformy Azure, użyj następujących parametrów:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Aby znaleźć w pełni kwalifikowaną nazwę domeny headnode, użyj informacji w zarządzania hdinsight przy użyciu dokumentu interfejsu API AMbari REST.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Użytkownicy klastrów HDInsight z ESP

Klaster nieobjęty usługią ESP HDInsight ma dwa konta użytkowników, które są tworzone podczas tworzenia klastra:

- **Ambari admin**: To konto jest również znany jako *użytkownik Hadoop* lub *użytkownika HTTP*. To konto może służyć do logowania się `https://CLUSTERNAME.azurehdinsight.net`do Ambari w . Może również służyć do uruchamiania zapytań w widokach Ambari, wykonywania zadań za pomocą narzędzi zewnętrznych (na przykład PowerShell, Templeton, Visual Studio) i uwierzytelniania za pomocą sterownika HIVE ODBC i narzędzi BI (na przykład Excel, Power BI lub Tableau).

Klaster HDInsight z ESP ma trzech nowych użytkowników oprócz Administratora Ambari.

- **Ranger admin**: To konto jest lokalnym kontem administratora Apache Ranger. Nie jest to użytkownik domeny usługi active directory. To konto może służyć do konfigurowania zasad i tworzenia administratorów innych użytkowników lub administratorów delegowanych (tak aby ci użytkownicy mogli zarządzać zasadami). Domyślnie nazwa użytkownika jest *administratorem,* a hasło jest takie samo jak hasło administratora Ambari. Hasło można zaktualizować na stronie Ustawienia w Ranger.

- **Użytkownik domeny administratora klastra**: To konto jest użytkownikiem domeny usługi active directory wyznaczonym jako administrator klastra Hadoop, w tym Ambari i Ranger. Poświadczenia tego użytkownika należy podać podczas tworzenia klastra. Ten użytkownik ma następujące uprawnienia:
    - Dołącz do maszyn do domeny i umieść je w ou, który określisz podczas tworzenia klastra.
    - Tworzenie podmiotów usługi w ramach jednostki organizacyjnej, które można określić podczas tworzenia klastra.
    - Tworzenie odwrotnych wpisów DNS.

    Należy zauważyć, że inni użytkownicy usługi AD również mają te uprawnienia.

    Istnieje kilka punktów końcowych w klastrze (na przykład Templeton), które nie są zarządzane przez Ranger, a zatem nie są bezpieczne. Te punkty końcowe są zablokowane dla wszystkich użytkowników z wyjątkiem użytkownika domeny administratora klastra.

- **Regularne**: Podczas tworzenia klastra można udostępnić wiele grup usługi active directory. Użytkownicy w tych grupach są synchronizowane z Ranger i Ambari. Ci użytkownicy są użytkownikami domeny i mają dostęp tylko do punktów końcowych zarządzanych przez Ranger (na przykład Hiveserver2). Wszystkie zasady RBAC i inspekcji będą miały zastosowanie do tych użytkowników.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Role klastrów HDInsight z ESP

Pakiet zabezpieczeń HDInsight Enterprise ma następujące role:

- Administrator klastra
- Operator klastra
- Administrator usługi
- Operator serwisu
- Użytkownik klastra

**Aby wyświetlić uprawnienia tych ról**

1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwieranie interfejsu użytkownika zarządzania ambari](#open-the-ambari-management-ui).
2. W menu po lewej stronie wybierz pozycję **Role**.
3. Wybierz niebieski znak zapytania, aby wyświetlić uprawnienia:

    ![Uprawnienia ról ESP HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Otwórz interfejs użytkownika zarządzania Ambari

1. Przejdź `https://CLUSTERNAME.azurehdinsight.net/` do miejsca, w którym nazwa klastra jest nazwą klastra.
1. Zaloguj się do ambari przy użyciu nazwy użytkownika domeny administratora klastra i hasła.
1. Wybierz menu rozwijane **administratora** w prawym górnym rogu, a następnie wybierz pozycję **Zarządzaj ambari**.

    ![ESP HDInsight zarządzać Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Interfejs użytkownika wygląda następująco:

    ![Interfejs użytkownika zarządzania ESP HDInsight Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Wyświetlanie listy użytkowników domeny zsynchronizowanych z usługi Active Directory

1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwieranie interfejsu użytkownika zarządzania ambari](#open-the-ambari-management-ui).
2. Z lewego menu wybierz pozycję **Użytkownicy**. Zobaczysz wszystkich użytkowników zsynchronizowanych z usługi Active Directory do klastra HDInsight.

    ![ESP HDInsight Ambari zarządzanie ui lista użytkowników](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Wyświetlanie listy grup domen zsynchronizowanych z usługi Active Directory

1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwieranie interfejsu użytkownika zarządzania ambari](#open-the-ambari-management-ui).
2. Z lewego menu wybierz pozycję **Grupy**. Zostaną wyświetlone wszystkie grupy zsynchronizowane z usługi Active Directory z klastrem HDInsight.

    ![Grupy list interfejsu użytkownika zarządzania zarządzania ESP HDInsight Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurowanie uprawnień widoków gałęzi

1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwieranie interfejsu użytkownika zarządzania ambari](#open-the-ambari-management-ui).
2. Z lewego menu wybierz pozycję **Widoki**.
3. Wybierz **opcję HIVE,** aby wyświetlić szczegóły.

    ![Widoki gałęzi interfejsu użytkownika zarządzania zarządzania ESP HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Wybierz **łącze Widok gałęzi,** aby skonfigurować widoki gałęzi.
5. Przewiń w dół do sekcji **Uprawnienia.**

    ![Widoki gałęzi interfejsu użytkownika interfejsu użytkownika zarządzania ESP HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Wybierz **pozycję Dodaj użytkownika** lub Dodaj **grupę,** a następnie określ użytkowników lub grupy, które mogą używać widoków gałęzi.

## <a name="configure-users-for-the-roles"></a>Konfigurowanie użytkowników dla ról

 Aby wyświetlić listę ról i ich uprawnień, zobacz Role klastrów HDInsight z ESP.

1. Otwórz interfejs użytkownika zarządzania Ambari.  Zobacz [Otwieranie interfejsu użytkownika zarządzania ambari](#open-the-ambari-management-ui).
2. W menu po lewej stronie wybierz pozycję **Role**.
3. Wybierz **pozycję Dodaj użytkownika** lub Dodaj **grupę,** aby przypisać użytkowników i grupy do różnych ról.

## <a name="next-steps"></a>Następne kroki

- Aby skonfigurować klaster HDInsight z pakietem zabezpieczeń przedsiębiorstwa, zobacz [Konfigurowanie klastrów HDInsight za pomocą pliku ESP](apache-domain-joined-configure.md).
- Aby skonfigurować zasady gałęzi i uruchomić kwerendy gałęzi, zobacz [Konfigurowanie zasad gałęzi apache dla klastrów usługi HDInsight za pomocą usługi ESP](apache-domain-joined-run-hive.md).
