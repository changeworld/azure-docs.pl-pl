---
title: Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu witryny Azure portal
description: Dowiedz się, jak utworzyć i zarządzać klastrami HDInsight przy użyciu witryny Azure portal.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: hrasheed
ms.openlocfilehash: 44de565b8c94369c5751938b5df939f0af6e7f36
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068915"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu witryny Azure portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Za pomocą [witryny Azure portal](https://portal.azure.com), można zarządzać [Apache Hadoop](https://hadoop.apache.org/) klastrów w usłudze Azure HDInsight. Selektor karty powyżej, aby uzyskać informacje na temat zarządzania klastrami Hadoop w HDInsight przy użyciu innych narzędzi.

## <a name="prerequisites"></a>Wymagania wstępne

Istniejący klaster Apache Hadoop w HDInsight.  Zobacz [opartych na systemie Linux z Tworzenie klastrów w HDInsight przy użyciu witryny Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Wprowadzenie
Zaloguj się do witryny [https://portal.azure.com](https://portal.azure.com).

## <a name="showClusters"></a> Listy i wyświetlaniu klastrów
**Klastry HDInsight** stronie zostanie wyświetlona lista istniejących klastrów.  Z poziomu portalu:
1. Wybierz **wszystkich usług** menu po lewej stronie.
2. Wybierz **klastry HDInsight** w obszarze **analizy**.

## <a name="homePage"></a> Strona główna klastra 
Wybierz nazwę klastra z [ **klastry HDInsight** ](#showClusters) strony.  Spowoduje to otwarcie **Przegląd** widoku, który wygląda podobnie do poniższej ilustracji:

![Podstawy klastra HDInsight portalu Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Menu u góry:**  

| Element| Opis |
|---|---|
|Przenieś|Przenosi klastra do innej grupy zasobów lub do innej subskrypcji.|
|Usuwanie|Usuwa klaster. |
|Odśwież|Odświeża widoku.|

**Menu po lewej stronie:**  
  - **Menu w lewym górnym**

    | Element| Opis |
    |---|---|
    |Omówienie|Zawiera ogólne informacje dla klastra.|
    |Dziennik aktywności|Pokaż i wyszukiwać w dziennikach aktywności.|
    |Kontrola dostępu (IAM)|Za pomocą przypisań ról.  Zobacz [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md).|
    |`Tags`|Umożliwia ustawienie pary klucz/wartość, aby zdefiniować taksonomię niestandardowych usług w chmurze. Na przykład, może utworzyć klucz o nazwie **projektu**, a następnie użyj wspólnej wartości dla wszystkich usług skojarzone z określonego projektu.|
    |Diagnozowanie i rozwiązywanie problemów|Wyświetl informacje dotyczące rozwiązywania problemów.|
    |Szybki start|Wyświetla informacje, które ułatwia rozpoczęcie korzystania z HDInsight.|
    |Narzędzia|Pomoc dotycząca HDInsight narzędzia pokrewne.|

  - **Menu Ustawienia**  

    | Element| Opis |
    |---|---|
    |Rozmiar klastra|Sprawdź, zwiększyć i zmniejszyć liczbę węzłów procesu roboczego klastra. Zobacz [Skaluj klastry](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Limity przydziału|Wyświetl rdzeni używane i dostępne dla Twojej subskrypcji.|
    |Logowania do klastra i protokołu SSH|Zawiera instrukcje łączenia z klastrem przy użyciu połączenia protokołu Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Usługa Data Lake Storage 1. generacji|Konfigurowanie dostępu Data Lake Storage Gen1.  Zobacz [Szybki start: Konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).|
    |Konta magazynu|Wyświetl klucze i kont magazynu. Konta magazynu są skonfigurowane w trakcie procesu tworzenia klastra.|
    |Aplikacje|Dodawać i usuwać aplikacje HDInsight.  Zobacz [instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md).|
    |Akcje skryptu|Uruchamiać skrypty powłoki systemowej w klastrze. Zobacz [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).|
    |Zewnętrzne magazyny metadanych|Widok [Apache Hive](https://hive.apache.org/) i [Apache Oozie](https://oozie.apache.org/) magazyny metadanych. Magazyny metadanych można skonfigurować tylko podczas procesu tworzenia klastra.|
    |HDInsight partnera|Dodaj/Usuń bieżącego partnera HDInsight.|
    |Właściwości|Widok [właściwości klastra](#properties).|
    |Blokady|Dodaj blokadę, aby zapobiec klastra trwa zmodyfikowane lub usunięte.|
    |Eksportowanie szablonu|Wyświetlanie i eksportowanie szablonu usługi Azure Resource Manager dla klastra. Obecnie można wyeksportować tylko konta magazynu Azure zależnego. Zobacz [klastrów opartych na systemie Linux z tworzenia Apache Hadoop w HDInsight przy użyciu szablonów usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Monitorowanie menu**

    | Element| Opis |
    |---|---|
    |Alerty|Zarządzanie alertami i akcje.|
    |Metryki|Monitoruj metryki klastra w dziennikach w usłudze Azure Monitor.|
    |Ustawienia diagnozy|Ustawienia, na którym będą przechowywane metryki diagnostyki.|
    |Operations Management Suite|Monitorowanie klastra w dziennikach Azure Operations Management Suite (OMS) i usługi Azure Monitor.|

  - **Pomoc techniczna i rozwiązywanie problemów z menu**

    | Element| Opis |
    |---|---|
    |Kondycja zasobów|Zobacz [Przegląd kondycji zasobów platformy Azure](../service-health/resource-health-overview.md).|
    |Nowe żądanie pomocy technicznej|Umożliwia tworzenie biletu pomocy technicznej za pomocą techniczną firmy Microsoft.|

## <a name="properties"></a> Właściwości klastra

Z [klastra, strona główna](#homePage)w obszarze **ustawienia** wybierz **właściwości**.

|Element | Opis |
|---|---|
|NAZWA HOSTA|Nazwa klastra.|
|ADRES URL KLASTRA|Adres URL dla interfejsu sieci web Ambari.|
|Punkt końcowy prywatne|Prywatnych punktów końcowych dla klastra.|
|Bezpieczna powłoka (SSH)|Nazwa użytkownika i hosta do użycia podczas uzyskiwania dostępu do klastra za pośrednictwem protokołu SSH.|
|STAN|Jeden z: Przerwane i zaakceptowane, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operacyjne, uruchomione, błąd, usuwanie, usunięte, przekroczenie limitu czasu, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, lub ClusterCustomization.|
|REGION|Lokalizacja platformy Azure. Aby uzyskać listę obsługiwanych lokalizacji platformy Azure, zobacz **Region** pole listy rozwijanej na [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|DATA UTWORZENIA|Data została wdrożona w klastrze.|
|SYSTEM OPERACYJNY|Albo **Windows** lub **Linux**.|
|TYP|Hadoop, HBase, Storm, Spark.|
|Version|Zobacz [wersji HDInsight](hdinsight-component-versioning.md).|
|SUBSKRYPCJA|Nazwa subskrypcji.|
|DOMYŚLNE ŹRÓDŁO DANYCH|Domyślny system plików klastra.|
|Rozmiary węzłów procesu roboczego|Wybrany rozmiar maszyny Wirtualnej węzłów procesu roboczego.|
|Rozmiar węzła HEAD|Wybrany rozmiar maszyny Wirtualnej węzłów głównych.|
|Sieć wirtualna|Nazwa sieci wirtualnej, który jest wdrażany klaster, jeśli wybrano jeden w czasie wdrażania.|

## <a name="move-clusters"></a>Przenieś klastrów

Klaster usługi HDInsight można przenieść do innej grupy zasobów platformy Azure lub innej subskrypcji.

Z [klastra, strona główna](#homePage):

1. Wybierz **przenieść** z górnego menu.
2. Wybierz **przenieść do innej grupy zasobów** lub **przenieść do innej subskrypcji**.
3. Postępuj zgodnie z instrukcjami na nowej stronie.

## <a name="delete-clusters"></a>Usuwanie klastrów
Usunięcie klastrów nie powoduje usunięcia domyślne konto magazynu, ani wszystkie połączone konta magazynu. Można ponownie utworzyć klaster przy użyciu tego samego konta magazynu i samej magazyny metadanych. Zalecamy używanie nowego domyślnego kontenera obiektów Blob podczas ponownego tworzenia klastra.

Z [klastra, strona główna](#homePage):

1. Wybierz **Usuń** z górnego menu.
2. Postępuj zgodnie z instrukcjami na nowej stronie.

Zobacz też [Pause/zamykania klastrów](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Dodawanie kolejnych kont magazynu

Po utworzeniu klastra możesz dodać dodatkowe konta usługi Azure Storage i kont magazynu usługi Azure Data Lake. Aby uzyskać więcej informacji, zobacz [Dodawanie kolejnych kont magazynu do usługi HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Skalowanie klastrów

Skalowanie funkcji klastra umożliwia zmianę liczby węzłów procesu roboczego używane przez klaster usługi HDInsight na platformie Azure bez konieczności ponownego tworzenia klastra.

Zobacz [klastrów HDInsight skalowania](./hdinsight-scaling-best-practices.md) pełne informacje.

## <a name="pauseshut-down-clusters"></a>Wstrzymywanie/zamykania klastrów

Większość zadań usługi Hadoop to zadania wsadowe, które działają tylko od czasu do czasu. Większość klastrów platformy Hadoop istnieją duże okresy czasu, przez jaki klastra nie jest używany do przetwarzania. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany.
Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Istnieje wiele sposobów, można programować procesu:

* Użytkownik usługi Azure Data Factory. Zobacz [tworzenie na żądanie Apache Hadoop oparte na systemie Linux klastrów w HDInsight przy użyciu usługi Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) tworzenia HDInsight na żądanie połączonych usług.
* Używanie programu Azure PowerShell.  Zobacz [analizowanie danych dotyczących opóźnień lotów](hdinsight-analyze-flight-delay-data-linux.md).
* Użyj wiersza polecenia platformy Azure. Zobacz [zarządzania usługi Azure HDInsight clusters, przy użyciu wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md).
* Użyj zestawu .NET SDK HDInsight. Zobacz [zadania przesyłania Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Aby uzyskać informacje o cenach, zobacz [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Aby usunąć klaster z poziomu portalu, zobacz [usuwać klastry](#delete-clusters)

## <a name="upgrade-clusters"></a>Uaktualnianie klastrów

Zobacz [klastra HDInsight uaktualnienia do nowszej wersji](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Otwórz interfejs użytkownika sieci web Apache Ambari

Ambari udostępnia intuicyjne, łatwe w użyciu Hadoop zarządzania interfejsu użytkownika sieci web obsługiwane przez jego interfejsów API RESTful. Ambari umożliwia administratorom systemu monitorowania klastrów platformy Hadoop oraz zarządzania nimi.

Z [klastra, strona główna](#homePage):

1. Wybierz **pulpity nawigacyjne klastra**.

    ![Menu klastra usługi HDInsight Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Wybierz **Ambari macierzystego** na nowej stronie.
1. Wprowadź nazwę użytkownika klastra i hasło.  Domyślna nazwa użytkownika klastra _administratora_.

Aby uzyskać więcej informacji, zobacz [HDInsight Zarządzanie klastrami za pomocą Interfejsu sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Zmienianie haseł
Klaster usługi HDInsight może mieć dwóch kont użytkowników. Konto użytkownika klastra HDInsight (konto użytkownika HTTP) i konto użytkownika SSH są tworzone podczas procesu tworzenia. Można użyć portalu, aby zmienić hasło konta użytkownika klastra i akcji skryptu, aby zmienić konto użytkownika SSH.

### <a name="change-the-cluster-user-password"></a>Zmień hasło użytkownika klastra

> [!NOTE]  
> Zmiana hasła użytkownika (administratora) klastra może spowodować akcji skryptu, uruchamiać ten klaster nie powiedzie się. Jeśli masz wszystkie akcje utrwalonego skryptu tej węzłów procesu roboczego w docelowej tych skryptów może zakończyć się niepowodzeniem Kiedy dodajesz węzły do klastra za pośrednictwem rozmiar operacji. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

Z [klastra, strona główna](#homePage):
1. Wybierz **klastra i protokołu SSH logowania** w obszarze **ustawienia**.
2. Wybierz **Zresetuj poświadczenia**.
3. Wprowadź i Potwierdź nowe hasło w polach tekstowych.
4. Kliknij przycisk **OK**.

Hasło jest zmieniane na wszystkich węzłach w klastrze.

### <a name="change-the-ssh-user-password"></a>Zmień hasło użytkownika SSH
1. Za pomocą edytora tekstów, Zapisz poniższy tekst w pliku o nazwie **changepassword.sh**.

    > [!IMPORTANT]  
    > Należy użyć edytora, który używa LF, jako koniec wiersza. Jeśli Edytor używa CRLF, następnie skrypt nie działa.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Przekaż plik do lokalizacji magazynu, który jest możliwy z HDInsight przy użyciu adresu protokołu HTTP lub HTTPS. Na przykład plik publicznego przechowywanie takich jak OneDrive lub Azure Blob storage. Zapisz identyfikator URI (adres HTTP lub HTTPS) do pliku, ponieważ ten identyfikator URI jest potrzebna w następnym kroku.
3. Z [klastra, strona główna](#homePage), wybierz opcję **akcji skryptu** w obszarze **ustawienia**.
4. Z **akcji skryptu** wybierz opcję **Prześlij nowe**.
5. Z **Prześlij akcję skryptu** strony, wprowadź następujące informacje:

   | Pole | Wartość |
   | --- | --- |
   | Typ skryptu | Wybierz **- niestandardowa** z listy rozwijanej.|
   | Name (Nazwa) |"Zmień ssh hasła" |
   | Identyfikator URI skryptu powłoki systemowej |Identyfikator URI pliku changepassword.sh |
   | Typy węzłów: (Head procesu roboczego, Nimbus, nadzorca, Zookeeper, itp.) |✓ dla wszystkich typów węzła na liście |
   | Parametry |Wprowadź nazwę użytkownika SSH, a następnie nowe hasło. Powinna być jedną spację między nazwę użytkownika i hasło. |
   | Utrwal tę akcję skryptu... |Nie zaznaczaj tego pola wyboru. |

6. Wybierz **Utwórz** zastosować skrypt. Po zakończeniu działania skryptu jesteś w stanie połączyć się z klastrem przy użyciu protokołu SSH przy użyciu nowego hasła.

## <a name="grantrevoke-access"></a>Dostęp do przydzielenia/odwołania
Klastry HDInsight mają następujące usługi sieci web HTTP (wszystkie te usługi mają punktów końcowych RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Domyślnie te usługi są przyznawane dostępu. Możesz można odwołać/Udziel dostępu za pomocą [programu Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Znajdź identyfikator subskrypcji
Każdy klaster jest powiązany z subskrypcją platformy Azure.  Subskrypcja platformy Azure, identyfikator nie jest widoczny [klastra, strona główna](#homePage).

## <a name="find-the-resource-group"></a>Znajdź grupę zasobów
W trybie usługi Azure Resource Manager każdy klaster HDInsight jest tworzony z grupy usługi Azure Resource Manager. Grupa usługi Resource Manager nie jest widoczny [klastra, strona główna](#homePage).

## <a name="find-the-storage-accounts"></a>Znajdowanie konta magazynu
Klastry HDInsight Użyj konta usługi Azure Storage lub Azure Data Lake Storage do przechowywania danych. Każdy klaster HDInsight może mieć jeden domyślne konto magazynu i liczba połączonych kontach magazynu. Aby wyświetlić listę kont magazynu z [klastra, strona główna](#homePage) w obszarze **ustawienia**, wybierz opcję **kont magazynu**.

## <a name="monitor-jobs"></a>Monitorowanie zadań
Zobacz [HDInsight Zarządzanie klastrami za pomocą Interfejsu sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Rozmiar klastra

**Rozmiar klastra** Kafelek z [klastra, strona główna](#homePage) Wyświetla liczbę rdzeni przydzielonych do tego klastra i jak są przydzielane dla węzłów w klastrze.

> [!IMPORTANT]  
> Aby monitorować usługi świadczone przez klaster usługi HDInsight, należy użyć sieci Ambari Web lub interfejs API REST Ambari. Aby uzyskać więcej informacji na temat korzystania z narzędzia Ambari, zobacz [HDInsight Zarządzanie klastrami przy użyciu narzędzia Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Łączenie z klastrem

* [Use Apache Hive z HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Używanie protokołu SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule mają przedstawiono niektóre podstawowe funkcje administracyjne. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Administrowanie przy użyciu programu PowerShell usługi Azure HDInsight](hdinsight-administer-use-powershell.md)
* [Administrowanie przy użyciu wiersza polecenia platformy Azure HDInsight](hdinsight-administer-use-command-line.md)
* [Tworzenie klastrów HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Szczegółowe informacje na temat korzystania z interfejsu API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Use Apache Hive w HDInsight](hadoop/hdinsight-use-hive.md)
* [W HDInsight przy użyciu narzędzia Apache Sqoop](hadoop/hdinsight-use-sqoop.md)
* [Funkcje (UDF) zdefiniowane przez użytkownika języka Python korzystanie z programu Apache Hive i Apache Pig w HDInsight](hadoop/python-udf-hdinsight.md)
* [Jakiej wersji programu Apache Hadoop znajduje się w usłudze Azure HDInsight?](hdinsight-component-versioning.md)