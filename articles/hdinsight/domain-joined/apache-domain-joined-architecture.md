---
title: Architektura usługi Azure HDInsight z pakietem zabezpieczeń dla przedsiębiorstw
description: Dowiedz się, jak zaplanować zabezpieczenia usługi Azure HDInsight za pomocą pakietu zabezpieczeń przedsiębiorstwa.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 452a3b04637126b40aca907178bebd6f74ec4481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365791"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Korzystanie z pakietu Enterprise Security w usłudze HDInsight

Standardowy klaster usługi Azure HDInsight jest klastrem dla jednego użytkownika. Jest odpowiedni dla większości firm, które mają mniejsze zespoły aplikacji, które budują duże obciążenia danych. Każdy użytkownik może utworzyć dedykowany klaster na żądanie i zniszczyć go, gdy nie jest już potrzebne.

Wiele przedsiębiorstw przesunęło się w kierunku modelu, w którym zespoły IT zarządzają klastrami, a wiele zespołów aplikacji udostępnia klastry. Te większe przedsiębiorstwa potrzebują dostępu dla wielu administratorów do każdego klastra w usłudze Azure HDInsight.

Usługa HDInsight opiera się na popularnym dostawcy tożsamości — usłudze Active Directory — w sposób zarządzany. Integrując usługę HDInsight z [usługami domenowymi Active Directory platformy Azure (Usługi Azure AD DS),](../../active-directory-domain-services/overview.md)można uzyskać dostęp do klastrów przy użyciu poświadczeń domeny.

Maszyny wirtualne (VM) w umiań HD są domeną przyłączone do dostarczonej domeny. Tak więc wszystkie usługi uruchomione na serwerze HDInsight (Apache Ambari, Serwer Apache Hive, Apache Ranger, Serwer oszczędności Apache Spark i inne) działają bezproblemowo dla uwierzytelnionego użytkownika. Administratorzy mogą następnie tworzyć silne zasady autoryzacji przy użyciu Apache Ranger, aby zapewnić kontrolę dostępu opartą na rolach dla zasobów w klastrze.

## <a name="integrate-hdinsight-with-active-directory"></a>Integracja usługi HDInsight z usługą Active Directory

Open-source Apache Hadoop opiera się na protokole Kerberos uwierzytelniania i bezpieczeństwa. W związku z tym węzły klastra USŁUGI HDInsight z pakietem zabezpieczeń przedsiębiorstwa (ESP) są przyłączane do domeny zarządzanej przez usługi Azure AD DS. Zabezpieczenia protokołu Kerberos są skonfigurowane dla składników Hadoop w klastrze.

Następujące rzeczy są tworzone automatycznie:

- Podmiot usługi dla każdego składnika Hadoop
- Podmiot obrabiany dla każdej maszyny, która jest przyłączona do domeny
- Jednostka organizacyjna (OU) dla każdego klastra do przechowywania tych usług i podmiotów

Podsumowując, należy skonfigurować środowisko z:

- Domena usługi Active Directory (zarządzana przez usługi Azure AD DS). **Nazwa domeny musi mieć 39 znaków lub mniej do pracy z usługą Azure HDInsight.**
- Bezpieczna usługa LDAP (LDAPS) włączona w usługach Azure AD DS.
- Właściwa łączność sieciowa z sieci wirtualnej HDInsight do sieci wirtualnej usługi Azure AD DS, jeśli wybierzesz dla nich oddzielne sieci wirtualne. Maszyna wirtualna wewnątrz sieci wirtualnej HDInsight powinna mieć linię wzroku do usług Azure AD DS za pośrednictwem komunikacji równorzędnej sieci wirtualnej. Jeśli usługi HDInsight i usługi Azure AD DS są wdrażane w tej samej sieci wirtualnej, łączność jest automatycznie udostępniana i nie są potrzebne żadne dalsze działania.

## <a name="set-up-different-domain-controllers"></a>Konfigurowanie różnych kontrolerów domeny

