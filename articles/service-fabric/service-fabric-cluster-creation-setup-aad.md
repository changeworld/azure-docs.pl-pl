---
title: Konfigurowanie Azure Active Directory do Service Fabric uwierzytelniania klientów | Microsoft Docs
description: Dowiedz się, jak skonfigurować Azure Active Directory (Azure AD) do uwierzytelniania klientów Service Fabric klastrów.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2019
ms.author: atsenthi
ms.openlocfilehash: 6c195357c4a037534307571a53589b2ae861d88b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "67486019"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Konfigurowanie Azure Active Directory na potrzeby uwierzytelniania klientów

W przypadku klastrów działających na platformie Azure zaleca się zabezpieczenie dostępu do punktów końcowych zarządzania przez Azure Active Directory (Azure AD).  W tym artykule opisano sposób konfigurowania usługi Azure AD w celu uwierzytelniania klientów w klastrze Service Fabric, co należy wykonać przed [utworzeniem klastra](service-fabric-cluster-creation-via-arm.md).  Usługa Azure AD umożliwia organizacjom (znanym jako dzierżawy) zarządzanie dostępem użytkowników do aplikacji. Aplikacje są podzielone na te z interfejsem użytkownika logowania opartego na sieci Web i z natywną obsługą klienta. 

Klaster usługi Service Fabric udostępnia kilka punktów wejścia dla swoich funkcji zarządzania, w tym internetowe narzędzie [Service Fabric Explorer][service-fabric-visualizing-your-cluster] i [program Visual Studio][service-fabric-manage-application-in-visual-studio]. W związku z tym utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra: jedną aplikację internetową i jedną aplikację natywną.  Po utworzeniu aplikacji przypiszesz użytkowników do ról tylko do odczytu i administratora.

> [!NOTE]
> Przed utworzeniem klastra musisz wykonać następujące kroki. Ponieważ skrypty oczekują określenia nazw klastra i punktów końcowych, wartości powinny być zaplanowane i inne od wartości już utworzonych.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że dzierżawa została już utworzona. Jeśli tak nie jest, zacznij od zapoznania się z artykułem [Jak uzyskać dzierżawę usługi Azure Active Directory][active-directory-howto-tenant].

Aby uprościć niektóre kroki konfigurowania usługi Azure AD za pomocą klastra usługi Service Fabric, utworzyliśmy zestaw skryptów programu Windows PowerShell.

