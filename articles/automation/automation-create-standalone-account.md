---
title: Tworzenie autonomicznego konta usługi Azure Automation
description: W tym artykule przedstawiono kroki tworzenia, testowania i używania przykładowego uwierzytelniania podmiotu zabezpieczeń w Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0dcfcfe5bc6e59eeb4ccb7272ed3f68edc9c4172
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850401"
---
# <a name="create-a-standalone-azure-automation-account"></a>Tworzenie autonomicznego konta usługi Azure Automation

W tym artykule opisano sposób tworzenia konta Azure Automation w Azure Portal. Korzystając z konta usługi Automation w portalu, można oszacować i poznać automatyzację bez używania dodatkowych rozwiązań do zarządzania ani integracji z dziennikami Azure Monitor. Możesz dodać te rozwiązania do zarządzania lub zintegrować je z dziennikami Azure Monitor w celu zaawansowanego monitorowania zadań elementu Runbook w dowolnym momencie w przyszłości.

Za pomocą konta usługi Automation można uwierzytelniać elementy Runbook przez zarządzanie zasobami w ramach jednego Azure Resource Manager lub klasycznego modelu wdrażania. Jedno konto usługi Automation może zarządzać zasobami we wszystkich regionach i subskrypcjach dla danej dzierżawy.

Po utworzeniu konta usługi Automation w Azure Portal te konta są tworzone automatycznie:

* **Konto Uruchom jako**. To konto wykonuje następujące zadania:
  * Tworzy nazwę główną usługi w Azure Active Directory (Azure AD).
  * Tworzy certyfikat.
  * Przypisuje Access Control opartych na rolach współautor (RBAC), które zarządzają Azure Resource Manager zasobami przy użyciu elementów Runbook.

Dzięki tym kontom można szybko rozpocząć Kompilowanie i wdrażanie elementów Runbook w celu obsługi Twoich potrzeb dotyczących automatyzacji.

## <a name="permissions-required-to-create-an-automation-account"></a>Uprawnienia wymagane do utworzenia konta usługi Automation

Aby utworzyć lub zaktualizować konto usługi Automation oraz wykonać zadania opisane w tym artykule, musisz mieć następujące uprawnienia i uprawnienia:

