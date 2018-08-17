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
ms.date: 08/15/2018
ms.author: aljo
ms.openlocfilehash: aa470ca69b30733a9b41b03a8accadfa8f674ef0
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180934"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Konfigurowanie usługi Azure Active Directory do uwierzytelniania klientów

W przypadku klastrów działających na platformie Azure Azure Active Directory (Azure AD) zaleca się zabezpieczenie dostępu do punktów końcowych zarządzania.  W tym artykule opisano sposób konfiguracji usługi Azure AD do uwierzytelniania klientów dla klastra usługi Service Fabric, które należy wykonać przed [tworzenia klastra](service-fabric-cluster-creation-via-arm.md).  Usługi Azure AD umożliwia zarządzanie dostępem użytkowników do aplikacji organizacji (znanych jako dzierżaw). Aplikacje są podzielone na tych z opartą na sieci web, interfejs użytkownika logowania i te w środowisku klienta natywnego. W tym artykule przyjęto założenie, że utworzono już dzierżawę. Jeśli nie masz, zapoznanie się z tematem [jak uzyskać dzierżawę usługi Azure Active Directory][active-directory-howto-tenant].

## <a name="create-azure-ad-applications"></a>Tworzenie aplikacji usługi Azure AD
Klaster usługi Service Fabric udostępnia kilka punktów wejścia do jego funkcje zarządzania, w tym, oparta na sieci web [narzędzia Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] i [programu Visual Studio] [ service-fabric-manage-application-in-visual-studio]. W rezultacie, utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra: jednej aplikacji sieci web i po jednej aplikacji natywnej.  Po utworzeniu aplikacji, przypisywanie użytkowników do tylko do odczytu i ról administratora.

Aby uprościć niektóre etapy konfigurowania usługi Azure AD z klastrem usługi Service Fabric, utworzyliśmy zestaw skryptów programu Windows PowerShell.

> [!NOTE]
> Przed utworzeniem klastra, wykonaj następujące kroki. Ponieważ skrypty oczekuje nazwy klastra i punktów końcowych, wartości powinny być planowane i nie wartości, że masz już utworzoną.

1. [Pobierz skrypty] [ sf-aad-ps-script-download] do komputera.
2. Kliknij prawym przyciskiem myszy plik zip, wybierz **właściwości**, wybierz opcję **odblokowanie** pole wyboru, a następnie kliknij przycisk **Zastosuj**.
3. Wyodrębnij plik ZIP.
4. Uruchom `SetupApplications.ps1`i podaj identyfikator dzierżawy, ClusterName i WebApplicationReplyUrl jako parametry. Na przykład:

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
```

> [!NOTE]
> Chmur krajowych (Azure Government, Azure (Chiny) platformy Azure w Niemczech), należy także określić `-Location` parametru.

Wykonując polecenie programu PowerShell można znaleźć identyfikator TenantId `Get-AzureSubscription`. Wykonywanie to polecenie wyświetla identyfikator TenantId dla każdej subskrypcji.

ClusterName służy jako prefiks aplikacji usługi Azure AD, które są tworzone przez skrypt. Nie musi dokładnie odpowiadać rzeczywista nazwa klastra. Jest ona przeznaczona tylko po to, aby ułatwić mapowania klastra usługi Service Fabric, który jest używany z artefaktów w usłudze Azure AD.

WebApplicationReplyUrl jest domyślny punkt końcowy zwracające usługi Azure AD dla użytkowników po ich zakończeniu logowania. Ustaw ten punkt końcowy jako punkt końcowy narzędzia Service Fabric Explorer dla klastra, która domyślnie jest:

https://&lt;cluster_domain&gt;:19080/Explorer

Monit logować się do konta z uprawnieniami administratora dla dzierżawy usługi Azure AD. Po zalogowaniu, skrypt tworzy sieci web i aplikacji natywnych, aby reprezentować klaster usługi Service Fabric. Jeśli spojrzysz na aplikacje dzierżawcy w [witryny Azure portal][azure-portal], powinny zostać wyświetlone dwa nowe wpisy:

   * *ClusterName*\_klastra
   * *ClusterName*\_klienta

Skrypt wyświetla plik JSON wymagany przez szablon usługi Azure Resource Manager podczas tworzenia klastra w następnej sekcji, więc to dobry pomysł, aby nie zamykaj okna programu PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Przypisywanie użytkowników do ról
Po utworzeniu aplikacji ma reprezentować klaster przypisać użytkowników do ról obsługiwanych przez usługę Service Fabric: tylko do odczytu i administratora. Role można przypisać za pomocą [witryny Azure portal][azure-portal].

1. W witrynie Azure portal wybierz dzierżawy w prawym górnym rogu.

    ![Wybierz przycisk dzierżawy][select-tenant-button]
2. Wybierz **usługi Azure Active Directory** na karcie po lewej stronie, a następnie wybierz opcję "aplikacje dla przedsiębiorstw".
3. Wybierz pozycję "Wszystkie aplikacje", a następnie znajdź i wybierz aplikację sieci web, która ma nazwę takich jak `myTestCluster_Cluster`.
4. Kliknij przycisk **użytkowników i grup** kartę.

    ![Karcie Użytkownicy i grupy][users-and-groups-tab]
5. Kliknij przycisk **Dodaj użytkownika** znajdujący się na nowej stronie, wybierz użytkownika i rolę do przypisania, a następnie kliknij przycisk **wybierz** znajdujący się u dołu strony.

    ![Przypisywanie użytkowników do ról strony][assign-users-to-roles-page]
6. Kliknij przycisk **przypisać** znajdujący się u dołu strony.

    ![Dodaj Potwierdzenie przypisania][assign-users-to-roles-confirm]

> [!NOTE]
> Aby uzyskać więcej informacji o rolach w usłudze Service Fabric, zobacz [kontroli dostępu opartej na rolach dla klientów usługi Service Fabric](service-fabric-cluster-security-roles.md).
>
>

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

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Połączenie przy użyciu programu PowerShell zakończy się niepowodzeniem z powodu błędu: "określone poświadczenia są nieprawidłowe"
#### <a name="problem"></a>Problem
Jeśli używasz programu PowerShell do łączenia z klastrem przy użyciu trybu zabezpieczeń "Usługi AzureActiveDirectory", po zalogowaniu pomyślnie z usługą Azure AD, połączenie kończy się niepowodzeniem z powodu błędu: "określone poświadczenia są nieprawidłowe."

#### <a name="solution"></a>Rozwiązanie
To rozwiązanie jest taka sama jak poprzedni.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Narzędzie Service Fabric Explorer zwraca błąd, podczas logowania: "AADSTS50011"
#### <a name="problem"></a>Problem
Przy próbie Zaloguj się do usługi Azure AD w narzędziu Service Fabric Explorer strony zwraca błąd: "AADSTS50011: adres zwrotny &lt;adresu url&gt; jest niezgodna z adresy zwrotne skonfigurowane dla aplikacji: &lt;guid&gt;."

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
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png