1. [Sklonuj repozytorium](https://github.com/Azure-Samples/service-fabric-aad-helpers) na komputerze.
2. [Upewnij się, że są spełnione wszystkie wymagania wstępne](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) dotyczące zainstalowanych skryptów.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Tworzenie aplikacji usługi Azure AD i przypisywanie ról do użytkowników

Użyjemy skryptów, aby utworzyć dwie aplikacje usługi Azure AD w celu kontrolowania dostępu do klastra: jedną aplikację sieci Web i jedną aplikację natywną. Po utworzeniu aplikacji do reprezentowania klastra utworzysz użytkowników dla [ról obsługiwanych przez Service Fabric](service-fabric-cluster-security-roles.md): tylko do odczytu i administrator.

Uruchom skrypt `SetupApplications.ps1` i podaj jako parametry identyfikator dzierżawy, nazwę klastra i adres URL odpowiedzi aplikacji internetowej.  Podaj także nazwy użytkowników i ich hasła. Na przykład:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> W przypadku chmur krajowych (na przykład Azure Government, Azure — Chiny, Azure — Niemcy) należy także określić parametr `-Location`.

Możesz znaleźć *TenantId* przez wykonanie polecenia `Get-AzureSubscription`programu PowerShell. Wykonanie tego polecenia wyświetla TenantId dla każdej subskrypcji.

Wartość *ClusterName* służy jako prefiks aplikacji usługi Azure AD tworzonych przez skrypt. Nie musi ona dokładnie pasować do rzeczywistej nazwy klastra. Ma na celu tylko ułatwienie mapowania artefaktów usługi Azure AD do klastra usługi Service Fabric, w którym są używane.

Wartość *WebApplicationReplyUrl* to domyślny punkt końcowy zwracany przez usługę Azure AD do użytkowników, gdy zakończą logowanie. Ustaw ten punkt końcowy jako punkt końcowy narzędzia Service Fabric Explorer dla klastra. Domyślnie to:

https://&lt;domena_klastra&gt;:19080/Explorer

Zostanie wyświetlony monit logowania na konto z uprawnieniami administratora dzierżawy usługi Azure AD. Po zalogowaniu skrypt utworzy aplikacje internetową i natywną mające reprezentować klaster usługi Service Fabric. Jeśli popatrzysz na aplikacje dzierżawy w witrynie [Azure Portal][azure-portal], powinny być widoczne dwie nowe pozycje:

   * *NazwaKlastra*\_Cluster
   * *NazwaKlastra*\_Client

Skrypt drukuje kod JSON wymagany przez szablon Azure Resource Manager podczas [tworzenia klastra](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access). dobrym pomysłem jest pozostawienie otwartego okna programu PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Pomoc dotycząca rozwiązywania problemów podczas konfigurowania Azure Active Directory
Konfigurowanie usługi Azure AD i korzystanie z niej może być trudne, dlatego Oto kilka wskaźników na temat tego, co można zrobić, aby debugować problem.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer prosi o wybranie certyfikatu
#### <a name="problem"></a>Problem
Po pomyślnym zalogowaniu się do usługi Azure AD w Service Fabric Explorer przeglądarka powróci do strony głównej, ale zostanie wyświetlony monit o wybranie certyfikatu.

![Okno dialogowe certyfikatu SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Reason
Użytkownik nie ma przypisanej roli w aplikacji klastra usługi Azure AD. W rezultacie uwierzytelnianie usługi Azure AD kończy się niepowodzeniem w klastrze Service Fabric. Service Fabric Explorer powracać do uwierzytelniania certyfikatów.

#### <a name="solution"></a>Rozwiązanie
Postępuj zgodnie z instrukcjami dotyczącymi konfigurowania usługi Azure AD i przypisz role użytkowników. Ponadto zalecamy włączenie pozycji "przypisanie użytkownika wymagane w celu uzyskania dostępu do aplikacji" zgodnie z oczekiwaniami `SetupApplications.ps1` .

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Połączenie z programem PowerShell kończy się niepowodzeniem z powodu błędu: "Określone poświadczenia są nieprawidłowe"
#### <a name="problem"></a>Problem
W przypadku używania programu PowerShell do nawiązywania połączenia z klastrem przy użyciu trybu zabezpieczeń "usługi azureactivedirectory" po pomyślnym zalogowaniu się do usługi Azure AD połączenie kończy się niepowodzeniem z powodu błędu: "Określone poświadczenia są nieprawidłowe."

#### <a name="solution"></a>Rozwiązanie
To rozwiązanie jest takie samo jak powyżej.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer zwraca błąd podczas logowania: "AADSTS50011"
#### <a name="problem"></a>Problem
Podczas próby zalogowania się do usługi Azure AD w Service Fabric Explorer strona zwraca błąd: "AADSTS50011: &lt;Adres URL&gt; adresu odpowiedzi nie jest zgodny z adresami odpowiedzi skonfigurowanymi dla aplikacji &lt;:&gt;GUID.

![Adres odpowiedzi SFX jest niezgodny][sfx-reply-address-not-match]

#### <a name="reason"></a>Reason
Aplikacja klastra (sieci Web) reprezentująca Service Fabric Explorer próbuje uwierzytelnić się w usłudze Azure AD, a w ramach żądania zawiera adres URL zwrotu przekierowania. Ale adres URL nie jest wyświetlany na liście **adresów URL odpowiedzi** aplikacji usługi Azure AD.

#### <a name="solution"></a>Rozwiązanie
Wybierz pozycję "Rejestracje aplikacji" na stronie usługi AAD, wybierz aplikację klastra, a następnie wybierz przycisk **adresy URL odpowiedzi** . Na stronie "adresy URL odpowiedzi" Dodaj adres URL Service Fabric Explorer do listy lub Zastąp jeden z elementów na liście. Po zakończeniu Zapisz zmiany.

![Adres URL odpowiedzi aplikacji sieci Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Łączenie klastra przy użyciu uwierzytelniania usługi Azure AD za pomocą programu PowerShell
Aby połączyć klaster Service Fabric, użyj następującego przykładu polecenia programu PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Aby dowiedzieć się więcej, zobacz [polecenie cmdlet Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Czy mogę ponownie użyć tej samej dzierżawy usługi Azure AD w wielu klastrach?
Tak. Pamiętaj jednak, aby dodać adres URL Service Fabric Explorer do aplikacji klastra (sieci Web). W przeciwnym razie Service Fabric Explorer nie działa.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Dlaczego nadal potrzebuję certyfikatu serwera, gdy usługa Azure AD jest włączona?
FabricClient i FabricGateway wykonują wzajemne uwierzytelnianie. Podczas uwierzytelniania za pomocą usługi Azure AD integracja z usługą Azure AD zapewnia tożsamość klienta na serwerze, a certyfikat serwera służy do weryfikowania tożsamości serwera. Aby uzyskać więcej informacji na temat Service Fabric certyfikatów, zobacz [X. 509 certyfikaty i Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu Azure Active Directory aplikacji i ustawianiu ról dla użytkowników [Skonfiguruj i Wdróż klaster](service-fabric-cluster-creation-via-arm.md).


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
