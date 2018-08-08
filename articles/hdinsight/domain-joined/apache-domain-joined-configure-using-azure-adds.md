---
title: Konfigurowanie klastra HDInsight przyłączone do domeny za pomocą usługi Azure AD DS
description: Dowiedz się, jak instalowanie i konfigurowanie klastra HDInsight przyłączone do domeny za pomocą usługi Azure Active Directory Domain Services
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.topic: conceptual
ms.date: 07/17/2018
ms.openlocfilehash: 0d44812c92fd14bf87aac9a942241f8de55f2eec
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590591"
---
# <a name="configure-a-domain-joined-hdinsight-cluster-by-using-azure-active-directory-domain-services"></a>Konfigurowanie klastra HDInsight przyłączone do domeny za pomocą usługi Azure Active Directory Domain Services

Klastry przyłączone do domeny zapewniają dostęp wielu użytkowników w klastrach usługi Azure HDInsight. Klastry HDInsight przyłączone do domeny są podłączone do domeny, tak, aby użytkownicy domeny mogą używać swoich poświadczeń domeny przy użyciu klastrów i uruchamiać zadania obsługi danych big data. 

W tym artykule dowiesz się, jak skonfigurować klaster HDInsight przyłączone do domeny za pomocą usługi Azure Active Directory Domain Services (Azure AD DS).

## <a name="enable-azure-ad-ds"></a>Włączanie usług Azure AD DS

