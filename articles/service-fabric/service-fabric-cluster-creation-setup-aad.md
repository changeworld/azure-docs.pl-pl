---
title: Konfigurowanie usługi Azure Active Directory do uwierzytelniania klienta
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory (Azure AD) do uwierzytelniania klientów dla klastrów sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: 28c4c65cfcc77607dfe9a463a09ecd10389a6eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193388"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Konfigurowanie usługi Azure Active Directory do uwierzytelniania klienta

W przypadku klastrów uruchomionych na platformie Azure usługa Azure Active Directory (Azure AD) jest zalecana do bezpiecznego dostępu do punktów końcowych zarządzania. W tym artykule opisano sposób konfigurowania usługi Azure AD w celu uwierzytelnienia klientów dla klastra sieci szkieletowej usług.

W tym artykule termin "aplikacja" będzie używany do odwoływania się do [aplikacji usługi Azure Active Directory,](../active-directory/develop/developer-glossary.md#client-application)a nie aplikacji sieci szkieletowej usług; rozróżnienie zostanie dokonane w razie potrzeby. Usługa Azure AD umożliwia organizacjom (znanym jako dzierżawy) zarządzanie dostępem użytkowników do aplikacji.

Klaster usługi Service Fabric udostępnia kilka punktów wejścia dla swoich funkcji zarządzania, w tym internetowe narzędzie [Service Fabric Explorer][service-fabric-visualizing-your-cluster] i [program Visual Studio][service-fabric-manage-application-in-visual-studio]. W rezultacie utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra: jedną aplikację sieci web i jedną aplikację macierzystą. Po utworzeniu aplikacji można przypisać użytkowników do ról tylko do odczytu i administratora.

> [!NOTE]
> W systemie Linux należy wykonać następujące kroki przed utworzeniem klastra. W systemie Windows istnieje również możliwość [skonfigurowania uwierzytelniania usługi Azure AD dla istniejącego klastra](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

> [!NOTE]
> Jest [to znany problem,](https://github.com/microsoft/service-fabric/issues/399) że aplikacje i węzły w klastrach obsługujących AAD systemu Linux nie można wyświetlić w usłudze Azure Portal.



## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że dzierżawa została już utworzona. Jeśli tak nie jest, zacznij od zapoznania się z artykułem [Jak uzyskać dzierżawę usługi Azure Active Directory][active-directory-howto-tenant].

Aby uprościć niektóre kroki konfigurowania usługi Azure AD za pomocą klastra usługi Service Fabric, utworzyliśmy zestaw skryptów programu Windows PowerShell.

1. [Sklonuj repozytorium](https://github.com/Azure-Samples/service-fabric-aad-helpers) do komputera.
2. [Upewnij się, że masz wszystkie wymagania wstępne](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) dla zainstalowanych skryptów.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Tworzenie aplikacji usługi Azure AD i przypisywanie ról do użytkowników

Skrypty będą używane do tworzenia dwóch aplikacji usługi Azure AD do kontrolowania dostępu do klastra: jednej aplikacji sieci web i jednej aplikacji macierzystej. Po utworzeniu aplikacji do reprezentowania klastra, można utworzyć użytkowników dla [ról obsługiwanych przez sieć szkieletową usług:](service-fabric-cluster-security-roles.md)tylko do odczytu i administratora.

Uruchom skrypt `SetupApplications.ps1` i podaj jako parametry identyfikator dzierżawy, nazwę klastra i adres URL odpowiedzi aplikacji internetowej.  Podaj także nazwy użytkowników i ich hasła. Przykład:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> W przypadku chmur krajowych (na przykład Azure Government, Azure — Chiny, Azure — Niemcy) należy także określić parametr `-Location`.

Identyfikatora *dzierżawy* można znaleźć, wykonując polecenie `Get-AzureSubscription`programu PowerShell. Wykonanie tego polecenia wyświetla identyfikator dzierżawcy dla każdej subskrypcji.

Wartość *ClusterName* służy jako prefiks aplikacji usługi Azure AD tworzonych przez skrypt. Nie musi ona dokładnie pasować do rzeczywistej nazwy klastra. Ma na celu tylko ułatwienie mapowania artefaktów usługi Azure AD do klastra usługi Service Fabric, w którym są używane.

Wartość *WebApplicationReplyUrl* to domyślny punkt końcowy zwracany przez usługę Azure AD do użytkowników, gdy zakończą logowanie. Ustaw ten punkt końcowy jako punkt końcowy Eksploratora sieci szkieletowej usług dla klastra. Jeśli tworzysz aplikacje usługi Azure AD do reprezentowania istniejącego klastra, upewnij się, że ten adres URL jest zgodny z punktem końcowym istniejącego klastra. Jeśli tworzysz aplikacje dla nowego klastra, zaplanuj punkt końcowy, który będzie miał klaster, i upewnij się, że nie używa punktu końcowego istniejącego klastra. Domyślnie punktem końcowym Eksploratora sieci szkieletowej usług jest:

https://&lt;domena_klastra&gt;:19080/Explorer

Zostanie wyświetlony monit logowania na konto z uprawnieniami administratora dzierżawy usługi Azure AD. Po zalogowaniu skrypt utworzy aplikacje internetową i natywną mające reprezentować klaster usługi Service Fabric. Jeśli popatrzysz na aplikacje dzierżawy w witrynie [Azure Portal][azure-portal], powinny być widoczne dwie nowe pozycje:

   * *Klaster nazwy klastra*\_
   * *Klient clustername*\_

Skrypt drukuje JSON wymagane przez szablon usługi Azure Resource Manager podczas [tworzenia klastra obsługującego usługę AAD](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), więc warto zachować otwarte okno programu PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Troubleshooting help in setting up Azure Active Directory (Rozwiązywanie problemów z pomocą podczas konfigurowania usługi Azure Active Directory)
Konfigurowanie usługi Azure AD i korzystanie z niej może być trudne, więc oto kilka wskazówek na temat tego, co można zrobić, aby debugować problem.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Eksplorator sieci szkieletowej usług monituje o wybranie certyfikatu
#### <a name="problem"></a>Problem
Po pomyślnym zalogowaniu się do usługi Azure AD w Eksploratorze sieci szkieletowej usług przeglądarka powraca do strony głównej, ale zostanie wyświetlony komunikat z monitem o wybranie certyfikatu.

![Okno dialogowe certyfikatu SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Przyczyna
Użytkownik nie jest przypisany rolę w aplikacji klastra usługi Azure AD. W związku z tym uwierzytelnianie usługi Azure AD kończy się niepowodzeniem w klastrze sieci szkieletowej usług. Eksplorator sieci szkieletowej usług powraca do uwierzytelniania certyfikatów.

#### <a name="solution"></a>Rozwiązanie
Postępuj zgodnie z instrukcjami konfigurowania usługi Azure AD i przypisz role użytkowników. Ponadto zaleca się włączenie "Przypisanie użytkownika wymagane do `SetupApplications.ps1` uzyskania dostępu do aplikacji", podobnie jak w tym celu.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Połączenie z programem PowerShell kończy się niepowodzeniem z powodu błędu: "Określone poświadczenia są nieprawidłowe"
#### <a name="problem"></a>Problem
Korzystając z programu PowerShell do łączenia się z klastrem przy użyciu trybu zabezpieczeń "AzureActiveDirectory", po pomyślnym zalogowaniu się do usługi Azure AD połączenie zakończy się niepowodzeniem z powodu błędu: "Określone poświadczenia są nieprawidłowe".

#### <a name="solution"></a>Rozwiązanie
To rozwiązanie jest takie samo jak poprzednie.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Eksplorator sieci szkieletowej usług zwraca błąd podczas logowania: "AADSTS50011"
#### <a name="problem"></a>Problem
Podczas próby zalogowania się do usługi Azure AD w Eksploratorze sieci szkieletowej usług strona zwraca &lt;błąd: "AADSTS50011: Adres URL&gt; adresu odpowiedzi nie jest zgodny z adresami odpowiedzi skonfigurowanym dla aplikacji: &lt;guid&gt;."

![Adres odpowiedzi SFX nie jest zgodny][sfx-reply-address-not-match]

#### <a name="reason"></a>Przyczyna
Aplikacja klastra (sieci web), która reprezentuje Eksploratora sieci szkieletowej usług próbuje uwierzytelnić się w usłudze Azure AD i jako część żądania udostępnia adres URL zwrotny przekierowania. Ale adres URL nie jest wymieniony na liście **adresów URL reply** aplikacji usługi Azure AD.

#### <a name="solution"></a>Rozwiązanie
Na stronie rejestracji aplikacji usługi Azure AD dla klastra wybierz pozycję **Uwierzytelnianie**i w sekcji **Przekierowanie identyfikatorów URI** dodaj adres URL Eksploratora sieci szkieletowej usług do listy. Zapisz swoją zmianę.

![Adres URL odpowiedzi aplikacji sieci Web][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>Łączenie się z klastrem przy użyciu uwierzytelniania usługi Azure AD za pośrednictwem programu PowerShell powoduje błąd podczas logowania: "AADSTS50011"
#### <a name="problem"></a>Problem
Podczas próby nawiązania połączenia z klastrem sieci szkieletowej usług przy użyciu usługi Azure AD za pośrednictwem programu PowerShell strona logowania zwraca błąd: "AADSTS50011: Adres URL odpowiedzi określony w żądaniu nie jest zgodny z adresami URL odpowiedzi skonfigurowanym dla aplikacji: &lt;guid&gt;."

#### <a name="reason"></a>Przyczyna
Podobnie jak w poprzednim problemie program PowerShell próbuje uwierzytelnić się w usłudze Azure AD, która udostępnia adres URL przekierowania, który nie jest wymieniony na liście **adresów URL odpowiedzi** aplikacji usługi Azure AD.  

#### <a name="solution"></a>Rozwiązanie
Użyj tego samego procesu, co w poprzednim problemie, `urn:ietf:wg:oauth:2.0:oob`ale adres URL musi być ustawiony na specjalne przekierowanie do uwierzytelniania wiersza polecenia.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Łączenie klastra przy użyciu uwierzytelniania usługi Azure AD za pośrednictwem programu PowerShell
Aby połączyć klaster sieci szkieletowej usług, użyj następującego przykładu polecenia programu PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Aby dowiedzieć się więcej, zobacz [polecenie cmdlet Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Czy można ponownie użyć tej samej dzierżawy usługi Azure AD w wielu klastrach?
Tak. Należy jednak pamiętać o dodaniu adresu URL Eksploratora sieci szkieletowej usług do aplikacji klastra (sieci web). W przeciwnym razie Eksplorator sieci szkieletowej usług nie działa.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Dlaczego nadal potrzebuję certyfikatu serwera, gdy usługa Azure AD jest włączona?
FabricClient i FabricGateway wykonać wzajemne uwierzytelnianie. Podczas uwierzytelniania usługi Azure AD integracja usługi Azure AD zapewnia tożsamość klienta do serwera, a certyfikat serwera jest używany przez klienta do weryfikacji tożsamości serwera. Aby uzyskać więcej informacji na temat certyfikatów sieci szkieletowej usług, zobacz [X.509 certyfikatów i sieci szkieletowej usług][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu aplikacji usługi Azure Active Directory i ustawieniu ról dla użytkowników [należy skonfigurować i wdrożyć klaster](service-fabric-cluster-creation-via-arm.md).


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
