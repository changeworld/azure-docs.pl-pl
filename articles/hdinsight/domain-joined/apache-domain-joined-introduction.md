---
title: Wprowadzenie do zabezpieczeń usługi Hadoop z przyłączonym do domeny klastrów usługi Azure HDInsight
description: Dowiedz się, jak przyłączonych do domeny usługi Azure HDInsight klastry obsługują cztery filarów zabezpieczeń przedsiębiorstwa.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.openlocfilehash: c13fd979562cc89831d031c5050fe9dbb184267b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041136"
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>Wprowadzenie do zabezpieczeń usługi Hadoop przy użyciu klastrów HDInsight przyłączone do domeny

W przeszłości, Azure HDInsight obsługuje tylko jednego użytkownika: administratora lokalnego. Bardzo dobrze funkcjonowało to dla mniejszych zespołów aplikacji lub działów. Jak obciążeń opartych na usłudze Hadoop zwiększania popularności w sektorze przedsiębiorstw potrzeba do obsługi funkcji przeznaczonych dla przedsiębiorstw, takich jak uwierzytelnianie oparte na usłudze Active Directory, wielu użytkowników i kontroli dostępu opartej na rolach, staje się coraz ważniejsza. 

Można utworzyć klaster usługi HDInsight, który jest przyłączony do domeny usługi Active Directory. Następnie można skonfigurować listę pracowników przedsiębiorstwa, którzy mogą uwierzytelniać się za pośrednictwem usługi Azure Active Directory do logowania do klastra HDInsight. Żadna osoba spoza przedsiębiorstwa nie może zalogować się lub uzyskać dostęp do klastra HDInsight. 

