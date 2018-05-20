---
title: Tworzenie autonomicznego konta usługi Azure Automation
description: W tym artykule przedstawiono kroki tworzenia, testowania i przy użyciu przykład zabezpieczeń podmiotu zabezpieczeń uwierzytelniania w usłudze Automatyzacja Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 77d766358135f5cdabfe7e9b62e54513c2604ed3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="create-a-standalone-azure-automation-account"></a>Tworzenie autonomicznego konta usługi Azure Automation

W tym artykule przedstawiono sposób utworzyć konto usługi Automatyzacja Azure w portalu Azure. Portal konta automatyzacji można użyć do oceny i więcej informacji na temat automatyzacji bez korzystania z rozwiązania do zarządzania dodatkowe i integracja z usługi Analiza dzienników Azure. Można dodać te rozwiązania do zarządzania lub zintegrować z analizy dzienników dla zaawansowanego monitorowania zadań elementu runbook w dowolnym momencie w przyszłości.

Przy użyciu konta automatyzacji elementów runbook mogą uwierzytelniać za zarządzanie zasobami w usłudze Azure Resource Manager lub w klasycznym modelu wdrażania.

Podczas tworzenia konta automatyzacji w portalu Azure, te konta są tworzone automatycznie:

* **Konto Uruchom jako**. To konto wykonuje następujące zadania:
  * Tworzy nazwy głównej usługi w usłudze Azure Active Directory (Azure AD).
  * Tworzy certyfikat.
  * Przypisuje Contributor Role-Based kontroli dostępu (RBAC), która zarządza zasobów usługi Azure Resource Manager za pomocą elementów runbook.
* **Klasycznym konta Uruchom jako**. To konto będzie przekazywać certyfikatu zarządzania. Certyfikat zarządza zasoby klasyczne przy użyciu elementów runbook.

Tych kont tworzona można szybko rozpocząć tworzenia i wdrażania elementów runbook do różnych potrzeb automatyzacji.

## <a name="permissions-required-to-create-an-automation-account"></a>Uprawnienia wymagane do tworzenia konta automatyzacji

Utwórz lub zaktualizuj konto usługi Automatyzacja i wykonać zadania opisane w tym artykule, musi mieć następujące uprawnienia i uprawnienia:

