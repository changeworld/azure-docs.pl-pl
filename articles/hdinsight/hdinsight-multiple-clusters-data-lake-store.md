---
title: Wiele klastrów HDInsight za pomocą konta usługi Azure Data Lake Store — platformy Azure
description: Dowiedz się, jak używać więcej niż jednego klastra HDInsight z jednego konta Data Lake Store
keywords: Magazyn usługi hdinsight, system plików hdfs, danych strukturalnych, dane bez określonej struktury, usługi data lake store
services: hdinsight,storage
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: eb046e62b5ed2d9512af0d25908c3253962f5a6a
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954675"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-store-account"></a>Wiele klastrów HDInsight za pomocą konta usługi Azure Data Lake Store

Począwszy od HDInsight w wersji 3.5, można utworzyć klastry HDInsight z kontami usługi Azure Data Lake Store jako domyślny system plików.
Data Lake Store obsługuje nieograniczony magazyn, który to idealny nie tylko do obsługi dużych ilości danych. ale także do obsługi HDInsight wielu klastrów korzystających z jednego konta Data Lake Store. Aby uzyskać instrukcje na temat sposobu Tworzenie klastra HDInsight z usługą Data Lake Store jako magazynu, zobacz [Szybki Start: konfigurowanie klastrów w HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Ten artykuł zawiera zalecenia dotyczące usługi Data Lake administratora magazynu do konfigurowania pojedynczego i udostępnianie Store konta usługi Data Lake używany w wielu **active** klastry HDInsight. Te zalecenia dotyczą hostowanie wielu bezpiecznych, a także — Zabezpieczanie klastrów platformy Hadoop w udostępnionego konta usługi Data Lake store.


## <a name="data-lake-store-file-and-folder-level-acls"></a>Data Lake Store, plików i folderów na poziomie listy kontroli dostępu

W pozostałej części tego artykułu przyjęto założenie, iż dobrej znajomości poziom listy ACL plików i folderów w usługi Azure Data Lake Store, opisana szczegółowo w [kontroli dostępu w usłudze Azure Data Lake Store](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-store-setup-for-multiple-hdinsight-clusters"></a>Instalator programu Data Lake Store dla wielu klastrów HDInsight
Pozwól nam zająć hierarchię folderów dwupoziomowej, aby wyjaśnić, zalecenia dotyczące korzystania z klastrów HDInsight wiele z kontem usługi Data Lake Store. Należy wziąć pod uwagę konta Data Lake Store przy użyciu struktury folderów **/klastrów/Finanse**. Z tą strukturą wszystkie klastry, które są wymagane przez organizację Finanse służy /clusters/finance jako lokalizacji przechowywania. W przyszłości, jeśli jest to inny organizacji powiedzieć marketingu chce utworzyć HDInsight klastrów za pomocą tego samego konta Data Lake Store, ich można utworzyć/klastrów/marketingu. Teraz, po prostu Użyjmy **/klastrów/Finanse**.

Aby umożliwić tej struktury folderów do efektywnego użycia przez klastry HDInsight, administrator Data Lake Store musi przypisać odpowiednie uprawnienia, zgodnie z opisem w tabeli. Uprawnienia wymienione w tabeli odpowiadają listy ACL dostępu i nie domyślne-list ACL. 


|Folder  |Uprawnienia  |Użytkownik będący właścicielem  |Grupa będąca właścicielem  | Użytkownik nazwany | Uprawnienia użytkownika o nazwie | Nazwanej grupy | Uprawnienia grupy nazwane |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |Administrator |Administrator  |Jednostka usługi |--x  |FINGRP   |r-x         |
|/Clusters | rwxr-x--x |Administrator |Administrator |Jednostka usługi |--x  |FINGRP |r-x         |
|/ klastrów/Finanse | rwxr-x--t |Administrator |FINGRP  |Jednostka usługi |rwx  |-  |-     |

W tabeli

- **Administrator** twórcy i administratora konta Data Lake Store.
- **Nazwa główna usługi** jest nazwy głównej usługi Azure Active Directory (AAD), które zostały skojarzone z kontem.
- **FINGRP** to grupa użytkowników utworzonych w usłudze AAD, która zawiera użytkowników z organizacji Finanse.

Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji usługi AAD (który również tworzy nazwę główną usługi), zobacz [utworzyć aplikację AAD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Aby uzyskać instrukcje, jak utworzyć grupę użytkowników w usłudze AAD, zobacz [Zarządzanie grupami w usłudze Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Niektóre kluczowe kwestie do rozważenia.

- Dwa na poziomie struktury folderów (**/klastrów/Finanse/**) muszą być tworzone i udostępniane z odpowiednimi uprawnieniami przez administratora Data Lake Store **przed** przy użyciu konta magazynu dla klastrów. Ta struktura nie jest tworzony automatycznie podczas tworzenia klastrów.
- W powyższym przykładzie zaleca się ustawienie grupa będąca właścicielem **/klastrów/Finanse** jako **FINGRP** i akceptując **r-x** dostęp do FINGRP do całego folderu hierarchii od w katalogu głównym. Dzięki temu członkowie FINGRP przejść strukturę folderów, począwszy od katalogu głównego.
- W przypadku, gdy różne jednostki usługi AAD, można utworzyć klastrów w ramach **/klastrów/Finanse**, atrybut sticky bit (po ustawieniu na **finance** folder) zapewnia, że foldery utworzone przez jedną jednostkę usługi Nie można usunąć przez innych.
- Po strukturę folderów i uprawnień w miejscu, procesu tworzenia klastra HDInsight tworzy lokalizacja magazynu specyficzne dla klastra, w obszarze **/klastrów/Finanse/**. Na przykład może być magazyn dla klastra przy użyciu fincluster01 nazwa **/clusters/finance/fincluster01**. Prawo własności i uprawnienia do folderów utworzoną przez klaster HDInsight jest wyświetlane w tabeli, w tym miejscu.

    |Folder  |Uprawnienia  |Użytkownik będący właścicielem  |Grupa będąca właścicielem  | Użytkownik nazwany | Uprawnienia użytkownika o nazwie | Nazwanej grupy | Uprawnienia grupy nazwane |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/Clusters/finanace/fincluster01 | rwxr-x---  |Nazwa główna usługi |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Zalecenia dotyczące zadania dane wejściowe i wyjściowe

Zaleca się, że dane wejściowe do zadania i dane wyjściowe z zadania być przechowywane w folderze poza **/klastrów**. To zapewnia, że nawet, jeśli folder specyficzne dla klastra jest usuwany do odzyskania niektóre miejsca do magazynowania, o dane wejściowe zadania i dane wyjściowe są nadal dostępne do użytku w przyszłości. W takim przypadku upewnij się, że hierarchia folderów do przechowywania danych wejściowych zadania i dane wyjściowe zezwala na odpowiedni poziom dostępu dla jednostki usługi.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Ograniczenie udostępniania jednego konta magazynu w klastrach

Limit liczby klastrów, które mogą współkorzystać z jednego konta Data Lake Store zależy od obciążenia są uruchamiane w tych klastrach. O zbyt wiele klastrów lub bardzo dużych obciążeń w klastrach, które udostępnić konto magazynu może spowodować magazynu konta ruchem przychodzącym/wychodzącym do ograniczona.

## <a name="support-for-default-acls"></a>Obsługę list ACL domyślne

Podczas tworzenia jednostki usługi z dostępem użytkownika o nazwie (jak pokazano w powyższej tabeli), firma Microsoft zaleca **nie** Dodawanie o nazwie użytkownika przy użyciu domyślnej listy ACL. Inicjowanie obsługi administracyjnej użytkowników o nazwie dostęp przy użyciu list ACL domyślne wyniki w przypisaniu 770 uprawnień użytkownika będącego właścicielem, grupy będącej właścicielem i innych. Gdy ta wartość domyślną 770 nie odbieranie uprawnień użytkownika będącego właścicielem (7) lub grupy będącej właścicielem (7), zajmuje natychmiast wszystkich uprawnień dla innych osób (0). Skutkuje to znany problem z jednego konkretnego przypadków użycia, która została omówiona szczegółowo w temacie [— znane problemy i rozwiązania problemu](#known-issues-and-workarounds) sekcji.

## <a name="known-issues-and-workarounds"></a>Znane problemy i rozwiązania

W tej sekcji wymieniono znane problemy dotyczące używania HDInsight za pomocą programu Data Lake Store i ich obejścia.

### <a name="publicly-visible-localized-yarn-resources"></a>Publicznie widoczne zlokalizowanych zasobów usługi YARN

Po utworzeniu nowego konta usługi Azure Data Lake store, katalog główny jest automatycznie konfigurowani przy użyciu listy ACL dostępu uprawnienie bitów 770. Folder główny będącej właścicielem, użytkownik jest ustawiony jako użytkownik, który utworzył konto (administratora Data Lake Store) i grupa będąca właścicielem jest ustawiana grupa podstawowa użytkownika, który utworzył konto. Brak dostępu do Umowy "inne".

Wiadomo, że te ustawienia mają wpływ na jednym określonym HDInsight związanymi z zastosowaniami przechwytywane w [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Przesyłanie zadania może się nie powieść z komunikatem o błędzie podobny do następującego:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Zgodnie z zapisem w usłudze JIRA YARN, wcześniej połączona podczas lokalizowania zasobów publicznych lokalizatorowi weryfikuje, że żądanych zasobów są rzeczywiście publicznych, sprawdzając ich uprawnień w zdalnym systemie plików. Wszelkie LocalResource, który nie mieści się tego warunku jest odrzucana dla lokalizacji. Sprawdź uprawnienia, obejmuje dostęp do odczytu do pliku dla "inne". Ten scenariusz nie działa poza pole w przypadku hostowania klastrów HDInsight w usłudze Azure Data Lake, ponieważ usługa Azure Data Lake nie zezwala na dostęp do "inne" na poziomie folderu głównego.

#### <a name="workaround"></a>Obejście
Zestaw odczytu — uprawnienia do uruchamiania **innych** za pośrednictwem hierarchii, na przykład **/**, **/klastrów** i   **/klastrów/Finanse** jak pokazano w powyższej tabeli.

## <a name="see-also"></a>Zobacz także

* [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)


