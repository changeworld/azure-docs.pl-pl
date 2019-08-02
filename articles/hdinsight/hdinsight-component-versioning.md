---
title: Apache Hadoop składniki i wersje — Azure HDInsight
description: Poznaj składniki Apache Hadoop i wersje w usłudze HDInsight oraz poziomy usług dostępne w tej dystrybucji chmurowej platformy danych Hortonworks.
keywords: wersje usługi Hadoop, składniki ekosystemu usługi Hadoop, składniki usługi Hadoop, Sprawdzanie wersji usługi Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/07/2019
ms.openlocfilehash: a9de7c75ef2bd29b2e401ba387ca16a5dfda34fb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68442049"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Jakie składniki Apache Hadoop i wersje są dostępne w usłudze HDInsight?

Dowiedz się więcej o składnikach i wersjach ekosystemu [Apache Hadoop](https://hadoop.apache.org/) w Microsoft Azure HDInsight, a także pakiet Enterprise Security. Należy również zapoznać się z tematem Sprawdzanie wersji składników usługi Hadoop w usłudze HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Składniki Apache Hadoop dostępne z różnymi wersjami usługi HDInsight

Usługa Azure HDInsight obsługuje wiele wersji klastra Hadoop, które można wdrożyć w dowolnym momencie. Każda opcja wyboru wersji tworzy określoną wersję dystrybucji HDP oraz zestaw składników zawartych w tej dystrybucji. Od 4 kwietnia 2017 domyślna wersja klastra używana przez usługę Azure HDInsight to 3,6 i jest oparta na HDP 2,6.

Wersje składników skojarzone z wersjami klastra usługi HDInsight są wymienione w poniższej tabeli: 

> [!NOTE]  
> Domyślna wersja usługi HDInsight może ulec zmianie bez powiadomienia. Jeśli masz zależność wersji, określ wersję usługi HDInsight podczas tworzenia klastrów przy użyciu zestawu .NET SDK z Azure PowerShell i klasycznego interfejsu wiersza polecenia platformy Azure.

| Składnik | HDInsight 4.0 | HDInsight 3,6 (domyślnie) | HDInsight 3,5 | HDInsight 3,4 | HDInsight 3,3 | HDInsight 3,2 |
|---------------------------|---------------|-----------------------------|---------------|---------------|---------------|----------------------|
| Hortonworks Data Platform | 3.0 | 2.6 | 2.5 | 2.4 | 2.3 | 2.2 |
| Apache Hadoop i PRZĘDZa | 3.1.1 | 2.7.3 | 2.7.3 | 2.7.1 | 2.7.1 | 2.6.0 |
| Apache Tez | 0.9.1 | 0.7.0 | 0.7.0 | 0.7.0 | 0.7.0 | 0.5.2 |
| Apache Pig | 0.16.0 | 0.16.0 | 0.16.0 | 0.15.0 | 0.15.0 | 0.14.0 |
| Apache Hive i HCatalog | - | 1.2.1 | 1.2.1 | 1.2.1 | 1.2.1 | 0.14.0 |
| Apache Hive | 3.1.0 | 2.1.0 | - | - | - | - |
| Apache Tez Hive2 | - | 0.8.4 | - | - | - | - |
| Apache Ranger | 1.1.0 | 0.7.0 | 0.6.0 | - | - | - |
| Apache HBase | 2.0.1 | 1.1.2 | 1.1.2 | 1.1.2 | 1.1.1 | 0.98.4 |
| Apache Sqoop | 1.4.7 | 1.4.6 | 1.4.6 | 1.4.6 | 1.4.6 | 1.4.5 |
| Apache Oozie | 4.3.1 | 4.2.0 | 4.2.0 | 4.2.0 | 4.2.0 | 4.1.0 |
| Apache dozorcy | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 |
| Apache Storm | - | 1.1.0 | 1.0.1 | 0.10.0 | 0.10.0 | 0.9.3 |
| Apache Mahout | - | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0 |
| Apache Phoenix | 5 | 4.7.0 | 4.7.0 | 4.4.0 | 4.4.0 | 4.2.0 |
| Apache Spark | 2.3.1, 2,4 | 2.3.0, 2.2.0, 2.1.0 | 1.6.2, 2,0 | 1.6.0 | 1.5.2 | 1.3.1 (tylko system Windows) |
| Apache usługi Livy | 0,5 | 0,4, 0,4, 0,3 | 0,3 | 0,3 | 0.2 | - |
| Apache Kafka | 1.1.1, 2.1 | 1,1, 1,0 * (patrz Uwaga poniżej) | 0.10.0 | 0.9.0 | - | - |
| Apache Ambari | 2.7.0 | 2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 | - |
| Apache Zeppelin | 0.8.0 | 0.7.0 | - | - | - | - |
| Mono | 4.2.1 | 4.2.1 | 4.2.1 | 3.2.8 | - | - |

> [!NOTE]
> Ze względu na wydajność systemu Pomoc techniczna dla Kafka w wersji 0,10 wygasła w marcu 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Wyszukaj bieżące informacje o wersji składnika usługi Hadoop

Wersje składników ekosystemu usługi Hadoop skojarzone z wersjami klastra usługi HDInsight mogą ulec zmianie przy użyciu aktualizacji usługi HDInsight. Aby sprawdzić składniki usługi Hadoop i sprawdzić, które wersje są używane dla klastra, użyj interfejsu API REST Ambari. Polecenie **GetComponentInformation** pobiera informacje o składnikach usługi. Aby uzyskać szczegółowe informacje, zobacz [dokumentację usługi Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Informacje o wersji

Zobacz [Informacje o wersji usługi HDInsight](hdinsight-release-notes.md) , aby uzyskać dodatkowe informacje o wersji dotyczące najnowszych wersji usługi HDInsight.

## <a name="supported-hdinsight-versions"></a>Obsługiwane wersje usługi HDInsight

W poniższych tabelach przedstawiono wersje usługi HDInsight. Wersje HDP odpowiadające każdej wersji usługi HDInsight są wyświetlane wraz z datami wydania produktu. Są również udostępniane daty wygaśnięcia i wycofania pomocy technicznej, jeśli są znane.

### <a name="available-versions"></a>Dostępne wersje

W poniższej tabeli wymieniono wersje usługi HDInsight, które są dostępne w Azure Portal oraz inne metody wdrażania, takie jak PowerShell i .NET SDK.

| Wersja usługi HDInsight | Wersja HDP | VM OS | Data wydania | Data wygaśnięcia pomocy technicznej | Data wycofania | Wysoka dostępność |  Dostępność Azure Portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |HDP 3.0 |Ubuntu 16.0.4 LTS |24 września 2018 r. | | |Yes |Tak |
| HDInsight 3,6 |HDP 2.6 |Ubuntu 16.0.4 LTS |4 kwietnia 2017 | 30 czerwca 2020 |31 grudnia 2020 |Tak |Tak |


> [!NOTE]  
> Po wygaśnięciu obsługi wersji program może nie być dostępny w Microsoft Azure Portal. Wersje klastra są jednak nadal dostępne za pomocą `Version` parametru w poleceniach [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) programu Windows PowerShell i zestawu .NET SDK do momentu wycofania wersji.
>

### <a name="retired-versions"></a>Wycofane wersje

W poniższej tabeli wymieniono wersje usługi HDInsight, które **nie** są dostępne w Azure Portal.

| Wersja usługi HDInsight | Wersja HDP | VM OS | Data wydania | Data wygaśnięcia pomocy technicznej | Data wycofania | Wysoka dostępność |  Dostępność Azure Portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 <br> (Poza platformą Spark) |HDP 2.5 |Ubuntu 16.0.4 LTS |30 września 2016 |5 września 2017 |28 czerwca 2018 r. |Tak |Nie |
| HDInsight 3,4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 marca 2016 r. |29 grudnia 2016 |9 stycznia 2018 r. |Tak |Nie |
| HDInsight 3,3 |HDP 2.3 |Windows Server 2012 R2 |2 grudnia 2015 |27 czerwca 2016 |31 lipca 2018 r. |Yes |Nie |
| HDInsight 3,3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 grudnia 2015 |27 czerwca 2016 |31 lipca 2017 |Tak |Nie |
| HDInsight 3,2 |HDP 2.2 |Ubuntu 12,04 LTS lub Windows Server 2012 R2 |18 lutego 2015 |1 marca 2016 |1 kwietnia 2017 |Tak |Nie |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24 czerwca 2014 |18 maja 2015 |30 czerwca 2016 r. |Tak |Nie |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 lutego 2014 |17 września 2014 |30 czerwca 2015 |Tak |Nie |
| HDInsight 2.1 |HDP 1,3 |Windows Server 2012 R2 |28 października 2013 |12 maja 2014 |31 maja 2015 |Yes |Nie |
| HDInsight 1,6 |HDP 1.1 | |28 października 2013 |26 kwietnia 2014 |31 maja 2015 |Nie |Nie |

> [!NOTE]  
> Klastry o wysokiej dostępności z dwoma węzłami głównymi są wdrażane domyślnie dla usługi HDInsight w wersji 2,1 lub nowszej. Nie są one dostępne w klastrach usługi HDInsight w wersji 1,6.

## <a name="enterprise-security-package-for-hdinsight"></a>pakiet Enterprise Security usługi HDInsight

Zabezpieczenia przedsiębiorstwa to opcjonalny pakiet, który można dodać do klastra usługi HDInsight w ramach przepływu pracy tworzenia klastra. Pakiet Enterprise Security obsługuje:

- Integracja z usługą Active Directory na potrzeby uwierzytelniania.

    W przeszłości można tworzyć tylko klastry usługi HDInsight z uprawnieniami administratora lokalnego i lokalnego użytkownika SSH. Użytkownik lokalny administrator ma dostęp do wszystkich plików, folderów, tabel i kolumn.  Za pomocą pakiet Enterprise Security można włączyć kontrolę dostępu opartą na rolach przez integrację klastrów usługi HDInsight z własnym Active Directory, które obejmują lokalne Active Directory, Azure Active Directory Domain Services lub Active Directory w IaaS Maszyna wirtualna. Administrator domeny w klastrze może udzielić użytkownikom dostępu do klastra za pomocą nazwy użytkownika i hasła firmowej (domeny). 

    Aby uzyskać więcej informacji, zobacz:

    - [Wprowadzenie do Apache Hadoop zabezpieczeń z przyłączonymi do domeny klastrami usługi HDInsight](./domain-joined/hdinsight-security-overview.md)
    - [Planowanie klastrów Apache Hadoop przyłączonych do domeny platformy Azure w usłudze HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Konfigurowanie przyłączonego do domeny środowiska piaskownicy](./domain-joined/apache-domain-joined-configure.md)
    - [Konfigurowanie przyłączonych do domeny klastrów usługi HDInsight przy użyciu Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autoryzacja danych

  - Integracja z platformą Apache Ranger na potrzeby autoryzacji dla kolejek Hive, Spark SQL i przędzy.
  - Można ustawić kontrolę dostępu dla plików i folderów.

    Aby uzyskać więcej informacji, zobacz:

  - [Konfigurowanie zasad Apache Hive w usłudze HDInsight przyłączonych do domeny](./domain-joined/apache-domain-joined-run-hive.md)

- Wyświetl dzienniki inspekcji, aby monitorować dostęp i skonfigurowane zasady. 

### <a name="supported-cluster-types"></a>Obsługiwane typy klastrów

Obecnie tylko następujące typy klastrów obsługują pakiet Enterprise Security:

- Hadoop (tylko Usługa HDInsight 3,6)
- platforma Spark
- Zapytanie interakcyjne

### <a name="support-for-azure-data-lake-storage"></a>Obsługa usługi Azure Data Lake Storage

Pakiet Enterprise Security obsługuje używanie Azure Data Lake Storage zarówno jako magazynu podstawowego, jak i do magazynu dodatków.

### <a name="pricing-and-service-level-agreement"></a>Cennik i umowa dotycząca poziomu usług

Aby uzyskać informacje o cenach i umowie SLA dla pakiet Enterprise Security, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Umowa dotycząca poziomu usług dla wersji klastra usługi HDInsight

Umowa dotycząca poziomu usług (SLA) jest definiowana w obszarze _okna obsługi_. Okno Pomoc techniczna to okres, przez który wersja klastra usługi HDInsight jest obsługiwana przez dział obsługi klienta firmy Microsoft. Jeśli wersja ma przekazaną _datę wygaśnięcia obsługi_ , klaster usługi HDInsight jest poza oknem obsługi. Data wygaśnięcia pomocy technicznej dla określonej wersji usługi HDInsight X (dostępna po nowszej wersji X + 1) jest obliczana jako nowsza z:  

* Wzór 1: Dodaj 180 dni do daty wydania klastra usługi HDInsight w wersji X.
* Formuła 2: Dodaj 90 dni do daty, w której klaster usługi HDInsight w wersji X + 1 jest dostępny w Azure Portal.

_Data wycofania_ to data, po której nie można utworzyć wersji klastra w usłudze HDInsight. Od 31 lipca 2017 nie można zmienić rozmiaru klastra usługi HDInsight po jego dacie wycofania. 

> [!NOTE]  
> Klastry systemu Windows HDInsight (w tym wersje 2,1, 3,0, 3,1, 3,2 i 3,3) działają w rodzinie systemów operacyjnych gościa platformy Azure w wersji 4, która używa wersji 64-bitowej systemu Windows Server 2012 R2. Rodzina systemów operacyjnych gościa platformy Azure w wersji 4 obsługuje .NET Framework wersje 4,0, 4,5, 4.5.1 i 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Informacje o wersji Hortonworks skojarzone z wersjami usługi HDInsight

Sekcja zawiera linki do informacji o wersji dla dystrybucji platform danych Hortonworks i składników Apache, które są używane z usługą HDInsight.
* Klaster usługi HDInsight w wersji 4,0 korzysta z dystrybucji usługi Hadoop opartej na [platformie Hortonworks Data Platform 3,0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html)
* Klaster usługi HDInsight w wersji 3,6 korzysta z dystrybucji usługi Hadoop opartej na [platformie Hortonworks Data Platform 2,6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Klaster usługi HDInsight w wersji 3,5 korzysta z dystrybucji usługi Hadoop opartej na [platformie Hortonworks Data Platform 2,5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Klaster usługi HDInsight w wersji 3,5 jest _domyślnym_ klastrem usługi Hadoop tworzonym w Azure Portal.
* Klaster usługi HDInsight w wersji 3,4 korzysta z dystrybucji usługi Hadoop opartej na [platformie Hortonworks Data Platform 2,4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Klaster usługi HDInsight w wersji 3,3 korzysta z dystrybucji usługi Hadoop opartej na [platformie Hortonworks Data Platform 2,3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Informacje o wersji Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) są dostępne w witrynie Apache.
  * [Informacje o wersji Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) są dostępne w witrynie Apache.
* Klaster usługi HDInsight w wersji 3,2 korzysta z dystrybucji usługi Hadoop opartej na [platformie Hortonworks Data Platform 2,2][hdp-2-2].

  * Informacje o wersji dla określonych składników Apache są dostępne w następujący sposób: [Hive 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [świnie 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [przędzy 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2,0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [burz 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)i [Oozie 4.1.0 ](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Klaster usługi HDInsight w wersji 3,1 korzysta z dystrybucji usługi Hadoop opartej na [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Klastry usługi HDInsight 3,1 utworzone przed listopadem, 7, 2014, są oparte na [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Klaster usługi HDInsight w wersji 3,0 korzysta z dystrybucji usługi Hadoop opartej na [platformie Hortonworks Data Platform 2,0][hdp-2-0-8].
* Klaster usługi HDInsight w wersji 2,1 korzysta z dystrybucji usługi Hadoop opartej na [platformie Hortonworks Data Platform 1,3][hdp-1-3-0].
* Klaster usługi HDInsight w wersji 1,6 korzysta z dystrybucji usługi Hadoop opartej na [platformie Hortonworks Data Platform 1,1][hdp-1-1-0].

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Domyślna konfiguracja węzła i rozmiary maszyn wirtualnych dla klastrów

W poniższej tabeli wymieniono domyślne rozmiary maszyn wirtualnych dla klastrów usługi HDInsight.  Ten wykres jest konieczny, aby zrozumieć rozmiary maszyn wirtualnych używane podczas tworzenia skryptów programu PowerShell lub interfejsu wiersza polecenia platformy Azure w celu wdrożenia klastrów usługi HDInsight.

> [!IMPORTANT]  
> Jeśli potrzebujesz więcej niż 32 węzłów procesu roboczego w klastrze, musisz wybrać rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.

* Wszystkie obsługiwane regiony, z wyjątkiem Brazylii Południowe i Japonia Zachodnia:

|Typ klastra|Hadoop|HBase|Zapytanie interakcyjne|Storm|platforma Spark|ML Server|Kafka|
|---|---|---|---|---|---|---|---|
|Nagłówek: domyślny rozmiar maszyny wirtualnej|D12 v2|D12 v2|D13 v2|A3|D12 v2|D12 v2|D3v2|
|Główna: zalecane rozmiary maszyn wirtualnych|D3 v2|D3 v2|D13|A4 v2|D12 v2|D12 v2|A2M v2|
||D4 v2|D4 v2|D14|A8 v2|D13 v2|D13 v2|D3 v2|
||D12 v2|D12 v2|E16 v3|A2m v2|D14 v2|D14 v2|D4 v2|
||E4 v3|E4 v3|E32 v3|E4 v3|E4 v3|E4 v3|D12 v2|
|Proces roboczy: domyślny rozmiar maszyny wirtualnej|D4 v2|D4 v2|D14 v2|D3 v2|D13 v2|D4 v2|4 D12v2 z 2 dyskami S30 na brokera|
|Proces roboczy: zalecane rozmiary maszyn wirtualnych|D3 v2|D3 v2|D13|D3 v2|D4 v2|D4 v2|D13 v2|
||D4 v2|D4 v2|D14|D4 v2|D12 v2|D12 v2|DS12 v2|
||D12 v2|D12 v2|E16 v3|D12 v2|D13 v2|D13 v2|DS13 v2|
||E4 v3|E4 v3|E20 v3|E4 v3|D14 v2|D14 v2|E4 v3|
||||E32 v3||E16 v3|E16 v3|ES4 v3|
||||E64 v3||E20 v3|E20 v3|E8 v3|
||||||E32 v3|E32 v3|ES8 v3|
||||||E64 v3|E64 v3||
|Dozorcy: domyślny rozmiar maszyny wirtualnej||A4 v2|A4 v2|A4 v2||A2 v2|D3v2|
|Dozorcy: zalecane rozmiary maszyn wirtualnych||A4 v2||A2 v2|||A2M v2|
|||A8 v2||A4 v2|||D3 v2|
|||A2m v2||A8 v2|||E8 v3|
|Usługi ML: domyślny rozmiar maszyny wirtualnej||||||D4 v2||
|Usługi w usłudze ML: zalecany rozmiar maszyny wirtualnej||||||D4 v2||
|||||||D12 v2||
|||||||D13 v2||
|||||||D14 v2||
|||||||E16 v3||
|||||||E20 v3||
|||||||E32 v3||
|||||||E64 v3||

* Brazylia Południowa i Japonia Zachodnia (bez rozmiarów v2):

  | Typ klastra | Hadoop | HBase | Zapytanie interakcyjne |Storm | platforma Spark | ML Services |
  | --- | --- | --- | --- | --- | --- | --- |
  | Nagłówek: domyślny rozmiar maszyny wirtualnej |D12 |D12  | D13 |A3 |D12 |D12 |
  | Główna: zalecane rozmiary maszyn wirtualnych |D3<br/> D4<br/> D12 |D3<br/> D4<br/> D12  | D13<br/> D14 |A3<br/> KART<br/> A5 |D12<br/> D13<br/> D14 |D12<br/> D13<br/> D14 |
  | Proces roboczy: domyślny rozmiar maszyny wirtualnej |D4 |D4  |  D14 |D3 |D13 |D4 |
  | Proces roboczy: zalecane rozmiary maszyn wirtualnych |D3<br/> D4<br/> D12 |D3<br/> D4<br/> D12  | D13<br/> D14 |D3<br/> D4<br/> D12 |D4<br/> D12<br/> D13<br/> D14 | D4<br/> D12<br/> D13<br/> D14 |
  | Dozorcy: domyślny rozmiar maszyny wirtualnej | |A4 v2 | A4 v2| A4 v2 | | A2 v2|
  | Dozorcy: zalecane rozmiary maszyn wirtualnych | |A2<br/> A3<br/> A4 | |A2<br/> A3<br/> A4 | | |
  | Usługi ML: domyślne rozmiary maszyn wirtualnych | | | | | |D4 |
  | Usługi w usłudze ML: zalecane rozmiary maszyn wirtualnych | | | | | |D4<br/> D12<br/> D13<br/> D14 |

> [!NOTE]
> - Nagłówek jest znany jako *Nimbus* dla typu klastra burza.
> - Proces roboczy jest znany jako *opiekun* dla typu klastra burza.
> - Proces roboczy jest znany jako *region* dla typu klastra HBase.

## <a name="next-steps"></a>Następne kroki
- [Konfiguracja klastra dla Apache Hadoop, Spark i nie tylko w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Pracuj w Apache Hadoop w usłudze HDInsight z komputera z systemem Windows](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
