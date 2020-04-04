---
title: Rozwiązywanie problemów z usługami domenowymi Active Directory platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać typowe błędy podczas tworzenia usług domenowych Active Directory usługi Azure Active Directory lub zarządzania nimi
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 84efe294533186fdcf2e0a3356a7d6b01eccaf5f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654389"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Typowe błędy i kroki rozwiązywania problemów w usługach domenowych Active Directory platformy Azure

Centralna część tożsamości i uwierzytelniania aplikacji usługi domenowe Active Directory (Azure AD DS) czasami występują problemy. Jeśli napotkasz problemy, istnieją pewne typowe komunikaty o błędach i skojarzone kroki rozwiązywania problemów, które pomogą Ci ponownie uruchomić. W dowolnym momencie można również [otworzyć żądanie pomocy technicznej platformy Azure,][azure-support] aby uzyskać dodatkową pomoc w rozwiązywaniu problemów.

Ten artykuł zawiera kroki rozwiązywania typowych problemów w usługach Azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Nie można włączyć usług domenowych usługi Azure AD dla katalogu usługi Azure AD

Jeśli masz problemy z włączaniem usług Azure AD DS, przejrzyj następujące typowe błędy i kroki, aby je rozwiązać:

| **Przykładowy komunikat o błędzie** | **Rozdzielczość** |
| --- |:--- |
| *Nazwa aaddscontoso.com jest już używana w tej sieci. Określ nazwę, która nie jest używana.* |[Konflikt nazw domen w sieci wirtualnej](troubleshoot.md#domain-name-conflict) |
| *Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Usługa nie ma odpowiednich uprawnień do aplikacji o nazwie "Synchronizacja usług domenowych usługi azure ad". Usuń aplikację o nazwie "Synchronizacja usług domenowych usługi azure ad", a następnie spróbuj włączyć usługi domenowe dla dzierżawy usługi Azure AD.* |[Usługi domenowe nie mają odpowiednich uprawnień do aplikacji Synchronizacji usług domenowych usługi azure ad](troubleshoot.md#inadequate-permissions) |
| *Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Aplikacja Usługi domenowe w dzierżawie usługi Azure AD nie ma wymaganych uprawnień do włączania usług domenowych. Usuń aplikację za pomocą identyfikatora aplikacji d87dcbc6-a371-462e-88e3-28ad15ec4e64, a następnie spróbuj włączyć usługi domeny dla dzierżawy usługi Azure AD.* |[Aplikacja Usługi domenowe nie jest poprawnie skonfigurowana w dzierżawie usługi Azure AD](troubleshoot.md#invalid-configuration) |
| *Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Aplikacja Microsoft Azure AD jest wyłączona w dzierżawie usługi Azure AD. Włącz aplikację za pomocą identyfikatora aplikacji 00000002-0000-0000-c0000-000000000000, a następnie spróbuj włączyć usługi domenowe dla dzierżawy usługi Azure AD.* |[Aplikacja Microsoft Graph jest wyłączona w dzierżawie usługi Azure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Konflikt nazwy domeny

**Komunikat o błędzie**

*Nazwa aaddscontoso.com jest już używana w tej sieci. Określ nazwę, która nie jest używana.*

**Rozdzielczość**

Sprawdź, czy nie masz istniejącego środowiska usług AD DS o tej samej nazwie domeny w tej samej lub równorzędnej sieci wirtualnej. Na przykład może mieć domenę usług AD DS o nazwie *aaddscontoso.com,* która działa na maszynach wirtualnych platformy Azure. Podczas próby włączenia domeny zarządzanej usług Azure AD DS o tej samej nazwie domeny *aaddscontoso.com* w sieci wirtualnej żądana operacja kończy się niepowodzeniem.

Ten błąd jest spowodowany konfliktami nazw nazwy domeny w sieci wirtualnej. Wyszukiwanie DNS sprawdza, czy istniejące środowisko usług AD DS odpowiada na żądaną nazwę domeny. Aby rozwiązać ten błąd, użyj innej nazwy, aby skonfigurować domenę zarządzaną usług Azure AD DS lub wyrejeścić istniejącą domenę usług AD DS, a następnie spróbuj ponownie włączyć usługi Azure AD DS.

### <a name="inadequate-permissions"></a>Nieodpowiednie uprawnienia

**Komunikat o błędzie**

*Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Usługa nie ma odpowiednich uprawnień do aplikacji o nazwie "Synchronizacja usług domenowych usługi azure ad". Usuń aplikację o nazwie "Synchronizacja usług domenowych usługi azure ad", a następnie spróbuj włączyć usługi domenowe dla dzierżawy usługi Azure AD.*

**Rozdzielczość**

Sprawdź, czy w katalogu usługi Azure AD jest aplikacja o nazwie *Synchronizacja usług domenowych usługi azure.* Jeśli ta aplikacja istnieje, usuń ją, a następnie spróbuj ponownie włączyć usługi Azure AD DS. Aby sprawdzić dostępność istniejącej aplikacji i usunąć ją w razie potrzeby, wykonaj następujące czynności:

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory** z menu nawigacji po lewej stronie.
1. Wybierz **aplikacje enterprise**. Wybierz *pozycję Wszystkie aplikacje* z menu rozwijanego **Typ aplikacji,** a następnie wybierz pozycję **Zastosuj**.
1. W polu wyszukiwania wprowadź *synchronizację usług domenowych usługi azure ad*. Jeśli aplikacja istnieje, zaznacz ją i wybierz polecenie **Usuń**.
1. Po usunięciu aplikacji spróbuj ponownie włączyć usługi Azure AD DS.

### <a name="invalid-configuration"></a>Nieprawidłowa konfiguracja

**Komunikat o błędzie**

*Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Aplikacja Usługi domenowe w dzierżawie usługi Azure AD nie ma wymaganych uprawnień do włączania usług domenowych. Usuń aplikację za pomocą identyfikatora aplikacji d87dcbc6-a371-462e-88e3-28ad15ec4e64, a następnie spróbuj włączyć usługi domeny dla dzierżawy usługi Azure AD.*

**Rozdzielczość**

Sprawdź, czy masz istniejącą aplikację o nazwie *AzureActiveDirectoryDomainControllerServices* z identyfikatorem aplikacji *d87dcbc6-a371-462e-88e3-28ad15ec4e64* w katalogu usługi Azure AD. Jeśli ta aplikacja istnieje, usuń ją, a następnie spróbuj ponownie włączyć usługi Azure AD DS.

Użyj następującego skryptu programu PowerShell, aby wyszukać istniejące wystąpienie aplikacji i usunąć je w razie potrzeby:

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Microsoft Graph wyłączony

**Komunikat o błędzie**

*Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Aplikacja Microsoft Azure AD jest wyłączona w dzierżawie usługi Azure AD. Włącz aplikację za pomocą identyfikatora aplikacji 00000002-0000-0000-c0000-000000000000, a następnie spróbuj włączyć usługi domenowe dla dzierżawy usługi Azure AD.*

**Rozdzielczość**

Sprawdź, czy aplikacja została wyłączona z identyfikatorem *00000002-0000-0000-c0000-00000000000*. Ta aplikacja jest aplikacją Microsoft Azure AD i zapewnia dostęp interfejsu API graph do dzierżawy usługi Azure AD. Aby zsynchronizować dzierżawę usługi Azure AD, ta aplikacja musi być włączona.

Aby sprawdzić stan tej aplikacji i włączyć ją w razie potrzeby, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory** z menu nawigacji po lewej stronie.
1. Wybierz **aplikacje enterprise**. Wybierz *pozycję Wszystkie aplikacje* z menu rozwijanego **Typ aplikacji,** a następnie wybierz pozycję **Zastosuj**.
1. W polu wyszukiwania wpisz *00000002-0000-0000-c000-0000000000*. Wybierz aplikację, a następnie wybierz polecenie **Właściwości**.
1. Jeśli **włączono logowanie użytkowników** jest ustawiona na *Nie,* ustaw wartość *Tak,* a następnie wybierz pozycję **Zapisz**.
1. Po włączeniu aplikacji spróbuj ponownie włączyć usługi Azure AD DS.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Użytkownicy nie mogą zalogować się do domeny zarządzanej usług Azure AD Domain Services

Jeśli jeden lub więcej użytkowników w dzierżawie usługi Azure AD nie może zalogować się do domeny zarządzanej usług Azure AD DS, wykonaj następujące kroki rozwiązywania problemów:

* **Format poświadczeń** — spróbuj określić poświadczenia `dee@aaddscontoso.onmicrosoft.com`przy użyciu formatu UPN, na przykład . Format upn jest zalecanym sposobem określania poświadczeń w usługach Azure AD DS. Upewnij się, że ta owana jest poprawnie ta owana w usłudze Azure AD.

    *SamAccountName* dla twojego konta, takich jak *AADDSCONTOSO\driley* może być automatyczniegenerowane, jeśli istnieje wielu użytkowników z tym samym prefiksem NAZWY UPN w dzierżawie lub jeśli prefiks nazwy UPN jest zbyt długi. W związku z tym format *SAMAccountName* dla konta może się różnić od tego, czego oczekujesz lub używasz w domenie lokalnej.

* **Synchronizacja haseł** — upewnij się, że synchronizacja haseł została włączona dla użytkowników tylko w [chmurze][cloud-only-passwords] lub dla [środowisk hybrydowych korzystających z usługi Azure AD Connect][hybrid-phs].
    * **Hybrydowe konta zsynchronizowane:** Jeśli konta użytkowników, których dotyczy problem, są synchronizowane z katalogu lokalnego, sprawdź następujące obszary:
    
      * Wdrożono lub zaktualizowano [najnowszą zalecaną wersję usługi Azure AD Connect.](https://www.microsoft.com/download/details.aspx?id=47594)
      * Usługa Azure AD Connect została skonfigurowana do [pełnej synchronizacji.][hybrid-phs]
      * W zależności od rozmiaru katalogu może upłynąć trochę czasu, aby konta użytkowników i skróty poświadczeń były dostępne w usługach Azure AD DS. Upewnij się, że czekasz wystarczająco długo, zanim spróbujesz uwierzytelnić się w domenie zarządzanej.
      * Jeśli problem będzie się powtarzał po zweryfikowaniu poprzednich kroków, spróbuj ponownie uruchomić *usługę microsoft azure ad sync service*. Na serwerze usługi Azure AD Connect otwórz wiersz polecenia i uruchom następujące polecenia:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Konta tylko w chmurze:** Jeśli konto użytkownika, którego dotyczy problem, jest kontem użytkownika tylko w chmurze, upewnij się, że [użytkownik zmienił swoje hasło po włączeniu usługi Azure AD DS][cloud-only-passwords]. To zresetowanie hasła powoduje, że wymagane skróty poświadczeń dla usług domenowych usługi Azure AD mają być generowane.

* **Sprawdź, czy konto użytkownika jest aktywne**: Domyślnie pięć prób nieprawidłowego hasła w ciągu 2 minut w domenie zarządzanej powoduje zablokowanie konta użytkownika na 30 minut. Użytkownik nie może się zalogować, gdy konto jest zablokowane. Po 30 minutach konto użytkownika zostanie automatycznie odblokowane.
  * Próby nieprawidłowego hasła w domenie zarządzanej usług Azure AD DS nie blokują konta użytkownika w usłudze Azure AD. Konto użytkownika jest zablokowane tylko w domenie zarządzanej. Sprawdź stan konta użytkownika w *konsoli administracyjnej usługi Active Directory (ADAC)* przy użyciu [maszyny Wirtualnej zarządzania][management-vm], a nie w usłudze Azure AD.
  * Można również [skonfigurować zasady haseł drobnoziarnistych,][password-policy] aby zmienić domyślny próg blokady i czas trwania.

* **Konta zewnętrzne** — sprawdź, czy konto użytkownika, którego dotyczy problem, nie jest kontem zewnętrznym w dzierżawie usługi Azure AD. Przykłady kont zewnętrznych obejmują `dee@live.com` konta Microsoft, takie jak lub konta użytkowników z zewnętrznego katalogu usługi Azure AD. Usługi Azure AD DS nie przechowuje poświadczeń dla kont użytkowników zewnętrznych, więc nie mogą zalogować się do domeny zarządzanej.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>W domenie zarządzanej znajduje się co najmniej jeden alert

Jeśli istnieją aktywne alerty w domenie zarządzanej usług Azure AD DS, może to uniemożliwić poprawne działanie procesu uwierzytelniania.

Aby sprawdzić, czy istnieją aktywne alerty, [sprawdź stan kondycji domeny zarządzanej usług Azure AD DS][check-health]. Jeśli są wyświetlane jakieś alerty, [rozwiąż je i rozwiąż je][troubleshoot-alerts].

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Users removed from your Azure AD tenant are not removed from your managed domain (Użytkownicy usunięci z dzierżawy usługi Azure AD nie są usuwani z domeny zarządzanej)

Usługa Azure AD chroni przed przypadkowym usunięciem obiektów użytkownika. Po usunięciu konta użytkownika z dzierżawy usługi Azure AD odpowiedni obiekt użytkownika jest przenoszony do kosza. Gdy ta operacja usuwania jest synchronizowana z domeną zarządzana usługą Azure AD DS, odpowiednie konto użytkownika jest oznaczone jako wyłączone. Ta funkcja pomaga odzyskać lub cofnąć usunięcie konta użytkownika.

Konto użytkownika pozostaje w stanie wyłączonym w domenie zarządzanej usługi Azure AD DS, nawet jeśli ponownie utworzyć konto użytkownika z tą samą anum UPN w katalogu usługi Azure AD. Aby usunąć konto użytkownika z domeny zarządzanej usług Azure AD DS, należy je na żądanie usunąć z dzierżawy usługi Azure AD.

Aby w pełni usunąć konto użytkownika z domeny zarządzanej usługi Azure AD DS, należy go trwale usunąć z dzierżawy usługi Azure AD przy użyciu polecenia cmdlet [programu Delete-MsolUser][Remove-MsolUser] PowerShell z parametrem. `-RemoveFromRecycleBin`

## <a name="next-steps"></a>Następne kroki

Jeśli nadal występują problemy, [otwórz żądanie pomocy technicznej platformy Azure,][azure-support] aby uzyskać dodatkową pomoc w rozwiązywaniu problemów.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
