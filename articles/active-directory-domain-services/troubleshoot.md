---
title: 'Azure Active Directory Domain Services: Przewodnik rozwiązywania problemów | Microsoft Docs'
description: Przewodnik rozwiązywania problemów Azure AD Domain Services
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
ms.openlocfilehash: acb001417b85b8ff45b2617e148e8b1961f3cbfa
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772987"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services — Przewodnik rozwiązywania problemów
W tym artykule przedstawiono wskazówki dotyczące rozwiązywania problemów, które mogą wystąpić podczas konfigurowania usług domenowych w usłudze Azure Active Directory (AD) lub zarządzania nimi.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Nie można włączyć Azure AD Domain Services dla katalogu usługi Azure AD
Ta sekcja ułatwia rozwiązywanie problemów podczas próby włączenia Azure AD Domain Services dla katalogu.

Wybierz kroki rozwiązywania problemów, które są zgodne z wyświetlonym komunikatem o błędzie.

| **Komunikat o błędzie** | **Rozdzielczość** |
| --- |:--- |
| *Nazwa contoso100.com jest już używana w tej sieci. Określ nazwę, która nie jest używana.* |[Konflikt nazw domen w sieci wirtualnej](troubleshoot.md#domain-name-conflict) |
| *Nie można włączyć usług Domain Services w tej dzierżawie usługi Azure AD. Usługa nie ma odpowiednich uprawnień do aplikacji o nazwie „Azure AD Domain Services Sync”. Usuń aplikację o nazwie „Azure AD Domain Services Sync”, a następnie spróbuj włączyć usługi Domain Services dla dzierżawy usługi Azure AD.* |[Usługi domenowe nie mają wystarczających uprawnień do aplikacji do synchronizacji Azure AD Domain Services](troubleshoot.md#inadequate-permissions) |
| *Nie można włączyć usług Domain Services w tej dzierżawie usługi Azure AD. Aplikacja usług Domain Services w dzierżawie usługi Azure AD nie uprawnień wymaganych do włączenia usług Domain Services. Usuń aplikację z identyfikatorem d87dcbc6-a371-462e-88e3-28ad15ec4e64, a następnie spróbuj włączyć usługi Domain Services dla dzierżawy usługi Azure AD.* |[Aplikacja usług domenowych nie jest prawidłowo skonfigurowana w Twojej dzierżawie](troubleshoot.md#invalid-configuration) |
| *Nie można włączyć usług Domain Services w tej dzierżawie usługi Azure AD. Aplikacja usługi Microsoft Azure AD jest wyłączona w dzierżawie usługi Azure AD. Włącz aplikację z identyfikatorem 00000002-0000-0000-c000-000000000000, a następnie spróbuj włączyć usługi Domain Services dla dzierżawy usługi Azure AD.* |[Aplikacja Microsoft Graph jest wyłączona w dzierżawie usługi Azure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Konflikt nazw domen
**Komunikat o błędzie:**

*Nazwa contoso100.com jest już używana w tej sieci. Określ nazwę, która nie jest używana.*

**Korygowania**

Upewnij się, że nie masz istniejącej domeny z taką samą nazwą domeny, która jest dostępna w tej sieci wirtualnej. Na przykład załóżmy, że masz domenę o nazwie „contoso.com” już dostępną w wybranej sieci wirtualnej. Później spróbujesz włączyć Azure AD Domain Services domenę zarządzaną o tej samej nazwie domeny (czyli "contoso.com") w tej sieci wirtualnej. Wystąpił błąd podczas próby włączenia Azure AD Domain Services.

Ten błąd jest spowodowany konfliktami nazw dla nazwy domeny w tej sieci wirtualnej. W takiej sytuacji musisz użyć innej nazwy podczas konfigurowania domeny zarządzanej Usług domenowych Azure AD. Możesz również anulować aprowizację istniejącej domeny i kontynuować włączanie Usług domenowych Azure AD.

### <a name="inadequate-permissions"></a>Niewystarczające uprawnienia
**Komunikat o błędzie:**

*Nie można włączyć usług Domain Services w tej dzierżawie usługi Azure AD. Usługa nie ma odpowiednich uprawnień do aplikacji o nazwie „Azure AD Domain Services Sync”. Usuń aplikację o nazwie „Azure AD Domain Services Sync”, a następnie spróbuj włączyć usługi Domain Services dla dzierżawy usługi Azure AD.*

**Korygowania**

Sprawdź, czy istnieje aplikacja o nazwie "Azure AD Domain Services Sync" w katalogu usługi Azure AD. Jeśli ta aplikacja istnieje, usuń ją, a następnie ponownie włącz Azure AD Domain Services.

Wykonaj następujące kroki, aby sprawdzić obecność aplikacji i usunąć ją, jeśli aplikacja istnieje:

1. Przejdź do sekcji **aplikacje** w katalogu usługi Azure AD w [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Wybierz pozycję **wszystkie aplikacje** na liście rozwijanej **Pokaż** . Wybierz **dowolne** z listy rozwijanej **stan aplikacji** . Wybierz **dowolną** pozycję na liście rozwijanej **widoczność aplikacji** .
3. W polu wyszukiwania wpisz **Azure AD Domain Services Sync** . Jeśli aplikacja istnieje, kliknij ją i kliknij przycisk **Usuń** na pasku narzędzi, aby ją usunąć.
4. Po usunięciu aplikacji spróbuj ponownie włączyć Azure AD Domain Services.

### <a name="invalid-configuration"></a>Nieprawidłowa konfiguracja
**Komunikat o błędzie:**

*Nie można włączyć usług Domain Services w tej dzierżawie usługi Azure AD. Aplikacja usług Domain Services w dzierżawie usługi Azure AD nie uprawnień wymaganych do włączenia usług Domain Services. Usuń aplikację z identyfikatorem d87dcbc6-a371-462e-88e3-28ad15ec4e64, a następnie spróbuj włączyć usługi Domain Services dla dzierżawy usługi Azure AD.*

**Korygowania**

Sprawdź, czy masz aplikację o nazwie "AzureActiveDirectoryDomainControllerServices" (z identyfikatorem aplikacji d87dcbc6-a371-462e-88e3-28ad15ec4e64) w katalogu usługi Azure AD. Jeśli ta aplikacja istnieje, należy ją usunąć, a następnie ponownie włączyć Azure AD Domain Services.

Użyj poniższego skryptu programu PowerShell, aby znaleźć aplikację i usunąć ją.

> [!NOTE]
> Ten skrypt używa poleceń cmdlet **programu Azure AD PowerShell w wersji 2** . Aby uzyskać pełną listę wszystkich dostępnych poleceń cmdlet i pobrać moduł, Przeczytaj dokumentację dotyczącą programu [AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).
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

### <a name="microsoft-graph-disabled"></a>Microsoft Graph wyłączone
**Komunikat o błędzie:**

Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Aplikacja usługi Microsoft Azure AD jest wyłączona w dzierżawie usługi Azure AD. Włącz aplikację przy użyciu identyfikatora aplikacji 00000002-0000-0000-C000-000000000000, a następnie spróbuj włączyć usługi domenowe dla dzierżawy usługi Azure AD.

**Korygowania**

Sprawdź, czy aplikacja została wyłączona o identyfikatorze 00000002-0000-0000-C000-000000000000. Ta aplikacja jest aplikacją Microsoft Azure AD i zapewnia interfejs API programu Graph dostęp do dzierżawy usługi Azure AD. Azure AD Domain Services wymaga włączenia tej aplikacji w celu synchronizacji dzierżawy usługi Azure AD z domeną zarządzaną.

Aby rozwiązać ten problem, Włącz tę aplikację, a następnie spróbuj włączyć usługi domenowe dla dzierżawy usługi Azure AD.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Użytkownicy nie mogą zalogować się do domeny zarządzanej usług Azure AD Domain Services
Jeśli co najmniej jeden użytkownik w dzierżawie usługi Azure AD nie może zalogować się do nowo utworzonej domeny zarządzanej, wykonaj następujące kroki rozwiązywania problemów:

* **Zaloguj się przy użyciu formatu UPN:** Spróbuj zalogować się, korzystając z nazwy w formacie UPN (na przykład „joeuser@contoso.com”) zamiast w formacie SAMAccountName („CONTOSO\joeuser”). SAMAccountName może być automatycznie generowany dla użytkowników, których prefiks nazwy UPN jest zbyt długi lub jest taki sam jak inny użytkownik w domenie zarządzanej. Format nazwy UPN ma być unikatowy w ramach dzierżawy usługi Azure AD.

> [!NOTE]
> Zalecamy używanie formatu UPN do logowania się do Azure AD Domain Services domeny zarządzanej.
>
>

* Upewnij się, że masz [włączoną synchronizację haseł](active-directory-ds-getting-started-password-sync.md) zgodnie z procedurą opisaną w przewodniku Wprowadzenie.
* **Konta zewnętrzne:** Upewnij się, że konto użytkownika, którego dotyczy problem, nie jest kontem zewnętrznym w dzierżawie usługi Azure AD. Przykłady kont zewnętrznych to konta Microsoft (na przykład "joe@live.com") lub konta użytkowników z zewnętrznego katalogu usługi Azure AD. Ponieważ Azure AD Domain Services nie ma poświadczeń dla takich kont użytkowników, użytkownicy nie mogą zalogować się do domeny zarządzanej.
* **Zsynchronizowane konta:** Jeśli konta użytkowników, których to dotyczy, są synchronizowane z katalogu lokalnego, należy sprawdzić, czy:

  * Wdrożono lub Zaktualizowano do [najnowszej zalecanej wersji Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
  * Skonfigurowano Azure AD Connect w celu [przeprowadzenia pełnej synchronizacji](active-directory-ds-getting-started-password-sync.md).
  * W zależności od rozmiaru katalogu może upłynąć trochę czasu dla kont użytkowników i skrótów poświadczeń, które będą dostępne w Azure AD Domain Services. Przed ponowną próbą uwierzytelnienia upewnij się, że zaczekasz wystarczająco długo.
  * Jeśli problem będzie się powtarzać po sprawdzeniu powyższych kroków, spróbuj ponownie uruchomić usługę synchronizacji Microsoft Azure AD. Na maszynie synchronizacji Uruchom wiersz polecenia i wykonaj następujące polecenia:

    1. polecenie net stop "Microsoft Azure AD Sync"
    2. NET start "Microsoft Azure AD Sync"
* **Konta tylko w chmurze**: Jeśli konto użytkownika, którego to dotyczy, jest kontem użytkownika tylko w chmurze, upewnij się, że użytkownik zmienił hasło po włączeniu Azure AD Domain Services. Ten krok powoduje wygenerowanie skrótów poświadczeń wymaganych przez usługi Azure AD Domain Services.
* **Sprawdź, czy konto użytkownika jest aktywne**: Jeśli konto użytkownika jest zablokowane, nie może się zalogować, dopóki konto nie zostanie ponownie uaktywnione. Pięć nieudanych prób wprowadzenia hasła w ciągu 2 minut w domenie zarządzanej powoduje, że konto użytkownika zostanie zablokowane przez 30 minut. Po 30 minutach konto użytkownika zostanie automatycznie odblokowane.
  * Nieprawidłowe próby hasła w domenie zarządzanej nie Zablokuj konta użytkownika w usłudze Azure AD. Konto użytkownika jest blokowane tylko w ramach domeny zarządzanej Azure AD Domain Services. Sprawdź stan konta użytkownika za pomocą konsoli administracyjnej Active Directory (usługach ADAC) dla domeny zarządzanej AD DS platformy Azure, a nie w usłudze Azure AD.
  * Można również [skonfigurować szczegółowe zasady haseł, które zmieniają domyślny próg blokady i czas trwania](https://docs.microsoft.com/azure/active-directory-domain-services/password-policy).

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Istnieje co najmniej jeden alert w domenie zarządzanej

Zobacz jak rozwiązywać alerty w domenie zarządzanej, odwiedzając artykuł [Rozwiązywanie problemów](troubleshoot-alerts.md) z alertami.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Użytkownicy usunięci z dzierżawy usługi Azure AD nie zostaną usunięci z domeny zarządzanej
Usługa Azure AD zapewnia ochronę przed przypadkowym usunięciem obiektów użytkowników. Po usunięciu konta użytkownika z dzierżawy usługi Azure AD odpowiedni obiekt użytkownika jest przenoszony do Kosza. Gdy ta operacja usuwania jest synchronizowana z domeną zarządzaną, powoduje, że odpowiednie konto użytkownika zostanie oznaczone jako wyłączone. Ta funkcja umożliwia późniejsze odzyskiwanie lub cofanie usunięcia konta użytkownika.

Konto użytkownika pozostaje w stanie wyłączenia w domenie zarządzanej, nawet jeśli ponownie utworzysz konto użytkownika z tą samą nazwą UPN w katalogu usługi Azure AD. Aby usunąć konto użytkownika z domeny zarządzanej, należy wymusić usunięcie go z dzierżawy usługi Azure AD.

Aby całkowicie usunąć konto użytkownika z domeny zarządzanej, Usuń użytkownika trwale z dzierżawy usługi Azure AD. Użyj polecenia cmdlet `-RemoveFromRecycleBin` [](/previous-versions/azure/dn194132(v=azure.100))programu PowerShell z opcją, zgodnie z opisem w tym artykule w witrynie MSDN `Remove-MsolUser` .


## <a name="contact-us"></a>Skontaktuj się z nami
Skontaktuj się z zespołem pomocy technicznej Azure Active Directory Domain Services, aby [udostępnić opinię lub pomoc techniczną](contact-us.md).
