---
title: Konfigurowanie domeny klastrów usługi HDInsight przy użyciu usługi AAD DS
description: Informacje o sposobie instalowania i konfigurowania klastrów HDInsight przyłączonych do domeny za pomocą usług domenowych Azure Active Directory
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 7bde1c834038bdc2a010987b4e32fa49222101ee
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715279"
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Konfigurowanie klastrów HDInsight przyłączonych do domeny za pomocą usług domenowych Azure Active Directory

Klastry przyłączonych do domeny zapewniają dostęp wielu użytkowników w klastrach usługi HDInsight. Klastry HDInsight przyłączonych do domeny są połączone z domeną, aby użytkownicy domeny mogli używać swoich poświadczeń domeny do uwierzytelniania z klastrów i uruchamianie zadań danych big data. 

W tym artykule Dowiedz się jak skonfigurować klaster HDInsight przyłączonych do domeny za pomocą usług domenowych Azure Active Directory.

> [!NOTE]
> Tworzenie klastrów HDInsight przyłączonych do domeny wymaga usług domenowych Azure Active Directory. Tworzenie domeny HDInsight, klaster przy użyciu usługi Active Directory hostowanych na maszynach wirtualnych Azure IaaS nie jest już obsługiwana.

## <a name="create-azure-adds"></a>Utwórz DODAJE Azure

Włączanie usług domenowych Azure AD (AAD DS) jest wymagane, przed rozpoczęciem tworzenia klastra usługi HDInsight przyłączonych do domeny. Aby włączyć wystąpienie usługi AAD DS, zobacz [sposób włączania usługi AAD-DS przy użyciu portalu Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Tylko Administratorzy dzierżawy mają uprawnienia do utworzenia wystąpienia usługi AAD DS. Jeśli używasz usługi Azure Data Lake magazyn (ADLS) jako domyślny magazyn dla usługi HDInsight, upewnij się, że domyślna dzierżawa usługi Azure AD dla ADLS jest taka sama jak domena klastra usługi HDInsight. Sincde Hadoop korzysta z protokołu Kerberos i uwierzytelnianie podstawowe, uwierzytelnianie wieloskładnikowe musi być wyłączona dla użytkowników mających dostęp do klastra.

Po zainicjowano wystąpienia usługi AAD DS, należy utworzyć konto usługi w usłudze AAD (który będą synchronizowane z usługi AAD DS) z odpowiednich uprawnień. Jeśli to konto usługi już istnieje, należy zresetować hasło i poczekaj, aż synchronizowanych AAD DS (resetowania spowoduje tworzenia skrótu hasła protokołu kerberos i może potrwać do 30 min do synchronizacji usługi AAD DS). To konto usługi musi mieć następujące uprawnienia:

- Przyłączanie komputerów do domeny i umieścić podmiotów maszyny w jednostce Organizacyjnej, który jest określany podczas tworzenia klastra.
- Utwórz jednostki usługi w jednostce Organizacyjnej, który jest określany podczas tworzenia klastra.

> [!NOTE]
> Ponieważ Apache Zeppelin użyta zostanie nazwa domeny do uwierzytelniania konta usługi administracyjnej, konto usługi musi mieć taką samą nazwę domeny, jak jego sufiks nazwy UPN Zeppelin Apache działać prawidłowo.

Aby dowiedzieć się więcej na temat jednostek organizacyjnych i jak nimi zarządzać, zobacz [utworzyć jednostkę Organizacyjną na DS AAD](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md). 

Bezpiecznego protokołu LDAP dla domeny zarządzane AAD DS jest wymagana. Aby włączyć bezpiecznego protokołu LDAP, zobacz [domeny zarządzane jak skonfigurować bezpiecznego protokołu LDAP (LDAPS) dla usługi AAD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Tworzenie klastra HDInsight przyłączonych do domeny

Następnym krokiem jest do tworzenia klastra usługi HDInsight przy użyciu usługi Katalogowej usługi AAD i konto usługi utworzony w poprzedniej sekcji.

Łatwiej można umieścić AAD DS wraz z klastrem usługi HDInsight w tej samej network(VNet) wirtualnych platformy Azure. W przypadku, gdy wybrano opcję umieść je w innej sieci wirtualnych, dzięki czemu HDI maszyny wirtualne muszą procesów z wiersza do kontrolera domeny za dołączenie do maszyn wirtualnych musi elementu równorzędnego tych sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [równorzędna sieci wirtualnej](../../virtual-network/virtual-network-peering-overview.md).

Podczas tworzenia klastra usługi HDInsight przyłączonych do domeny, należy podać następujące parametry:

- **Nazwa domeny**: nazwa domeny skojarzony z usługi AAD DS. Na przykład contoso.onmicrosoft.com
- **Nazwa użytkownika domeny**: konto usługi w ramach domeny zarządzanej, która jest tworzona w poprzedniej sekcji. Na przykład hdiadmin@contoso.onmicrosoft.com. Ten użytkownik domeny będzie administrator tego klastra usługi HDInsight.
- **Hasła domeny**: hasło konta usługi.
- **Jednostka organizacyjna**: nazwę wyróżniającą jednostki Organizacyjnej, do którego chcesz używać z klastrem usługi HDInsight. Na przykład: OU = HDInsightOU, DC = contoso, DC = onmicrosohift, DC = com. Jeśli nie istnieje tej jednostki Organizacyjnej, klastra usługi HDInsight próbuje utworzyć jednostkę Organizacyjną, korzystając z uprawnień, który ma konto usługi. (Na przykład, jeśli konto usługi jest w grupie administratorów usługi AAD DS, ma odpowiednie uprawnienia, aby utworzyć jednostkę Organizacyjną, w przeciwnym razie należy najpierw utworzyć jednostkę Organizacyjną i najpierw nadaj konta usługi pełną kontrolę nad tej jednostce Organizacyjnej — zobacz [utworzyć jednostkę Organizacyjną na AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)).

    > [!IMPORTANT]
    > Należy dołączyć wszystkie sepearated kontrolerów domeny przez przecinek po jednostki Organizacyjnej (. przykład: OU = HDInsightOU, DC = contoso, DC = onmicrosohift, DC = com).

- **Adres URL LDAPS**: na przykład ldaps://contoso.onmicrosoft.com:636

    > [!IMPORTANT]
    > Wprowadź pełny adres url, w tym ldaps: / / i numer portu (: 636)

- **Grupy użytkowników dostępu**: grupy zabezpieczeń użytkowników, którzy mają być synchronizowane z klastrem. Na przykład HiveUsers. Jeśli chcesz określić wiele grup użytkowników, oddziel je przecinkami ','.
 
Poniższy zrzut ekranu przedstawia konfiguracje w portalu Azure:

![Azure HDInsight przyłączonych do domeny konfiguracji usług domenowych w usłudze Active Directory](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Kolejne kroki
* Aby znaleźć informacje na temat konfigurowania zasad Hive i uruchamiania kwerend Hive, zobacz [Konfigurowanie zasad usługi Hive dla przyłączonych do domeny klastrów usługi HDInsight](apache-domain-joined-run-hive.md).
* Aby uzyskać przy użyciu protokołu SSH, aby połączyć się z klastrami HDInsight przyłączonych do domeny, zobacz [używanie SSH z opartą na systemie Linux platformą Hadoop w usłudze HDInsight z systemów Linux, Unix lub OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

