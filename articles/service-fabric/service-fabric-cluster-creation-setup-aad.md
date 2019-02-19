---
title: Konfigurowanie usługi Azure Active Directory do uwierzytelniania klientów usługi Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory (Azure AD) do uwierzytelniania klientów w przypadku klastrów usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/15/2019
ms.author: aljo
ms.openlocfilehash: 15561969e27512c4882eccc10f75aa932bcf23df
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338992"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Konfigurowanie usługi Azure Active Directory do uwierzytelniania klientów

W przypadku klastrów działających na platformie Azure Azure Active Directory (Azure AD) zaleca się zabezpieczenie dostępu do punktów końcowych zarządzania.  W tym artykule opisano sposób konfiguracji usługi Azure AD do uwierzytelniania klientów dla klastra usługi Service Fabric, które należy wykonać przed [tworzenia klastra](service-fabric-cluster-creation-via-arm.md).  Usługi Azure AD umożliwia zarządzanie dostępem użytkowników do aplikacji organizacji (znanych jako dzierżaw). Aplikacje są podzielone na tych z opartą na sieci web, interfejs użytkownika logowania i te w środowisku klienta natywnego. 

Klaster usługi Service Fabric udostępnia kilka punktów wejścia do jego funkcje zarządzania, w tym, oparta na sieci web [narzędzia Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] i [programu Visual Studio] [ service-fabric-manage-application-in-visual-studio]. W rezultacie, utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra: jednej aplikacji sieci web i po jednej aplikacji natywnej.  Po utworzeniu aplikacji, przypisywanie użytkowników do tylko do odczytu i ról administratora.

> [!NOTE]
> Przed utworzeniem klastra, wykonaj następujące kroki. Ponieważ skrypty oczekuje nazwy klastra i punktów końcowych, wartości powinny być planowane i nie wartości, że masz już utworzoną.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że utworzono już dzierżawę. Jeśli nie masz, zapoznanie się z tematem [jak uzyskać dzierżawę usługi Azure Active Directory][active-directory-howto-tenant].

Aby uprościć niektóre etapy konfigurowania usługi Azure AD z klastrem usługi Service Fabric, utworzyliśmy zestaw skryptów programu Windows PowerShell.

1. [Pobierz skrypty](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) do komputera.
2. Kliknij prawym przyciskiem myszy plik zip, wybierz **właściwości**, wybierz opcję **odblokowanie** pole wyboru, a następnie kliknij przycisk **Zastosuj**.
3. Wyodrębnij plik ZIP.

## <a name="create-azure-ad-applications-and-asssign-users-to-roles"></a>Tworzenie aplikacji usługi Azure AD i asssign użytkowników do ról
Utworzone dwie aplikacje usługi Azure AD w celu kontrolowania dostępu do klastra: jednej aplikacji sieci web i po jednej aplikacji natywnej. Po utworzeniu aplikacji ma reprezentować klaster, należy przypisać użytkownikom [role obsługiwane przez usługę Service Fabric](service-fabric-cluster-security-roles.md): tylko do odczytu i administratora.

Uruchom `SetupApplications.ps1`i podaj identyfikator, nazwę klastra i adres URL odpowiedzi aplikacji sieci web dzierżawy jako parametry.  Również określić nazwy użytkowników i hasła dla użytkowników.  Na przykład:

