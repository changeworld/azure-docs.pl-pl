---
title: Zarządzanie systemem Windows klastrów platformy Hadoop w usłudze HDInsight przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do administrowania usługą usługi HDInsight. Tworzenie klastra usługi HDInsight, otwórz interakcyjne konsoli języka JavaScript, a konsoli poleceń usługi Hadoop.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 9295a988-bd88-453a-8c8b-55fa103bf39c
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 78541feda8c82be143f91fdde88eac179cf0b1a5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405368"
---
# <a name="manage-windows-based-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Zarządzanie systemem Windows klastrów platformy Hadoop w usłudze HDInsight przy użyciu portalu Azure

Przy użyciu [portalu Azure][azure-portal], można utworzyć klastry z systemem Windows Hadoop w usłudze Azure HDInsight, Zmień hasło użytkownika Hadoop i dostępu do polecenia Hadoop, Włącz protokół RDP (Remote Desktop) Konsola w klastrze.

Informacje przedstawione w tym artykule dotyczą tylko klastrów usługi HDInsight opartych na okna. Aby uzyskać informacje na temat zarządzania opartych na systemie Linux klastrów, zobacz [klastrów zarządzania Hadoop w usłudze HDInsight przy użyciu portalu Azure](hdinsight-administer-use-portal-linux.md).

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Konto usługi Azure Storage** -klastra usługi HDInsight używa kontenera magazynu obiektów Blob platformy Azure jako domyślny system plików. Aby uzyskać więcej informacji o sposobie magazynu obiektów Blob Azure zapewnia bezproblemową obsługę z klastrami usługi HDInsight, zobacz [Użyj magazynu obiektów Blob Azure z usługą HDInsight](hdinsight-hadoop-use-blob-storage.md). Aby uzyskać więcej informacji na temat tworzenia konta usługi Azure Storage, zobacz [jak utworzyć konto magazynu](../storage/common/storage-create-storage-account.md).

