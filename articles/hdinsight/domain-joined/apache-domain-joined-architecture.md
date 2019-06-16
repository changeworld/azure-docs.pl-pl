---
title: Architektura usługi Azure HDInsight z pakietem Enterprise Security
description: Dowiedz się, jak planowanie zabezpieczeń HDInsight z pakietem Enterprise Security.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 46eb90d2ec9902a9b5b7830063d0a6164ae948dd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061128"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Użyj pakiet Enterprise Security w HDInsight

Klaster w warstwie standardowa usługi Azure HDInsight to klaster z jednego użytkownika. Jest ona odpowiednia dla większości firm, które mają mniejsze zespoły aplikacji tworzące obciążenia dużych ilości danych. Każdego użytkownika można tworzyć dedykowanych klastrów na żądanie i zniszcz go, gdy nie jest już potrzebne. 

Wiele przedsiębiorstw zostały przeniesione na model, w którym zespoły zarządzania klastrami, a wiele zespołów aplikacji klastrów udziału. Te większych przedsiębiorstw potrzebują wielu użytkowników dostępu do poszczególnych klastrów w usłudze Azure HDInsight.

HDInsight zależy od dostawcy popularnych tożsamości — usłudze Active Directory — w zarządzany sposób. Po zintegrowaniu HDInsight przy użyciu [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md), dostęp do tych klastrów przy użyciu poświadczeń domeny. 

Maszyny wirtualne (VM) w HDInsight są przyłączony do domeny podana. Tak wszystkie usługi działające na HDInsight (Apache Ambari, serwerze Apache Hive, Apache Ranger, Apache Spark thrift server i inne) współpracują bezproblemowo dla tego uwierzytelnionego użytkownika. Administratorzy mogą tworzyć zasady silnych autoryzacji przy użyciu struktury Apache Ranger w celu zapewnienia kontroli dostępu opartej na rolach dla zasobów w klastrze.

## <a name="integrate-hdinsight-with-active-directory"></a>Integracja usługi HDInsight z usługą Active Directory

Open source Apache Hadoop korzysta z protokołu Kerberos do uwierzytelniania i zabezpieczeń. W związku z tym węzły klastra HDInsight przy użyciu pakietu zabezpieczeń przedsiębiorstwa (ESP) są przyłączone do domeny, który jest zarządzany przez usługi Azure AD DS. Zabezpieczenia protokołu Kerberos został skonfigurowany do składniki platformy Hadoop w klastrze. 

Następujące elementy są tworzone automatycznie:

- jednostki usługi dla poszczególnych składników usługi Hadoop
- podmiot zabezpieczeń maszyny dla każdej maszyny, który jest przyłączony do domeny
- Jednostki organizacyjnej (OU) dla każdego klastra do przechowywania podmiotów zabezpieczeń tych usług oraz maszyn

Aby podsumować, należy skonfigurować środowisko przy użyciu:

- Domeny usługi Active Directory (zarządzane przez usługi Azure AD DS).
- Bezpiecznego protokołu LDAP (LDAPS) włączone w usłudze Azure AD DS.
- Odpowiednie sieci łączność z siecią wirtualną HDInsight, do usługi Azure AD DS sieci wirtualnej, jeśli wybierzesz oddzielne sieci wirtualnej dla nich. Maszynę wirtualną w sieci wirtualnej HDInsight mają bezpośredni kontakt z usługą Azure AD DS za pomocą komunikacji równorzędnej sieci wirtualnej. Jeśli HDInsight i Azure AD DS są wdrożone w tej samej sieci wirtualnej, połączenie jest dostarczana automatycznie i nie trzeba wykonywać dalszych akcji.