Włączanie usługi Azure AD DS jest wymaganiem wstępnym, przed utworzeniem klastra HDInsight przyłączone do domeny. Aby uzyskać więcej informacji, zobacz [włączyć usługi Azure Active Directory Domain Services w witrynie Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Tylko Administratorzy dzierżawy mają uprawnienia do tworzenia wystąpienia usługi Azure AD DS. Jeśli używasz usługi Azure Data Lake Storage Gen1 jako magazynem domyślnym dla HDInsight, upewnij się, że dzierżawy usługi Azure AD domyślny dla Data Lake Storage Gen1 jest taka sama jak domena klastra HDInsight. Ponieważ Hadoop korzysta z protokołu Kerberos i uwierzytelnianie podstawowe, uwierzytelnianie wieloskładnikowe musi wyłączony dla użytkowników, którzy będą uzyskiwać dostęp do klastra.

Po zainicjowaniu wystąpienia usługi Azure AD DS, należy utworzyć konto usługi Azure Active Directory (Azure AD) przy użyciu odpowiednich uprawnień. Jeśli to konto usługi już istnieje, to należy zresetować jego hasło i zaczekaj, aż synchronizowanych usługi Azure AD DS. Spowoduje to zresetowanie do tworzenia skrótu hasła protokołu Kerberos i może potrwać do 30 minut do synchronizacji usługi Azure AD DS. 

Konto usługi musi mieć następujące uprawnienia:

- Przyłączanie maszyn do domeny i umieścić podmiotów maszyn w jednostce Organizacyjnej, który jest określany podczas tworzenia klastra.
- Tworzenie jednostek usługi w jednostce Organizacyjnej, który jest określany podczas tworzenia klastra.

> [!NOTE]
> Ponieważ Apache Zeppelin używa nazwy domeny do uwierzytelniania konta usługi administracyjnej, konto usługi *musi* mają taką samą nazwę domeny jako jego sufiks nazwy UPN dla Zeppelin Apache działać prawidłowo.

Aby dowiedzieć się więcej o jednostkach organizacyjnych i jak nimi zarządzać, zobacz [Utwórz jednostkę Organizacyjną w domenie zarządzanej usługi Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md). 

Jest protokół Secure LDAP dla domeny zarządzanej usług Azure AD DS. Aby uzyskać więcej informacji, zobacz [domeny zarządzanej przez konfigurowanie protokołu secure LDAP dla usługi Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Tworzenie klastra HDInsight przyłączone do domeny

Następnym krokiem jest do utworzenia klastra HDInsight przy użyciu usługi Azure AD DS i konto usługi, który został utworzony w poprzedniej sekcji.

Łatwiej można umieścić wystąpienia usługi Azure AD DS wraz z klastrów HDInsight w tej samej sieci wirtualnej platformy Azure. Jeśli zdecydujesz się umieścić je w różnych sieciach wirtualnych, tak aby maszyny wirtualne HDInsight mają linii wzroku do kontrolera domeny do przyłączania maszyn wirtualnych musi komunikacji równorzędnej tych sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [komunikacja równorzędna sieci wirtualnych](../../virtual-network/virtual-network-peering-overview.md).

Kiedy tworzysz klaster HDInsight przyłączone do domeny, należy podać następujące parametry:

- **Nazwa domeny**: nazwa domeny, która jest skojarzona z usługi Azure AD DS. Przykładem jest contoso.onmicrosoft.com.

- **Domena nazwa użytkownika**: konto usługi w ramach kontrolera domeny platforma Azure DODAJE zarządzane domeny, który został utworzony w poprzedniej sekcji. Może to być na przykład hdiadmin@contoso.onmicrosoft.com. Ten użytkownik domeny będzie administratorem tego klastra HDInsight.

- **Hasło domeny**: hasło konta usługi.

- **Jednostka organizacyjna**: nazwa wyróżniająca jednostki Organizacyjnej, która ma być używany z klastrem HDInsight. Przykładem jest OU = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com. Jeśli nie ma tej jednostki Organizacyjnej, klaster HDInsight próbuje utworzyć jednostkę Organizacyjną przy użyciu uprawnień, które ma konto usługi. Na przykład jeśli konto usługi jest w grupie Administratorzy usługi Azure AD DS, ma odpowiednie uprawnienia, aby utworzyć jednostkę Organizacyjną. W przeciwnym razie należy najpierw utworzyć jednostkę Organizacyjną i oferuje usługi konta pełną kontrolę nad tej jednostce Organizacyjnej. Aby uzyskać więcej informacji, zobacz [Utwórz jednostkę Organizacyjną w domenie zarządzanej usługi Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).

    > [!IMPORTANT]
    > Obejmuje wszystkie kontrolery domeny, oddzielone przecinkami, po jednostki Organizacyjnej (na przykład, jednostki Organizacyjnej HDInsightOU, DC = = contoso, DC = onmicrosoft, DC = com).

- **Adres URL protokołu LDAPS**: są to na przykład ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Wprowadź pełny adres URL, w tym "ldaps: / /" i numer portu (: 636).

- **Dostęp do grupy użytkowników**: grup zabezpieczeń, której użytkownicy mają być synchronizowane z klastrem. Na przykład HiveUsers. Jeśli chcesz określić wiele grup użytkowników, oddziel je średnikami ";". Grupy muszą istnieć w katalogu przed inicjowania obsługi administracyjnej. Aby uzyskać więcej informacji, zobacz [tworzyć grupy i dodawać członków w usłudze Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Jeśli grupa nie istnieje, wystąpi błąd: "Grupa HiveUsers nie znaleziono w usłudze Active Directory."

Poniższy zrzut ekranu przedstawia konfiguracje w witrynie Azure portal:

   ![Usługa Azure HDInsight przyłączone do domeny Active Directory Domain Services konfiguracji](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Kolejne kroki
* Konfigurowanie zasad usługi Hive i uruchamiania zapytań programu Hive, zobacz [Konfigurowanie zasad usługi Hive dla przyłączonych do domeny klastrów HDInsight](apache-domain-joined-run-hive.md).
* Aby uzyskać przy użyciu protokołu SSH, aby połączyć się z przyłączonym do domeny klastrami HDInsight, zobacz [używanie protokołu SSH z opartą na systemie Linux platformą Hadoop w HDInsight z systemów Linux, Unix lub OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

