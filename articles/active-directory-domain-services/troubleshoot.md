---
title: 'Azure Active Directory Domain Services: Przewodnik rozwiązywania problemów | Dokumentacja firmy Microsoft'
description: Przewodnik rozwiązywania problemów usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 2df1ac6325f692e2d433238ae0b92d8e3f8482b5
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472280"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services — przewodnik rozwiązywania problemów
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów, które można napotkać podczas konfigurowania lub administrowania usługi domenowe Azure Active Directory (AD).

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Nie można włączyć usług domenowych Azure AD dla katalogu usługi Azure AD
Ta sekcja ułatwia rozwiązywanie problemów podczas próby włączenia usług domenowych Azure AD dla katalogu.

Wybierz kroki rozwiązywania problemów, które odpowiadają komunikat o błędzie, który wystąpi.

| **Komunikat o błędzie** | **Rozdzielczość** |
| --- |:--- |
| *Nazwa contoso100.com jest już używana w tej sieci. Określ nazwę, która nie jest używana.* |[Konflikt nazw domeny w sieci wirtualnej](troubleshoot.md#domain-name-conflict) |
| *Nie można włączyć usług Domain Services w tej dzierżawie usługi Azure AD. Usługa nie ma odpowiednich uprawnień do aplikacji o nazwie „Azure AD Domain Services Sync”. Usuń aplikację o nazwie „Azure AD Domain Services Sync”, a następnie spróbuj włączyć usługi Domain Services dla dzierżawy usługi Azure AD.* |[Usługi domenowe nie ma wystarczających uprawnień do aplikacji Azure AD Domain Services Sync](troubleshoot.md#inadequate-permissions) |
| *Nie można włączyć usług Domain Services w tej dzierżawie usługi Azure AD. Aplikacja usług Domain Services w dzierżawie usługi Azure AD nie uprawnień wymaganych do włączenia usług Domain Services. Usuń aplikację z identyfikatorem d87dcbc6-a371-462e-88e3-28ad15ec4e64, a następnie spróbuj włączyć usługi Domain Services dla dzierżawy usługi Azure AD.* |[Aplikacja usług Domain Services nie jest skonfigurowana poprawnie w Twojej dzierżawie](troubleshoot.md#invalid-configuration) |
| *Nie można włączyć usług Domain Services w tej dzierżawie usługi Azure AD. Aplikacja usługi Microsoft Azure AD jest wyłączona w dzierżawie usługi Azure AD. Włącz aplikację z identyfikatorem 00000002-0000-0000-c000-000000000000, a następnie spróbuj włączyć usługi Domain Services dla dzierżawy usługi Azure AD.* |[Aplikacja Microsoft Graph jest wyłączona w dzierżawie usługi Azure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Konflikt nazw domeny
**Komunikat o błędzie:**

*Nazwa contoso100.com jest już używana w tej sieci. Określ nazwę, która nie jest używana.*

**Środki zaradcze:**

Upewnij się, że nie masz istniejącej domeny z tą samą nazwą domeny, dostępne w tej sieci wirtualnej. Na przykład załóżmy, że masz domenę o nazwie „contoso.com” już dostępną w wybranej sieci wirtualnej. Później spróbuj włączyć domeny zarządzanej usług domenowych Azure AD, z tą samą nazwą domeny (czyli "contoso.com") w tej sieci wirtualnej. Wystąpi błąd podczas próby włączenia usług domenowych Azure AD.

Ten błąd jest spowodowany konfliktów nazw dla nazwy domeny w tej sieci wirtualnej. W takiej sytuacji musisz użyć innej nazwy podczas konfigurowania domeny zarządzanej Usług domenowych Azure AD. Możesz również anulować aprowizację istniejącej domeny i kontynuować włączanie Usług domenowych Azure AD.

### <a name="inadequate-permissions"></a>Niewystarczające uprawnienia
**Komunikat o błędzie:**

*Nie można włączyć usług Domain Services w tej dzierżawie usługi Azure AD. Usługa nie ma odpowiednich uprawnień do aplikacji o nazwie „Azure AD Domain Services Sync”. Usuń aplikację o nazwie „Azure AD Domain Services Sync”, a następnie spróbuj włączyć usługi Domain Services dla dzierżawy usługi Azure AD.*

**Środki zaradcze:**

Sprawdź, czy istnieje aplikacja o nazwie "Azure AD Domain Services Sync" w katalogu usługi Azure AD. Jeśli ta aplikacja istnieje, usuń go, a następnie ponownie włącz usługi domenowe Azure AD.

Wykonaj następujące kroki, aby sprawdzić obecność aplikacji i usuń go, jeśli istnieje aplikacji:

1. Przejdź do **aplikacje** sekcji katalogu usługi Azure AD w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Wybierz **wszystkie aplikacje** w **Pokaż** listy rozwijanej. Wybierz **wszelkie** w **stan aplikacji** listy rozwijanej. Wybierz **wszelkie** w **widoczność aplikacji** listy rozwijanej.
3. Typ **usługi Azure AD Domain Services Sync** w polu wyszukiwania. Jeśli aplikacja, kliknij go i kliknij przycisk **Usuń** przycisk na pasku narzędzi, aby go usunąć.
4. Po usunięciu aplikacji, spróbuj ponownie włączyć usługi domenowe Azure AD.

### <a name="invalid-configuration"></a>Nieprawidłowa konfiguracja
**Komunikat o błędzie:**

*Nie można włączyć usług Domain Services w tej dzierżawie usługi Azure AD. Aplikacja usług Domain Services w dzierżawie usługi Azure AD nie uprawnień wymaganych do włączenia usług Domain Services. Usuń aplikację z identyfikatorem d87dcbc6-a371-462e-88e3-28ad15ec4e64, a następnie spróbuj włączyć usługi Domain Services dla dzierżawy usługi Azure AD.*

**Środki zaradcze:**

Sprawdź, jeśli masz aplikację o nazwie "AzureActiveDirectoryDomainControllerServices" (z identyfikatorem aplikacji d87dcbc6-a371-462e-88e3-28ad15ec4e64) w katalogu usługi Azure AD. Jeśli ta aplikacja istnieje, należy ją usunąć i ponownie włączyć usługi domenowe Azure AD.

Poniższy skrypt programu PowerShell umożliwia znajdowanie aplikacji i usuń go.

> [!NOTE]
> Ten skrypt używa **Azure AD PowerShell w wersji 2** polecenia cmdlet. Aby uzyskać pełną listę wszystkich dostępnych poleceń cmdlet i pobrać moduł, przeczytaj [dokumentacja programu PowerShell usługi Azure AD](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

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
<br>

### <a name="microsoft-graph-disabled"></a>Program Microsoft Graph wyłączone
**Komunikat o błędzie:**

Nie można włączyć usług Domain Services w tej dzierżawie usługi Azure AD. Aplikacja usługi Microsoft Azure AD jest wyłączona w dzierżawie usługi Azure AD. Włącz aplikację z 00000002-0000-0000-c000-000000000000 identyfikator aplikacji, a następnie spróbuj włączyć usługi Domain Services dla dzierżawy usługi Azure AD.

**Środki zaradcze:**

Sprawdź, jeśli wyłączono aplikację z identyfikatorem 00000002-0000-0000-c000-000000000000. Ta aplikacja jest aplikacją usługi Microsoft Azure AD i zapewnia dostęp do interfejsu API programu Graph do dzierżawy usługi Azure AD. Azure AD Domain Services wymaga tej aplikacji, aby możliwe było synchronizowanie dzierżawy usługi Azure AD do domeny zarządzanej.

Aby rozwiązać ten problem, należy włączyć tę aplikację, a następnie spróbuj włączyć usługi Domain Services dla dzierżawy usługi Azure AD.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Użytkownicy nie mogą zalogować się do domeny zarządzanej usług Azure AD Domain Services
Jeśli co najmniej jeden użytkownik w dzierżawie usługi Azure AD są w stanie zalogować się do nowo utworzonego domeny zarządzanej, wykonaj następujące kroki:

* **Zaloguj się przy użyciu nazwy w formacie UPN:** Spróbuj zalogować się, korzystając z nazwy w formacie UPN (na przykład „joeuser@contoso.com”) zamiast w formacie SAMAccountName („CONTOSO\joeuser”). SAMAccountName mogą być generowane automatycznie dla użytkowników, których prefiks nazwy UPN jest zbyt długi lub jest taki sam jak inny użytkownik w domenie zarządzanej. Format nazwy UPN są musi być unikatowy w ramach dzierżawy usługi Azure AD.

> [!NOTE]
> Firma Microsoft zaleca w formacie UPN zalogować się do domeny zarządzanej usług domenowych Azure AD.
>
>

* Upewnij się, że masz [włączoną synchronizację haseł](active-directory-ds-getting-started-password-sync.md) zgodnie z procedurą opisaną w przewodniku Wprowadzenie.
* **Zewnętrzne konta:** Upewnij się, że konto użytkownika, którego dotyczy problem, nie jest kontem zewnętrznym w dzierżawie usługi Azure AD. Zewnętrzne konta przykłady kont Microsoft (na przykład "joe@live.com") lub kont użytkowników z zewnętrznej katalog usługi Azure AD. Ponieważ usługi domenowe Azure AD nie ma poświadczeń dla takich kont użytkownika, tych użytkowników nie Zaloguj się do domeny zarządzanej.
* **Zsynchronizowane konta:** Konta użytkownika są synchronizowane z katalogu lokalnego, sprawdź, czy:

  * Wdrożeniu lub zaktualizowane w celu [najnowsze zalecana wersja programu Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
  * Skonfigurowano usługi Azure AD Connect, aby [przeprowadzić pełną synchronizację](active-directory-ds-getting-started-password-sync.md).
  * W zależności od rozmiaru katalogu może zająć trochę czasu kont użytkowników i poświadczeń skróty, które mają być dostępne w usługach domenowych Azure AD. Upewnij się, że czekasz wystarczająco długo przed ponowieniem próby uwierzytelnienia.
  * Jeśli problem nie zniknie po sprawdzeniu w poprzednich krokach, spróbuj ponownie uruchomić usługę Microsoft Azure AD Sync. Na maszynie synchronizacji Uruchom wiersz polecenia i uruchom następujące polecenia:

    1. polecenie net stop 'Microsoft Azure AD Sync'
    2. polecenie net start 'Microsoft Azure AD Sync'
* **Tylko w chmurze kont**: Konto użytkownika w przypadku kont użytkowników tylko w chmurze, upewnij się, że użytkownik zmienił swojego hasła, po włączeniu usług domenowych Azure AD. Ten krok powoduje wygenerowanie skrótów poświadczeń wymaganych przez usługi Azure AD Domain Services.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Istnieje jeden lub więcej alertów w domenie zarządzanej

Zobacz, jak rozwiązywanie alertów dotyczących domeny zarządzanej, odwiedzając [Rozwiązywanie problemów z alertami](troubleshoot-alerts.md) artykułu.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Usunięci z dzierżawy usługi Azure AD użytkownicy nie zostaną usunięte z Twojej domeny zarządzanej
Usługa Azure AD zapewnia ochronę przed przypadkowym usunięciem obiektów użytkowników. Po usunięciu konta użytkownika z dzierżawy usługi Azure AD odpowiedni obiekt użytkownika jest przenoszony do Kosza. Operacja usuwania jest zsynchronizowany z domeną zarządzaną, powoduje odpowiedniego konta użytkownika, który był oznaczony jako wyłączony. Ta funkcja pomaga odzyskać lub później cofnięcia usunięcia konta użytkownika.

Konto użytkownika pozostają w stanie wyłączenia w Twojej domeny zarządzanej, nawet wtedy, gdy ponownie utworzyć konto użytkownika o tej samej nazwy UPN w katalogu usługi Azure AD. Aby usunąć konto użytkownika z Twojej domeny zarządzanej, należy wymusić usunięcie go z dzierżawą usługi Azure AD.

Aby całkowicie usunąć konto użytkownika z domeny zarządzanej, należy usunąć użytkownika trwale z dzierżawy usługi Azure AD. Użyj `Remove-MsolUser` polecenia cmdlet programu PowerShell przy użyciu `-RemoveFromRecycleBin` opcji, zgodnie z opisem w tym [artykuł w witrynie MSDN](/previous-versions/azure/dn194132(v=azure.100)).


## <a name="contact-us"></a>Skontaktuj się z nami
Skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services, aby [Podziel się opinią lub pomocy technicznej](contact-us.md).
