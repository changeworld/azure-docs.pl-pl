---
title: Architektura usługi Azure HDInsight z pakietem Enterprise Security
description: Dowiedz się, jak planowanie zabezpieczeń HDInsight z pakietem Enterprise Security.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 5ee03d8dc8dba08994715ace940875980c4f21bb
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041547"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Użyj pakiet Enterprise Security w HDInsight

Klaster w warstwie standardowa usługi Azure HDInsight to klaster z jednego użytkownika. Jest ona odpowiednia dla większości firm, które mają mniejsze zespoły aplikacji tworzące obciążenia dużych ilości danych. Każdego użytkownika można tworzyć dedykowanych klastrów na żądanie i zniszcz go, gdy nie jest już potrzebne. 

Wiele przedsiębiorstw zostały przeniesione na model, w którym klastry są zarządzane przez zespoły IT, a wiele zespołów aplikacji klastrów udziału. Te większych przedsiębiorstw potrzebują wielu użytkowników dostępu do poszczególnych klastrów w usłudze Azure HDInsight.

HDInsight zależy od dostawcy popularnych tożsamości — usłudze Active Directory — w zarządzany sposób. Po zintegrowaniu HDInsight przy użyciu [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), dostęp do tych klastrów przy użyciu poświadczeń domeny. 

Maszyny wirtualne (VM) w HDInsight są przyłączony do domeny podana. Tak, wszystkie usługi działające na HDInsight (Ambari, Hive server, Ranger, Spark thrift server itd) bezproblemowo działały dla uwierzytelnionego użytkownika. Administratorzy mogą tworzyć zasady silnych autoryzacji przy użyciu struktury Apache Ranger w celu zapewnienia kontroli dostępu opartej na rolach dla zasobów w klastrze.

## <a name="integrate-hdinsight-with-active-directory"></a>Integracja usługi HDInsight z usługą Active Directory

Hadoop typu open-source opiera się na protokołu Kerberos do uwierzytelniania i zabezpieczeń. W związku z tym węzły klastra HDInsight przy użyciu pakietu zabezpieczeń przedsiębiorstwa (ESP) są przyłączone do domeny, który jest zarządzany przez usługi Azure AD DS. Zabezpieczenia protokołu Kerberos został skonfigurowany do składniki platformy Hadoop w klastrze. 

Następujące elementy są tworzone automatycznie:
- jednostki usługi dla poszczególnych składników usługi Hadoop 
- podmiot zabezpieczeń maszyny dla każdej maszyny, który jest przyłączony do domeny
- Jednostki organizacyjnej (OU) dla każdego klastra do przechowywania podmiotów zabezpieczeń tych usług oraz maszyn 

Aby podsumować, należy skonfigurować środowisko przy użyciu:

- Domeny usługi Active Directory (zarządzane przez usługi Azure AD DS).
- Bezpiecznego protokołu LDAP (LDAPS) włączone w usłudze Azure AD DS.
- Odpowiednie sieci łączność z siecią wirtualną HDInsight, do usługi Azure AD DS sieci wirtualnej, jeśli wybierzesz oddzielne sieci wirtualnej dla nich. Maszynę wirtualną w sieci wirtualnej HDInsight mają bezpośredni kontakt z usługą Azure AD DS za pomocą komunikacji równorzędnej sieci wirtualnej. HDInsight i Azure AD DS w przypadku wdrożenia w tej samej sieci wirtualnej, połączenie jest dostarczana automatycznie i nie trzeba wykonywać dalszych akcji.

## <a name="set-up-different-domain-controllers"></a>Konfigurowanie różnych kontrolerach domeny
HDInsight aktualnie obsługuje tylko usługi Azure AD DS jako kontrolera domeny głównej, używanego przez klaster komunikacji protokołu Kerberos. Jednak inne złożonych konfiguracji usługi Active Directory jest to możliwe, tak długo, jak takiej konfiguracji prowadzi do włączania usługi Azure AD DS dla dostępu HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Usługi Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) zapewnia domeny zarządzanej, która jest w pełni zgodna z usługą Active Directory systemu Windows Server. Microsoft zajmuje się zarządzaniem, obsługą jej poprawek oraz monitorowanie domeny w konfiguracji o wysokiej dostępności (HA). Możesz wdrożyć klaster bez martwienia się o utrzymania kontrolerów domeny. 

Użytkownikom, grupom i hasła są synchronizowane z usługą Azure Active Directory (Azure AD). Jednokierunkowa synchronizacji z wystąpienia usługi Azure AD do usługi Azure AD DS pozwala użytkownikom logować się do klastra przy użyciu tych samych poświadczeń firmowych. 

Aby uzyskać więcej informacji, zobacz [HDInsight konfigurowanie klastrów przy użyciu ESP przy użyciu usługi Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Lokalne usługi Active Directory lub usługi Active Directory na maszynach wirtualnych IaaS

Jeśli masz wystąpienie usługi Active Directory w środowisku lokalnym lub bardziej złożonych konfiguracji usługi Active Directory dla domeny, można zsynchronizować te tożsamości do usługi Azure AD za pomocą usługi Azure AD Connect. Następnie można włączyć usługi Azure AD DS w tej dzierżawie usługi Active Directory. 

Ponieważ protokół Kerberos opiera się na wartości skrótów haseł, konieczne będzie [Włączanie synchronizacji skrótów haseł w usłudze Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Jeśli używasz federacji z usługi Active Directory Federation Services (AD FS), możesz opcjonalnie skonfigurować synchronizacji skrótów haseł do przechowywania kopii zapasowych na wypadek awarii infrastruktury usług AD FS. Aby uzyskać więcej informacji, zobacz [Włączanie synchronizacji skrótów haseł za pomocą usługi Azure AD Connect sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

Używanie w lokalnej usłudze Active Directory lub usługi Active Directory na maszynach wirtualnych IaaS samodzielnie, bez usługi Azure AD i Azure AD DS, nie jest obsługiwana konfiguracja w przypadku klastrów HDInsight przy użyciu ESP.

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie klastrów HDInsight przy użyciu ESP](apache-domain-joined-configure-using-azure-adds.md)
* [Konfigurowanie zasad usługi Hive dla klastrów HDInsight przy użyciu ESP](apache-domain-joined-run-hive.md)
* [Zarządzanie klastrami HDInsight przy użyciu ESP](apache-domain-joined-manage.md) 
