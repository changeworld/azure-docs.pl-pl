---
title: Wiele klastrów usługi HDInsight & jednym kontem usługi Azure Data Lake Storage
description: Dowiedz się, jak używać więcej niż jednego klastra USŁUGI HDInsight z jednym kontem usługi Data Lake Storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: cc67acca11e7e0f24dc0597dcd19672a38a7bf28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495744"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Używanie wielu klastrów usługi HDInsight z kontem usługi Azure Data Lake Storage

Począwszy od usługi HDInsight w wersji 3.5, można tworzyć klastry HDInsight z kontami usługi Azure Data Lake Storage jako domyślny system plików.
Data Lake Storage obsługuje nieograniczoną pamięć masową, która sprawia, że idealnie nadaje się nie tylko do przechowywania dużych ilości danych; ale także do obsługi wielu klastrów HDInsight, które współużytkuje jedno konto przechowywania usługi Data Lake. Aby uzyskać instrukcje dotyczące tworzenia klastra HDInsight z magazynem usługi Data Lake jako magazynem, zobacz [Szybki start: Konfigurowanie klastrów w programie HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Ten artykuł zawiera zalecenia dla administratora magazynu usługi Data Lake do konfigurowania jednego i udostępnionego konta magazynu usługi Data Lake, które mogą być używane w wielu **aktywnych** klastrach HDInsight. Zalecenia te dotyczą hostowania wielu bezpiecznych, a także niezabezpieczonych klastrów Apache Hadoop na udostępnionym koncie usługi Data Lake Storage.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Listy ACL na poziomie magazynu usługi Data Lake Storage

W dalszej części tego artykułu przyjęto założenie, że masz dobrą znajomość list ACL na poziomie plików i folderów w usłudze Azure Data Lake Storage, która jest szczegółowo opisana w [obszarze Kontrola dostępu w usłudze Azure Data Lake Storage.](../data-lake-store/data-lake-store-access-control.md)

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Konfiguracja magazynu usługi Data Lake Storage dla wielu klastrów HDInsight

Weźmy hierarchię folderów dwupoziomowych, aby wyjaśnić zalecenia dotyczące używania wielu klastrów HDInsight z kontem usługi Data Lake Storage. Należy wziąć pod uwagę, że masz konto Magazynu usługi Data Lake z strukturą **folderów /klastry/finanse**. Dzięki tej strukturze wszystkie klastry wymagane przez organizację finanse mogą używać /clusters/finance jako lokalizacji magazynu. W przyszłości, jeśli inna organizacja, na przykład Marketing, chce utworzyć klastry HDInsight przy użyciu tego samego konta Usługi Data Lake Storage, mogą utworzyć /clusters/marketing. Na razie po prostu użyjmy **/clusters/finance**.

Aby ta struktura folderów była skutecznie używana przez klastry USŁUGI HDInsight, administrator magazynu usługi Data Lake musi przypisać odpowiednie uprawnienia, zgodnie z opisem w tabeli. Uprawnienia wyświetlane w tabeli odpowiadają listom dostępu, a nie listom ACL default.

|Folder  |Uprawnienia  |Użytkownik będący właścicielem  |Grupa będąca właścicielem  | Nazwany użytkownik | Uprawnienia nazwanych użytkowników | Nazwana grupa | Nazwane uprawnienia grupy |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |administrator |administrator  |Jednostka usługi |--x  |FINGRP ( FINGRP )   |r-x         |
|/klastry | rwxr-x--x |administrator |administrator |Jednostka usługi |--x  |FINGRP ( FINGRP ) |r-x         |
|/klastry/finanse | rwxr-x--t |administrator |FINGRP ( FINGRP )  |Jednostka usługi |Rwx  |-  |-     |

W tabeli

- **administrator** jest twórcą i administratorem konta Data Lake Storage.
- **Podmiot zabezpieczeń usługi** jest jednostką usługi Azure Active Directory (AAD) skojarzoną z kontem.
- **FINGRP** to grupa użytkowników utworzona w udziale Wówrzeszu AAD, która zawiera użytkowników z organizacji Finanse.

Aby uzyskać instrukcje dotyczące tworzenia aplikacji usługi AAD (która również tworzy jednostkę usługi), zobacz [Tworzenie aplikacji usługi AAD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Aby uzyskać instrukcje dotyczące tworzenia grupy użytkowników w usłudze AAD, zobacz [Zarządzanie grupami w usłudze Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Kilka kluczowych kwestii do rozważenia.

- Dwupoziomowa struktura folderów (**/clusters/finance/**) musi zostać utworzona i aprowizowana z odpowiednimi uprawnieniami administratora magazynu usługi Data Lake **przed** użyciem konta magazynu dla klastrów. Ta struktura nie jest tworzona automatycznie podczas tworzenia klastrów.
- W powyższym przykładzie zaleca się ustawienie grupy właścicielskiej **/clusters/finance** jako **FINGRP** i zezwolenie na dostęp **r-x** do FINGRP do całej hierarchii folderów, zaczynając od katalogu głównego. Dzięki temu członkowie FINGRP mogą poruszać się po strukturze folderów, zaczynając od katalogu głównego.
- W przypadku, gdy różne podmioty usługi AAD usługi można utworzyć klastry w **obszarze /clusters/finance**, lepki bit (po ustawieniu w folderze **finansowym)** zapewnia, że foldery utworzone przez jednego podmiotu zabezpieczeń usługi nie mogą być usunięte przez innych.
- Po wprowadzeniu struktury folderów i uprawnień proces tworzenia klastra HDInsight tworzy lokalizację magazynu specyficzne dla klastra w obszarze **/clusters/finance/**. Na przykład magazyn dla klastra o nazwie fincluster01 może być **/clusters/finance/fincluster01**. Własność i uprawnienia do folderów utworzonych przez klaster HDInsight są wyświetlane w tabeli w tym miejscu.

    |Folder  |Uprawnienia  |Użytkownik będący właścicielem  |Grupa będąca właścicielem  | Nazwany użytkownik | Uprawnienia nazwanych użytkowników | Nazwana grupa | Nazwane uprawnienia grupy |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/klastry/finanace/ fincluster01 | rwxr-x---  |Jednostka usługi |FINGRP ( FINGRP )  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Zalecenia dotyczące danych wejściowych i wyjściowych zadań

Zaleca się, aby dane wejściowe do zadania, a dane wyjściowe z zadania były przechowywane w folderze poza **/clusters**. Gwarantuje to, że nawet jeśli folder specyficzny dla klastra zostanie usunięty w celu odzyskania pewnego miejsca do magazynowania, dane wejściowe i wyjściowe zadania są nadal dostępne do wykorzystania w przyszłości. W takim przypadku upewnij się, że hierarchia folderów do przechowywania danych wejściowych i wyjściowych zadań umożliwia odpowiedni poziom dostępu dla jednostki usługi.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Ograniczanie klastrów udostępnianych przez jedno konto magazynu

Limit liczby klastrów, które mogą współużytkować pojedyncze konto usługi Data Lake Storage, zależy od obciążenia uruchamianego w tych klastrach. Mając zbyt wiele klastrów lub bardzo dużych obciążeń w klastrach, które współużytkują konto magazynu może spowodować, że konto magazynu przychodzących/wychodzących, aby uzyskać ograniczone.

## <a name="support-for-default-acls"></a>Obsługa domyślnych list ACL

Podczas tworzenia jednostki usługi z dostępem nazwanych użytkowników (jak pokazano w powyższej tabeli), **nie** zaleca się dodawania nazwanego użytkownika z domyślną k. ACL. Inicjowanie obsługi administracyjnej dostępu o nazwie użytkownika przy użyciu domyślnych list ACL powoduje przypisanie 770 uprawnień dla użytkowników- użytkownik, grupa-właścicielem i inne. Ta domyślna wartość 770 nie odbiera uprawnień użytkownikowi będącym właścicielem (7) lub grupie będącej właścicielem (7), ale odbiera wszystkie uprawnienia innym osobom (0). Powoduje to znany problem z jednym przypadku określonego użycia, który jest szczegółowo omówione w [znane problemy i obejścia](#known-issues-and-workarounds) sekcji.

## <a name="known-issues-and-workarounds"></a>Znane problemy i obejścia

W tej sekcji wymieniono znane problemy związane z używaniem usługi HDInsight z magazynem usługi Data Lake i ich obejścia.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Publicznie widoczne zlokalizowane zasoby Przędzy Apache Hadoop

Po utworzeniu nowego konta usługi Azure Data Lake Storage katalog główny jest automatycznie aprowizowany przy bitach uprawnień access-ACL ustawionych na 770. Użytkownik posiadający folder główny jest ustawiony na użytkownika, który utworzył konto (administrator magazynu usługi Data Lake Storage), a grupa stanowiąca jest ustawiona na podstawową grupę użytkownika, który utworzył konto. Nie ma dostępu do "innych".

Wiadomo, że te ustawienia mają wpływ na jeden konkretny przypadek użycia HDInsight przechwycony w [yarn 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Przesyłanie zadań może zakończyć się niepowodzeniem, a komunikat o błędzie podobny do tego:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Jak stwierdzono w YARN JIRA połączone wcześniej, podczas lokalizowania zasobów publicznych, localizer sprawdza, czy wszystkie żądane zasoby są rzeczywiście publiczne, sprawdzając ich uprawnienia do zdalnego systemu plików. Wszelkie LocalResource, który nie pasuje do tego warunku jest odrzucany dla lokalizacji. Sprawdzanie uprawnień, obejmuje dostęp do odczytu do pliku dla "innych". Ten scenariusz nie działa out-of-the-box podczas hostowania klastrów HDInsight w usłudze Azure Data Lake, ponieważ usługa Azure Data Lake odmawia dostępu do "innych" na poziomie folderu głównego.

#### <a name="workaround"></a>Obejście

Ustaw uprawnienia do odczytu i wykonywania dla **innych** **/** osób za pośrednictwem hierarchii, na przykład w **, /clusters** i **/clusters/finance,** jak pokazano w powyższej tabeli.

## <a name="see-also"></a>Zobacz też

- [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
- [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
