---
title: Użyj wielu klastrów usługi HDInsight przy użyciu konta usługi Azure Data Lake Store - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać więcej niż jednego klastra usługi HDInsight przy użyciu pojedynczego konta usługi Data Lake Store
keywords: magazynu usługi hdinsight, system plików hdfs, danych strukturalnych, bez struktury danych, data lake — Magazyn
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 48e5a8d270701c43276e1d248d8ea4dc748d15b2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31404571"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-store-account"></a>Użyj wielu klastrów usługi HDInsight przy użyciu konta usługi Azure Data Lake Store

Począwszy od usługi HDInsight w wersji 3.5, można utworzyć klastry usługi HDInsight przy użyciu kont usługi Azure Data Lake Store jako domyślny system plików.
Data Lake Store obsługuje nieograniczony magazyn, który to idealny nie tylko do obsługi dużych ilości danych. można jednak również hostingu HDInsight wielu klastrów korzystających z jednego konta magazynu Data Lake. Aby uzyskać instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight z usługą Data Lake Store jako magazynu, zobacz [Tworzenie klastrów usługi HDInsight z usługą Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Ten artykuł zawiera zalecenia dotyczące usługi Data Lake administratora magazynu do konfigurowania pojedynczego i udostępnionych przechowywania konta usługi Data Lake używany w wielu **active** klastrów usługi HDInsight. Te zalecenia dotyczą hosting wielu klastrów platformy Hadoop bezpieczny, a także niezabezpieczonego udostępnionego konta sklepu usługi Data Lake.


## <a name="data-lake-store-file-and-folder-level-acls"></a>Data Lake — magazyn plików i folderów na poziomie listy kontroli dostępu

Dalszej części tego artykułu założono, że dobrą znajomość poziomu plików i folderów listy kontroli dostępu w usłudze Azure Data Lake Store, które opisano szczegółowo w [kontroli dostępu w usłudze Azure Data Lake Store](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-store-setup-for-multiple-hdinsight-clusters"></a>Instalator usługi Data Lake Store dla wielu klastrów usługi HDInsight
Daj nam zająć hierarchię folderów dwupoziomowej wyjaśnić zalecenia dotyczące używania wiele klastrów usługi HDInsight przy użyciu konta usługi Data Lake Store. Należy wziąć pod uwagę konta usługi Data Lake Store z tej struktury folderów **/klastrów/finance**. Ta struktura wszystkich klastrów, które są wymagane przez organizację Finance można za pomocą /clusters/finance jako lokalizacji magazynu. W przyszłości, jeśli innej organizacji powiedz marketingu chce utworzyć klastry przy użyciu tego samego konta usługi Data Lake Store, ich można utworzyć/klastrów/marketing HDInsight. Teraz, po prostu Użyjmy **/klastrów/finance**.

Aby włączyć tę strukturę folderów skutecznie używanego przez klastry usługi HDInsight, administrator usługi Data Lake Store przypisać odpowiednie uprawnienia, zgodnie z opisem w tabeli. Uprawnienia wymienione w tabeli odpowiadają dostępu ACL, a nie domyślnej ACL. 


|Folder  |Uprawnienia  |Użytkownik będący właścicielem  |Grupa będąca właścicielem  | Użytkownik nazwany | Uprawnienia użytkownika o nazwie | Grupa o nazwie | Uprawnienia grupy o nazwie |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |administrator |administrator  |Jednostka usługi |--x  |FINGRP   |r-x         |
|/Clusters | rwxr-x--x |administrator |administrator |Jednostka usługi |--x  |FINGRP |r-x         |
|/ klastrów/finance | rwxr-x--t |administrator |FINGRP  |Jednostka usługi |rwx  |-  |-     |

W tabeli

- **Administrator** jest twórcę i administratora konta usługi Data Lake Store.
- **Nazwy głównej usługi** jest nazwy głównej usługi Azure Active Directory (AAD), które zostały skojarzone z kontem.
- **FINGRP** to grupa użytkowników utworzone w usłudze AAD, zawierającą użytkowników z finansowych organizacji.

Aby uzyskać instrukcje dotyczące tworzenia aplikacji usługi AAD (która także tworzy nazwy głównej usługi), zobacz [utworzyć aplikację AAD](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Aby uzyskać instrukcje, jak utworzyć grupę użytkowników w usłudze AAD, zobacz [Zarządzanie grupami w usłudze Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Niektóre kluczowe kwestie do rozważenia.

- Struktura folderów na poziomie dwa (**/klastrów/finance/**) muszą być utworzone i udostępnione z odpowiednimi uprawnieniami przez administratora usługi Data Lake Store **przed** przy użyciu konta magazynu dla klastrów. Ta struktura nie jest tworzona automatycznie podczas tworzenia klastrów.
- W powyższym przykładzie zaleca ustawienie będący właścicielem grupy **/klastrów/finance** jako **FINGRP** i umożliwiający **r x** dostęp do FINGRP do hierarchii cały folder początkowy z katalogu głównego. Dzięki temu, że członkowie FINGRP można przechodzić struktury folderów, począwszy od katalogu głównego.
- W przypadku, gdy różne jednostki usługi AAD można utworzyć klastry w obszarze **/klastrów/finance**, trwałe bitowe (ustawionej dla **finance** folder) zapewnia, że foldery utworzone przez nazwę główną usługi jeden Nie można usunąć przez innych.
- Po zatwierdzeniu struktury folderów i uprawnienia w miejscu, procesu tworzenia klastra usługi HDInsight tworzy lokalizacja magazynu specyficznych dla klastra, w obszarze **/klastrów/finance/**. Na przykład można magazynu dla klastra o nazwie fincluster01 **/clusters/finance/fincluster01**. Własność i uprawnienia do folderów utworzoną przez klaster usługi HDInsight są widoczne w tabeli poniżej.

    |Folder  |Uprawnienia  |Użytkownik będący właścicielem  |Grupa będąca właścicielem  | Użytkownik nazwany | Uprawnienia użytkownika o nazwie | Grupa o nazwie | Uprawnienia grupy o nazwie |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/Clusters/finanace/fincluster01 | rwxr-x---  |Nazwy głównej usługi |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Zalecenia dotyczące zadania danych wejściowych i wyjściowych

Zalecamy przechowywanie danych wejściowych do zadania i dane wyjściowe z zadania w folderze poza **/klastrów**. Dzięki temu nawet, jeśli folder specyficznych dla klastra jest usuwany odzyskiwaniu niektóre miejsca do magazynowania, dane wejściowe zadania i dane wyjściowe są nadal dostępne do użycia w przyszłości. W takim przypadku sprawdź, czy hierarchia folderów do przechowywania zadania wejściami i wyjściami pozwalają odpowiedni poziom dostępu dla nazwy głównej usługi.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Ograniczenia w klastrach udostępnianie konta jednego magazynu

Limit liczby klastrów, które współużytkują jedno konto usługi Data Lake Store zależy od obciążenia uruchomione na tych klastrach. O zbyt wielu klastrów lub bardzo duże obciążenia w klastrze, które mają konto magazynu może spowodować magazynu konta wejście/wyjście do pobrania ograniczany.

## <a name="support-for-default-acls"></a>Obsługa listy ACL domyślne

Podczas tworzenia nazwy głównej usługi z dostępem użytkownika o nazwie (zgodnie z informacjami podanymi w powyższej tabeli), zaleca się **nie** Dodawanie o nazwie użytkownika z domyślnej listy ACL. Inicjowanie obsługi o nazwie użytkownika dostęp przy użyciu list ACL domyślne wyniki przypisania uprawnień 770 użytkownik właściciel, będący właścicielem grupy i inne. Gdy ta wartość domyślną 770 nie odbieranie uprawnień z użytkownik właściciel (7) lub grupy będącej właścicielem [7], optymalizacji trwa wszystkie uprawnienia dla innych użytkowników (0). Powoduje to znany problem dotyczący jednego określonego przypadek użycia, która została omówiona szczegółowo w [— znane problemy i rozwiązania](#known-issues-and-workarounds) sekcji.

## <a name="known-issues-and-workarounds"></a>Znane problemy i rozwiązania

W tej sekcji wymieniono znane problemy dotyczące korzystania z usługi HDInsight z usługą Data Lake Store i ich obejścia.

### <a name="publicly-visible-localized-yarn-resources"></a>Widocznego publicznie zlokalizowanych zasobów YARN

Po utworzeniu nowego konta magazynu usługi Azure Data Lake katalogu głównego jest automatycznie udostępniane z ustawioną 770 bity uprawnienia dostępu ACL. Folder główny będącej właścicielem, jeśli użytkownik ma ustawioną wartość użytkownik, który utworzył konto (administratora usługi Data Lake Store) i grupy będący właścicielem jest równa grupą podstawową użytkownik, który utworzył konto. Brak dostępu jest dostępna dla "inne".

Wiadomo, że te ustawienia mają wpływ na jednego określonego HDInsight przypadek użycia przechwyconych w trakcie [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Przesyłanie zadań może zakończyć się niepowodzeniem z komunikatem o błędzie podobny do poniższego:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Zgodnie z JIRA YARN połączone wcześniej, podczas lokalizowania publicznego zasobów lokalizatora sprawdza, czy wszystkie wymagane zasoby są rzeczywiście publiczny, sprawdzając uprawnień w zdalnym systemie plików. Lokalizacja odrzucenia żadnych LocalResource, który nie mieści się tego warunku. Sprawdzanie uprawnień, obejmuje dostęp do odczytu do pliku "inne". W tym scenariuszu nie działa poza pole odnośnie do hostowania klastrów HDInsight w usłudze Azure Data Lake, ponieważ usługa Azure Data Lake nie zezwala na dostęp do "inne" na poziomie folderu głównego.

#### <a name="workaround"></a>Obejście problemu
Zestaw odczytu-uprawnienia do uruchamiania **innym** w hierarchii, na przykład **/**, **/klastrów** i   **/klastrów/finance** opisane w powyższej tabeli.

## <a name="see-also"></a>Zobacz także

* [Tworzenie klastra usługi HDInsight z usługą Data Lake Store jako magazyn](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)


