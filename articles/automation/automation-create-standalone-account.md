---
title: Tworzenie autonomicznego konta usługi Azure Automation
description: Ten artykuł zawiera opis kroków tworzenia, testowania i przy użyciu uwierzytelniania podmiotu zabezpieczeń do przykładu w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cdffc339bee1f5456e4eeb619e566b1f9c34b143
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61076797"
---
# <a name="create-a-standalone-azure-automation-account"></a>Tworzenie autonomicznego konta usługi Azure Automation

W tym artykule pokazano, jak utworzyć konto usługi Azure Automation w witrynie Azure portal. Aby ocenić i Dowiedz się więcej o automatyzacji bez korzystania z dodatkowych rozwiązań do zarządzania i integracji przy użyciu dzienników usługi Azure Monitor, można użyć portalu konta usługi Automation. Można dodać te rozwiązania do zarządzania lub zintegrować przy użyciu dzienników usługi Azure Monitor w przypadku zaawansowanego monitorowania zadań elementów runbook w dowolnym momencie w przyszłości.

Za pomocą konta usługi Automation można uwierzytelniać elementy runbook, umożliwiając zarządzanie zasobami w usłudze Azure Resource Manager lub Klasyczny model wdrażania. Jedno konto usługi Automation może zarządzać zasobami we wszystkich regionach i subskrypcjach dla danej dzierżawy.

Po utworzeniu konta usługi Automation w witrynie Azure portal te konta są tworzone automatycznie:

* **Konto Uruchom jako**. To konto wykonuje następujące zadania:
  * Tworzy nazwę główną usługi w usłudze Azure Active Directory (Azure AD).
  * Tworzy certyfikat.
  * Przypisuje Contributor Role-Based kontroli dostępu (RBAC), która zarządza zasobami usługi Azure Resource Manager przy użyciu elementów runbook.
* **Klasyczne konto Uruchom jako**. To konto służy do przekazywania certyfikatu zarządzania. Certyfikat zarządzania klasycznymi zasobami przy użyciu elementów runbook.

Z tych kont jest utworzony można szybko rozpocząć tworzenie i wdrażanie elementów runbook dla różnych potrzeb automatyzacji.

## <a name="permissions-required-to-create-an-automation-account"></a>Uprawnienia wymagane do utworzenia konta usługi Automation

Aby utworzyć lub zaktualizować konto usługi Automation i wykonać zadania opisane w tym artykule, musi mieć następujące uprawnienia i uprawnienia:

* Aby utworzyć konto usługi Automation, Twoje konto użytkownika usługi Azure AD należy dodać do roli z uprawnieniami odpowiadającymi roli właściciela dla **firmy Microsoft. Automatyzacja** zasobów. Aby uzyskać więcej informacji, zobacz [opartej na rolach kontrola dostępu w usłudze Azure Automation](automation-role-based-access-control.md).
* W witrynie Azure portal w obszarze **usługi Azure Active Directory** > **ZARZĄDZAJ** > **rejestracje aplikacji**, jeśli **rejestracje aplikacji**  ustawiono **tak**, użytkownicy niebędący administratorami w Twojej dzierżawie usługi Azure AD mogą [rejestrować aplikacje usługi Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Jeśli **rejestracje aplikacji** ustawiono **nie**, użytkownik, który wykonuje tę akcję musi być administratorem globalnym w usłudze Azure AD.

Jeśli nie jesteś członkiem wystąpienia usługi Active Directory subskrypcji przed dodaniem do roli administratora globalnego/współadministrator subskrypcji, są dodawane do usługi Active Directory jako gościa. W tym scenariuszu ten komunikat jest wyświetlany na **Dodawanie konta usługi Automation** strony: "Nie masz uprawnień do utworzenia."

Jeśli użytkownik został dodany do roli administratora globalnego/współadministrator najpierw należy można je usunąć z wystąpienia usługi Active Directory dla subskrypcji i ich je do pełnego roli użytkownika w usłudze Active Directory.

Aby sprawdzić, role użytkowników:

1. W witrynie Azure portal przejdź do **usługi Azure Active Directory** okienka.
1. Wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz **wszyscy użytkownicy**.
1. Po wybraniu określonego użytkownika, wybierz opcję **profilu**. Wartość **typ użytkownika** atrybutu w ramach profilu użytkownika nie powinny być **gościa**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Utwórz nowe konto usługi Automation w witrynie Azure portal

Aby utworzyć konto usługi Azure Automation w witrynie Azure portal, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal przy użyciu konta będącego członkiem roli Administratorzy subskrypcji i współadministrator subskrypcji.
1. Wybierz **+ Utwórz zasób**.
1. Wyszukaj **automatyzacji**. W wynikach wyszukiwania wybierz **automatyzacji**.

   ![Wyszukaj i wybierz pozycję Automation & Control w witrynie Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na następnym ekranie Wybierz **Utwórz**.

   ![Dodawanie konta usługi Automation](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Jeśli zostanie wyświetlony następujący komunikat w **Dodawanie konta usługi Automation** okienko, Twoje konto nie jest członkiem roli Administratorzy subskrypcji i współadministrator subskrypcji.
   >
   > ![Dodaj ostrzeżenie konta usługi Automation](media/automation-create-standalone-account/create-account-without-perms.png)

1. W **Dodawanie konta usługi Automation** okienku w obszarze **nazwa** wprowadź nazwę dla nowego konta usługi Automation. Nie można zmienić tę nazwę, po jego wybraniu. *Nazwy kont usługi Automation są unikatowe w każdym regionie i grupie zasobów. Nazwy kont usługi Automation, które zostały usunięte, mogą nie być natychmiast dostępne.*
1. Jeśli masz więcej niż jedną subskrypcję w **subskrypcji** określ subskrypcję, dla którego chcesz użyć dla nowego konta.
1. Aby uzyskać **grupy zasobów**wprowadź lub wybierz grupę zasobów do nowej lub istniejącej.
1. Aby uzyskać **lokalizacji**, wybierz taką lokalizację centrum danych platformy Azure.
1. Aby uzyskać **Tworzenie konta Uruchom jako platformy** opcji, upewnij się, że **tak** jest wybrany, a następnie wybierz **Utwórz**.

   > [!NOTE]
   > Jeśli nie chcesz utworzyć konto Uruchom jako, wybierając **nie** dla **Tworzenie konta Uruchom jako platformy**, zostanie wyświetlony komunikat w **Dodawanie konta usługi Automation** okienka. Mimo że konto zostanie utworzone w witrynie Azure portal, to konto nie ma odpowiedniej tożsamości uwierzytelniania w ramach subskrypcji w modelu wdrożenia klasycznego lub w usłudze katalogowej subskrypcji usługi Azure Resource Manager. W związku z tym konto usługi Automation nie ma dostępu do zasobów w ramach subskrypcji. Zapobiega to wszelkie elementy runbook odwołujące się do tego konta miałyby możliwość uwierzytelniania i wykonywania zadań w odniesieniu do zasobów w tych modelach wdrożenia.
   >
   > ![Dodaj ostrzeżenie konta usługi Automation](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Gdy nie została utworzona nazwa główna usługi, nie jest przypisany do roli współautor.
   >

1. Aby śledzić postęp tworzenia konta automatyzacji w menu, wybierz **powiadomienia**.

### <a name="resources-included"></a>Zasoby dołączone

Po pomyślnym utworzeniu konta usługi Automation automatycznie zostanie utworzonych kilka zasobów. Po utworzeniu tych elementów runbook można bezpiecznie usunąć, jeśli nie chcesz zachować. Konta Uruchom jako, może być używany do uwierzytelniania do swojego konta w elemencie runbook, a powinien pozostać, chyba że Utwórz inny i nie wymagają je. Poniższa tabela zawiera podsumowanie zasobów dla konta Uruchom jako.

| Resource | Opis |
| --- | --- |
| AzureAutomationTutorial Runbook |Przykładowy graficzny element runbook, który demonstruje sposób uwierzytelniania przy użyciu konta Uruchom jako. Element runbook pobiera wszystkie zasoby usługi Resource Manager. |
| AzureAutomationTutorialScript Runbook |Przykładowy element runbook programu PowerShell, który demonstruje sposób uwierzytelniania przy użyciu konta Uruchom jako. Element runbook pobiera wszystkie zasoby usługi Resource Manager. |
| Element runbook AzureAutomationTutorialPython2 |Przykładowy element runbook Python, który demonstruje sposób uwierzytelniania przy użyciu konta Uruchom jako. Element runbook zawiera listę wszystkich grup zasobów obecnych w subskrypcji. |
| AzureRunAsCertificate |Zasób certyfikat utworzony automatycznie po utworzeniu konta usługi Automation lub za pomocą skryptu programu PowerShell dla istniejącego konta. Certyfikat uwierzytelnia się za pomocą platformy Azure, aby można było zarządzać zasobami usługi Azure Resource Manager, z poziomu elementów runbook. Ten certyfikat ma roczny okres obowiązywania. |
| AzureRunAsConnection |Zasób połączenia, które są tworzone automatycznie podczas tworzenia konta usługi Automation lub za pomocą skryptu programu PowerShell dla istniejącego konta. |

Poniższa tabela zawiera podsumowanie zasobów dla klasycznego konta Uruchom jako.

| Resource | Opis |
| --- | --- |
| AzureClassicAutomationTutorial Runbook |Przykładowy graficzny element runbook. Element runbook pobiera wszystkie klasyczne maszyny wirtualne w subskrypcji przy użyciu klasycznego konta Uruchom jako (certyfikatu). Następnie wyświetla nazwy maszyn wirtualnych i stan. |
| AzureClassicAutomationTutorial Script Runbook |Przykładowy element runbook programu PowerShell. Element runbook pobiera wszystkie klasyczne maszyny wirtualne w subskrypcji przy użyciu klasycznego konta Uruchom jako (certyfikatu). Następnie wyświetla nazwy maszyn wirtualnych i stan. |
| AzureClassicRunAsCertificate |Zasób certyfikatu, które są tworzone automatycznie. Certyfikat uwierzytelnia się za pomocą platformy Azure, co umożliwia zarządzanie klasycznymi zasobami platformy Azure z poziomu elementów runbook. Ten certyfikat ma roczny okres obowiązywania. |
| AzureClassicRunAsConnection |Zasób połączenia, które są tworzone automatycznie. Element zawartości uwierzytelnia się za pomocą platformy Azure, więc można zarządzać klasycznymi zasobami platformy Azure z poziomu elementów runbook. |

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat tworzenia elementów graficznych, zobacz [tworzenia elementów graficznych w usłudze Azure Automation](automation-graphical-authoring-intro.md).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby rozpocząć pracę z elementami runbook przepływu pracy programu PowerShell, zobacz [Mój pierwszy element runbook przepływu pracy programu PowerShell](automation-first-runbook-textual.md).
* Aby rozpocząć pracę z elementami Runbook programu Python2, zobacz [Mój pierwszy element Runbook programu Python2](automation-first-runbook-textual-python2.md).