* Aby utworzyć konto usługi Automatyzacja, Twoje konto użytkownika usługi Azure AD musi dodany do roli uprawnienia równorzędne do roli właściciela dla **firmy Microsoft. Automatyzacja** zasobów. Aby uzyskać więcej informacji, zobacz [opartej na rolach kontroli dostępu w usłudze Automatyzacja Azure](automation-role-based-access-control.md).
* W portalu Azure w obszarze **usługi Azure Active Directory** > **ZARZĄDZAJ** > **rejestracji aplikacji**, jeśli **rejestracji aplikacji**  ustawiono **tak**, użytkownicy inni niż administratorzy w Twojej dzierżawie usługi Azure AD można [rejestrowania aplikacji usługi Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Jeśli **rejestracji aplikacji** ustawiono **nr**, użytkownik, który dokonuje tego działania musi być administratorem globalnym w usłudze Azure AD.

Jeśli nie jesteś członkiem wystąpienia usługi Active Directory subskrypcji przed dodaniem do roli administratora globalnego/coadministrator subskrypcji, są dodawane do usługi Active Directory jako Gość. W tym scenariuszu ten komunikat zostanie wyświetlony na **Dodaj konto automatyzacji** strony: "Nie ma uprawnień do utworzenia."

Jeśli użytkownik zostanie dodany do roli administratora globalnego/coadministrator najpierw należy można je usunąć z wystąpienia usługi Active Directory dla tej subskrypcji, a potem ponownie dodaj je do pełnego roli użytkownika w usłudze Active Directory.

Aby sprawdzić role użytkowników:

1. W portalu Azure, przejdź do **usługi Azure Active Directory** okienka.
1. Wybierz **użytkowników i grup**.
1. Wybierz **wszyscy użytkownicy**.
1. Po wybraniu określonego użytkownika, wybierz **profilu**. Wartość **typ użytkownika** atrybutu w profilu użytkownika nie powinna być **gościa**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Utwórz nowe konto usługi Automatyzacja w portalu Azure

Aby utworzyć konto usługi Automatyzacja Azure w portalu Azure, wykonaj następujące kroki:

1. Zaloguj się do portalu Azure przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i coadministrator subskrypcji.
1. Wybierz **+ Utwórz zasób**.
1. Wyszukaj **automatyzacji**. W wynikach wyszukiwania wybierz **automatyzacji**.

   ![Wyszukaj i wybierz automatyzacji & kontroli w portalu Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na następnym ekranie Wybierz **Utwórz**.
  ![Dodaj konto automatyzacji](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

  > [!NOTE]
  > Jeśli zostanie wyświetlony następujący komunikat w **Dodaj konto automatyzacji** okienko, Twoje konto nie jest członkiem roli Administratorzy subskrypcji i coadministrator subskrypcji.
  >
  > ![Dodaj ostrzeżenie konta automatyzacji](media/automation-create-standalone-account/create-account-without-perms.png)
  >
1. W **Dodaj konto automatyzacji** okienko w **nazwa** wprowadź nazwę nowego konta automatyzacji.
1. Jeśli masz więcej niż jedną subskrypcję, w **subskrypcji** określ subskrypcję, którego chcesz użyć dla nowego konta.
1. Aby uzyskać **grupy zasobów**, wprowadź lub wybierz nowy lub istniejący zasób grupy.
1. Aby uzyskać **lokalizacji**, wybierz lokalizację centrum danych Azure.
1. Dla **tworzenia konta Uruchom jako Azure** , należy upewnić się, że **tak** jest wybrany, a następnie wybierz **Utwórz**.

  > [!NOTE]
  > Jeśli użytkownik nie chce utworzyć konto Uruchom jako, wybierając **nr** dla **tworzenia konta Uruchom jako Azure**, zostanie wyświetlony komunikat w **Dodaj konto automatyzacji** okienka. Mimo że konto jest tworzone w portalu Azure, konto nie ma odpowiedniego tożsamość uwierzytelniania w ramach subskrypcji modelu klasycznym wdrożenia lub w usłudze katalogowej subskrypcji usługi Azure Resource Manager. W związku z tym konta automatyzacji nie ma dostępu do zasobów w ramach subskrypcji. Dzięki temu wszystkie elementy runbook, odwołujący się tego konta mogą uwierzytelniać i wykonywać zadania dotyczące zasobów w tych modelach wdrażania.
  >
  > ![Dodaj ostrzeżenie konta automatyzacji](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
  >
  > Gdy nazwy głównej usługi nie jest tworzony, nie jest przypisana rola współautora.
  >

1. Aby śledzić postęp tworzenia konta automatyzacji, w menu, wybierz **powiadomienia**.

### <a name="resources-included"></a>Zasoby dołączone

Po pomyślnym utworzeniu konta usługi Automation automatycznie zostanie utworzonych kilka zasobów. Po utworzeniu elementy runbook mogą być bezpiecznie usunąć, jeśli nie chcesz, aby zachować. Konta Uruchom jako, można użyć do uwierzytelniania konta w elemencie runbook, a następnie należy pozostawić, jeśli nie możesz utworzyć nową lub ich nie wymagają. Poniższa tabela zawiera podsumowanie zasobów dla konta Uruchom jako.

| Zasób | Opis |
| --- | --- |
| AzureAutomationTutorial Runbook |Przykład graficzny element runbook, który demonstruje sposób uwierzytelniania przy użyciu konta Uruchom jako. Element runbook pobiera wszystkie zasoby usługi Resource Manager. |
| AzureAutomationTutorialScript Runbook |Element runbook programu PowerShell przykładzie, który demonstruje sposób uwierzytelniania przy użyciu konta Uruchom jako. Element runbook pobiera wszystkie zasoby usługi Resource Manager. |
| Element runbook AzureAutomationTutorialPython2 |Przykład Python element runbook, który demonstruje sposób uwierzytelniania przy użyciu konta Uruchom jako. Element runbook zawiera listę wszystkich grup zasobów w subskrypcji. |
| AzureRunAsCertificate |Zasób certyfikatu, który jest automatycznie tworzony podczas tworzenia konta automatyzacji lub za pomocą skryptu programu PowerShell dla istniejącego konta. Certyfikat uwierzytelnia z platformy Azure, więc zasoby usługi Azure Resource Manager można zarządzać z poziomu elementów runbook. Ten certyfikat ma roczny okres obowiązywania. |
| AzureRunAsConnection |Zasób połączenia, który jest automatycznie tworzony podczas tworzenia konta automatyzacji lub za pomocą skryptu programu PowerShell dla istniejącego konta. |

Poniższa tabela zawiera podsumowanie zasobów dla klasycznego konta Uruchom jako.

| Zasób | Opis |
| --- | --- |
| AzureClassicAutomationTutorial Runbook |Przykładowy graficzny element runbook. Element runbook pobiera wszystkie klasycznych maszyn wirtualnych w ramach subskrypcji przy użyciu klasycznego konto Uruchom jako (certyfikatu). Następnie wyświetla nazw maszyn wirtualnych i stanu. |
| AzureClassicAutomationTutorial Script Runbook |Przykładowy element runbook programu PowerShell. Element runbook pobiera wszystkie klasycznych maszyn wirtualnych w ramach subskrypcji przy użyciu klasycznego konto Uruchom jako (certyfikatu). Następnie wyświetla nazw maszyn wirtualnych i stanu. |
| AzureClassicRunAsCertificate |Zasób certyfikatu, który jest automatycznie tworzony. Certyfikat uwierzytelniany w usłudze Azure, Azure zasoby klasyczne można zarządzać z poziomu elementów runbook. Ten certyfikat ma roczny okres obowiązywania. |
| AzureClassicRunAsConnection |Zasób połączenia, które zostało automatycznie utworzone. Element zawartości uwierzytelniany w usłudze Azure, Azure zasoby klasyczne można zarządzać z poziomu elementów runbook. |

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat tworzenia graficznego, zobacz [tworzenia graficznego automatyzacji Azure](automation-graphical-authoring-intro.md).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby rozpocząć pracę z elementami runbook przepływu pracy programu PowerShell, zobacz [Mój pierwszy element runbook przepływu pracy programu PowerShell](automation-first-runbook-textual.md).
* Aby rozpocząć pracę z elementami Runbook programu Python2, zobacz [Mój pierwszy element Runbook programu Python2](automation-first-runbook-textual-python2.md).