* Aby można było utworzyć konto usługi Automation, konto użytkownika usługi Azure AD musi zostać dodane do roli z uprawnieniami odpowiadającymi roli właściciela dla **firmy Microsoft. Zasoby usługi Automation** . Aby uzyskać więcej informacji, zobacz [Access Control oparte na rolach w Azure Automation](automation-role-based-access-control.md).
* W Azure Portal w obszarze **Azure Active Directory** > **zarządzanie** **ustawieniami użytkownika** > , jeśli **rejestracje aplikacji** ma wartość **tak**, użytkownicy niebędący administratorami w dzierżawie usługi Azure AD mogą [rejestrować aplikacje Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Jeśli **rejestracje aplikacji** jest ustawiona na wartość **nie**, użytkownik wykonujący tę akcję musi być administratorem globalnym w usłudze Azure AD.

Jeśli nie jesteś członkiem wystąpienia Active Directory subskrypcji przed dodaniem do roli administratora globalnego/współadministratora subskrypcji, dodasz Cię do Active Directory jako gość. W tym scenariuszu na stronie **Dodawanie konta usługi Automation** zostanie wyświetlony następujący komunikat: "nie masz uprawnień do utworzenia".

Jeśli użytkownik zostanie najpierw dodany do roli administratora globalnego/współadministratora, można usunąć go z wystąpienia Active Directory subskrypcji, a następnie odczytywać je do roli pełna rola użytkownika w programie Active Directory.

Aby sprawdzić role użytkowników:

1. W Azure Portal przejdź do okienka **Azure Active Directory** .
1. Wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Wszyscy użytkownicy**.
1. Po wybraniu określonego użytkownika wybierz pozycję **profil**. Wartość atrybutu **typu użytkownika** w profilu użytkownika nie powinna być **gościem**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Utwórz nowe konto usługi Automation w Azure Portal

Aby utworzyć konto Azure Automation w Azure Portal, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.
1. Wybierz pozycję **+ Utwórz zasób**.
1. Wyszukaj **automatyzację**. W wynikach wyszukiwania wybierz pozycję **Automatyzacja**.

   ![Wyszukaj i wybierz Automation & Control w portalu Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na następnym ekranie wybierz pozycję **Utwórz**.

   ![Dodawanie konta usługi Automation](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Jeśli w okienku **Dodawanie konta usługi Automation** zostanie wyświetlony następujący komunikat, Twoje konto nie jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.
   >
   > ![Ostrzeżenie dotyczące dodawania konta usługi Automation](media/automation-create-standalone-account/create-account-without-perms.png)

1. W okienku **Dodawanie konta usługi Automation** w polu **Nazwa** wprowadź nazwę nowego konta usługi Automation. Tej nazwy nie można zmienić po jej wybraniu. *Nazwy kont usługi Automation są unikatowe dla poszczególnych regionów i grup zasobów. Nazwy kont usługi Automation, które zostały usunięte, mogą nie być od razu dostępne.*
1. Jeśli masz więcej niż jedną subskrypcję, w polu **subskrypcja** Określ subskrypcję, której chcesz użyć dla nowego konta.
1. W obszarze **Grupa zasobów**wprowadź lub wybierz nową lub istniejącą grupę zasobów.
1. W obszarze **Lokalizacja**wybierz lokalizację centrum danych platformy Azure.
1. Upewnij **się, że opcja** **Utwórz konto Uruchom jako platformy Azure** jest zaznaczona, a następnie wybierz pozycję **Utwórz**.

   > [!NOTE]
   > Jeśli zdecydujesz się nie tworzyć konta Uruchom jako, wybierając pozycję **nie** , aby **utworzyć konto Uruchom jako platformy Azure**, w okienku **Dodawanie konta usługi Automation** zostanie wyświetlony komunikat. Mimo że konto jest tworzone w Azure Portal, konto nie ma odpowiedniej tożsamości uwierzytelniania w ramach subskrypcji klasycznego modelu wdrażania lub subskrypcji Azure Resource Manager usługi katalogowej. W związku z tym konto usługi Automation nie ma dostępu do zasobów w Twojej subskrypcji. Dzięki temu wszystkie elementy Runbook odwołujące się do tego konta nie będą mogły uwierzytelniać i wykonywać zadań względem zasobów w tych modelach wdrożenia.
   >
   > ![Ostrzeżenie dotyczące dodawania konta usługi Automation](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Gdy nie zostanie utworzona jednostka usługi, rola współautor nie jest przypisana.
   >

1. Aby śledzić postęp tworzenia konta usługi Automation, w menu wybierz pozycję **powiadomienia**.

### <a name="resources-included"></a>Zasoby dołączone

Po pomyślnym utworzeniu konta usługi Automation automatycznie zostanie utworzonych kilka zasobów. Po utworzeniu te elementy Runbook można bezpiecznie usunąć, jeśli nie chcesz ich zachować. Konta Uruchom jako mogą służyć do uwierzytelniania na koncie w elemencie Runbook i powinny pozostać bez względu na to, czy nie zostanie utworzona inna lub nie jest wymagana. Poniższa tabela zawiera podsumowanie zasobów dla konta Uruchom jako.

| Zasób | Opis |
| --- | --- |
| AzureAutomationTutorial Runbook |Przykładowy graficzny element Runbook, który demonstruje sposób uwierzytelniania przy użyciu konta Uruchom jako. Element Runbook pobiera wszystkie zasoby Menedżer zasobów. |
| AzureAutomationTutorialScript Runbook |Przykładowy element Runbook programu PowerShell, który demonstruje sposób uwierzytelniania przy użyciu konta Uruchom jako. Element Runbook pobiera wszystkie zasoby Menedżer zasobów. |
| Element runbook AzureAutomationTutorialPython2 |Przykładowy element Runbook języka Python, który demonstruje sposób uwierzytelniania przy użyciu konta Uruchom jako. Element Runbook zawiera listę wszystkich grup zasobów obecnych w subskrypcji. |
| AzureRunAsCertificate |Zasób certyfikatu tworzony automatycznie podczas tworzenia konta usługi Automation lub przy użyciu skryptu programu PowerShell dla istniejącego konta. Certyfikat jest uwierzytelniany na platformie Azure, aby można było zarządzać zasobami Azure Resource Manager z elementów Runbook. Ten certyfikat ma roczny okres obowiązywania. |
| AzureRunAsConnection |Zasób połączenia, który jest tworzony automatycznie podczas tworzenia konta usługi Automation lub przy użyciu skryptu programu PowerShell dla istniejącego konta. |

## <a name="classic-run-as-accounts"></a>Klasyczne konta Uruchom jako

Klasyczne konta Uruchom jako nie są już tworzone, domyślnie podczas tworzenia konta Azure Automation. Jeśli nadal potrzebujesz klasycznego konta Uruchom jako, wykonaj następujące czynności.

1. Na stronie **konto usługi Automation** wybierz pozycję **konta Uruchom jako** w obszarze **Ustawienia konta**.
2. Wybierz **klasyczne konto Uruchom jako platformy Azure**.
3. Kliknij przycisk **Utwórz** , aby kontynuować tworzenie klasycznego konta Uruchom jako.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tworzeniu graficznym, zobacz [Tworzenie graficzne w Azure Automation](automation-graphical-authoring-intro.md).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby rozpocząć pracę z elementami runbook przepływu pracy programu PowerShell, zobacz [Mój pierwszy element runbook przepływu pracy programu PowerShell](automation-first-runbook-textual.md).
* Aby rozpocząć pracę z elementami Runbook programu Python2, zobacz [Mój pierwszy element Runbook programu Python2](automation-first-runbook-textual-python2.md).