## <a name="open-the-portal"></a>Otwórz Portal
1. Zaloguj się do [ https://portal.azure.com ](https://portal.azure.com).
2. Po otwarciu portalu, możesz:

   * Kliknij przycisk **Utwórz zasób** z menu po lewej stronie do utworzenia nowego klastra:

       ![przycisk Nowy klaster usługi HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * Kliknij przycisk **klastrów usługi HDInsight** z menu po lewej stronie.

       ![Przycisk klastra usługi HDInsight portalu Azure](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     Jeśli **HDInsight** nie są wyświetlane w menu po lewej stronie kliknij pozycję **Przeglądaj**.

     ![Przycisk klastra przeglądania portalu Azure](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>Tworzenie klastrów
Aby uzyskać instrukcje tworzenia przy użyciu portalu, zobacz [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

HDInsight działa ze składnikami szeroki zakres Hadoop. Lista składników, które zostały zweryfikowane i obsługiwane, zobacz [jest wersję platformy Hadoop w usłudze Azure HDInsight](hdinsight-component-versioning.md). HDInsight można dostosować za pomocą jednej z następujących opcji:

* Za pomocą akcji skryptu umożliwiają uruchamianie niestandardowych skryptów, które można dostosować klastra, aby zainstalować składniki niestandardowe, takie jak Giraph lub Solr albo zmień konfigurację klastra. Aby uzyskać więcej informacji, zobacz [dostosować klastra usługi HDInsight przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster.md).
* Użyj parametrów dostosowania klastra w programu Azure PowerShell lub zestawu .NET SDK usługi HDInsight podczas tworzenia klastra. Te zmiany w konfiguracji są następnie zachowywane przez okres istnienia klastra i nie dotyczy reimages węzła klastra, które platformy Azure okresowo przesyła konserwacji. Aby uzyskać więcej informacji na temat używania parametrów dostosowania klastra, zobacz [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Niektóre składniki natywnego języka Java, takich jak Mahout i usuwania kaskadowego, można uruchomić w klastrze jako pliki JAR. Te pliki JAR można dystrybuować do magazynu obiektów Blob platformy Azure i przesłać do klastrów usługi HDInsight za pomocą mechanizmów przesyłanie zadań Hadoop. Aby uzyskać więcej informacji, zobacz [Hadoop przesyłania zadania programowo](hadoop/submit-apache-hadoop-jobs-programmatically.md).

  > [!NOTE]
  > Jeśli masz problemy z wdrożeniem pliki JAR do klastrów usługi HDInsight lub wywoływania pliki JAR w klastrach HDInsight, skontaktuj się z [Microsoft Support](https://azure.microsoft.com/support/options/).
  >
  > Kaskadowe nie jest obsługiwany przez usługi HDInsight, a nie jest uprawniony do firmy Microsoft Support. Aby uzyskać listę obsługiwanych składników, zobacz [nowości w wersjach klastra dostarczanych z usługą HDInsight](hdinsight-component-versioning.md).
  >
  >

Instalacja oprogramowania niestandardowych w klastrze za pomocą usługi Podłączanie pulpitu zdalnego nie jest obsługiwana. Należy unikać przechowywania plików na dyskach węzła głównego, jak będą utracone, jeśli potrzebujesz ponownie utworzyć klastry. Zalecane jest przechowywanie plików w magazynie obiektów Blob platformy Azure. Magazyn obiektów blob jest trwały.

## <a name="list-and-show-clusters"></a>Lista i Pokaż klastrów
1. Zaloguj się do [ https://portal.azure.com ](https://portal.azure.com).
2. Kliknij przycisk **klastrów usługi HDInsight** z menu po lewej stronie.
3. Kliknij nazwę klastra. Jeśli na liście klastra jest długa, można użyć filtrów w górnej części strony.
4. Kliknij dwukrotnie klastra z listy, aby wyświetlić szczegóły.

    **Menu i essentials**:

    ![Essentials klastra usługi HDInsight portalu Azure](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * Aby dostosować menu, kliknij prawym przyciskiem myszy w dowolnym miejscu w menu, a następnie kliknij przycisk **Dostosuj**.
   * **Ustawienia** i **wszystkie ustawienia**: Wyświetla **ustawienia** bloku dla klastra, co pozwala na dostęp do szczegółowych informacji dla klastra.
   * **Pulpit nawigacyjny**, **pulpit nawigacyjny klastra** i **adresu URL: są to wszystkie sposoby dostęp do pulpitu nawigacyjnego klastra, który jest sieci Ambari Web w klastrach opartych na systemie Linux. -** Bezpiecznej powłoki **: Zawiera instrukcje, aby nawiązać połączenie z klastrem przy użyciu połączenia protokołu Secure Shell (SSH).
   * **Skalowanie klastra**: umożliwia zmianę liczby węzłów procesu roboczego dla tego klastra.
   * **Usuń**: usuwa klastra.
   * **Szybki Start**: Wyświetla informacje, które pomogą Ci rozpocząć korzystanie z usługi HDInsight.
   * ** Użytkownicy: Umożliwia ustawienie uprawnień dla *portalu zarządzania* klastra dla innych użytkowników w Twojej subskrypcji platformy Azure.

     > [!IMPORTANT]
     > To *tylko* dotyczy dostępu i uprawnienia do tego klastra w portalu Azure i nie ma wpływu na kto może nawiązać połączenia lub przesyłanie zadań do klastra usługi HDInsight.
     >
     >
   * **Tagi**: tagi umożliwiają skonfigurowanie pary klucz wartość do definiowania niestandardowych taksonomii usług w chmurze. Na przykład może utworzyć klucz o nazwie **projektu**, a następnie użyć wspólną wartość wszystkie usługi powiązane z określonego projektu.
   * **Widoki Ambari**: linki sieci Web Ambari.

     > [!IMPORTANT]
     > Aby zarządzać usług świadczonych przez klaster usługi HDInsight, należy użyć narzędzia Ambari Web lub interfejsu Ambari API REST. Aby uzyskać więcej informacji na temat używania narzędzia Ambari, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu narzędzia Ambari](hdinsight-hadoop-manage-ambari.md).
     >
     >

     **Użycie**:

     ![Obciążenie klastra usługi HDInsight portalu Azure](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. Kliknij przycisk **ustawienia**.

    ![Obciążenie klastra usługi HDInsight portalu Azure](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **Właściwości**: wyświetlanie właściwości klastra.
   * **Tożsamość usługi AAD klastra**:
   * **Klucze magazynu Azure**: wyświetlić domyślne konto magazynu i klucza. Konto magazynu jest konfiguracji podczas procesu tworzenia klastra.
   * **Klaster logowania**: Zmień klastra HTTP, nazwa użytkownika i hasło.
   * **Zewnętrzne magazyny**: Wyświetl magazyny Hive i Oozie. Magazyny można skonfigurować tylko w trakcie procesu tworzenia klastra.
   * **Skalowanie klastra**: Zwiększ i zmniejsza liczbę węzłów procesu roboczego w klastrze.
   * **Pulpit zdalny**: Włącz i Wyłącz dostęp do usług pulpitu zdalnego (RDP) i skonfiguruj RDP username.  Nazwa użytkownika pulpitu zdalnego musi być inna niż nazwa użytkownika HTTP.
   * **Partnera partner of Record**:

     > [!NOTE]
     > Jest to rodzajowy lista dostępnych ustawień; nie wszystkie z nich będzie stosowany w przypadku wszystkich typów klastra.
     >
     >
6. Kliknij przycisk **właściwości**:

    Sekcja właściwości zawiera następujące elementy:

   * **Nazwa hosta**: Nazwa klastra.
   * **Adres URL klastra**.
   * **Stan**: obejmują zostało przerwane, zaakceptowane, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operacyjne, uruchomione, błąd, usuwanie, usunięty, upłynął limit czasu, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, ClusterCustomization CertRolloverQueued, ResizeQueued,
   * **Region**: Lokalizacja platformy Azure. Aby uzyskać listę obsługiwanych lokalizacji platformy Azure, zobacz **Region** lista rozwijana na [cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Dane utworzone**.
   * **System operacyjny**: albo **Windows** lub **Linux**.
   * **Typ**: Hadoop, HBase, Storm, Spark.
   * **Wersja**. Zobacz [wersji usługi HDInsight](hdinsight-component-versioning.md)
   * **Subskrypcja**: Nazwa subskrypcji.
   * **Identyfikator subskrypcji**.
   * **Podstawowe źródło danych**. Konto magazynu obiektów Blob platformy Azure używana jako domyślnego systemu plików usługi Hadoop.
   * **Warstwa cenowa węzłów procesu roboczego**.
   * **Warstwę cenową węzła HEAD**.

## <a name="delete-clusters"></a>Usuwanie klastrów
Usunięcie klastra nie spowoduje usunięcia domyślne konto magazynu lub wszystkie połączone konta magazynu. Można ponownie utworzyć klastra przy użyciu tego samego konta magazynu i tym samym magazyny.

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **Przeglądaj wszystko** z menu po lewej stronie kliknij **klastrów usługi HDInsight**, kliknij swoją nazwę klastra.
3. Kliknij przycisk **usunąć** z górnego menu, a następnie postępuj zgodnie z instrukcjami.

Zobacz też [Wstrzymaj/zamykania klastrów](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Skalowanie klastrów
Skalowanie funkcji klastra umożliwia zmianę liczby węzłów procesu roboczego używane przez klaster, który jest uruchomiony w usłudze Azure HDInsight bez konieczności ponownego tworzenia klastra.

> [!NOTE]
> Tylko klastry usługi HDInsight w wersji 3.1.3 lub nowszym są obsługiwane. Jeśli masz pewności, jaka wersja klastra, można sprawdzić na stronie właściwości.  Zobacz [klastrów listy i Pokaż](#list-and-show-clusters).
>
>

Wpływ zmianę liczby węzłów danych dla każdego typu obsługiwanych przez HDInsight klastra:

* Hadoop

    Można bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze platformy Hadoop, który jest uruchomiony bez wpływu na wszystkie oczekujące lub uruchomione zadania. Również można przesłać nowe zadania, gdy operacja jest w toku. Błędy w operacji skalowania bezpiecznie obsługi tak, aby zawsze pozostanie w stanie funkcjonalności klastra.

    Podczas skalowania klastra usługi Hadoop w dół dzięki zmniejszeniu liczby węzłów danych są ponownie uruchamiane niektóre z tych usług w klastrze. Powoduje to wszystkich uruchomionych i oczekujące zadania się niepowodzeniem po zakończeniu operacji skalowania. Można jednak Prześlij ponownie zadania po zakończeniu operacji.
* HBase

    Można bezproblemowo dodać lub usunąć węzły do klastra HBase jest uruchomiona. Serwery regionalne automatycznie równoważy w ciągu kilku minut od zakończenia operacji skalowania. Można jednak również ręcznie saldo serwery regionalne logowanie do headnode klastra i uruchamiając następujące polecenia z poziomu okna wiersza polecenia:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    Aby uzyskać więcej informacji dotyczących korzystania z powłoki HBase Zobacz]
* Storm

    Bezproblemowo można dodawać i usuwać dane węzły do klastra Storm, jest uruchomiona. Jednak po pomyślnym zakończeniu operacji skalowania, konieczne będzie ponowne zrównoważenie topologii.

    Ponowne równoważenie można zrobić na dwa sposoby:

  * STORM interfejsu użytkownika sieci web
  * Narzędzia interfejsu wiersza polecenia (CLI)

    Zapoznaj się z [dokumentację Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) więcej szczegółów.

    Interfejs użytkownika sieci web Storm jest dostępna w klastrze usługi HDInsight:

    ![Zrównoważ skali storm w usłudze HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Oto przykład sposobu użycia polecenia interfejsu wiersza polecenia do ponowne zrównoważenie topologii Storm:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Aby skalować klastrów**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **Przeglądaj wszystko** z menu po lewej stronie kliknij **klastrów usługi HDInsight**, kliknij swoją nazwę klastra.
3. Kliknij przycisk **ustawienia** z menu u góry, a następnie kliknij przycisk **klaster w skali**.
4. Wprowadź **węzłami liczba procesów roboczych**. Limit liczby węzłów klastra jest zależny od używanej subskrypcji platformy Azure. Możesz skontaktować się z pomocy dotyczącej rozliczeń w celu zwiększenia limitu.  Informacje o kosztach zostaną one zastosowane zmiany wprowadzone do liczby węzłów.

    ![Skala Spark Storm bazy danych HBase w usłudze HDInsight Hadoop](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Wstrzymaj/zamykania klastrów
Większość zadań usługi Hadoop to zadania wsadowe, które są tylko od czasu do czasu uruchomienia. Większość klastrów platformy Hadoop istnieje duże okresach czasu, który nie jest używany klaster do przetwarzania. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany.
Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Istnieje wiele sposobów zostanie proces:

* Użytkownik fabryki danych Azure. Zobacz [połączoną usługą usługi HDInsight Azure](../data-factory/compute-linked-services.md) i [transformacji i analizy przy użyciu fabryki danych Azure](../data-factory/transform-data.md) dla usługi HDInsight na żądanie i samodzielnie zdefiniowanym połączone usługi.
* Za pomocą programu Azure PowerShell.  Zobacz [analizowanie danych opóźnienie transmitowane](hdinsight-analyze-flight-delay-data.md).
* Za pomocą interfejsu wiersza polecenia platformy Azure. Zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu wiersza polecenia Azure](hdinsight-administer-use-command-line.md).
* Używanie zestawu SDK .NET usługi HDInsight. Zobacz [Hadoop przesyłania zadań](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Aby uzyskać informacje o cenach, zobacz [cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Aby usunąć klaster z portalu, zobacz [usuwać klastry](#delete-clusters)

## <a name="change-cluster-username"></a>Zmiana nazwy użytkownika z klastra
Klaster usługi HDInsight mogą być dwa konta użytkownika. Utworzono konto użytkownika klastra usługi HDInsight w trakcie procesu tworzenia. Można również utworzyć konto użytkownika RDP do uzyskiwania dostępu do klastra za pomocą protokołu RDP. Zobacz [Włącz pulpit zdalny](#connect-to-hdinsight-clusters-by-using-rdp).

**Aby zmienić nazwę użytkownika klastra usługi HDInsight i hasło**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **Przeglądaj wszystko** z menu po lewej stronie kliknij **klastrów usługi HDInsight**, kliknij swoją nazwę klastra.
3. Kliknij przycisk **ustawienia** z menu u góry, a następnie kliknij przycisk **logowania do klastra**.
4. Jeśli **logowania do klastra** została włączona, należy kliknąć opcję **wyłączyć**, a następnie kliknij przycisk **włączyć** przed zmianą nazwy użytkownika i hasła.
5. Zmień **nazwa logowania klastra** i/lub **hasło logowania klastra**, a następnie kliknij przycisk **zapisać**.

    ![HDInsight zmienić http klastra użytkownika nazwy użytkownika hasło](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>Dostęp do przydzielenia/odwołania
Klastry HDInsight są następujące usługi sieci web HTTP (wszystkie te usługi mają RESTful punkty końcowe):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Domyślnie te usługi są przyznawane dostępu. Możesz można odwołać/Udziel dostępu w portalu Azure.

> [!NOTE]
> Przez przyznawanie/odwoływanie dostępu, spowoduje zresetowanie klastra, nazwa użytkownika i hasło.
>
>

**Do przydzielenia/odwołania dostępu do usług sieci web HTTP**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **Przeglądaj wszystko** z menu po lewej stronie kliknij **klastrów usługi HDInsight**, kliknij swoją nazwę klastra.
3. Kliknij przycisk **ustawienia** z menu u góry, a następnie kliknij przycisk **logowania do klastra**.
4. Jeśli **logowania do klastra** została włączona, należy kliknąć opcję **wyłączyć**, a następnie kliknij przycisk **włączyć** przed zmianą nazwy użytkownika i hasła.
5. Aby uzyskać **nazwa użytkownika logowania klastra** i **hasło logowania klastra**, wprowadź nową nazwę użytkownika i hasło (odpowiednio) dla klastra.
6. Kliknij przycisk **SAVE** (Zapisz).

    ![HDInsight sumy Usuń dostęp do usługi sieci web http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>Znajdź domyślne konto magazynu
Każdy klaster usługi HDInsight ma domyślne konto magazynu. Domyślne konto magazynu i kluczy dla klastra jest wyświetlana w obszarze **ustawienia**/**właściwości**/**klucze magazynu Azure**. Zobacz [klastrów listy i Pokaż](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Znajdź grupę zasobów
W trybie Azure Resource Manager każdego klastra usługi HDInsight jest tworzony z grupy zasobów platformy Azure. Grupy zasobów platformy Azure, do której należy klaster zostanie wyświetlony w:

* Lista klastrów ma **grupy zasobów** kolumny.
* Klaster **podstawowych** kafelka.  

Zobacz [klastrów listy i Pokaż](#list-and-show-clusters).

## <a name="open-hdinsight-query-console"></a>Otwórz konsolę usługi HDInsight zapytania
Konsolę zapytania HDInsight obejmuje następujące funkcje:

* **Edytor hive**: interfejs sieci web graficznego interfejsu użytkownika A przesyłania zadań Hive.  Zobacz [uruchamianie zapytań Hive przy użyciu konsoli zapytania](hadoop/apache-hadoop-use-hive-query-console.md).

    ![Edytor portalu hive HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **Historia zadania**: Monitor Hadoop zadania.  

    ![HDInsight historii zadań portalu](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Kliknij przycisk **nazwa zapytania** wyświetlić szczegółowe informacje, łącznie z właściwości zadania **zapytanie dotyczące zadań**, i ** dane wyjściowe zadania. Możesz również pobrać zarówno kwerendy, jak i dane wyjściowe do stacji roboczej.
* **Przeglądarka plików**: Przejrzyj domyślne konto usługi storage i połączone konta magazynu.

    ![HDInsight portalu pliku przeglądarki przeglądania](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    Na zrzucie ekranu **<Account>** typ wskazuje element jest konto magazynu platformy Azure.  Kliknij nazwę konta, aby przeglądać pliki.
* **Interfejs użytkownika Hadoop**.

    ![Portal usługi HDInsight Hadoop interfejsu użytkownika](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    Z **interfejsu użytkownika Hadoop*, przeglądać pliki, a w dzienniku.
* **Yarn interfejsu użytkownika**.

    ![Portal usługi HDInsight YARN interfejsu użytkownika](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Uruchamianie zapytań Hive
Do uruchamiania zadań Hive z portalu, kliknij przycisk **Edytor Hive** w konsoli usługi HDInsight zapytania. Zobacz [konsoli Otwórz zapytanie HDInsight](#open-hdinsight-query-console).

## <a name="monitor-jobs"></a>Monitorowanie zadań
Aby monitorować zadania z portalu, kliknij przycisk **historii zadań** w konsoli usługi HDInsight zapytania. Zobacz [konsoli Otwórz zapytanie HDInsight](#open-hdinsight-query-console).

## <a name="browse-files"></a>Przeglądanie plików
Aby przeglądać pliki przechowywane w domyślne konto magazynu i kont magazynu połączone, kliknij przycisk **przeglądarka plików** w konsoli usługi HDInsight zapytania. Zobacz [konsoli Otwórz zapytanie HDInsight](#open-hdinsight-query-console).

Można również użyć **przeglądać systemu plików** narzędzie z **interfejsu użytkownika Hadoop** w konsoli usługi HDInsight.  Zobacz [konsoli Otwórz zapytanie HDInsight](#open-hdinsight-query-console).

## <a name="monitor-cluster-usage"></a>Monitorowanie użycia klastra
**Użycia** części bloku klastra usługi HDInsight przedstawia informacje na temat liczby rdzeni dostępne dla Twojej subskrypcji do użycia z usługą HDInsight, a także od liczby rdzeni przydzielone do tego klastra i sposób przydzielania dla węzłów w tym klastrze. Zobacz [klastrów listy i Pokaż](#list-and-show-clusters).

> [!IMPORTANT]
> Aby monitorować usług świadczonych przez klaster usługi HDInsight, należy używać narzędzia Ambari Web lub interfejsu Ambari API REST. Aby uzyskać więcej informacji na temat używania narzędzia Ambari, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu narzędzia Ambari](hdinsight-hadoop-manage-ambari.md)
>
>

## <a name="open-hadoop-ui"></a>Otwórz Hadoop interfejsu użytkownika
Aby monitorować klastra, przeglądać systemu plików i sprawdź dzienniki, kliknij przycisk **interfejsu użytkownika Hadoop** w konsoli usługi HDInsight zapytania. Zobacz [konsoli Otwórz zapytanie HDInsight](#open-hdinsight-query-console).

## <a name="open-yarn-ui"></a>Otwórz Yarn interfejsu użytkownika
Aby korzystać z interfejsu użytkownika Yarn, kliknij przycisk **interfejsie użytkownika Yarn** w konsoli usługi HDInsight zapytania. Zobacz [konsoli Otwórz zapytanie HDInsight](#open-hdinsight-query-console).

## <a name="connect-to-clusters-using-rdp"></a>Łączenie się z klastrami za pomocą protokołu RDP
Poświadczenia podane podczas jego tworzenia klastra zapewniają dostęp do usługi w klastrze, ale nie do klastra za pośrednictwem pulpitu zdalnego. Dostęp do usług pulpitu zdalnego można włączyć podczas udostępniania klastra lub po zainicjowaniu obsługi klastra. Aby uzyskać instrukcje dotyczące włączania Pulpit zdalny podczas tworzenia, zobacz [HDInsight tworzenia klastra](hdinsight-hadoop-provision-linux-clusters.md).

**Aby włączyć Pulpit zdalny**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **Przeglądaj wszystko** z menu po lewej stronie kliknij **klastrów usługi HDInsight**, kliknij swoją nazwę klastra.
3. Kliknij przycisk **ustawienia** z menu u góry, a następnie kliknij przycisk **pulpitu zdalnego**.
4. Wprowadź **wygasa**, **nazwa użytkownika pulpitu zdalnego** i **hasła pulpitu zdalnego**, a następnie kliknij przycisk **włączyć**.

    ![HDInsight Włączanie wyłączanie Konfigurowanie pulpitu zdalnego](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    Wartości domyślne dla wygasa jest tydzień.

   > [!NOTE]
   > Umożliwia także zestawu .NET SDK usługi HDInsight można włączyć pulpitu zdalnego w klastrze. Użyj **EnableRdp** metody dla obiekt klienta usługi HDInsight w następujący sposób: **klienta. EnableRdp (nazwa_klastra, lokalizacji, "rdpuser", "rdppassword" DateTime.Now.AddDays(6))**. Podobnie, aby wyłączyć pulpitu zdalnego w klastrze, można użyć **klienta. DisableRdp (clustername, lokalizacja)**. Aby uzyskać więcej informacji na temat tych metod, zobacz [odwołania do zestawu SDK .NET usługi HDInsight](http://go.microsoft.com/fwlink/?LinkId=529017). Ma to zastosowanie tylko w przypadku klastrów usługi HDInsight w systemie Windows.
   >
   >

**Aby ustanowić połączenie z klastrem przy użyciu protokołu RDP**

1. Zaloguj się do [Portal][azure-portal].
2. Kliknij przycisk **Przeglądaj wszystko** z menu po lewej stronie kliknij **klastrów usługi HDInsight**, kliknij swoją nazwę klastra.
3. Kliknij przycisk **ustawienia** z menu u góry, a następnie kliknij przycisk **pulpitu zdalnego**.
4. Kliknij przycisk **Connect** i postępuj zgodnie z instrukcjami. Jeśli połączenie jest wyłączona, należy włączyć ją najpierw. Upewnij się, przy użyciu podanej nazwy użytkownika pulpitu zdalnego i hasła.  Nie można użyć poświadczeń użytkownika klastra.

## <a name="open-hadoop-command-line"></a>Otwórz wiersz polecenia usługi Hadoop
Połącz się z klastrem przy użyciu pulpitu zdalnego i za pomocą wiersza polecenia platformy Hadoop, musisz najpierw włączyć Pulpit zdalny dostęp do klastra zgodnie z opisem w poprzedniej sekcji.

**Aby otworzyć wiersz polecenia usługi Hadoop**

1. Połącz z klastrem przy użyciu pulpitu zdalnego.
2. Na pulpicie kliknij dwukrotnie **wiersza polecenia usługi Hadoop**.

    ![HDI.HadoopCommandLine][image-hadoopcommandline]

    Aby uzyskać więcej informacji o poleceniach Hadoop, zobacz [Hadoop polecenia odwołanie](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

W poprzednim zrzucie ekranu Nazwa folderu ma numer wersji usługi Hadoop, osadzonych. Numer wersji można zmieniać w oparciu o wersja składników Hadoop zainstalowany w klastrze. Zmienne środowiskowe Hadoop służy do odwoływania się do tych folderów. Na przykład:

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

## <a name="next-steps"></a>Kolejne kroki
W tym artykule uzyskanych sposób tworzenia klastra usługi HDInsight przy użyciu portalu i jak otworzyć narzędzia wiersza polecenia usługi Hadoop. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Administrowanie HDInsight przy użyciu programu Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administrowanie HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md)
* [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Przesyłanie zadań Hadoop programowo](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Jest wersję platformy Hadoop w usłudze Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Wiersz polecenia usługi Hadoop"
