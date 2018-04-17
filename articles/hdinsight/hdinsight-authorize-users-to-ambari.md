---
title: Autoryzowanie użytkowników dla widoków Ambari - Azure HDInsight | Dokumentacja firmy Microsoft
description: Jak zarządzać uprawnień użytkowników i grup Ambari w klastrach HDInsight przyłączonych do domeny.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 0e7eb7fa57630e5ae52d4bf5e4321456c6bff54a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="authorize-users-for-ambari-views"></a>Autoryzowanie użytkowników na potrzeby widoków Ambari

[Klastry HDInsight przyłączonych do domeny](./domain-joined/apache-domain-joined-introduction.md) zapewniają funkcje klasy korporacyjnej, łącznie z uwierzytelniania opartego na usłudze Azure Active Directory. Możesz [synchronizować nowych użytkowników](hdinsight-sync-aad-users-to-cluster.md) dodani do grup usługi Azure AD, które podano dostęp do klastra, pozwalając określonych do wykonywania pewnych działań. Praca z użytkowników, grup i uprawnień w Ambari jest obsługiwana dla klastra usługi HDInsight przyłączonych do domeny i standardowe klastra usługi HDInsight.

Użytkownicy usługi Active Directory mogą zalogować się do węzłów klastra przy użyciu swoich poświadczeń domeny. Mogą również wykorzystać swoich poświadczeń domeny do uwierzytelniania klastra interakcji z innych zatwierdzonych punktów końcowych, takie jak Hue, widoki Ambari ODBC, JDBC, programu PowerShell i interfejsów API REST.

> [!WARNING]
> Nie należy zmieniać hasła strażnika Ambari (hdinsightwatchdog) w klastrze usługi HDInsight opartej na systemie Linux. Zmiana hasła dzieli możliwość akcje skryptu lub operacji skalowania z klastrem.

Jeśli nie zostało to jeszcze zrobione, wykonaj [tych instrukcji](./domain-joined/apache-domain-joined-configure.md) do udostępnienia nowego klastra przyłączonych do domeny.

## <a name="access-the-ambari-management-page"></a>Dostęp do strony zarządzania Ambari

Aby uzyskać dostęp do **strony Zarządzanie Ambari** na [Interfejsu sieci Web Ambari](hdinsight-hadoop-manage-ambari.md), przejdź do **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Wprowadź nazwę użytkownika administratora klastra i hasło, zdefiniowanym przez użytkownika podczas tworzenia klastra. Następnie z poziomu pulpitu nawigacyjnego Ambari, wybierz pozycję **Zarządzanie Ambari** pod **admin** menu:

![Zarządzanie Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-hive-views"></a>Udziel uprawnień do widoków gałęzi

Ambari jest dostarczany z wystąpienia widoku dla gałęzi i Tez, między innymi. Aby udzielić dostępu do co najmniej jednego wystąpienia widoku Hive, przejdź do **strony Zarządzanie Ambari**.

1. Na stronie zarządzania wybierz **widoków** łącze w obszarze **widoków** nagłówek menu po lewej stronie.

    ![Łącze widoków](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. Na stronie widoków rozwinąć **HIVE** wiersza. Istnieje jeden domyślny widok Hive, który jest tworzony, gdy usługa Hive zostanie dodany do klastra. Można również utworzyć więcej wystąpień widoku Hive zgodnie z potrzebami. Wybierz widok gałęzi:

    ![Widoków — widok gałęzi](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Przewiń do dołu strony widoku. W obszarze *uprawnienia* sekcji, dostępne są dwie opcje dotyczące przyznawania uprawnień do widoku użytkownicy domeny:

**Przyznaj uprawnienie do tych użytkowników** ![Przyznaj uprawnienie do tych użytkowników](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Przyznaj uprawnienie do tych grup** ![przyznanie uprawnień do tych grup](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

4. Aby dodać użytkownika, zaznacz **Dodaj użytkownika** przycisku.

    * Wpisz nazwę użytkownika i zostanie wyświetlona lista rozwijana z uprzednio zdefiniowanej nazwy.

    ![Autocompletes użytkownika](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

    * Wybierz lub zakończyć wpisywanie nazwy użytkownika. Aby dodać nazwę tego użytkownika jako nowy użytkownik, wybierz **nowy** przycisku.

    * Aby zapisać zmiany, wybierz **wyboru niebieski**.

    ![Użytkownik wprowadził](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

5. Aby dodać grupę, wybierz **Dodaj grupę** przycisku.

    * Zacznij wpisywać nazwę grupy. Proces zaznaczenie istniejącej nazwy grupy lub Dodawanie nowej grupy, jest taka sama jak w przypadku dodawania użytkowników.
    * Aby zapisać zmiany, wybierz **wyboru niebieski**.

    ![Wprowadzonej grupy](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Dodawanie użytkowników bezpośrednio w widoku jest przydatne, gdy chcesz przypisać uprawnienia użytkownika do tego widoku, ale nie chcesz, aby być członkiem grupy, która ma dodatkowe uprawnienia. Aby zmniejszyć liczbę czynności administracyjnych, może być prostsze przypisać uprawnienia do grup.

## <a name="grant-permissions-to-tez-views"></a>Udziel uprawnień do widoków Tez

Wyświetl wystąpienia aplikacji Tez Zezwalaj użytkownikom na monitorowanie i debugowanie wszystkich zadań Tez przesłane przez zapytań programu Hive i Pig skryptów. Istnieje jeden domyślny Tez widoku wystąpienie tworzonego po zainicjowaniu obsługi klastra.

Aby przypisać użytkowników i grup do wystąpienia widoku aplikacji Tez, rozwiń węzeł **TEZ** wiersza na stronie widoków opisanych powyżej.

![Widoków — widok Tez](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Aby dodać użytkowników lub grup, powtórz kroki od 3 do 5 w poprzedniej sekcji.

## <a name="assign-users-to-roles"></a>Przypisywanie użytkowników do ról

Istnieje pięć ról zabezpieczeń dla użytkowników i grup, wymienione w kolejności malejącej uprawnienia dostępu:

* Administrator klastra
* Operator klastra
* Administrator usługi
* Operator usługi
* Użytkownika klastra

Aby zarządzać rolami, przejdź do **strony Zarządzanie Ambari**, a następnie wybierz pozycję **ról** łącza w ramach *klastrów* grupy menu po lewej stronie.

![Łącze menu ról](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Aby zapoznać się z listą uprawnień do każdej roli, kliknij przycisk niebieski znak zapytania obok pozycji **ról** nagłówek tabeli na stronie ról.

![Łącze menu ról](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Na tej stronie są dwa widoki można użyć do zarządzania rolami użytkowników i grup: blok i listy.

### <a name="block-view"></a>Widok bloku

Widok bloku Wyświetla każdej roli w swoim własnym wierszu oraz **przypisać role do tych użytkowników** i **przypisać role do tych grup** opcje opisanych powyżej.

![Role blokowania widoku](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Widok listy

Widok listy zapewnia szybkie możliwości edycji w dwóch różnych kategoriach: użytkowników i grup.

* Kategoria Użytkownicy widoku listy zostanie wyświetlona lista wszystkich użytkowników, umożliwiając wybranie roli dla każdego użytkownika na liście rozwijanej.

    ![Widok - Użytkownicy listy ról](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* Kategoria grupy widoku listy wyświetla wszystkie grupy, a także od roli przypisanej do każdej grupy. W naszym przykładzie lista grup jest zsynchronizowany z grup usługi Azure AD, określona w **grupy użytkowników dostępu** właściwości klastra ustawienia domeny. Zobacz [utworzyć klaster HDInsight przyłączonych do domeny](/domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster).

    ![Widok - grup listy ról](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na ilustracji powyżej jest przypisany do grupy "hiveusers" *użytkownika klastra* roli. To jest rola tylko do odczytu, który umożliwia użytkownikom z tej grupy, aby wyświetlić bez możliwości zmiany konfiguracji usług i metryki klastra.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Zaloguj się do narzędzia Ambari jako użytkownik tylko do odczytu

Firma Microsoft naszych użytkowników domeny usługi Azure AD "hiveuser1" uprawnień do przypisano widoki Hive i Tez. Gdy firma Microsoft może Uruchom interfejs użytkownika sieci Web Ambari, a następnie wprowadź poświadczenia domeny użytkownika (nazwa użytkownika usługi Azure AD w formacie wiadomości e-mail i hasło), użytkownik jest przekierowanie do strony widoki Ambari. W tym miejscu użytkownik może wybrać dowolny dostępny widok. Użytkownik nie można znaleźć w dowolnej części witryny, w tym stron pulpitu nawigacyjnego, usług, hosty, alerty lub administratora.

![Użytkownik z tylko widoki](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Zaloguj się do narzędzia Ambari jako użytkownik klastra

Firma Microsoft przydzielono naszych użytkowników domeny usługi Azure AD "hiveuser2" *użytkownika klastra* roli. Ta rola jest w stanie uzyskać dostęp do pulpitu nawigacyjnego i wszystkich elementów menu. Użytkownik klastra ma mniej opcji dozwolonych niż administrator. Na przykład hiveuser2 można przeglądać konfiguracje dla poszczególnych usług, ale nie można ich edytować.

![Użytkownik z rolą użytkownika klastra](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie zasad Hive w usłudze HDInsight z przyłączonych do domeny](./domain-joined/apache-domain-joined-run-hive.md)
* [Zarządzanie klastrami HDInsight przyłączonych do domeny](./domain-joined/apache-domain-joined-manage.md)
* [Użyj widoku Hive z usługą Hadoop w usłudze HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synchronizowanie użytkowników usługi Azure AD do klastra](hdinsight-sync-aad-users-to-cluster.md)
