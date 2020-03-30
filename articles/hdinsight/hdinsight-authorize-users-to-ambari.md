---
title: Autoryzuj użytkowników dla widoków Ambari — Azure HDInsight
description: Jak zarządzać uprawnieniami użytkowników i grup Ambari dla klastrów HDInsight z włączoną obsługą ESP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 3bb58334e96bf5378fb78b70125f9c7994a7c2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435640"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autoryzowanie użytkowników na potrzeby widoków Apache Ambari

[Klastry usługi HDInsight z włączonym pakietem zabezpieczeń przedsiębiorstwa zapewniają funkcje](./domain-joined/hdinsight-security-overview.md) klasy korporacyjnej, w tym uwierzytelnianie oparte na usłudze Azure Active Directory. Można [synchronizować nowych użytkowników dodanych](hdinsight-sync-aad-users-to-cluster.md) do grup usługi Azure AD, które zostały udostępnione dostęp do klastra, umożliwiając tym określonym użytkownikom wykonywanie określonych akcji. Praca z użytkownikami, grupami i uprawnieniami w [Apache Ambari](https://ambari.apache.org/) jest obsługiwana zarówno dla klastrów ESP HDInsight, jak i standardowych klastrów HDInsight.

Użytkownicy usługi Active Directory mogą logować się do węzłów klastra przy użyciu poświadczeń domeny. Mogą również używać swoich poświadczeń domeny do uwierzytelniania interakcji klastra z innymi zatwierdzonymi punktami końcowymi, takimi jak [Hue](https://gethue.com/), Ambari Views, ODBC, JDBC, PowerShell i REST API.

> [!WARNING]  
> Nie należy zmieniać hasła programu Ambari watchdog (hdinsightwatchdog) w klastrze HDInsight opartym na systemie Linux. Zmiana hasła przerywa możliwość używania akcji skryptu lub wykonywania operacji skalowania za pomocą klastra.

Jeśli jeszcze tego nie zrobiono, postępuj zgodnie [z tymi instrukcjami,](./domain-joined/apache-domain-joined-configure.md) aby aprowizować nowy klaster ESP.

## <a name="access-the-ambari-management-page"></a>Dostęp do strony zarządzania Ambari

Aby przejść do **strony zarządzania Ambari** w [interfejsie użytkownika sieci Web Apache Ambari,](hdinsight-hadoop-manage-ambari.md)przejdź do pliku `https://CLUSTERNAME.azurehdinsight.net`. Wprowadź nazwę użytkownika i hasło administratora klastra zdefiniowane podczas tworzenia klastra. Następnie z pulpitu nawigacyjnego Ambari wybierz pozycję **Zarządzaj ambari** pod menu **administratora:**

![Apache Ambari dashboard zarządzanie](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="add-users"></a>Dodawanie użytkowników

### <a name="add-users-through-the-portal"></a>Dodawanie użytkowników za pośrednictwem portalu

1. Na stronie zarządzania wybierz pozycję **Użytkownicy**.

    ![Użytkownicy strony zarządzania Apache Ambari](./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png)

1. Wybierz **+ Utwórz użytkownika lokalnego**.

1. Podaj **nazwę użytkownika** i **hasło**. Wybierz **zapisz**.

### <a name="add-users-through-powershell"></a>Dodawanie użytkowników za pośrednictwem programu PowerShell

Edytuj poniższe zmienne, `NEWUSER`zastępując `PASSWORD` `CLUSTERNAME`i odpowiednimi wartościami.

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>Dodawanie użytkowników za pomocą curl

Edytuj poniższe zmienne, `ADMINPASSWORD` `NEWUSER`zastępując `CLUSTERNAME` `USERPASSWORD` , , i odpowiednimi wartościami. Skrypt jest przeznaczony do wykonania z bash. Niewielkie modyfikacje będą potrzebne dla wiersza polecenia systemu Windows.

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>Udzielanie uprawnień widokom ula Apache

Ambari pochodzi z przykładów widok [apache hive](https://hive.apache.org/) i [Apache TEZ](https://tez.apache.org/), między innymi. Aby udzielić dostępu do jednego lub więcej wystąpień widoku hive, przejdź do **strony zarządzania Ambari**.

1. Na stronie zarządzania wybierz **łącze Widoki** w nagłówku menu **Widoki** po lewej stronie.

    ![Apache Ambari widoki zobacz linki](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. Na stronie Widoki rozwiń wiersz **HIVE.** Istnieje jeden domyślny widok gałęzi, który jest tworzony po dodaniu usługi Hive do klastra. Można również utworzyć więcej wystąpień widoku gałęzi w razie potrzeby. Wybierz widok gałęzi:

    ![Widoki HDInsight — widok ula Apache](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Przewiń w kierunku dołu strony Widok. W sekcji *Uprawnienia* dostępne są dwie opcje przyznawania użytkownikom domeny ich uprawnień do widoku:

**Udziel uprawnień tym użytkownikom** ![Udzielanie uprawnień tym użytkownikom](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Udziel uprawnień do tych grup** ![Udzielanie uprawnień do tych grup](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Aby dodać użytkownika, wybierz przycisk **Dodaj użytkownika.**

   * Zacznij wpisywać nazwę użytkownika, a zobaczysz listę rozwijaną wcześniej zdefiniowanych nazw.

     ![Apache Ambari użytkownik auto uzupełnia](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Wybierz lub zakończ wpisywanie nazwy użytkownika. Aby dodać tę nazwę użytkownika jako nowego użytkownika, wybierz przycisk **Nowy.**

   * Aby zapisać zmiany, zaznacz **niebieskie pole wyboru**.

     ![Apache Ambari udziela uprawnień użytkownikom](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Aby dodać grupę, wybierz przycisk **Dodaj grupę.**

   * Zacznij wpisywać nazwę grupy. Proces wybierania istniejącej nazwy grupy lub dodawania nowej grupy jest taki sam, jak w przypadku dodawania użytkowników.
   * Aby zapisać zmiany, zaznacz **niebieskie pole wyboru**.

     ![Apache Ambari udziela uprawnień](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

Dodawanie użytkowników bezpośrednio do widoku jest przydatne, gdy chcesz przypisać uprawnienia do użytkownika do korzystania z tego widoku, ale nie chcesz, aby był członkiem grupy, która ma dodatkowe uprawnienia. Aby zmniejszyć obciążenie administracyjne, może być łatwiej przypisać uprawnienia do grup.

## <a name="grant-permissions-to-apache-tez-views"></a>Udzielanie uprawnień do widoków Apache TEZ

Wystąpienia widoku [Apache TEZ](https://tez.apache.org/) umożliwiają użytkownikom monitorowanie i debugowanie wszystkich zadań Tez przesłanych przez kwerendy [gałęzi Apache](https://hive.apache.org/) i skrypty [Apache Pig.](https://pig.apache.org/) Istnieje jedno domyślne wystąpienie widoku Tez, które jest tworzone podczas inicjowania obsługi administracyjnej klastra.

Aby przypisać użytkowników i grupy do wystąpienia widoku Tez, rozwiń wiersz **TEZ** na stronie Widoki, zgodnie z wcześniejszym opisem.

![Widoki HDInsight — widok Apache Tez](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Aby dodać użytkowników lub grupy, powtórz kroki 3 - 5 w poprzedniej sekcji.

## <a name="assign-users-to-roles"></a>Przypisywanie użytkowników do ról

Istnieje pięć ról zabezpieczeń dla użytkowników i grup, wymienionych w kolejności malejących uprawnień dostępu:

* Administrator klastra
* Operator klastra
* Administrator usługi
* Operator serwisu
* Użytkownik klastra

Aby zarządzać rolami, przejdź do **strony zarządzania Ambari**, a następnie wybierz łącze **Role** w grupie menu *Klastry* po lewej stronie.

![Apache Ambari role linki menu](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Aby wyświetlić listę uprawnień przyznanych każdej roli, kliknij niebieski znak zapytania obok nagłówka tabeli **Role** na stronie Role.

![Apache Ambari role uprawnienia do łącza menu](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Apache Ambari role uprawnienia do łącza menu")

Na tej stronie istnieją dwa różne widoki, których można używać do zarządzania rolami dla użytkowników i grup: Blokuj i Lista.

### <a name="block-view"></a>Widok bloku

Widok Blokuj wyświetla każdą rolę we własnym wierszu i udostępnia **opcje Przypisywanie ról tym użytkownikom** i **Przypisywanie ról do tych grup** w sposób opisany wcześniej.

![Apache Ambari blok ról widok](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>Widok listy

Widok Lista zapewnia szybkie możliwości edycji w dwóch kategoriach: Użytkownicy i Grupy.

* Kategoria Użytkownicy w widoku Lista wyświetla listę wszystkich użytkowników, co pozwala wybrać rolę dla każdego użytkownika na liście rozwijanej.

    ![Apache Ambari role widok listy - użytkownicy](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* W kategorii Grupy w widoku Lista są wyświetlane wszystkie grupy, a rola przypisana do każdej grupy. W naszym przykładzie lista grup jest synchronizowana z grupami usługi Azure AD określonymi we właściwości **grupy użytkowników programu Access** w ustawieniach domeny klastra. Zobacz [Tworzenie klastra HDInsight z włączoną funkcją ESP](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).

    ![Apache Ambari role widok listy - grupy](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na powyższej ilustracji grupie "hiveusers" jest przypisana rola *użytkownika klastra.* Jest to rola tylko do odczytu, która umożliwia użytkownikom tej grupy wyświetlanie, ale nie zmienianie konfiguracji usługi i metryk klastra.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Zaloguj się do Ambari jako użytkownik tylko do widoku

Do widoków Hive i Tez przypisano naszym użytkownikom domeny usługi Azure AD uprawnienia "hiveuser1". Po uruchomieniu interfejsu użytkownika sieci Web Ambari i wprowadzeniu poświadczeń domeny tego użytkownika (nazwa użytkownika usługi Azure AD w formacie poczty e-mail i hasło), użytkownik jest przekierowywane do strony Widoki Ambari. W tym miejscu użytkownik może wybrać dowolny dostępny widok. Użytkownik nie może odwiedzać żadnej innej części witryny, w tym pulpitu nawigacyjnego, usług, hostów, alertów ani stron administratora.

![Użytkownik Apache Ambari tylko z widokami](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Zaloguj się do Ambari jako użytkownik klastra

Przydzieliliśmy naszego użytkownika domeny usługi Azure AD "hiveuser2" do roli *użytkownika klastra.* Ta rola jest w stanie uzyskać dostęp do pulpitu nawigacyjnego i wszystkich elementów menu. Użytkownik klastra ma mniej dozwolonych opcji niż administrator. Na przykład hiveuser2 można wyświetlić konfiguracje dla każdej z usług, ale nie można ich edytować.

![Apache Ambari wyświetlacz deski rozdzielczej](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie zasad gałęzi Apache w udziale HDInsight za pomocą usługi ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Zarządzanie klastrami ESP HDInsight](./domain-joined/apache-domain-joined-manage.md)
* [Użyj widoku ula Apache z Apache Hadoop w hdinsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synchronizowanie użytkowników usługi Azure AD z klastrem](hdinsight-sync-aad-users-to-cluster.md)
* [Zarządzanie klastrami HDInsight przy użyciu interfejsu API Apache Ambari REST](./hdinsight-hadoop-manage-ambari-rest-api.md)
