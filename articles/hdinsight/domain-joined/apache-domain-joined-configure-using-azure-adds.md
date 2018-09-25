---
title: Konfigurowanie klastra HDInsight z pakietem Enterprise Security za pomocą usług AD DS platformy Azure
description: Dowiedz się, jak instalowanie i konfigurowanie klastra HDInsight Enterprise Security pakietu przy użyciu usługi Azure Active Directory Domain Services
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a5b377381fd540c2a9f1d85e0cb7edce32c2dae8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968377"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Konfigurowanie klastra HDInsight z pakietem Enterprise Security za pomocą usługi Azure Active Directory Domain Services

Klastry Enterprise Security pakietu (ESP) zapewniają dostęp wielu użytkowników w klastrach usługi Azure HDInsight. Klastrami HDInsight przy użyciu ESP są podłączone do domeny, dzięki czemu użytkownicy domeny mogą używać swoich poświadczeń domeny przy użyciu klastrów i uruchamiać zadania obsługi danych big data. 

W tym artykule dowiesz się, jak skonfigurować klaster HDInsight przy użyciu ESP przy użyciu Azure Active Directory Domain Services (Azure AD DS).

>[!NOTE]
>ESP jest dostępna w usłudze HDI 3.6 + dla platformy Spark, interaktywny i Hadoop. ESP dla typów klastra HBase jest w wersji zapoznawczej.


## <a name="enable-azure-ad-ds"></a>Włączanie usługi Azure AD DS

Włączanie usług AD DS Azure to warunek wstępny, przed utworzeniem klastra HDInsight z ESP. Aby uzyskać więcej informacji, zobacz [włączyć usługi Azure Active Directory Domain Services w witrynie Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Tylko Administratorzy dzierżawy mają uprawnienia do tworzenia wystąpienia usług AD DS platformy Azure. Jeśli używasz usługi Azure Data Lake Storage Gen1 jako magazynem domyślnym dla HDInsight, upewnij się, że dzierżawy usługi Azure AD domyślny dla Data Lake Storage Gen1 jest taka sama jak domena klastra HDInsight. Ponieważ Hadoop korzysta z protokołu Kerberos i uwierzytelnianie podstawowe, uwierzytelnianie wieloskładnikowe musi wyłączony dla użytkowników, którzy będą uzyskiwać dostęp do klastra.

Jest protokół Secure LDAP dla domeny zarządzanej usług AD DS platformy Azure. Podczas włączania protokołu LDAPS, umieść nazwę domeny do nazwy podmiotu lub alternatywnej nazwy podmiotu w certyfikacie. Aby uzyskać więcej informacji, zobacz [domeny zarządzanej przez Konfigurowanie bezpiecznego protokołu LDAP dla usługi Azure AD — DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="add-managed-identity"></a>Dodaj tożsamość zarządzaną

Po włączeniu usług AD DS Azure utworzyć tożsamość zarządzaną i przypisz ją do **Współautor usługi domeny HDInsight** roli kontroli dostępu usług AD DS w platformie Azure.

![Usługa Azure Active Directory Domain Services dostępu formantu](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Aby uzyskać więcej informacji, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Tworzenie klastra HDInsight z ESP

Następnym krokiem jest do utworzenia klastra HDInsight z ESP włączone za pomocą usług AD DS platformy Azure.

Łatwiej można umieścić wystąpienia usług AD DS Azure wraz z klastrów HDInsight w tej samej sieci wirtualnej platformy Azure. Jeśli zdecydujesz się umieścić je w różnych sieciach wirtualnych, tak aby maszyny wirtualne HDInsight mają linii wzroku do kontrolera domeny do przyłączania maszyn wirtualnych musi komunikacji równorzędnej tych sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [komunikacja równorzędna sieci wirtualnych](../../virtual-network/virtual-network-peering-overview.md).

Podczas tworzenia klastra usługi HDInsight, masz opcję, aby włączyć pakiet Enterprise Security do łączenia z klastrem przy użyciu usługi Azure AD — DS. 

![Zabezpieczenia usługi Azure HDInsight i sieci](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Po włączeniu ESP, typowych błędów konfiguracji związane z usługi Azure AD — DS będzie można automatycznie wykryte i sprawdzone.

![Weryfikacja domeny pakietu zabezpieczeń Azure HDInsight Enterprise](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Wczesne wykrywanie pozwala zaoszczędzić czas, co pozwala naprawić błędy przed utworzeniem klastra.

![Pakiet zabezpieczeń w usłudze Azure HDInsight przedsiębiorstwa nie powiodła się weryfikacja domeny](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Po utworzeniu klastra HDInsight z ESP, musisz podać następujące parametry:

- **Administrator klastra**: Wybierz z usługi zsynchronizowanych Azure AD — DS Administrator dla klastra.

- **Klaster grup dostępu**: grupy zabezpieczeń użytkowników, którzy mają być synchronizowane z klastrem powinny być zsynchronizowane i dostępne w usłudze Azure AD — DS. Na przykład HiveUsers. Jeśli chcesz określić wiele grup użytkowników, oddziel je średnikami ";". Grupy muszą istnieć w katalogu przed inicjowania obsługi administracyjnej. Aby uzyskać więcej informacji, zobacz [tworzyć grupy i dodawać członków w usłudze Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Jeśli grupa nie istnieje, wystąpi błąd: "Grupa HiveUsers nie znaleziono w usłudze Active Directory."

- **Adres URL protokołu LDAPS**: są to na przykład ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Wprowadź pełny adres URL, w tym "ldaps: / /" i numer portu (: 636).

Poniższy zrzut ekranu przedstawia konfiguracje w witrynie Azure portal:

   ![Konfiguracja usługi Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Kolejne kroki
* Konfigurowanie zasad usługi Hive i uruchamiania zapytań programu Hive, zobacz [Konfigurowanie zasad usługi Hive dla HDInsight klastrów przy użyciu ESP](apache-domain-joined-run-hive.md).
* Aby połączyć się z klastrami HDInsight przy użyciu ESP, przy użyciu protokołu SSH, zobacz [używanie protokołu SSH z opartą na systemie Linux platformą Hadoop w HDInsight z systemów Linux, Unix lub OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