Usługa HDInsight obsługuje obecnie tylko usługi Azure AD DS jako główny kontroler domeny używany przez klaster do komunikacji protokołu Kerberos. Ale inne złożone konfiguracje usługi Active Directory są możliwe, o ile taka konfiguracja prowadzi do włączania usług Azure AD DS dla dostępu HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Usługi Azure AD DS](../../active-directory-domain-services/overview.md) udostępnia domenę zarządzaną, która jest w pełni zgodna z usługą Active Directory systemu Windows Server. Firma Microsoft zajmuje się zarządzaniem domeną, łataniem i monitorowaniem jej w konfiguracji o wysokiej dostępności. Klaster można wdrażać bez obawy o utrzymywanie kontrolerów domeny.

Użytkownicy, grupy i hasła są synchronizowane z usługi Azure AD. Synchronizacja jednokierunkowa z wystąpienia usługi Azure AD do usługi Azure AD DS umożliwia użytkownikom zalogowanie się do klastra przy użyciu tych samych poświadczeń firmowych.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie klastrów usługi HDInsight przy użyciu usługi ESP przy użyciu usługi Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Lokalna usługa Active Directory lub usługa Active Directory na maszynach wirtualnych IaaS

Jeśli masz lokalne wystąpienie usługi Active Directory lub bardziej złożone konfiguracje usługi Active Directory dla domeny, możesz zsynchronizować te tożsamości z usługą Azure AD przy użyciu usługi Azure AD Connect. Następnie można włączyć usługi Azure AD DS w tej dzierżawie usługi Active Directory.

Ponieważ protokół Kerberos opiera się na skrótach haseł, należy [włączyć synchronizację skrótów haseł w usługach Azure AD DS.](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)

Jeśli używasz federacji z usługami federacjowymi Active Directory (AD FS), musisz włączyć synchronizację skrótów haseł. (Aby uzyskać zalecaną konfigurację, zobacz [ten film .)](https://youtu.be/qQruArbu2Ew) Synchronizacja skrótów haseł pomaga w odzyskiwaniu po awarii w przypadku awarii infrastruktury usług AD FS, a także pomaga zapewnić ochronę przed wyciekiem poświadczeń. Aby uzyskać więcej informacji, zobacz [Włączanie synchronizacji skrótów haseł z synchronizacją usługi Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

Korzystanie z lokalnej usługi Active Directory lub usługi Active Directory tylko na maszynach wirtualnych IaaS, bez usługi Azure AD i usługi Azure AD DS, nie jest obsługiwaną konfiguracją klastrów HDInsight z usługą ESP.

Jeśli federacja jest używana, a skróty haseł są synchronizowane poprawnie, ale występują błędy uwierzytelniania, sprawdź, czy uwierzytelnianie hasłem w chmurze jest włączone dla jednostki usługi Programu PowerShell. Jeśli nie, należy ustawić [zasady odnajdywania obszarów macierzystych (HRD)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) dla dzierżawy usługi Azure AD. Aby sprawdzić i ustawić zasady HRD:

1. Zainstaluj [moduł programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)w wersji zapoznawczej .

   ```powershell
   Install-Module AzureAD
   ```

2. Połącz się przy użyciu poświadczeń administratora globalnego (administratora dzierżawy).

   ```powershell
   Connect-AzureAD
   ```

3. Sprawdź, czy podmiot usługi Microsoft Azure PowerShell został już utworzony.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Jeśli nie istnieje, następnie utworzyć jednostkę usługi.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Utwórz i dołącz zasady do tej jednostki usługi.

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

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie klastrów usługi HDInsight za pomocą usługi ESP](apache-domain-joined-configure-using-azure-adds.md)
- [Konfigurowanie zasad gałęzi Apache dla klastrów usługi HDInsight za pomocą usługi ESP](apache-domain-joined-run-hive.md)
- [Zarządzanie klastrami HDInsight za pomocą esp](apache-domain-joined-manage.md)
