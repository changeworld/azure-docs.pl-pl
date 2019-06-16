---
title: Wiele klastrów HDInsight za pomocą konta usługi Azure Data Lake Storage — Azure
description: Dowiedz się, jak używać więcej niż jednego klastra HDInsight z jednego konta usługi Data Lake Storage
keywords: Magazyn usługi hdinsight, system plików hdfs, danych strukturalnych, dane bez określonej struktury, usługi data lake store
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: b580890b1663aa6ce742443e927e4d760585d4ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64700286"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Wiele klastrów HDInsight za pomocą konta usługi Azure Data Lake Storage

Począwszy od HDInsight w wersji 3.5, można utworzyć klastry HDInsight z kontami usługi Azure Data Lake Storage jako domyślny system plików.
Magazyn usługi Data Lake obsługuje nieograniczony magazyn, który to idealny nie tylko do obsługi dużych ilości danych. można jednak również do hostowania wielu HDInsight clusters udziału pojedynczego konta magazynu usługi Data Lake. Aby uzyskać instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight przy użyciu usługi Data Lake Storage jako magazynu, zobacz [Szybki Start: Konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Ten artykuł zawiera zalecenia dla administratora usługi Data Lake Storage dotyczące konfigurowania pojedynczego i udostępnianie magazynu konta usługi Data Lake używany w wielu **active** klastry HDInsight. Te zalecenia dotyczą obsługującego wiele bezpieczne, a także — Zabezpieczanie klastrów Apache Hadoop dla udostępnionego konta usługi Data Lake Storage.


## <a name="data-lake-storage-file-and-folder-level-acls"></a>Listy kontroli dostępu na poziomie usługi Data Lake Storage plików i folderów

W pozostałej części tego artykułu przyjęto założenie, iż dobrej znajomości poziom listy ACL plików i folderów w usłudze Azure Data Lake Storage, opisana szczegółowo w [Access control w usłudze Azure Data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Usługa Data Lake Storage instalacji dla wielu klastrów HDInsight
Pozwól nam zająć hierarchię folderów dwupoziomowej, aby wyjaśnić, zalecenia dotyczące korzystania z wielu klastrów HDInsight za pomocą konta usługi Data Lake Storage. Należy wziąć pod uwagę, masz konto usługi Data Lake Storage przy użyciu struktury folderów **/klastrów/Finanse**. Z tą strukturą wszystkie klastry, które są wymagane przez organizację Finanse służy /clusters/finance jako lokalizacji przechowywania. W przyszłości, jeśli jest to inny organizacji powiedzieć marketingu chce utworzyć HDInsight klastrów za pomocą tego samego konta usługi Data Lake Storage, ich można utworzyć/klastrów/marketingu. Teraz, po prostu Użyjmy **/klastrów/Finanse**.

Aby umożliwić tej struktury folderów do efektywnego użycia przez klastry HDInsight, administrator usługi Data Lake Storage, należy przypisać odpowiednie uprawnienia, zgodnie z opisem w tabeli. Uprawnienia wymienione w tabeli odpowiadają listy ACL dostępu i nie domyślne-list ACL. 


|Folder  |Uprawnienia  |Użytkownik będący właścicielem  |Grupa będąca właścicielem  | Użytkownik nazwany | Uprawnienia użytkownika o nazwie | Nazwanej grupy | Uprawnienia grupy nazwane |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |Nazwa główna usługi |--x  |FINGRP   |r-x         |
|/Clusters | rwxr-x--x |admin |admin |Nazwa główna usługi |--x  |FINGRP |r-x         |
|/ klastrów/Finanse | rwxr-x--t |admin |FINGRP  |Nazwa główna usługi |rwx  |-  |-     |

W tabeli

- **Administrator** twórcy i administratora konta usługi Data Lake Storage.
- **Nazwa główna usługi** jest nazwy głównej usługi Azure Active Directory (AAD), które zostały skojarzone z kontem.
- **FINGRP** to grupa użytkowników utworzonych w usłudze AAD, która zawiera użytkowników z organizacji Finanse.

Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji usługi AAD (który również tworzy nazwę główną usługi), zobacz [utworzyć aplikację AAD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Aby uzyskać instrukcje, jak utworzyć grupę użytkowników w usłudze AAD, zobacz [Zarządzanie grupami w usłudze Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Niektóre kluczowe kwestie do rozważenia.

- Dwa na poziomie struktury folderów ( **/klastrów/Finanse/** ) muszą być tworzone i udostępniane z odpowiednimi uprawnieniami przez administratora usługi Data Lake Storage **przed** przy użyciu konta magazynu dla klastrów. Ta struktura nie jest tworzony automatycznie podczas tworzenia klastrów.
- W powyższym przykładzie zaleca się ustawienie grupa będąca właścicielem **/klastrów/Finanse** jako **FINGRP** i akceptując **r-x** dostęp do FINGRP do całego folderu hierarchii od w katalogu głównym. Dzięki temu członkowie FINGRP przejść strukturę folderów, począwszy od katalogu głównego.
- W przypadku, gdy różne jednostki usługi AAD, można utworzyć klastrów w ramach **/klastrów/Finanse**, atrybut sticky bit (po ustawieniu na **finance** folder) zapewnia, że foldery utworzone przez jedną jednostkę usługi Nie można usunąć przez innych.
- Po strukturę folderów i uprawnień w miejscu, procesu tworzenia klastra HDInsight tworzy miejsce przechowywania specyficzne dla klastra, w obszarze **/klastrów/Finanse/** . Na przykład może być magazyn dla klastra przy użyciu fincluster01 nazwa **/clusters/finance/fincluster01**. Prawo własności i uprawnienia do folderów utworzoną przez klaster HDInsight jest wyświetlane w tabeli, w tym miejscu.

    |Folder  |Uprawnienia  |Użytkownik będący właścicielem  |Grupa będąca właścicielem  | Użytkownik nazwany | Uprawnienia użytkownika o nazwie | Nazwanej grupy | Uprawnienia grupy nazwane |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/Clusters/finanace/fincluster01 | rwxr-x---  |Jednostka usługi |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Zalecenia dotyczące zadania dane wejściowe i wyjściowe

Zaleca się, że dane wejściowe do zadania i dane wyjściowe z zadania być przechowywane w folderze poza **/klastrów**. To zapewnia, że nawet, jeśli folder specyficzne dla klastra jest usuwany do odzyskania niektóre miejsca do magazynowania, o dane wejściowe zadania i dane wyjściowe są nadal dostępne do użytku w przyszłości. W takim przypadku upewnij się, że hierarchia folderów do przechowywania danych wejściowych zadania i dane wyjściowe zezwala na odpowiedni poziom dostępu dla jednostki usługi.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Ograniczenie udostępniania jednego konta magazynu w klastrach

Limit liczby klastrów, które mogą współkorzystać z jednego konta usługi Data Lake Storage zależy od obciążenia są uruchamiane w tych klastrach. O zbyt wiele klastrów lub bardzo dużych obciążeń w klastrach, które udostępnić konto magazynu może spowodować magazynu konta ruchem przychodzącym/wychodzącym do ograniczona.

## <a name="support-for-default-acls"></a>Obsługę list ACL domyślne

Podczas tworzenia jednostki usługi z dostępem użytkownika o nazwie (jak pokazano w powyższej tabeli), firma Microsoft zaleca **nie** Dodawanie o nazwie użytkownika przy użyciu domyślnej listy ACL. Inicjowanie obsługi administracyjnej użytkowników o nazwie dostęp przy użyciu list ACL domyślne wyniki w przypisaniu 770 uprawnień użytkownika będącego właścicielem, grupy będącej właścicielem i innych. Gdy ta wartość domyślną 770 nie odbieranie uprawnień użytkownika będącego właścicielem (7) lub grupy będącej właścicielem (7), zajmuje natychmiast wszystkich uprawnień dla innych osób (0). Skutkuje to znany problem z jednego konkretnego przypadków użycia, która została omówiona szczegółowo w temacie [— znane problemy i rozwiązania problemu](#known-issues-and-workarounds) sekcji.

## <a name="known-issues-and-workarounds"></a>Znane problemy i rozwiązania

W tej sekcji wymieniono znane problemy dotyczące używania HDInsight za pomocą usługi Data Lake Storage i ich obejścia.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Publicznie widoczne zlokalizowane zasoby Apache Hadoop YARN

Po utworzeniu nowego konta usługi Azure Data Lake Storage, katalog główny jest automatycznie konfigurowani przy użyciu zestawu bity uprawnień listy ACL dostępu do 770. Folder główny będącej właścicielem, użytkownik jest ustawiony jako użytkownik, który utworzył konto (administratora usługi Data Lake Storage) i grupa będąca właścicielem jest ustawiana grupa podstawowa użytkownika, który utworzył konto. Brak dostępu do Umowy "inne".

Wiadomo, że te ustawienia mają wpływ na jednym określonym HDInsight związanymi z zastosowaniami przechwytywane w [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Przesyłanie zadania może się nie powieść z komunikatem o błędzie podobny do następującego:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Zgodnie z zapisem w usłudze JIRA YARN, wcześniej połączona podczas lokalizowania zasobów publicznych lokalizatorowi weryfikuje, że żądanych zasobów są rzeczywiście publicznych, sprawdzając ich uprawnień w zdalnym systemie plików. Wszelkie LocalResource, który nie mieści się tego warunku jest odrzucana dla lokalizacji. Sprawdź uprawnienia, obejmuje dostęp do odczytu do pliku dla "inne". Ten scenariusz nie działa poza pole w przypadku hostowania klastrów HDInsight w usłudze Azure Data Lake, ponieważ usługa Azure Data Lake nie zezwala na dostęp do "inne" na poziomie folderu głównego.

#### <a name="workaround"></a>Obejście
Zestaw odczytu — uprawnienia do uruchamiania **innych** za pośrednictwem hierarchii, na przykład **/** , **/klastrów** i   **/klastrów/Finanse** jak pokazano w powyższej tabeli.

## <a name="see-also"></a>Zobacz także

* [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
* [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
