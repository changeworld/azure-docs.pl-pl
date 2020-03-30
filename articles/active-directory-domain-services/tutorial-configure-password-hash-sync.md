---
title: Włączanie synchronizacji skrótów haseł dla usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak włączyć synchronizację skrótów haseł przy użyciu usługi Azure AD Connect w domenie zarządzanej usług domenowych usługi active directory platformy Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 93e5ee9b46fb3387b70dd5092f72efcaa8a2bc19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239168"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Samouczek: Włączanie synchronizacji haseł w usługach domenowych Active Directory platformy Azure w środowiskach hybrydowych

W środowiskach hybrydowych dzierżawę usługi Azure Active Directory (Azure AD) można skonfigurować do synchronizacji z lokalnym środowiskiem usług domenowych Active Directory (AD DS) przy użyciu usługi Azure AD Connect. Domyślnie usługa Azure AD Connect nie synchronizuje starszych skrótów haseł usługi NT LAN Manager (NTLM) i protokołu Kerberos, które są potrzebne dla usług domenowych Active Directory platformy Azure (Usługi Azure AD DS).

Aby używać usług Azure AD DS z kontami zsynchronizowanymi z lokalnego środowiska usług AD DS, należy skonfigurować usługę Azure AD Connect w celu zsynchronizowania tych skrótów haseł wymaganych do uwierzytelniania NTLM i Kerberos. Po skonfigurowaniu usługi Azure AD Connect zdarzenie tworzenia konta lokalnego lub zmiany hasła również następnie synchronizuje starsze skróty haseł z usługą Azure AD.

Nie trzeba wykonywać tych kroków, jeśli używasz kont tylko w chmurze bez lokalnego środowiska usług AD DS.

W tym samouczku dowiesz się:

> [!div class="checklist"]
> * Dlaczego potrzebne są starsze skróty haseł NTLM i Kerberos
> * Jak skonfigurować synchronizację skrótów haseł starszych dla usługi Azure AD Connect

Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, która jest synchronizowana z katalogiem lokalnym przy użyciu usługi Azure AD Connect.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
    * W razie potrzeby [włącz usługę Azure AD Connect do synchronizacji skrótów haseł][enable-azure-ad-connect].
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby [utwórz i skonfiguruj wystąpienie usług domenowych Active Directory platformy Azure][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Synchronizacja skrótów haseł przy użyciu usługi Azure AD Connect

Usługa Azure AD Connect służy do synchronizowania obiektów, takich jak konta użytkowników i grupy z lokalnego środowiska usług AD DS w dzierżawę usługi Azure AD. W ramach tego procesu synchronizacja skrótów haseł umożliwia kontom używanie tego samego hasła w środowisku usług AD DS i usłudze Azure AD.

Aby uwierzytelnić użytkowników w domenie zarządzanej, usługi Azure AD DS potrzebuje skrótów haseł w formacie odpowiednim do uwierzytelniania NTLM i Kerberos. Usługa Azure AD nie przechowuje skrótów haseł w formacie wymaganym do uwierzytelniania NTLM lub Kerberos, dopóki nie włączysz usług Azure AD DS dla dzierżawy. Ze względów bezpieczeństwa usługa Azure AD również nie przechowuje żadnych poświadczeń hasła w postaci zwykłego tekstu. W związku z tym usługa Azure AD nie może automatycznie wygenerować tych skrótów haseł NTLM lub Kerberos na podstawie istniejących poświadczeń użytkowników.

Usługę Azure AD Connect można skonfigurować do synchronizowania wymaganych skrótów haseł NTLM lub Kerberos dla usług Azure AD DS. Upewnij się, że wykonaliśmy kroki [umożliwiające włączenie usługi Azure AD Connect do synchronizacji skrótów haseł][enable-azure-ad-connect]. Jeśli masz istniejące wystąpienie usługi Azure AD Connect, [pobierz i zaktualizuj do najnowszej wersji,][azure-ad-connect-download] aby upewnić się, że można zsynchronizować skróty haseł starszych dla NTLM i Kerberos. Ta funkcja nie jest dostępna we wczesnych wersjach usługi Azure AD Connect ani w starszym narzędziu DirSync. Usługa Azure AD Connect w wersji *1.1.614.0* lub nowszej jest wymagana.

> [!IMPORTANT]
> Usługa Azure AD Connect powinna być instalowana i konfigurowana tylko do synchronizacji z lokalnymi środowiskami usług AD DS. Nie jest obsługiwana instalacja usługi Azure AD Connect w domenie zarządzanej usług Azure AD DS w celu synchronizowania obiektów z powrotem do usługi Azure AD.

## <a name="enable-synchronization-of-password-hashes"></a>Włączanie synchronizacji skrótów haseł

Po zainstalowaniu i skonfigurowanym do synchronizacji usługi Azure AD, teraz skonfiguruj synchronizację skrótów haseł starszych haseł dla NTLM i Kerberos. Skrypt programu PowerShell służy do konfigurowania wymaganych ustawień, a następnie uruchamiania pełnej synchronizacji haseł z usługą Azure AD. Po zakończeniu tego procesu synchronizacji skrótów haseł usługi Azure AD Connect użytkownicy mogą logować się do aplikacji za pośrednictwem usług Azure AD DS, które używają starszych skrótów haseł NTLM lub Kerberos.

1. Na komputerze z zainstalowaną usługą Azure AD Connect z menu Start otwórz **usługę Synchronizacja > synchronizacji usługi Azure AD Connect**.
1. Wybierz kartę **Łączniki.** Zostaną wyświetlone informacje o połączeniu używane do ustanawiania synchronizacji między lokalnym środowiskiem usług AD DS a usługą Azure AD.

    **Typ** wskazuje usługę *Windows Azure Active Directory (Microsoft)* dla łącznika usługi Azure AD lub *usługi domenowe Active Directory* dla lokalnego łącznika usług AD DS. Zanotuj nazwy łączników do użycia w skrypcie programu PowerShell w następnym kroku.

    ![Wyświetlanie nazw łączników w Menedżerze usług synchronizacji](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    W tym przykładzie używane są następujące łączniki:

    * Łącznik usługi Azure AD nosi nazwę *aaddscontoso.onmicrosoft.com — AAD*
    * Lokalny łącznik usług AD DS nosi nazwę *onprem.contoso.com*

1. Skopiuj i wklej następujący skrypt programu PowerShell na komputer z zainstalowaną usługą Azure AD Connect. Skrypt wyzwala pełną synchronizację haseł, która zawiera starsze skróty haseł. Zaktualizuj `$azureadConnector` zmienne i `$adConnector` nazwy łączników z poprzedniego kroku.

    Uruchom ten skrypt w każdym lesie usługi AD, aby zsynchronizować skróty haseł ntlm i protokołu Kerberos do usługi Azure AD.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    W zależności od rozmiaru katalogu pod względem liczby kont i grup synchronizacja skrótów haseł starszych do usługi Azure AD może zająć trochę czasu. Hasła są następnie synchronizowane z domeną zarządza zarządzana usługą Azure AD DS po ich zsynchronizowaniu z usługą Azure AD.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiedziałeś się:

> [!div class="checklist"]
> * Dlaczego potrzebne są starsze skróty haseł NTLM i Kerberos
> * Jak skonfigurować synchronizację skrótów haseł starszych dla usługi Azure AD Connect

> [!div class="nextstepaction"]
> [Dowiedz się, jak działa synchronizacja w domenie zarządzanej usług domenowych usługi Azure AD](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
