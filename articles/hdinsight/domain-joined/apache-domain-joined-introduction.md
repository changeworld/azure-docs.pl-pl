---
title: Wprowadzenie do zabezpieczeń platformy Apache Hadoop z pakietem Enterprise Security
description: Dowiedz się, jak pakiet Enterprise Security obsługuje cztery filarów zabezpieczeń przedsiębiorstwa.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/12/2019
ms.openlocfilehash: b7228fdf1bb67ff8029412174a883a3a0b123cfc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67126201"
---
# <a name="what-is-enterprise-security-package-in-azure-hdinsight"></a>Co to jest pakiet Enterprise Security w usłudze Azure HDInsight

W przeszłości, Azure HDInsight obsługuje tylko jednego użytkownika: administratora lokalnego. Bardzo dobrze funkcjonowało to dla mniejszych zespołów aplikacji lub działów. Jak obciążeń opartych na technologii Apache Hadoop zwiększania popularności w sektorze przedsiębiorstw potrzeba do obsługi funkcji przeznaczonych dla przedsiębiorstw, takich jak uwierzytelniania opartego na usłudze Active Directory lub wielu użytkowników i kontroli dostępu opartej na rolach, staje się coraz ważniejsza. 

Klaster usługi HDInsight można utworzyć za pomocą Enterprise pakietu zabezpieczeń (ESP), który jest przyłączony do domeny usługi Active Directory. Następnie można skonfigurować listę pracowników przedsiębiorstwa, którzy mogą uwierzytelniać się za pośrednictwem usługi Azure Active Directory, aby zarejestrować się w klastrze HDInsight. Nikt z poza firmę Zaloguj, lub uzyskać dostęp do klastra HDInsight. 

