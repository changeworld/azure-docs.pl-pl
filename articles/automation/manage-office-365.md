---
title: Zarządzanie usługami usługi Office 365 przy użyciu usługi Azure Automation
description: Informuje, jak używać usługi Azure Automation do zarządzania usługami subskrypcji usługi Office 365.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550424"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Zarządzanie usługami usługi Office 365 przy użyciu usługi Azure Automation

Usługi Azure Automation można używać do zarządzania usługami subskrypcji usługi Office 365 w przypadku produktów, takich jak Microsoft Word i Microsoft Outlook. Interakcje z usługą Office 365 są włączane przez [usługę Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Zobacz [Korzystanie z usługi Azure AD w usłudze Azure Automation w celu uwierzytelniania na platformie Azure.](automation-use-azure-ad.md)

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do zarządzania usługami subskrypcji usługi Office 365 w usłudze Azure Automation potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Zobacz [Przewodnik po decyzji o subskrypcji](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Obiekt automatyzacji na platformie Azure do przechowywania poświadczeń konta użytkownika i uruchomieniu. Y. Zobacz [wprowadzenie do usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD. Zobacz [Korzystanie z usługi Azure AD w usłudze Azure Automation w celu uwierzytelniania na platformie Azure.](automation-use-azure-ad.md)
* Dzierżawa usługi Office 365 z kontem. Zobacz [Konfigurowanie dzierżawy usługi Office 365](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Instalowanie modułów MSOnline i MSOnlineExt

Korzystanie z usługi Office 365 w ramach usługi Azure Automation`MSOnline` wymaga usługi Microsoft Azure Active Directory dla programu Windows PowerShell (moduł). Potrzebny będzie również moduł, [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)który upraszcza zarządzanie usługą Azure AD w środowiskach jedno- i wielodostępnych. Zainstaluj moduły zgodnie z opisem w [usłudze Azure AD w usłudze Azure Automation, aby uwierzytelnić się na platformie Azure.](automation-use-azure-ad.md)

>[!NOTE]
>Aby korzystać z programu MSOnline PowerShell, musisz być członkiem usługi Azure AD. Użytkownicy-goście nie mogą korzystać z modułu.

## <a name="creating-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

Aby wykonać kroki opisane w tym artykule, potrzebujesz konta w usłudze Azure Automation. Zobacz [Tworzenie konta usługi Azure Automation](automation-quickstart-create-account.md).
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>Dodawanie MSOnline i MSOnlineExt jako zasobów

Teraz dodaj zainstalowane moduły MSOnline i MSOnlineExt, aby włączyć funkcjonalność usługi Office 365. Zapoznaj się [z witryną Zarządzanie modułami w usłudze Azure Automation](shared-resources/modules.md).

1. W witrynie Azure portal wybierz pozycję **Konta automatyzacji**.
2. Wybierz swoje konto Automatyzacja.
3. Wybierz **galerię modułów** w obszarze **Zasoby udostępnione**.
4. Wyszukaj aplikację MSOnline.
5. Wybierz `MSOnline` moduł programu PowerShell i kliknij przycisk **Importuj,** aby zaimportować moduł jako zasób.
6. Powtórz kroki 4 i 5, `MSOnlineExt` aby zlokalizować i zaimportować moduł. 

## <a name="creating-a-credential-asset-optional"></a>Tworzenie zasobu poświadczeń (opcjonalnie)

Opcjonalnie jest utworzenie zasobu poświadczeń dla użytkownika administracyjnego usługi Office 365, który ma uprawnienia do uruchamiania skryptu. Może to jednak pomóc w nieuchyliniu nazw użytkowników i haseł w skryptach programu PowerShell. Aby uzyskać instrukcje, zobacz [Tworzenie zasobu poświadczeń](automation-use-azure-ad.md#creating-a-credential-asset).

## <a name="creating-an-office-365-service-account"></a>Tworzenie konta usługi Office 365

Aby uruchomić usługi subskrypcji usługi Office 365, potrzebujesz konta usługi Office 365 z uprawnieniami, aby robić to, co chcesz. Można użyć jednego konta administratora globalnego, jednego konta na usługę lub mieć jedną funkcję lub skrypt do wykonania. W każdym przypadku konto usługi wymaga złożonego i bezpiecznego hasła. Zobacz [Konfigurowanie usługi Office 365 dla firm](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Łączenie się z usługą online usługi Azure AD

>[!NOTE]
>Aby użyć poleceń cmdlet modułu MSOnline, należy uruchomić je z programu Windows PowerShell. Program PowerShell Core nie obsługuje tych poleceń cmdlet.

Moduł MSOnline służy do łączenia się z usługą Azure AD z subskrypcji usługi Office 365. Połączenie używa nazwy użytkownika i hasła usługi Office 365 lub używa uwierzytelniania wieloskładnikowego (MFA). Można połączyć się za pomocą witryny Azure portal lub wiersza polecenia programu Windows PowerShell (nie musi być podwyższony).

Przykład programu PowerShell jest pokazany poniżej. Polecenie cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) monituje o poświadczenia `Msolcred` i przechowuje je w zmiennej. Następnie polecenie cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) używa poświadczeń do łączenia się z usługą online katalogu platformy Azure. Jeśli chcesz połączyć się z określonym `AzureEnvironment` środowiskiem platformy Azure, użyj tego parametru.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Jeśli nie zostanie wyświetlony żaden błąd, połączenie zostanie pomyślnie nawiązane. Szybki test polega na uruchomieniu polecenia cmdlet usługi Office `Get-MsolUser`365, na przykład, i zobacz wyniki. Jeśli są wyświetlane błędy, należy pamiętać, że częstym problemem jest nieprawidłowe hasło.

>[!NOTE]
>Można również użyć modułu AzureRM lub modułu Az, aby połączyć się z usługą Azure AD z subskrypcji usługi Office 365. Głównym poleceniem cmdlet połączenia jest [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). To polecenie cmdlet obsługuje `AzureEnvironmentName` parametr dla określonych środowisk usługi Office 365.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Tworzenie uruchomienia programu PowerShell z istniejącego skryptu

Dostęp do funkcji usługi Office 365 ze skryptu programu PowerShell. Oto przykład skryptu dla poświadczeń o nazwie `Office-Credentials` o nazwie `admin@TenantOne.com`. Służy `Get-AutomationPSCredential` do importowania poświadczeń usługi Office 365.

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>Uruchamianie skryptu w uruchomieniu

Skryptu można użyć w uruchomieniu usługi Azure Automation. Na przykład cele użyjemy typu uruchomieniu programu PowerShell.

1. Utwórz nowy projekt runbook programu PowerShell. Zapoznaj się [z temat tworzenie wiązki uruchomieniu usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Z konta automatyzacji wybierz pozycję **Runbooks** w obszarze **Automatyzacja procesów**.
3. Zaznacz nowy projekt runbook i kliknij przycisk **Edytuj**.
4. Skopiuj skrypt i wklej go do edytora tekstu dla życiora.
5. Wybierz **pozycję ZASOBY**, a następnie rozwiń pozycję **Poświadczenia** i sprawdź, czy poświadczenia usługi Office 365 są dostępne.
6. Kliknij przycisk **Zapisz**.
7. Wybierz **okienko testu**, a następnie kliknij przycisk **Start,** aby rozpocząć testowanie elementu runbook. Zobacz [Zarządzanie podręcznikami w usłudze Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. Po zakończeniu testowania wyjdź z okienka testowego.

## <a name="publishing-and-scheduling-the-runbook"></a>Publikowanie i planowanie likmy

Aby opublikować, a następnie zaplanować swój system runbook, zobacz [Zarządzanie podręcznikami w usłudze Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Następne kroki

* Informacje o zasobach poświadczeń automatyzacji można znaleźć w [zasobach poświadczeń w usłudze Azure Automation](shared-resources/credentials.md).
* Zobacz [Zarządzanie modułami w usłudze Azure Automation,](shared-resources/modules.md) aby dowiedzieć się, jak pracować z modułami automatyzacji.
* Aby zapoznać się z omówieniem zarządzania elementami [runbook, zobacz Zarządzanie elementami runbook w usłudze Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
* Aby dowiedzieć się więcej na temat metod, które mogą służyć do uruchamiania uruchomieniu w usłudze Azure Automation, zobacz [Uruchamianie uruchomieniu w usłudze Azure Automation.](automation-starting-a-runbook.md)
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów referencyjnych i modułów szkoleniowych, zobacz [Dokumenty programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).