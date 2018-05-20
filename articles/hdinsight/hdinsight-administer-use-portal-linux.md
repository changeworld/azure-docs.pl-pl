---
title: Zarządzanie klastrami Hadoop w HDInsight przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia i zarządzania klastrami HDInsight przy użyciu portalu Azure.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: cd61aa4a69a5bed8de9874eebad059b54118ab63
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Zarządzanie klastrami Hadoop w usłudze HDInsight przy użyciu portalu Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Przy użyciu [portalu Azure][azure-portal], można zarządzać klastrów platformy Hadoop w usłudze Azure HDInsight. Selektor karty powyżej dla informacji o zarządzaniu klastrów platformy Hadoop w usłudze HDInsight przy użyciu innych narzędzi.

**Wymagania wstępne**

Aby wykonać kroki opisane w tym artykule, należy **subskrypcji platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-azure-portal"></a>Otwórz Azure portal
1. Zaloguj się do [ https://portal.azure.com ](https://portal.azure.com).
2. Po otwarciu portalu, możesz:

   * Kliknij przycisk **Utwórz zasób** z menu po lewej stronie do utworzenia nowego klastra:

       ![przycisk Nowy klaster usługi HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)

       Wprowadź **HDInsight** w **wyszukiwania portalu Marketplace**, kliknij przycisk **HDInsight**, a następnie kliknij przycisk **Utwórz**.

   * Kliknij przycisk **klastrów usługi HDInsight** z menu po lewej stronie, aby wyświetlić listę istniejących klastrów:

       ![Przycisk klastra usługi HDInsight portalu Azure](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Jeśli nie widzisz **klastrów usługi HDInsight** przycisk, a następnie kliknij przycisk **klastrów usługi HDInsight** w obszarze **analizy i analiza** sekcji.


## <a name="create-clusters"></a>Tworzenie klastrów
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight działa ze składnikami szeroki zakres Hadoop. Lista składników, które są weryfikowane i obsługiwane, zobacz [jest wersję platformy Hadoop w usłudze Azure HDInsight?](hdinsight-component-versioning.md) Aby uzyskać informacji o tworzeniu klastra ogólne, zobacz [klastrów utworzyć Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="access-control-requirements"></a>Wymagania dotyczące kontroli dostępu

Należy określić subskrypcji platformy Azure, podczas tworzenia klastra usługi HDInsight. Można utworzyć nową grupę zasobów platformy Azure lub istniejącej grupy zasobów klastra. Sprawdź swoje uprawnienia do tworzenia klastrów usługi HDInsight umożliwia następujące czynności:

- Aby utworzyć nową grupę zasobów:

    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    2. Kliknij przycisk **subskrypcji** z menu po lewej stronie. Ma ona żółta ikona klucza. Zostanie wyświetlona lista subskrypcji.
    3. Kliknij subskrypcję, która służy do tworzenia klastrów. 
    4. Kliknij przycisk **Moje uprawnienia**.  Widoczny jest Twoje [roli](../role-based-access-control/overview.md#built-in-roles) dla tej subskrypcji. Należy co najmniej współautora dostęp do tworzenia klastra usługi HDInsight.

- Aby użyć istniejącej grupy zasobów:

    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    2. Kliknij przycisk **grup zasobów** z menu po lewej stronie, aby wyświetlić listę grup zasobów.
    3. Kliknij grupę zasobów, która ma być używany do tworzenia klastra usługi HDInsight.
    4. Kliknij przycisk **(IAM) kontroli dostępu**i sprawdź, czy użytkownik (lub należeć do grupy) mają co najmniej dostęp współautora do grupy zasobów.

Jeśli wystąpi błąd NoRegisteredProviderFound lub błąd MissingSubscriptionRegistration, zobacz [Rozwiąż typowe błędy wdrożenia usługi Azure z usługą Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).

## <a name="list-and-show-clusters"></a>Lista i Pokaż klastrów
1. Zaloguj się do [ https://portal.azure.com ](https://portal.azure.com).
2. Kliknij przycisk **klastrów usługi HDInsight** z menu po lewej stronie, aby wyświetlić listę istniejących klastrów. Jeśli nie widzisz **klastrów usługi HDInsight**, kliknij przycisk **wszystkie usługi** pierwszy.
3. Kliknij nazwę klastra. Jeśli na liście klastra jest długa, można użyć filtrów w górnej części strony.
4. Kliknij klastra z listy w celu wyświetlenia strony Przegląd:

    ![Azure portalu essentials klastra usługi HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png) **Omówienie menu:**
    * **Pulpit nawigacyjny**: Otwiera Ambari web UI dla klastra.
    * **Secure Shell**: zawiera instrukcje, aby nawiązać połączenie z klastrem przy użyciu połączenia protokołu Secure Shell (SSH).
    * **Skalowanie klastra**: umożliwia zmianę liczby węzłów procesu roboczego dla tego klastra.
    * **Przenieś**: Przenosi klastra do innej grupy zasobów lub do innej subskrypcji.
    * **Usuń**: usuwa klastra.

    **Menu po lewej stronie:**
    * **Dzienniki aktywności**: Pokaż i zapytania Dzienniki aktywności.
    * **Kontrola (IAM) dostępu**: za pomocą przypisań ról.  Zobacz [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md).
    * **Tagi**: umożliwia ustawienie pary klucz wartość do definiowania niestandardowych taksonomii usług w chmurze. Na przykład może utworzyć klucz o nazwie **projektu**, a następnie użyć wspólną wartość wszystkie usługi powiązane z określonego projektu.
    * **Diagnozowanie i rozwiązywanie problemów**: Wyświetl informacje dotyczące rozwiązywania problemów.
    * **Blokuje**: Dodaj blokady, aby zapobiec, przy czym klastra, zmodyfikowane lub usunięte.
    * **Skrypt automatyzacji**: wyświetlanie i eksportowanie szablonu usługi Azure Resource Manager dla klastra. Obecnie można wyeksportować tylko konta magazynu Azure zależnego. Zobacz [utworzyć Linux opartych klastrów Hadoop w usłudze HDInsight przy użyciu szablonów usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
    * **Szybki Start**: Wyświetla informacje, który pomoże Ci rozpocząć korzystanie z usługi HDInsight.
    * **Narzędzia HDInsight**: narzędzia powiązane informacje pomocy dla usługi HDInsight.
    * **Użycie Core subskrypcji**: Wyświetl rdzeni używany i dostępny dla Twojej subskrypcji.
    * **Skalowanie klastra**: Zwiększ i zmniejsza liczbę węzłów procesu roboczego w klastrze. Zobacz[skalować klastrów](hdinsight-administer-use-management-portal.md#scale-clusters).
    * **SSH + logowania do klastra**: zawiera instrukcje, aby nawiązać połączenie z klastrem przy użyciu połączenia protokołu Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
    * **Partnera usługi HDInsight**: Dodaj/Usuń bieżącego partnera usługi HDInsight.
    * **Zewnętrzne magazyny**: Wyświetl magazyny Hive i Oozie. Magazyny można skonfigurować tylko w trakcie procesu tworzenia klastra. Zobacz [użyć na potrzeby magazynu metadanych Hive/Oozie](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore).
    * **Akcje skryptu**: skrypty Bash uruchomić w klastrze. Zobacz [klastrów usługi HDInsight opartej na dostosowanie systemu Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).
    * **Aplikacje**: aplikacje usługi HDInsight dodawania i usuwania.  Zobacz [instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md).
    * **Monitorowanie**: monitorowanie klastra w Analiza dzienników Azure.
    * **Właściwości**: wyświetlanie właściwości klastra.
    * **Konta magazynu**: Wyświetl konta magazynu i klucze. Konta magazynu są skonfigurowane w trakcie procesu tworzenia klastra.
    * **Dostęp do usługi Data Lake Store**: Konfigurowanie dostępu są przechowywane w usłudze Data Lake.  Zobacz [Tworzenie klastrów usługi HDInsight z usługą Data Lake Store za pomocą portalu Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
    * **Kondycja zasobów**: zobacz [Przegląd kondycji zasobów platformy Azure](../service-health/resource-health-overview.md).
    * **Nowe żądanie pomocy technicznej**: umożliwia tworzenie biletu pomocy technicznej o pomoc techniczna firmy Microsoft.
    
6. Kliknij przycisk **właściwości**:

    Właściwości są następujące:

   * **Nazwa hosta**: Nazwa klastra.
   * **Adres URL klastra**: adres URL dla interfejsu sieci web Ambari.
   * **Secure shell (SSH)**: Nazwa Nazwa użytkownika i hosta do użycia podczas uzyskiwania dostępu do klastra za pomocą protokołu SSH.
   * **Stan**: jeden z: zostało przerwane, zaakceptowane, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operacyjne, uruchomione, błąd, usuwanie, usunięty, upłynął limit czasu, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued lub ClusterCustomization.
   * **Region**: Lokalizacja platformy Azure. Aby uzyskać listę obsługiwanych lokalizacji platformy Azure, zobacz **Region** lista rozwijana na [cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Data utworzenia**: Data klastra została wdrożona.
   * **System operacyjny**: albo **Windows** lub **Linux**.
   * **Typ**: Hadoop, HBase, Storm, Spark.
   * **Wersja**. Zobacz [wersji usługi HDInsight](hdinsight-component-versioning.md).
   * **Subskrypcja**: Nazwa subskrypcji.
   * **Domyślne źródło danych**: domyślny system plików klastra.
   * **Rozmiar węzłów procesu roboczego**: wybrany rozmiar maszyny Wirtualnej z węzłami procesów roboczych.
   * **HEAD rozmiaru węzła**: wybrany rozmiar maszyny Wirtualnej węzłów głównych.
   * **Sieć wirtualna**: Nazwa sieci wirtualnej i podsieci, do którego jest wdrażana klastra, jeśli wybrano jeden w czasie wdrażania.

## <a name="delete-clusters"></a>Usuwanie klastrów
Usunięcie klastra nie powoduje usunięcia domyślne konto magazynu ani wszystkie połączone konta magazynu. Można ponownie utworzyć klastra przy użyciu tego samego konta magazynu i tym samym magazyny. Zalecamy używanie nowego domyślnego kontenera obiektów Blob podczas ponownego tworzenia klastra.

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **klastrów usługi HDInsight** z menu po lewej stronie. Jeśli nie widzisz **klastrów usługi HDInsight**, kliknij przycisk **wszystkie usługi** pierwszy.
3. Kliknij klaster, który chcesz usunąć.
4. Kliknij przycisk **usunąć** z górnego menu, a następnie postępuj zgodnie z instrukcjami.

Zobacz też [Wstrzymaj/zamykania klastrów](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Dodawanie kolejnych kont magazynu

Po utworzeniu klastra można dodać dodatkowych kont usługi Azure Storage i Azure Data Lake Store. Aby uzyskać więcej informacji, zobacz [Dodawanie kolejnych kont magazynu do usługi HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Skalowanie klastrów
Skalowanie funkcji klastra umożliwia zmianę liczby węzłów procesu roboczego używane przez klaster Azure HDInsight, bez konieczności ponownego tworzenia klastra.

> [!NOTE]
> Tylko klastry usługi HDInsight w wersji 3.1.3 lub nowszym są obsługiwane. Jeśli masz pewności, jaka wersja klastra, można sprawdzić na stronie właściwości.  Zobacz [klastrów listy i Pokaż](#list-and-show-clusters).
>
>

Wpływ zmianę liczby węzłów danych może być różna dla każdego typu obsługiwanych przez HDInsight klastra:

* Hadoop

    Można bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze platformy Hadoop, który jest uruchomiony bez wpływu na wszystkie oczekujące lub uruchomione zadania. Również można przesłać nowe zadania, gdy operacja jest w toku. Błędy w operacji skalowania bezpiecznie obsługi tak, aby zawsze pozostanie w stanie funkcjonalności klastra.

    Podczas skalowania klastra usługi Hadoop w dół dzięki zmniejszeniu liczby węzłów danych są ponownie uruchamiane niektóre z tych usług w klastrze. To działanie powoduje wszystkich uruchomionych i oczekujące zadania się niepowodzeniem po zakończeniu operacji skalowania. Można jednak Prześlij ponownie zadania po zakończeniu operacji.
* HBase

    Można bezproblemowo dodać lub usunąć węzły do klastra HBase jest uruchomiona. Serwery regionalne automatycznie równoważy w ciągu kilku minut od zakończenia operacji skalowania. Można jednak również ręcznie saldo serwery regionalne logowanie do headnode klastra i uruchamiając następujące polecenia z poziomu okna wiersza polecenia:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    Aby uzyskać więcej informacji dotyczących korzystania z powłoki HBase, zobacz [Rozpoczynanie pracy z przykładem bazy danych Apache HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Storm

    Bezproblemowo można dodawać i usuwać dane węzły do klastra Storm, jest uruchomiona. Jednak po pomyślnym zakończeniu operacji skalowania, konieczne będzie ponowne zrównoważenie topologii.

    Ponowne równoważenie można zrobić na dwa sposoby:

  * STORM interfejsu użytkownika sieci web
  * Narzędzia interfejsu wiersza polecenia (CLI)

    Zapoznaj się [dokumentację Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) więcej szczegółów.

    Interfejs użytkownika sieci web Storm jest dostępna w klastrze usługi HDInsight:

    ![HDInsight Storm Zrównoważ skali](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Oto przykład polecenia interfejsu wiersza polecenia, aby ponowne zrównoważenie topologii Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

**Aby skalować klastrów**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **klastrów usługi HDInsight** z menu po lewej stronie.
3. Kliknij klaster, który chcesz skalować.
3. Kliknij przycisk **skalowanie klastra**.
4. Wprowadź **węzłami liczba procesów roboczych**. Limit liczby węzłów klastra zależy od subskrypcji platformy Azure. Możesz skontaktować się z pomocy dotyczącej rozliczeń w celu zwiększenia limitu.  Informacje o kosztach odzwierciedla zmiany wprowadzone do liczby węzłów.

    ![HDInsight hadoop hbase storm spark skali](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

## <a name="pauseshut-down-clusters"></a>Wstrzymaj/zamykania klastrów

Większość zadań usługi Hadoop to zadania wsadowe, które będzie uruchamiane co pewien czas. Większość klastrów platformy Hadoop istnieje duże okresach czasu, który nie jest używany klaster do przetwarzania. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany.
Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Istnieje wiele sposobów zostanie proces:

* Użytkownik fabryki danych Azure. Zobacz [klastrów tworzenie na żądanie opartą na systemie Linux platformą Hadoop w usłudze HDInsight przy użyciu fabryki danych Azure](hdinsight-hadoop-create-linux-clusters-adf.md) do tworzenia usługi HDInsight na żądanie połączone usługi.
* Za pomocą programu Azure PowerShell.  Zobacz [analizowanie danych opóźnienie transmitowane](hdinsight-analyze-flight-delay-data.md).
* Za pomocą interfejsu wiersza polecenia platformy Azure. Zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu wiersza polecenia Azure](hdinsight-administer-use-command-line.md).
* Używanie zestawu SDK .NET usługi HDInsight. Zobacz [Hadoop przesyłania zadań](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Aby uzyskać informacje o cenach, zobacz [cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Aby usunąć klaster z portalu, zobacz [usuwać klastry](#delete-clusters)

## <a name="move-cluster"></a>Przenieś klastra

Klaster usługi HDInsight można przenieść do innej grupy zasobów platformy Azure lub innej subskrypcji.  Zobacz [klastrów listy i Pokaż](#list-and-show-clusters).

## <a name="upgrade-clusters"></a>Uaktualnij klastrów

Zobacz [uaktualnienia klastra usługi HDInsight do nowszej wersji](./hdinsight-upgrade-cluster.md).

## <a name="open-the-ambari-web-ui"></a>Otwórz interfejs użytkownika sieci web Ambari

Ambari zapewnia intuicyjny, łatwy w użyciu Hadoop zarządzania interfejsu użytkownika sieci web przez jego interfejsy API RESTful. Ambari umożliwia administratorom systemu zarządzania i monitorowania klastrów platformy Hadoop.

1. Otwórz klaster usługi HDInsight w portalu Azure.  Zobacz [klastrów listy i Pokaż](#list-and-show-clusters).
2. Kliknij przycisk **klastra pulpitu nawigacyjnego**.

    ![Menu klastra usługi HDInsight Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu.png)

1. Wprowadź nazwę klastra użytkownika i hasło.  Domyślna nazwa użytkownika klastra _admin_. Ambari web UI wygląda następująco:

    ![Interfejs sieci Web Ambari HDInsight Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-ambari-web-ui.png)

Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Zmienianie haseł
Klaster usługi HDInsight mogą być dwa konta użytkownika. (Alias konta użytkownika klastra usługi HDInsight Konto użytkownika HTTP) i konto użytkownika SSH są tworzone w trakcie procesu tworzenia. Interfejs użytkownika sieci web Ambari służy do zmiany nazwy użytkownika konta użytkownika klastra i hasła i akcji skryptu, aby zmienić konto użytkownika SSH

### <a name="change-the-cluster-user-password"></a>Zmień hasło użytkownika klastra
Interfejs sieci Web Ambari służy do zmiany hasła użytkownika klastra. Aby zalogować się do narzędzia Ambari, należy użyć istniejącego klastra nazwy użytkownika i hasła.

> [!NOTE]
> Zmiana hasła użytkownika (Administrator) klastra może spowodować uruchomienia tego klastra niepowodzenie akcji skryptu. Jeśli masz żadnych akcji utrwalonego skryptu węzłów procesu roboczego tego docelowego te skrypty może zakończyć się niepowodzeniem po dodaniu się, że węzły do klastra za pośrednictwem rozmiar operacji. Aby uzyskać więcej informacji dotyczących akcji skryptu, zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Zaloguj się do Interfejsu sieci Web Ambari, przy użyciu poświadczeń użytkownika klastra usługi HDInsight. Domyślna nazwa użytkownika to **admin**. Adres URL jest **https://&lt;nazwy klastra usługi HDInsight > azurehdinsight.net**.
2. Kliknij przycisk **Admin** z górnego menu, a następnie kliknij pozycję "Zarządzaj Ambari".
3. Z menu po lewej stronie kliknij **użytkowników**.
4. Kliknij przycisk **Admin**.
5. Kliknij przycisk **zmiany hasła**.

Ambari następnie zmiany hasła na wszystkich węzłach w klastrze.

### <a name="change-the-ssh-user-password"></a>Zmień hasło użytkownika SSH
1. Za pomocą edytora tekstu, Zapisz poniższy tekst w pliku o nazwie **changepassword.sh**.

    > [!IMPORTANT]
    > Należy użyć edytora używającą LF jako zakończenia linii. Jeśli Edytor używa CRLF, skrypt nie działa.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Przekaż plik do lokalizacji magazynu, który można uzyskać z usługi HDInsight przy użyciu adresu HTTP lub HTTPS. Na przykład plik publicznego przechowywać takie jak magazyn OneDrive lub obiektów Blob platformy Azure. Zapisz identyfikator URI (adres HTTP lub HTTPS) do pliku, odpowiednio tego identyfikatora URI w następnym kroku.
3. W portalu Azure kliknij **klastrów usługi HDInsight**.
4. Kliknij przycisk z klastrem usługi HDInsight.
4. Kliknij przycisk **skryptu akcji**.
4. Z **akcji skryptu** bloku, wybierz opcję **przesłać nowe**. Gdy **przesłać akcji skryptu** zostanie wyświetlony blok, wprowadź następujące informacje:

   | Pole | Wartość |
   | --- | --- |
   | Name (Nazwa) |Zmień ssh hasło |
   | Identyfikator URI skryptu powłoki systemowej |Identyfikator URI do pliku changepassword.sh |
   | Węzły (Head, proces roboczy, Nimbus, przełożonego, dozorcy itp.) |✓ dla wszystkich typów węzła na liście |
   | Parametry |Wprowadź nazwę użytkownika SSH, a następnie nowe hasło. Powinien być jedną spację między nazwę użytkownika i hasło. |
   | Utrwal tę akcję skryptu... |Nie zaznaczaj tego pola wyboru. |
5. Wybierz **Utwórz** zastosować skrypt. Po zakończeniu działania skryptu, będą mogli połączyć się z klastrem przy użyciu nowego hasła przy użyciu protokołu SSH.

## <a name="grantrevoke-access"></a>Dostęp do przydzielenia/odwołania
Klastry HDInsight są następujące usługi sieci web HTTP (wszystkie te usługi mają RESTful punkty końcowe):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Domyślnie te usługi są przyznawane dostępu. Użytkownik może revoke/Udziel dostępu przy użyciu [interfejsu wiersza polecenia Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) i [programu Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Znajdź identyfikator subskrypcji

**Aby znaleźć identyfikatory subskrypcji platformy Azure**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **subskrypcje**. Każda subskrypcja ma nazwę i identyfikator.

Każdy klaster jest powiązany z subskrypcją platformy Azure. Identyfikator jest wyświetlany w klastrze subskrypcji **podstawowych** kafelka. Zobacz [klastrów listy i Pokaż](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Znajdź grupę zasobów
W trybie Azure Resource Manager każdego klastra usługi HDInsight jest tworzony z grupy usługi Azure Resource Manager. Grupy Menedżera zasobów, do której należy klaster zostanie wyświetlony w:

* Lista klastrów ma **grupy zasobów** kolumny.
* Klaster **podstawowych** kafelka.  

Zobacz [klastrów listy i Pokaż](#list-and-show-clusters).

## <a name="find-the-storage-accounts"></a>Znajdź kont magazynu

Klastry HDInsight użyć konta magazynu Azure lub usługi Azure Data Lake Store do przechowywania danych. Każdy klaster usługi HDInsight może mieć jeden domyślne konto magazynu i liczba kont magazynu połączone. Aby wyświetlić listę kont magazynu, najpierw otwórz klaster z portalu, a następnie kliknij przycisk **kont magazynu**:

![Konta magazynu klastra usługi HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

Na poprzednim zrzucie ekranu jest __domyślne__ kolumny wskazującą, czy konto jest domyślne konto magazynu.

Aby wyświetlić listę kont usługi Data Lake Store, kliknij przycisk **dostępu do usługi Data Lake Store** na poprzednim zrzucie ekranu.

## <a name="run-hive-queries"></a>Uruchamianie zapytań Hive
Nie można uruchomić zadania Hive bezpośrednio w portalu Azure, ale można użyć widoku Hive w interfejsie użytkownika sieci Web Ambari.

**Uruchamianie zapytań Hive przy użyciu widoku Hive narzędzia Ambari**

1. Zaloguj się do Interfejsu sieci Web Ambari, przy użyciu poświadczeń użytkownika klastra usługi HDInsight. Domyślna nazwa użytkownika to **admin**. Adres URL jest **https://&lt;nazwy klastra usługi HDInsight > azurehdinsight.net**.
2. Otwórz widok gałąź rejestru, jak pokazano na poniższym zrzucie ekranu:  

    ![HDInsight hive widoku](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. Kliknij przycisk **zapytania** z górnego menu.
4. Wprowadź zapytanie Hive w **edytora zapytań**, a następnie kliknij przycisk **Execute**.

## <a name="monitor-jobs"></a>Monitorowanie zadań
Zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Przeglądanie plików
Przy użyciu portalu Azure, możesz przeglądać zawartość domyślnego kontenera.

1. Zaloguj się do [ https://portal.azure.com ](https://portal.azure.com).
2. Kliknij przycisk **klastrów usługi HDInsight** z menu po lewej stronie, aby wyświetlić listę istniejących klastrów.
3. Kliknij nazwę klastra. Jeśli na liście klastra jest długa, można użyć filtrów w górnej części strony.
4. Kliknij przycisk **kont magazynu** z menu po lewej stronie klastra.
5. Kliknij konto magazynu.
7. Kliknij przycisk **obiekty BLOB** kafelka.
8. Kliknij nazwę kontenera domyślnego.

## <a name="monitor-cluster-usage"></a>Monitorowanie użycia klastra
**Użycia** części bloku klastra usługi HDInsight przedstawia informacje na temat liczby rdzeni dostępne dla Twojej subskrypcji do użycia z usługą HDInsight, a także od liczby rdzeni przydzielone do tego klastra i sposób przydzielania dla węzłów w tym klastrze. Zobacz [klastrów listy i Pokaż](#list-and-show-clusters).

> [!IMPORTANT]
> Aby monitorować usług świadczonych przez klaster usługi HDInsight, należy używać narzędzia Ambari Web lub interfejsu Ambari API REST. Aby uzyskać więcej informacji na temat używania narzędzia Ambari, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu narzędzia Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Połącz z klastrem

* [Korzystanie z programu Hive z usługą HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Używanie protokołu SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule uzyskanych niektóre podstawowe funkcje administracyjne. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Administrowanie HDInsight przy użyciu programu Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administrowanie HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md)
* [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Więcej informacji na temat za pomocą interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md)
* [Szczegółowe informacje na temat używania interfejsu API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Korzystanie z programu Hive w usłudze HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z języka Pig w usłudze HDInsight](hadoop/hdinsight-use-pig.md)
* [Użyj Sqoop w usłudze HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Jest wersję platformy Hadoop w usłudze Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Wiersz polecenia usługi Hadoop"