Administrator przedsiębiorstwa można skonfigurować kontroli dostępu opartej na rolach (RBAC) dla zabezpieczeń Hive przy użyciu [struktury Apache Ranger](http://hortonworks.com/apache/ranger/). Konfigurowanie funkcji RBAC ogranicza dostęp do danych tylko potrzebne elementy. Na koniec administrator może przeprowadzać inspekcje dostępu do danych przez pracowników oraz wszystkich zmian zasad kontroli dostępu. Administrator może następnie osiągnąć wysoki stopień nadzoru nad zasobami firmy.

> [!NOTE]
> Nowe funkcje opisane w tym artykule są dostępne w wersji zapoznawczej tylko w następujących typów klastrów: Hadoop, Spark i interakcyjnych zapytań. Oozie jest teraz włączony na klastry przyłączone do domeny. Aby uzyskać dostęp do interfejsu użytkownika sieci web programu Oozie, należy włączyć użytkowników [tunelowania](../hdinsight-linux-ambari-ssh-tunnel.md).

Zabezpieczenia przedsiębiorstwa składają się czterech głównych filarów: zabezpieczenia brzegowe, uwierzytelnianie, autoryzacja i szyfrowanie.

![Korzyści z przyłączonym do domeny klastrów HDInsight w czterech filarach zabezpieczeń przedsiębiorstwa](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Zabezpieczenia brzegowe
Zabezpieczenia brzegowe w HDInsight odbywa się za pośrednictwem sieci wirtualne i usługi Azure VPN Gateway. Administrator przedsiębiorstwa może utworzyć klaster usługi HDInsight wewnątrz sieci wirtualnej i Użyj grup zabezpieczeń sieci (reguły zapory), aby ograniczyć dostęp do sieci wirtualnej. Tylko adresy IP określone w regułach zapory dla ruchu przychodzącego będzie można nawiązać połączenia z klastrem HDInsight. Ta konfiguracja zapewnia zabezpieczenia brzegowe.

Kolejna warstwa zabezpieczeń brzegowych odbywa się za pośrednictwem usługi bramy sieci VPN. Brama działa jako pierwsza linia obrony w przypadku wszystkich żądań przychodzących do klastra HDInsight. Go akceptuje żądanie, sprawdza jego poprawność, a następnie zezwala na przekazanie go do innych węzłów w klastrze. W ten sposób brama zapewnia zabezpieczenia brzegowe dla innych węzłów nazw i danych w klastrze.

## <a name="authentication"></a>Authentication
Administrator przedsiębiorstwa może utworzyć klaster HDInsight przyłączone do domeny w [sieci wirtualnej](https://azure.microsoft.com/services/virtual-network/). Wszystkie węzły klastra HDInsight są przyłączone do domeny, którą zarządza przedsiębiorstwo. Jest to osiągane za pośrednictwem [usługi Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). 

W przypadku takiej konfiguracji pracownicy przedsiębiorstwa mogą logować do węzłów klastra przy użyciu swoich poświadczeń domeny. Mogą również użyć poświadczeń domeny, do uwierzytelniania za pomocą innych zatwierdzonych punktów końcowych, takich jak widoków Ambari, ODBC, JDBC, PowerShell i interfejsów API REST do interakcji z klastrem. Administrator ma pełną kontrolę nad ograniczaniem liczby użytkowników, którzy współpracują z klastrem za pośrednictwem tych punktów końcowych.

## <a name="authorization"></a>Autoryzacja
Najlepszym rozwiązaniem należy wykonać większość przedsiębiorstw jest upewnienie się, że nie każdy pracownik ma dostęp do wszystkich zasobów organizacji. Analogicznie administrator może zdefiniować zasady kontroli dostępu opartej na rolach dla zasobów klastra. 

Na przykład administrator może skonfigurować środowisko [Apache Ranger](http://hortonworks.com/apache/ranger/) do ustawiania zasad kontroli dostępu dla usługi Hive. Ta funkcja zapewnia, że pracownicy mogą uzyskać dostęp tylko tych danych, musi być skutecznego wykonywania swoich zadań. Dostęp protokołu SSH z klastrem również jest ograniczony do tylko przez administratora.

## <a name="auditing"></a>Inspekcja
Inspekcja wszystkich dostępu do zasobów klastra i danych, jest niezbędne do śledzenia prób nieautoryzowanego lub niezamierzonego dostępu do zasobów. Jest tak ważna jak ochrona zasobów klastra HDInsight przed nieautoryzowanymi użytkownikami oraz zabezpieczaniem danych. 

Administrator można przeglądać i raportować wszystkie przypadki dostępu do danych i zasobów klastra usługi HDInsight. Administrator można przeglądać i raportować wszystkie zmiany wprowadzane do zasad kontroli dostępu, które są tworzone w struktury Apache Ranger obsługiwanych punktów końcowych. 

Klaster HDInsight przyłączone do domeny używa znanego użytkownika Apache Ranger do wyszukiwania dzienników inspekcji. Na zapleczu, środowisko Ranger używa [Apache Solr](http://hortonworks.com/apache/solr/) do przechowywania i wyszukiwania dzienników.

## <a name="encryption"></a>Szyfrowanie
Ochrona danych jest ważne dla spotkania zabezpieczeń i zgodności wymagań organizacyjnych. Poza ograniczaniem dostępu do danych nieautoryzowanych pracowników, należy go zaszyfrować. 

Magazyny danych, zarówno w przypadku klastrów HDInsight — usługi Azure Blob storage i Azure Data Lake magazynu Gen1 — obsługę przezroczystego po stronie serwera [szyfrowanie danych](../../storage/common/storage-service-encryption.md) magazynowanych. Zabezpieczonych klastrów HDInsight funkcjonuje bezproblemowo przy użyciu tej funkcji po stronie serwera szyfrowanie danych magazynowanych.

## <a name="next-steps"></a>Kolejne kroki
* [Planowanie klastrów HDInsight przyłączone do domeny](apache-domain-joined-architecture.md)
* [Konfigurowanie przyłączonych do domeny klastrów HDInsight](apache-domain-joined-configure.md)
* [Zarządzanie klastrami HDInsight przyłączone do domeny](apache-domain-joined-manage.md)
* [Konfigurowanie zasad usługi Hive dla przyłączonych do domeny klastrów HDInsight](apache-domain-joined-run-hive.md)
* [Używanie protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

