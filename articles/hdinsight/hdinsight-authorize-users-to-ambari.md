---
title: Autoryzowanie użytkowników do widoków Ambari — Azure HDInsight
description: Jak zarządzać uprawnieniami użytkowników i grup Ambari dla klastrów usługi HDInsight z włączonym protokołem ESP.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: hrasheed
ms.openlocfilehash: 533bd750056f2e961ca9239e995fbfc62b2381d0
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076681"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autoryzowanie użytkowników na potrzeby widoków Apache Ambari

[Pakiet Enterprise Security (ESP) włączone klastry usługi HDInsight](./domain-joined/hdinsight-security-overview.md) zapewniają możliwości klasy korporacyjnej, w tym uwierzytelnianie oparte na Azure Active Directory. Można [synchronizować nowych użytkowników](hdinsight-sync-aad-users-to-cluster.md) dodanych do grup usługi Azure AD, które uzyskały dostęp do klastra, co umożliwia tym określonym użytkownikom wykonywanie określonych akcji. Praca z użytkownikami, grupami i uprawnieniami w programie [Apache Ambari](https://ambari.apache.org/) jest obsługiwana zarówno w przypadku klastrów ESP, jak i standardowych klastrów usługi HDInsight.

Active Directory użytkownicy mogą logować się do węzłów klastra przy użyciu ich poświadczeń domeny. Mogą również używać poświadczeń domeny do uwierzytelniania interakcji klastra z innymi zatwierdzonymi punktami końcowymi, takimi jak [barwa](https://gethue.com/), widoki AMBARI, ODBC, JDBC, PowerShell i interfejsy API REST.

> [!WARNING]  
> Nie należy zmieniać hasła licznika Ambari (hdinsightwatchdog) w klastrze usługi HDInsight opartym na systemie Linux. Zmiana hasła zrywa możliwość korzystania z akcji skryptu lub wykonywania operacji skalowania w klastrze.

Jeśli jeszcze tego nie zrobiono, postępuj zgodnie z [tymi instrukcjami](./domain-joined/apache-domain-joined-configure.md) , aby zainicjować obsługę nowego klastra ESP.

## <a name="access-the-ambari-management-page"></a>Dostęp do strony zarządzania Ambari

Aby przejść do **strony Ambari Management** w [interfejsie użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md), przejdź **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** do. Wprowadź nazwę użytkownika i hasło administratora klastra zdefiniowane podczas tworzenia klastra. Następnie na pulpicie nawigacyjnym Ambari wybierz pozycję **Zarządzaj Ambari** pod menu **administrator** :

![Zarządzanie pulpitem nawigacyjnym Apache Ambari](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Przyznawanie uprawnień do Apache Hive widoków

Ambari zawiera wystąpienia widoku dla [Apache Hive](https://hive.apache.org/) i [Apache tez](https://tez.apache.org/), między innymi. Aby udzielić dostępu do co najmniej jednego wystąpienia widoku programu Hive, przejdź do **strony zarządzania Ambari**.

1. Na stronie Zarządzanie wybierz link **widoki** w nagłówku menu **widoki** po lewej stronie.

    ![Linki widoku widoków Apache Ambari](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. Na stronie widoki Rozwiń wiersz **Hive** . Istnieje jeden domyślny widok programu Hive tworzony podczas dodawania usługi Hive do klastra. W razie konieczności można także utworzyć więcej wystąpień widoku programu Hive. Wybierz widok Hive:

    ![Widoki usługi HDInsight — widok Apache Hive](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Przewiń w dół strony widoku. W sekcji *uprawnienia* dostępne są dwie opcje przyznania użytkownikom domeny ich uprawnienia do widoku:

**Udziel uprawnień tym użytkownikom** ![Udziel uprawnień tym użytkownikom](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Udziel uprawnień tym grupom** ![Udziel uprawnień tym grupom](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Aby dodać użytkownika, wybierz przycisk **Dodaj użytkownika** .

   * Zacznij wpisywać nazwę użytkownika i zobaczysz listę rozwijaną o wcześniej zdefiniowanych nazwach.

     ![Funkcja autouzupełniania użytkownika Apache Ambari](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Wybierz lub Zakończ wpisywanie nazwy użytkownika. Aby dodać tę nazwę użytkownika jako nowego użytkownika, wybierz przycisk **Nowy** .

   * Aby zapisać zmiany, zaznacz **niebieskie pole wyboru**.

     ![Przyznanie uprawnień użytkownika Apache Ambari](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Aby dodać grupę, wybierz przycisk **Dodaj grupę** .

   * Zacznij wpisywać nazwę grupy. Proces wybierania istniejącej nazwy grupy lub dodawania nowej grupy jest taki sam jak w przypadku dodawania użytkowników.
   * Aby zapisać zmiany, zaznacz **niebieskie pole wyboru**.

     ![Uprawnienia do przyznawania Apache Ambari](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

Dodawanie użytkowników bezpośrednio do widoku jest przydatne, gdy chcesz przypisać uprawnienia użytkownikowi do korzystania z tego widoku, ale nie chcesz, aby były członkiem grupy, która ma dodatkowe uprawnienia. Aby zmniejszyć ilość narzutów administracyjnych, można uprościć przypisywanie uprawnień do grup.

## <a name="grant-permissions-to-apache-tez-views"></a>Przyznawanie uprawnień do widoków Apache TEZ

Wystąpienia widoków [Apache tez](https://tez.apache.org/) umożliwiają użytkownikom monitorowanie i debugowanie wszystkich zadań tez, przesłanych przez zapytania [Apache Hive](https://hive.apache.org/) i skrypty [Apache wieprz](https://pig.apache.org/) . Istnieje jedno domyślne wystąpienie widoku tez, które jest tworzone podczas aprowizacji klastra.

Aby przypisać użytkowników i grupy do wystąpienia widoku tez, rozwiń wiersz **tez** na stronie widoki, jak opisano wcześniej.

![Widoki usługi HDInsight — widok Apache Tez](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Aby dodać użytkowników lub grupy, powtórz kroki 3-5 w poprzedniej sekcji.

## <a name="assign-users-to-roles"></a>Przypisywanie użytkowników do ról

Istnieją pięć ról zabezpieczeń dla użytkowników i grup, które są wymienione w kolejności zmniejszania uprawnień dostępu:

* Administrator klastra
* Operator klastra
* Administrator usługi
* Operator usługi
* Użytkownik klastra

Aby zarządzać rolami, przejdź do **strony zarządzania Ambari**, a następnie wybierz link **role** w grupie menu *klastry* po lewej stronie.

![Linki menu ról Apache Ambari](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Aby wyświetlić listę uprawnień przyznanych dla każdej roli, kliknij niebieski znak zapytania obok nagłówka tabeli **role** na stronie role.

![Uprawnienia do menu ról Apache Ambari](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Uprawnienia do menu ról Apache Ambari")

Na tej stronie istnieją dwa różne widoki, których można użyć do zarządzania rolami dla użytkowników i grup: Blok i lista.

### <a name="block-view"></a>Widok bloku

Widok blok przedstawia każdą rolę we własnym wierszu i udostępnia opcje **Przypisz role do tych użytkowników** i **przypisuje role do tych grup** , jak opisano wcześniej.

![Widok bloku ról Apache Ambari](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>Widok listy

Widok listy umożliwia szybkie edytowanie funkcji w dwóch kategoriach: Użytkownicy i grupy.

* Kategoria użytkownicy w widoku listy wyświetla listę wszystkich użytkowników, co pozwala na wybranie roli dla każdego użytkownika z listy rozwijanej.

    ![Widok listy ról Apache Ambari — użytkownicy](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  Kategoria grupy w widoku listy wyświetla wszystkie grupy i rolę przypisaną do każdej grupy. W naszym przykładzie lista grup jest synchronizowana z grup usługi Azure AD określonych we właściwości **dostęp do grupy użytkowników** w ustawieniach domeny klastra. Zobacz [Tworzenie klastra usługi HDInsight z włączoną funkcją ESP](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Widok listy ról Apache Ambari — grupy](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na powyższym obrazie Grupa "hiveusers" ma przypisaną rolę *użytkownika klastra* . Jest to rola tylko do odczytu, która umożliwia użytkownikom tej grupy wyświetlanie, ale nie Zmienianie konfiguracji usług i metryk klastra.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Zaloguj się do Ambari jako użytkownik tylko do wyświetlania

Przypisano Ci uprawnienia użytkownika domeny usługi Azure AD "użytkownik hiveuser1" do widoków Hive i tez. Po uruchomieniu interfejsu użytkownika sieci Web Ambari i wprowadzeniu poświadczeń domeny tego użytkownika (nazwa użytkownika usługi Azure AD w formacie poczty e-mail i hasła) użytkownik zostanie przekierowany do strony widoków Ambari. W tym miejscu użytkownik może wybrać dowolny dostępny widok. Użytkownik nie może odwiedzić żadnej innej części witryny, w tym pulpitu nawigacyjnego, usług, hostów, alertów ani stron administracyjnych.

![Użytkownik Apache Ambari z tylko widokami](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Zaloguj się do Ambari jako użytkownik klastra

Do roli *użytkownika klastra* przypisano użytkownika domeny usługi Azure AD "hiveuser2". Ta rola jest w stanie uzyskać dostęp do pulpitu nawigacyjnego i wszystkich elementów menu. Użytkownik klastra ma mniejszą liczbę dozwolonych opcji niż administrator. Na przykład hiveuser2 może wyświetlać konfiguracje dla każdej usługi, ale nie może ich edytować.

![Ekran pulpitu nawigacyjnego Apache Ambari](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie zasad Apache Hive w usłudze HDInsight przy użyciu protokołu ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Zarządzanie klastrami usługi HDInsight ESP](./domain-joined/apache-domain-joined-manage.md)
* [Korzystanie z widoku Apache Hive z Apache Hadoop w usłudze HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synchronizowanie użytkowników usługi Azure AD z klastrem](hdinsight-sync-aad-users-to-cluster.md)