## <a name="set-up-different-domain-controllers"></a>Konfigurowanie różnych kontrolerach domeny
HDInsight aktualnie obsługuje tylko usługi Azure AD DS jako kontrolera domeny głównej, używanego przez klaster komunikacji protokołu Kerberos. Jednak inne złożonych konfiguracji usługi Active Directory jest to możliwe, tak długo, jak takiej konfiguracji prowadzi do włączania usługi Azure AD DS dla dostępu HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Usługi Azure AD DS](../../active-directory-domain-services/overview.md) zapewnia domeny zarządzanej, która jest w pełni zgodna z usługą Active Directory systemu Windows Server. Microsoft zajmuje się zarządzaniem, obsługą jej poprawek oraz monitorowanie domeny w konfiguracji o wysokiej dostępności (HA). Możesz wdrożyć klaster bez martwienia się o utrzymania kontrolerów domeny. 

Użytkownikom, grupom i hasła są synchronizowane z usługi Azure AD. Jednokierunkowa synchronizacji z wystąpienia usługi Azure AD do usługi Azure AD DS pozwala użytkownikom logować się do klastra przy użyciu tych samych poświadczeń firmowych. 

Aby uzyskać więcej informacji, zobacz [HDInsight konfigurowanie klastrów przy użyciu ESP przy użyciu usługi Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Lokalne usługi Active Directory lub usługi Active Directory na maszynach wirtualnych IaaS

Jeśli masz wystąpienie usługi Active Directory w środowisku lokalnym lub bardziej złożonych konfiguracji usługi Active Directory dla domeny, można zsynchronizować te tożsamości do usługi Azure AD za pomocą usługi Azure AD Connect. Następnie można włączyć usługi Azure AD DS w tej dzierżawie usługi Active Directory. 

Ponieważ protokół Kerberos opiera się na wartości skrótów haseł, należy najpierw [Włączanie synchronizacji skrótów haseł w usłudze Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). 

Jeśli używasz federacji z usługi Active Directory Federation Services (AD FS), należy włączyć synchronizację skrótów haseł. (Aby uzyskać zalecane ustawienia, zobacz [ten film wideo](https://youtu.be/qQruArbu2Ew).) Synchronizacja skrótów haseł może ułatwić realizację odzyskiwania po awarii w przypadku, gdy ulegnie awarii infrastruktury usług AD FS, a także pomaga zapewnić ochronę wyciek poświadczeń. Aby uzyskać więcej informacji, zobacz [Włączanie synchronizacji skrótów haseł za pomocą usługi Azure AD Connect sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

Za pomocą lokalnej usługi Active Directory lub usługi Active Directory na maszynach wirtualnych IaaS samodzielnie, bez usługi Azure AD i Azure AD DS, nie ma obsługiwanej konfiguracji dla klastrów HDInsight przy użyciu ESP.

Jeśli federacyjnej jest używana i skróty haseł są poprawnie synchronizowane, ale pojawiają się błędy uwierzytelniania, sprawdź, czy hasło uwierzytelniania w chmurze jest włączony dla nazwy głównej usługi programu PowerShell. Jeśli nie, musisz ustawić [zasad Home obszaru odnajdywania (odnajdowanie obszaru macierzystego)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) dla dzierżawy usługi Azure AD. Aby sprawdzić i ustawianie zasad HRD:

1. Zainstaluj wersję zapoznawczą [modułu Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

   ```powershell
   Install-Module AzureADPreview
   ```

2. Wprowadź `Connect-AzureAD` przy użyciu poświadczeń administratora globalnego (administratora dzierżawy).

3. Sprawdź, jeśli została już utworzona nazwa główna usługi Microsoft Azure PowerShell.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Jeśli nie istnieje, następnie należy utworzyć jednostkę usługi.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Utwórz i Dołącz zasady do tej jednostki usługi.

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId
    
    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie klastrów HDInsight przy użyciu ESP](apache-domain-joined-configure-using-azure-adds.md)
* [Konfigurowanie zasad usługi Apache Hive dla klastrów HDInsight przy użyciu ESP](apache-domain-joined-run-hive.md)
* [Zarządzanie klastrami HDInsight przy użyciu ESP](apache-domain-joined-manage.md) 
