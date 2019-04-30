---
title: Autoryzowanie użytkowników na potrzeby widoków Ambari — Azure HDInsight
description: Jak zarządzać uprawnieniami użytkowników i grup Ambari do HDInsight klastrów przy użyciu ESP włączone.
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 69ae1bd05b64912b3d53ca88b468a72a90ff5a74
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097042"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autoryzowanie użytkowników na potrzeby widoków Apache Ambari

[Pakiet zabezpieczeń przedsiębiorstwa (ESP) włączone klastry HDInsight](./domain-joined/apache-domain-joined-introduction.md) zapewniają możliwości przeznaczonych dla przedsiębiorstw, w tym uwierzytelniania opartego na usłudze Azure Active Directory. Możesz [Synchronizowanie nowych użytkowników](hdinsight-sync-aad-users-to-cluster.md) dodane do grup usługi Azure AD, które zostały dołączone do dostępu do klastra, pozwalając określonych wykonywać niektórych akcji. Praca z użytkowników, grup i uprawnień w [Apache Ambari](https://ambari.apache.org/) jest obsługiwana w przypadku klastrów HDInsight ESP i klastry HDInsight standard.

Użytkownicy usługi Active Directory zalogować się do węzłów klastra, przy użyciu swoich poświadczeń domeny. Można też swoich poświadczeń domeny do uwierzytelniania interakcje klastra za pomocą innych zatwierdzonych punktów końcowych, takich jak [Hue](https://gethue.com/), widoków Ambari, ODBC, JDBC, PowerShell i interfejsów API REST.

> [!WARNING]  
> Nie zmieniaj hasła strażnika Ambari (hdinsightwatchdog) w klastrze usługi HDInsight opartych na systemie Linux. Zmienianie hasła przerywa możliwość użyj akcji skryptu, lub wykonywać operacje skalowania na potrzeby klastra.

Jeśli jeszcze tego nie zrobiono, wykonaj [w instrukcjach](./domain-joined/apache-domain-joined-configure.md) do nowego klastra ESP aprowizacji.

## <a name="access-the-ambari-management-page"></a>Dostęp do strony zarządzania Ambari

Aby uzyskać dostęp do **strony zarządzania Ambari** na [Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md), przejdź do **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Wprowadź nazwę użytkownika administratora klastra i hasło określone podczas tworzenia klastra. Następnie na pulpicie nawigacyjnym narzędzia Ambari, wybierz pozycję **Zarządzanie Ambari** poniżej **administratora** menu:

![Zarządzanie systemu Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Udziel uprawnień do widoków Apache Hive

Ambari, który jest dostarczany za pomocą wystąpienia widoku dla [Apache Hive](https://hive.apache.org/) i [Apache TEZ](https://tez.apache.org/), między innymi. Aby udzielić dostępu do co najmniej jednego wystąpienia z widoku Hive, przejdź do **strony zarządzania Ambari**.

1. Na stronie zarządzania wybierz **widoków** łącze w obszarze **widoków** nagłówek menu po lewej stronie.

    ![Link widoków](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. Na stronie widoków rozwinąć **HIVE** wiersza. Istnieje jeden domyślny widok programu Hive, które zostało utworzone po dodaniu usługi Hive w klastrze. Można również utworzyć większej liczby wystąpień widoku Hive, zgodnie z potrzebami. Wybierz widok programu Hive:

    ![Widoki — widoku Hive](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Przewiń do dołu strony widoku. W obszarze *uprawnienia* sekcji, dostępne są dwie opcje do przyznawania użytkownicy domeny ich uprawnienia do widoku:

**Przyznaj uprawnienie do tych użytkowników** ![udzielić uprawnień do tych użytkowników](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Udziel uprawnienia do tych grup** ![udzielić uprawnień do tych grup](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

1. Aby dodać użytkownika, wybierz pozycję **Dodaj użytkownika** przycisku.

   * Wpisz nazwę użytkownika i zostanie wyświetlony na liście rozwijanej wcześniej zdefiniowanych nazw.

     ![Uzupełnić użytkownika](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

   * Wybierz lub Zakończ, wpisując nazwę użytkownika. Aby dodać tej nazwy użytkownika jako nowego użytkownika, wybierz **New** przycisku.

   * Aby zapisać zmiany, wybierz pozycję **niebieskie pole wyboru**.

     ![Użytkownik wprowadził](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

1. Aby dodać grupę, wybierz pozycję **Dodaj grupę** przycisku.

   * Zacznij wpisywać nazwę grupy. Proces wybranie istniejącej nazwy grupy lub dodawania nowej grupy, jest taka sama, jak w przypadku dodawania użytkowników.
   * Aby zapisać zmiany, wybierz pozycję **niebieskie pole wyboru**.

     ![Wprowadzono grupę](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Dodawanie użytkowników bezpośrednio do widoku jest przydatne w przypadku, gdy chcesz przypisać uprawnienia dla użytkownika, aby użyć tego widoku, ale nie chcesz, aby być członkiem grupy, która ma dodatkowe uprawnienia. Aby zmniejszyć ilość czynności administracyjnych, może być prostsze przypisać uprawnienia do grup.

## <a name="grant-permissions-to-apache-tez-views"></a>Udziel uprawnień do widoków Apache TEZ

[Apache TEZ](https://tez.apache.org/) Wyświetl wystąpienia użytkownicy mogą monitorować i debugowanie zadań tez przy wszystkich, przesłane przez [Apache Hive](https://hive.apache.org/) zapytań i [Apache Pig](https://pig.apache.org/) skryptów. Istnieje jeden domyślny Tez widoku wystąpienie tworzonego po zainicjowaniu obsługi klastra.

Aby przypisać użytkowników i grup do wystąpienia widoku aplikacji Tez, rozwiń węzeł **TEZ** wiersza na stronie widoki, zgodnie z wcześniejszym opisem.

![Widoki — widok aplikacji Tez](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Aby dodać użytkowników lub grupy, powtórz kroki od 3 do 5 w poprzedniej sekcji.

## <a name="assign-users-to-roles"></a>Przypisywanie użytkowników do ról

Istnieje pięć ról zabezpieczeń dla użytkowników i grup, wymienione w kolejności malejących uprawnienia dostępu:

* Administrator klastra
* Operator klastra
* Administrator usługi
* Operator usługi
* Użytkownika klastra

Aby zarządzać rolami, przejdź do **strony zarządzania Ambari**, a następnie wybierz **role** połączyć w ramach *klastrów* grupy menu po lewej stronie.

![Łącze menu ról](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Aby wyświetlić listę uprawnienia udzielone każdej roli, kliknij niebieski znak zapytania obok **role** nagłówek tabeli na stronie ról.

![Łącze menu ról](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Na tej stronie istnieją dwa różne widoki, których można użyć do zarządzania rolami użytkowników i grup: Blok i listy.

### <a name="block-view"></a>Widok bloku

Widok bloku Wyświetla każdej roli w swoim własnym wierszu oraz **przypisać role do tych użytkowników** i **przypisać role do tych grup** opcje zgodnie z wcześniejszym opisem.

![Role blokowania widoku](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Widok listy

Widok listy zawiera szybkie możliwości edycji w dwie kategorie: Użytkownicy i grupy.

* Kategoria użytkowników w widoku listy wyświetla listę wszystkich użytkowników, dzięki czemu możesz wybrać rolę dla każdego użytkownika na liście rozwijanej.

    ![Widok — Użytkownicy listy ról](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  Kategoria grupy w widoku listy wyświetla wszystkich grup i ról przypisanych do każdej grupy. W naszym przykładzie lista grup jest zsynchronizowany z grup usługi Azure AD, określone w **dostęp do grupy użytkowników** właściwości ustawienia domeny klastra. Zobacz [Tworzenie klastra HDInsight z ESP włączone](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Widok — grup listy ról](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na powyższej ilustracji jest przypisany do grupy "hiveusers" *użytkownika klastra* roli. Jest to rola tylko do odczytu, która umożliwia użytkownikom tej grupy, aby wyświetlić, ale nie zmienianie konfiguracji usługi i metryki klastra.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Zaloguj się do narzędzia Ambari, jak użytkownik tylko do wyświetlania

Firma Microsoft ma uprawnienia użytkowników domeny usługi Azure AD "hiveuser1" widoki Hive, jak i aplikacji Tez. Gdy firma Microsoft może uruchomić interfejs użytkownika sieci Web Ambari, a następnie wprowadź poświadczenia domeny użytkownika (nazwę użytkownika usługi Azure AD w format wiadomości e-mail i hasło), użytkownik jest przekierowywany na stronę widoków Ambari. W tym miejscu użytkownik może wybrać dowolny dostępny widok. Użytkownik nie można znaleźć w innej części witryny, w tym stron pulpitu nawigacyjnego, usług, hosty, alerty lub administratora.

![Użytkownikowi tylko widoki](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Zaloguj się do systemu Ambari jako użytkownika klastra

Firma Microsoft przypisano użytkowników domeny usługi Azure AD "hiveuser2" *użytkownika klastra* roli. Ta rola jest w stanie uzyskać dostęp do pulpitu nawigacyjnego i wszystkie elementy menu. Użytkownik klastra ma mniej opcji dozwolone niż administrator. Na przykład użytkownik hiveuser2 można wyświetlić konfiguracje dla poszczególnych usług, ale nie można ich edytować.

![Użytkownik mający rolę użytkownika klastra](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie zasad usługi Apache Hive HDInsight przy użyciu ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Zarządzanie klastrami HDInsight ESP](./domain-joined/apache-domain-joined-manage.md)
* [Korzystanie z widoku Hive Apache przy użyciu technologii Apache Hadoop w HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synchronizowanie użytkowników usługi Azure AD do klastra](hdinsight-sync-aad-users-to-cluster.md)