```PowerShell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Chmur krajowych (na przykład Azure dla instytucji rządowych, chińska wersja platformy Azure, Azure [Niemcy]), należy także określić `-Location` parametru.

Możesz znaleźć swojej *TenantId* , wykonując polecenie programu PowerShell `Get-AzureSubscription`. Wykonywanie to polecenie wyświetla identyfikator TenantId dla każdej subskrypcji.

*ClusterName* służy jako prefiks aplikacji usługi Azure AD, które są tworzone przez skrypt. Nie musi dokładnie odpowiadać rzeczywista nazwa klastra. Jest ona przeznaczona tylko po to, aby ułatwić mapowania klastra usługi Service Fabric, który jest używany z artefaktów w usłudze Azure AD.

*WebApplicationReplyUrl* jest domyślny punkt końcowy zwracające usługi Azure AD dla użytkowników po ich zakończeniu logowania. Ustaw ten punkt końcowy jako punkt końcowy narzędzia Service Fabric Explorer dla klastra, która domyślnie jest:

https://&lt;cluster_domain&gt;:19080/Explorer

Monit logować się do konta z uprawnieniami administratora dla dzierżawy usługi Azure AD. Po zalogowaniu, skrypt tworzy sieci web i aplikacji natywnych, aby reprezentować klaster usługi Service Fabric. Jeśli spojrzysz na aplikacje dzierżawcy w [witryny Azure portal][azure-portal], powinny zostać wyświetlone dwa nowe wpisy:

   * *ClusterName*\_klastra
   * *ClusterName*\_klienta

Skrypt wyświetla plik JSON wymagany przez szablon usługi Azure Resource Manager po użytkownik [utworzenie klastra było możliwe](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), więc to dobry pomysł, aby nie zamykaj okna programu PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Rozwiązywanie problemów z pomocy w konfigurowaniu usługi Azure Active Directory
Konfigurowanie usługi Azure AD i korzystania z niego może stanowić wyzwanie, poniżej przedstawiono niektóre wskaźników, co można zrobić, aby debugować ten problem.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Narzędzie Service Fabric Explorer wyświetla monit o wybranie certyfikatu
#### <a name="problem"></a>Problem
Po zalogowaniu pomyślnie z usługą Azure AD w narzędziu Service Fabric Explorer, przeglądarka powraca do strony głównej, ale komunikat wyświetla monit o wybranie certyfikatu.

![Okno dialogowe certyfikat SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Przyczyna
Użytkownik nie ma przypisaną rolę w aplikacji do klastra usługi Azure AD. W związku z tym uwierzytelnianie usługi Azure AD nie powiedzie się w klastrze usługi Service Fabric. Narzędzie Service Fabric Explorer powraca do uwierzytelniania certyfikatu.

#### <a name="solution"></a>Rozwiązanie
Wykonaj instrukcje dotyczące konfigurowania usługi Azure AD i przypisz role użytkownika. Ponadto zaleca się włączenie funkcji "Wymagany dostęp do aplikacji, przypisywanie użytkownika" jako `SetupApplications.ps1` jest.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Połączenie przy użyciu programu PowerShell zakończy się niepowodzeniem z powodu błędu: "Określone poświadczenia są nieprawidłowe"
#### <a name="problem"></a>Problem
Jeśli używasz programu PowerShell do łączenia z klastrem przy użyciu trybu zabezpieczeń "Usługi AzureActiveDirectory", po zalogowaniu pomyślnie z usługą Azure AD, połączenie kończy się niepowodzeniem z powodu błędu: "Określone poświadczenia są nieprawidłowe."

#### <a name="solution"></a>Rozwiązanie
To rozwiązanie jest taka sama jak poprzedni.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Narzędzia Service Fabric Explorer zwraca błąd, po zalogowaniu: "AADSTS50011"
#### <a name="problem"></a>Problem
Gdy spróbujesz się zalogować się do usługi Azure AD w narzędziu Service Fabric Explorer, strony zwraca błąd: "AADSTS50011: Adres zwrotny &lt;adresu url&gt; jest niezgodna z adresy zwrotne skonfigurowane dla aplikacji: &lt;guid&gt;. "

![Adres zwrotny SFX nie jest zgodny.][sfx-reply-address-not-match]

#### <a name="reason"></a>Przyczyna
Aplikacji klastra (sieć web), który reprezentuje narzędzia Service Fabric Explorer podejmie próbę uwierzytelniania w usłudze Azure AD i jako część żądania zapewnia zwrotny adres URL przekierowania. Adres URL nie znajduje się w aplikacji usługi Azure AD, ale **adres URL odpowiedzi** listy.

#### <a name="solution"></a>Rozwiązanie
Wybierz pozycję "Rejestracje aplikacji" na stronie usługi AAD, wybierz swoją aplikację w klastrze, a następnie wybierz **adresy URL odpowiedzi** przycisku. Na stronie "Adresów URL odpowiedzi" Dodaj adres URL z Eksploratora usługi Service Fabric do listy lub Zastąp jeden z elementów na liście. Po zakończeniu zapisz zmiany.

![Adres url odpowiedzi aplikacji sieci Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Połącz klaster przy użyciu uwierzytelniania usługi Azure AD za pomocą programu PowerShell
Aby połączyć z klastra usługi Service Fabric, skorzystaj z poniższego przykładu polecenie programu PowerShell:

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Aby dowiedzieć się więcej, zobacz [polecenia cmdlet Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Czy mogę ponownie użyć tej samej dzierżawie usługi Azure AD w wielu klastrach?
Tak. Należy jednak pamiętać dodać adres URL z Eksploratora usługi Service Fabric do aplikacji klastra (sieć web). W przeciwnym razie Eksploratora usługi Service Fabric nie działa.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Dlaczego nadal należy certyfikat serwera po włączeniu usługi Azure AD?
FabricClient i FabricGateway przeprowadza uwierzytelnianie wzajemne. Podczas uwierzytelniania usługi Azure AD integracji z usługą Azure AD zapewnia usługi tożsamości klienta, na serwerze, a certyfikat serwera jest używana do zweryfikowania tożsamości serwera. Aby uzyskać więcej informacji o certyfikatach usługi Service Fabric, zobacz [certyfikaty X.509 i Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Kolejne kroki
Po skonfigurowaniu usługi Azure Active Directory, aplikacje i ustawienia ról dla użytkowników, [skonfigurować i wdrożyć klaster](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
