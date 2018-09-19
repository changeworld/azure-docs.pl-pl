---
title: Architektura usługi Azure HDInsight przyłączone do domeny
description: Dowiedz się, jak zaplanować usługę HDInsight przyłączoną do domeny.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: efdc9cfbbe9a78571e0a56437e512d0cbbc18b3e
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297281"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planowanie klastrów Hadoop przyłączonych do domeny platformy Azure w usłudze HDInsight

Klaster w warstwie standardowa usługi Azure HDInsight to klaster z jednego użytkownika. Jest ona odpowiednia dla większości firm, które mają mniejsze zespoły aplikacji tworzące obciążenia dużych ilości danych. Każdego użytkownika można tworzyć dedykowanych klastrów na żądanie i zniszcz go, gdy nie jest już potrzebne. 

Wiele przedsiębiorstw zostały przeniesione na model, w którym klastry są zarządzane przez zespoły IT, a wiele zespołów aplikacji klastrów udziału. Te większych przedsiębiorstw potrzebują wielu użytkowników dostępu do poszczególnych klastrów w usłudze Azure HDInsight.

HDInsight zależy od dostawcy popularnych tożsamości — usłudze Active Directory — w zarządzany sposób. Po zintegrowaniu HDInsight przy użyciu [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), dostęp do tych klastrów przy użyciu poświadczeń domeny. 

Maszyny wirtualne (VM) w HDInsight są przyłączony do domeny podana. Tak, wszystkie usługi działające na HDInsight (Ambari, Hive server, Ranger, Spark thrift server itd) bezproblemowo działały dla uwierzytelnionego użytkownika. Administratorzy mogą tworzyć zasady silnych autoryzacji przy użyciu struktury Apache Ranger w celu zapewnienia kontroli dostępu opartej na rolach dla zasobów w klastrze.


## <a name="integrate-hdinsight-with-active-directory"></a>Integracja usługi HDInsight z usługą Active Directory

Hadoop typu open-source opiera się na protokołu Kerberos do uwierzytelniania i zabezpieczeń. Dlatego węzły klastra HDInsight są przyłączone do domeny, który jest zarządzany przez usługi Azure AD DS. Zabezpieczenia protokołu Kerberos został skonfigurowany do składniki platformy Hadoop w klastrze. 

Dla poszczególnych składników usługi Hadoop jednostka usługi jest tworzona automatycznie. Odpowiedniej maszyny, podmiotu zabezpieczeń jest tworzona dla każdego komputera, który jest przyłączony do domeny. Do przechowywania tych usług i maszynowego podmiotów zabezpieczeń, musisz podać jednostki organizacyjnej (OU) w ramach kontrolera domeny (Azure AD DS), rozmieszczenie tych jednostek. 

Aby podsumować, należy skonfigurować środowisko przy użyciu:

- Domeny usługi Active Directory (zarządzane przez usługi Azure AD DS).
- Bezpiecznego protokołu LDAP (LDAPS) włączone w usłudze Azure AD DS.
- Odpowiednie sieci łączność z siecią wirtualną HDInsight, do usługi Azure AD DS sieci wirtualnej, jeśli wybierzesz oddzielne sieci wirtualnej dla nich. Maszynę wirtualną w sieci wirtualnej HDInsight mają bezpośredni kontakt z usługą Azure AD DS za pomocą komunikacji równorzędnej sieci wirtualnej. HDInsight i Azure AD DS w przypadku wdrożenia w tej samej sieci wirtualnej, połączenie jest dostarczana automatycznie i nie trzeba wykonywać dalszych akcji.
- Jednostka Organizacyjna [utworzyć dla usługi Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).
- Konto usługi, które ma uprawnienia do:
    - Tworzenie jednostek usługi w jednostce Organizacyjnej.
    - Przyłączanie maszyn do domeny i tworzenie jednostek maszyn w jednostce Organizacyjnej.

Poniższy zrzut ekranu przedstawia jednostki Organizacyjnej, utworzone w domenie contoso.com. Pokazuje także niektórych jednostek usługi i nazwy główne maszyny.

![Jednostka organizacyjna dla klastrów HDInsight przyłączone do domeny](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

## <a name="set-up-different-domain-controllers"></a>Konfigurowanie różnych kontrolerach domeny
HDInsight aktualnie obsługuje tylko usługi Azure AD DS jako kontrolera domeny głównej, używanego przez klaster komunikacji protokołu Kerberos. Jednak inne złożonych konfiguracji usługi Active Directory jest to możliwe, tak długo, jak takiej konfiguracji prowadzi do włączania usługi Azure AD DS dla dostępu HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Usługi Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) zapewnia domeny zarządzanej, która jest w pełni zgodna z usługą Active Directory systemu Windows Server. Microsoft zajmuje się zarządzaniem, obsługą jej poprawek oraz monitorowanie domeny w konfiguracji o wysokiej dostępności (HA). Możesz wdrożyć klaster bez martwienia się o utrzymania kontrolerów domeny. 

Użytkownikom, grupom i hasła są synchronizowane z usługą Azure Active Directory (Azure AD). Jednokierunkowa synchronizacji z wystąpienia usługi Azure AD do usługi Azure AD DS pozwala użytkownikom logować się do klastra przy użyciu tych samych poświadczeń firmowych. 

Aby uzyskać więcej informacji, zobacz [Konfiguruj HDInsight przyłączone do domeny klastrów za pomocą usługi Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Lokalne usługi Active Directory lub usługi Active Directory na maszynach wirtualnych IaaS

Jeśli masz wystąpienie usługi Active Directory w środowisku lokalnym lub bardziej złożonych konfiguracji usługi Active Directory dla domeny, można zsynchronizować te tożsamości do usługi Azure AD za pomocą usługi Azure AD Connect. Następnie można włączyć usługi Azure AD DS w tej dzierżawie usługi Active Directory. 

Ponieważ protokół Kerberos opiera się na wartości skrótów haseł, konieczne będzie [Włączanie synchronizacji skrótów haseł w usłudze Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Jeśli używasz federacji z usługi Active Directory Federation Services (AD FS), możesz opcjonalnie skonfigurować synchronizacji skrótów haseł do przechowywania kopii zapasowych na wypadek awarii infrastruktury usług AD FS. Aby uzyskać więcej informacji, zobacz [Włączanie synchronizacji skrótów haseł za pomocą usługi Azure AD Connect sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

Za pomocą lokalnej usługi Active Directory lub usługi Active Directory na maszynach wirtualnych IaaS samodzielnie, bez usługi Azure AD i Azure AD DS, konfiguracja nie jest obsługiwane w przypadku klastrów HDInsight przyłączone do domeny.

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie przyłączonych do domeny klastrów HDInsight](apache-domain-joined-configure-using-azure-adds.md)
* [Konfigurowanie zasad usługi Hive dla przyłączonych do domeny klastrów HDInsight](apache-domain-joined-run-hive.md)
* [Zarządzanie klastrami HDInsight przyłączone do domeny](apache-domain-joined-manage.md) 