Administrator przedsiębiorstwa można skonfigurować kontroli dostępu opartej na rolach (RBAC) dla zabezpieczeń Apache Hive za pomocą [struktury Apache Ranger](https://ranger.apache.org/). Konfigurowanie funkcji RBAC ogranicza dostęp do danych tylko potrzebne elementy. Na koniec administrator może przeprowadzać inspekcje dostępu do danych przez pracowników oraz wszystkich zmian zasad kontroli dostępu. Administrator może następnie osiągnąć wysoki stopień nadzoru nad zasobami firmy.

Apache Oozie jest teraz włączony w klastrach ESP. Aby uzyskać dostęp do interfejsu użytkownika sieci web programu Oozie, należy włączyć użytkowników [tunelowania](../hdinsight-linux-ambari-ssh-tunnel.md).

Zabezpieczenia przedsiębiorstwa składają się czterech głównych filarów: zabezpieczenia brzegowe, uwierzytelnianie, autoryzacja i szyfrowanie.

![Korzyści z klastrami HDInsight pakiet zabezpieczeń przedsiębiorstwa w czterech filarach zabezpieczeń przedsiębiorstwa](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Zabezpieczenia brzegowe
Zabezpieczenia brzegowe w HDInsight odbywa się za pośrednictwem sieci wirtualne i usługi Azure VPN Gateway. Administrator przedsiębiorstwa można utworzyć klaster ESP wewnątrz sieci wirtualnej i Użyj grup zabezpieczeń sieci (reguły zapory), aby ograniczyć dostęp do sieci wirtualnej. Tylko adresy IP określone w regułach zapory dla ruchu przychodzącego będzie można nawiązać połączenia z klastrem HDInsight. Ta konfiguracja zapewnia zabezpieczenia brzegowe.

Kolejna warstwa zabezpieczeń brzegowych odbywa się za pośrednictwem usługi bramy sieci VPN. Brama działa jako pierwsza linia obrony w przypadku wszystkich żądań przychodzących do klastra HDInsight. Go akceptuje żądanie, sprawdza jego poprawność, a następnie zezwala na przekazanie go do innych węzłów w klastrze. W ten sposób brama zapewnia zabezpieczenia brzegowe dla innych węzłów nazw i danych w klastrze.

## <a name="authentication"></a>Authentication
Administrator przedsiębiorstwa może utworzyć klaster HDInsight przy użyciu ESP w [sieci wirtualnej](https://azure.microsoft.com/services/virtual-network/). Wszystkie węzły klastra HDInsight są przyłączone do domeny, którą zarządza przedsiębiorstwo. Jest to osiągane za pośrednictwem [usługi Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). 

W przypadku takiej konfiguracji pracownicy przedsiębiorstwa zalogować się do węzłów klastra przy użyciu swoich poświadczeń domeny. Mogą również użyć poświadczeń domeny do uwierzytelniania za pomocą innych zatwierdzonych punktów końcowych, takich jak widoków Ambari Apache, ODBC, JDBC, PowerShell i interfejsów API REST do interakcji z klastrem. Administrator ma pełną kontrolę nad ograniczaniem liczby użytkowników, którzy współpracują z klastrem za pośrednictwem tych punktów końcowych.

## <a name="authorization"></a>Autoryzacja
Najlepszym rozwiązaniem należy wykonać większość przedsiębiorstw jest upewnienie się, że nie każdy pracownik ma dostęp do wszystkich zasobów organizacji. Analogicznie administrator może zdefiniować zasady kontroli dostępu opartej na rolach dla zasobów klastra. 

Na przykład administrator może skonfigurować środowisko [Apache Ranger](https://ranger.apache.org/) do ustawiania zasad kontroli dostępu dla usługi Hive. Ta funkcja zapewnia, że pracownicy mogą uzyskać dostęp tylko tych danych, musi być skutecznego wykonywania swoich zadań. Dostęp protokołu SSH z klastrem również jest ograniczony do tylko przez administratora.

## <a name="auditing"></a>Inspekcja
Inspekcja wszystkich dostępu do zasobów klastra i danych, jest niezbędne do śledzenia prób nieautoryzowanego lub niezamierzonego dostępu do zasobów. Jest tak ważna jak ochrona zasobów klastra HDInsight przed nieautoryzowanymi użytkownikami oraz zabezpieczaniem danych. 

Administrator można przeglądać i raportować wszystkie przypadki dostępu do danych i zasobów klastra usługi HDInsight. Administrator można przeglądać i raportować wszystkie zmiany wprowadzane do zasad kontroli dostępu, które są tworzone w struktury Apache Ranger obsługiwanych punktów końcowych. 

Klaster HDInsight przy użyciu ESP używa znanego użytkownika Apache Ranger do wyszukiwania dzienników inspekcji. Na zapleczu, środowisko Ranger używa [Apache Solr](http://lucene.apache.org/solr/) do przechowywania i wyszukiwania dzienników.

## <a name="encryption"></a>Szyfrowanie
Ochrona danych jest ważne dla spotkania zabezpieczeń i zgodności wymagań organizacyjnych. Poza ograniczaniem dostępu do danych nieautoryzowanych pracowników, należy go zaszyfrować. 

Magazyny danych, zarówno w przypadku klastrów HDInsight, Azure Blob storage i Azure Data Lake Storage Gen1/Gen2, obsługują przezroczyste po stronie serwera [szyfrowanie danych](../../storage/common/storage-service-encryption.md) magazynowanych. Zabezpieczonych klastrów HDInsight funkcjonuje bezproblemowo przy użyciu tej funkcji po stronie serwera szyfrowanie danych magazynowanych.

## <a name="next-steps"></a>Kolejne kroki

* [Planowanie z klastrami HDInsight przy użyciu ESP](apache-domain-joined-architecture.md)
* [Konfigurowanie klastrów HDInsight przy użyciu ESP](apache-domain-joined-configure.md)
* [Zarządzanie klastrami HDInsight przy użyciu ESP](apache-domain-joined-manage.md)