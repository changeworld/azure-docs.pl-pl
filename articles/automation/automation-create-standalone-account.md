---
title: Tworzenie autonomicznego konta usługi Azure Automation
description: W tym artykule otrzymasz od kroków tworzenia, testowania i używania przykładowego uwierzytelniania głównego zabezpieczeń w usłudze Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: 22efb5e94049b975780c6f6ea69aa94a71cc9992
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279015"
---
# <a name="create-a-standalone-azure-automation-account"></a>Tworzenie autonomicznego konta usługi Azure Automation

W tym artykule pokazano, jak utworzyć konto usługi Azure Automation w witrynie Azure portal. Za pomocą konta automatyzacji portalu można ocenić i dowiedzieć się więcej o automatyzacji bez korzystania z dodatkowych rozwiązań do zarządzania lub integracji z dziennikami usługi Azure Monitor. Można dodać te rozwiązania do zarządzania lub zintegrować z dziennikami usługi Azure Monitor w celu zaawansowanego monitorowania zadań uruchomieniu w dowolnym momencie w przyszłości.

Za pomocą konta automatyzacji można uwierzytelniać elementy runbook, zarządzając zasobami w usłudze Azure Resource Manager lub w klasycznym modelu wdrażania. Jedno konto usługi Automation może zarządzać zasobami we wszystkich regionach i subskrypcjach dla danej dzierżawy.

Podczas tworzenia konta automatyzacji w witrynie Azure portal, te konta są tworzone automatycznie:

* **Uruchom jako konto**. To konto wykonuje następujące zadania:
  * Tworzy jednostkę usługi w usłudze Azure Active Directory (Azure AD).
  * Tworzy certyfikat.
  * Przypisuje formant oparty na roli współautora kontroli dostępu (RBAC), który zarządza zasobami usługi Azure Resource Manager przy użyciu wiązki uruchomieniu. wiązaków.

Dzięki tym kontom utworzonym dla Ciebie można szybko rozpocząć tworzenie i wdrażanie śmigieł, aby zaspokoić potrzeby automatyzacji.

## <a name="permissions-required-to-create-an-automation-account"></a>Uprawnienia wymagane do utworzenia konta automatyzacji

Aby utworzyć lub zaktualizować konto automatyzacji i wykonać zadania opisane w tym artykule, musisz mieć następujące uprawnienia i uprawnienia:

* Aby utworzyć konto automatyzacji, konto użytkownika usługi Azure AD musi zostać dodane do roli z uprawnieniami równoważnymi roli Właściciel dla **firmy Microsoft. Zasoby automatyzacji.** Aby uzyskać więcej informacji, zobacz [kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).
* W witrynie Azure portal w obszarze**Ustawienia użytkownika**usługi Azure Active Directory**MANAGE,** > jeśli **rejestracja aplikacji** jest ustawiona na **Tak,** **użytkownicy** > niebędący administratorami w dzierżawie usługi Azure AD mogą [rejestrować aplikacje usługi Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Jeśli **rejestracje aplikacji** jest ustawiona na **Nie**, użytkownik, który wykonuje tę akcję musi być administratorem globalnym w usłudze Azure AD.

Jeśli nie jesteś członkiem wystąpienia usługi Active Directory subskrypcji przed dodaniem do roli globalnego administratora/współadministratora subskrypcji, użytkownik zostanie dodany do usługi Active Directory jako gość. W tym scenariuszu zostanie wyświetlony ten komunikat na stronie **Dodaj konto automatyzacji:** "Nie masz uprawnień do tworzenia".

Jeśli użytkownik zostanie najpierw dodany do roli administratora globalnego/współadministratora, można go usunąć z wystąpienia usługi Active Directory subskrypcji, a następnie odczytać je do pełnej roli użytkownika w usłudze Active Directory.

Aby zweryfikować role użytkowników:

1. W witrynie Azure portal przejdź do okienka **usługi Azure Active Directory.**
1. Wybierz **pozycję Użytkownicy i grupy**.
1. Wybierz **pozycję Wszyscy użytkownicy**.
1. Po wybraniu określonego użytkownika wybierz pozycję **Profil**. Wartość atrybutu **Typu Użytkownika** w profilu użytkownika nie powinna być **guest**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Tworzenie nowego konta automatyzacji w witrynie Azure portal

Aby utworzyć konto usługi Azure Automation w witrynie Azure portal, wykonaj następujące kroki:

1. Zaloguj się do witryny Azure portal przy za pomocą konta, które jest członkiem roli administratorów subskrypcji i współadministratorem subskrypcji.
1. Wybierz **+ Utwórz zasób**.
1. Wyszukaj **automatyzację**. W wynikach wyszukiwania wybierz pozycję **Automatyzacja**.

   ![Wyszukiwanie i wybieranie funkcji Automatyzacja & control w portalu Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na następnym ekranie wybierz pozycję **Utwórz**.

   ![Dodaj konto automatyzacji](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Jeśli w okienku **Dodaj konto automatyzacji** zostanie wyświetlony następujący komunikat, twoje konto nie jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.
   >
   > ![Ostrzeżenie o dodaniu konta automatyzacji](media/automation-create-standalone-account/create-account-without-perms.png)

1. W okienku **Dodawanie konta automatyzacji** w polu **Nazwa** wprowadź nazwę nowego konta automatyzacji. Tej nazwy nie można zmienić po jej wybraniu. *Nazwy kont automatyzacji są unikatowe dla regionu i grupy zasobów. Nazwy kont automatyzacji, które zostały usunięte, mogą nie być natychmiast dostępne.*
1. Jeśli masz więcej niż jedną subskrypcję, w polu **Subskrypcja** określ subskrypcję, której chcesz użyć dla nowego konta.
1. W obszarze **Grupa zasobów**wprowadź lub wybierz nową lub istniejącą grupę zasobów.
1. W obszarze **Lokalizacja**wybierz lokalizację centrum danych platformy Azure.
1. W przypadku opcji **Utwórz konto Azure Run Jako** upewnij się, że jest zaznaczona opcja **Tak,** a następnie wybierz pozycję **Utwórz**.

   > [!NOTE]
   > Jeśli nie chcesz tworzyć konta Uruchom jako, wybierając **pozycję Nie** dla **konta Utwórz usługę Azure Uruchom jako,** w okienku **Dodaj konto automatyzacji** pojawi się komunikat. Mimo że konto jest tworzone w witrynie Azure portal, konto nie ma odpowiedniej tożsamości uwierzytelniania w klasycznej subskrypcji modelu wdrażania lub w usłudze katalogowej subskrypcji usługi Azure Resource Manager. W związku z tym konto automatyzacji nie ma dostępu do zasobów w ramach subskrypcji. Zapobiega to wszelkie runbooks, które odwołują się do tego konta jest w stanie uwierzytelnić i wykonywać zadania względem zasobów w tych modelach wdrażania.
   >
   > ![Ostrzeżenie o dodaniu konta automatyzacji](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Gdy podmiot usługi nie jest tworzony, rola współautora nie jest przypisana.
   >

1. Aby śledzić postęp tworzenia konta automatyzacji, w menu wybierz **pozycję Powiadomienia**.

### <a name="resources-included"></a>Zasoby dołączone

Po pomyślnym utworzeniu konta usługi Automation automatycznie zostanie utworzonych kilka zasobów. Po utworzeniu te elementy runbook można bezpiecznie usunąć, jeśli nie chcesz ich przechowywać. Uruchom jako konta, może służyć do uwierzytelniania do konta w zestawie runbook i powinny być pozostawione, chyba że utworzysz inny lub nie wymagają ich. Poniższa tabela zawiera podsumowanie zasobów dla konta Uruchom jako.

| Zasób | Opis |
| --- | --- |
| AzureAutomationTutorial Runbook |Przykładowy element runbook graficzny, który pokazuje, jak uwierzytelniać za pomocą konta Uruchom jako. Program runbook pobiera wszystkie zasoby Menedżera zasobów. |
| AzureAutomationTutorialScript Runbook |Przykładowy element runbook programu PowerShell, który pokazuje, jak uwierzytelnić przy użyciu uruchom jako konto. Program runbook pobiera wszystkie zasoby Menedżera zasobów. |
| Element runbook AzureAutomationTutorialPython2 |Przykładowy element runbook języka Python, który pokazuje, jak uwierzytelnić przy użyciu uruchom jako konto. Podręcznik zawiera listę wszystkich grup zasobów znajdujących się w subskrypcji. |
| AzureRunAsCertificate |Zasób certyfikatu, który jest tworzony automatycznie podczas tworzenia konta automatyzacji lub przy użyciu skryptu programu PowerShell dla istniejącego konta. Certyfikat uwierzytelnia się za pomocą platformy Azure, dzięki czemu można zarządzać zasobami usługi Azure Resource Manager z uruchomieniu. ścieliń. Ten certyfikat ma roczny okres obowiązywania. |
| AzureRunAsConnection |Zasób połączenia, który jest tworzony automatycznie podczas tworzenia konta automatyzacji lub przy użyciu skryptu programu PowerShell dla istniejącego konta. |

## <a name="classic-run-as-accounts"></a>Klasyczne konta run-as

Klasyczne konta run-as nie są już tworzone domyślnie podczas tworzenia konta usługi Azure Automation. Jeśli nadal potrzebujesz klasycznego konta Run-As, wykonaj następujące czynności.

1. Na stronie **Konto automatyzacji** wybierz pozycję **Uruchom jako konta** w obszarze Ustawienia **konta**.
2. Wybierz **opcję Azure Classic Uruchom jako konto**.
3. Kliknij **przycisk Utwórz,** aby przejść do tworzenia konta Uruchom klasycznie jako.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tworzenie grafiki, zobacz [Tworzenie graficzne w usłudze Azure Automation](automation-graphical-authoring-intro.md).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby rozpocząć pracę z uruchomieniu przepływem pracy programu PowerShell, zobacz [Mój pierwszy program PowerShell .](automation-first-runbook-textual.md)
* Aby rozpocząć pracę z elementami Runbook programu Python2, zobacz [Mój pierwszy element Runbook programu Python2](automation-first-runbook-textual-python2.md).

