---
title: Integracja kontroli źródła usługi Azure Automation z usługą GitHub Enterprise
description: W tym artykule opisano szczegółowe informacje o sposobie konfigurowania integracji z usługą GitHub Enterprise do kontroli źródła, elementów runbook usługi Automation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8c7dc256b92252793545336ffc45a987054a5509
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096381"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Scenariusz automatyzacji platformy Azure — Integracja kontroli źródła usługi Automation z usługą GitHub Enterprise

Automatyzacja obecnie obsługuje integrację kontroli źródła, co pozwala na Kojarzenie elementów runbook na Twoim koncie usługi Automation do repozytorium kontroli źródła usługi GitHub. Jednak klienci, którzy wdrożyli [GitHub Enterprise](https://enterprise.github.com/home) do obsługi swoich praktyk DevOps, również ma on służyć do zarządzania cyklem życia elementów runbook, które są opracowywane automatyzować procesy biznesowe i obsługę operacji zarządzania.

W tym scenariuszu masz komputer Windows w centrum danych, skonfigurowany jako hybrydowy proces roboczy elementu Runbook z modułami usługi Azure Resource Manager i zainstalowane narzędzia Git. Maszyna procesu roboczego hybrydowego ma klonu lokalnego repozytorium Git. Po uruchomieniu elementu runbook w hybrydowym procesie roboczym jest zsynchronizowany katalog usługi Git, i zawartość pliku elementu runbook są importowane do konta usługi Automation.

W tym artykule opisano sposób konfigurowania tej konfiguracji w danym środowisku usługi Azure Automation. Możesz uruchomić Konfigurowanie usługi Automation przy użyciu poświadczeń zabezpieczeń wymaganych do obsługi tego scenariusza i wdrażania hybrydowego procesu roboczego elementu Runbook w centrum danych w celu uruchamiania elementów runbook i dostępu do repozytorium GitHub Enterprise, aby zsynchronizować elementy runbook elementów runbook za pomocą konta usługi Automation.

## <a name="getting-the-scenario"></a>Uzyskiwanie scenariusza

Ten scenariusz składa się z dwóch elementy runbook programu PowerShell, które można importować bezpośrednio z [galerii elementów Runbook](automation-runbook-gallery.md) w witrynie Azure portal lub Pobierz go z [galerii programu PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Elementy Runbook

Element Runbook | Opis|
--------|------------|
Export-RunAsCertificateToHybridWorker | Element Runbook wywozu certyfikatu RunAs z kontem usługi Automation, do hybrydowego procesu roboczego, tak aby elementów runbook w procesie roboczym mogą uwierzytelniać za pomocą platformy Azure w celu importowania elementów runbook na konto usługi Automation.|
LocalGitFolderToAutomationAccount synchronizacji | Element Runbook synchronizuje lokalny folder Git na komputerze hybrydowych, a następnie zaimportować pliki elementu runbook (*.ps1) na konto usługi Automation.|

### <a name="credentials"></a>Poświadczenia

Poświadczenie | Opis|
-----------|------------|
GitHRWCredential | Zasób poświadczenia, możesz utworzyć zawierać nazwy użytkownika i hasło dla użytkownika z uprawnieniami do hybrydowego procesu roboczego.|

## <a name="installing-and-configuring-this-scenario"></a>Instalowanie i konfigurowanie scenariusza

### <a name="prerequisites"></a>Wymagania wstępne

1. Synchronizacja LocalGitFolderToAutomationAccount elementu runbook jest uwierzytelniany przy użyciu [konta Uruchom jako platformy](automation-sec-configure-azure-runas-account.md).

2. Wymagany jest również obszar roboczy usługi Log Analytics za pomocą rozwiązania usługi Azure Automation, włączona i skonfigurowana. Jeśli nie masz, który jest skojarzony z konta usługi Automation umożliwiają instalowanie i konfigurowanie tego scenariusza, jest tworzony i skonfigurowane podczas wykonywania **New OnPremiseHybridWorker.ps1** skrypt z hybrydowego elementu runbook proces roboczy.

    > [!NOTE]
    > Obecnie w następujących regionach obsługują tylko automatyzacji integracji z usługą Log Analytics — **Australia południowo-wschodnia**, **wschodnie stany USA 2**, **Azja południowo-wschodnia**, i  **Europa Zachodnia**.

3. Komputer, który może służyć jako dedykowane hybrydowego procesu roboczego elementu Runbook obsługującego oprogramowania GitHub i obsługa plików elementu runbook (*runbook*.ps1) w katalogu źródłowym, w systemie plików, które można synchronizować między usługami GitHub i automatyzacji konto.

### <a name="import-and-publish-the-runbooks"></a>Importowanie i publikowanie elementów runbook

Aby zaimportować *RunAsCertificateToHybridWorker eksportu* i *LocalGitFolderToAutomationAccount synchronizacji* elementów runbook z galerii elementów Runbook z poziomu konta usługi Automation w witrynie Azure portal, postępuj zgodnie z procedury przedstawione w [importowania elementu Runbook, z galerii elementów Runbook](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal). Publikowanie elementów runbook, po ich zostały pomyślnie zaimportowane do konta usługi Automation.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Wdrażanie i konfigurowanie hybrydowego procesu roboczego Runbook

Jeśli nie masz hybrydowego procesu roboczego Runbook już wdrożone w centrum danych, zapoznaj się z wymaganiami i postępuj zgodnie z instrukcjami instalacji zautomatyzowanej za pomocą usługi Azure Automation hybrydowych procesów roboczych Runbook — automatyzowanie instalacji i konfiguracji dla procedury [Windows](automation-windows-hrw-install.md#automated-deployment) lub [Linux](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker). Po pomyślnym zainstalowaniu hybrydowy proces roboczy na komputerze, wykonaj następujące kroki, aby ukończyć konfigurację tak, aby umożliwić obsługę tego scenariusza.

1. Zaloguj się do komputera hostującego rolę hybrydowego procesu roboczego Runbook za pomocą konta z uprawnieniami administratora lokalnego, a następnie utwórz katalog do przechowywania plików elementu runbook usługi Git. Sklonuj z wewnętrznego repozytorium Git w katalogu.
1. Jeśli nie masz już utworzone konto Uruchom jako lub Utwórz nową jeden w wersji dedykowanej w tym celu, w witrynie Azure portal przejdź do konta usługi Automation wybierz konto usługi Automation i tworzenia [zasób poświadczeń](automation-credentials.md) , zawiera nazwę użytkownika i hasło dla użytkownika z uprawnieniami do hybrydowego procesu roboczego.
1. Na koncie usługi Automation [edytować element runbook](automation-edit-textual-runbook.md)**RunAsCertificateToHybridWorker eksportu** i zmodyfikować wartość zmiennej *$Password* silnym hasłem.    Po zmodyfikowaniu wartości kliknij przycisk **Publikuj** mieć wersję roboczą elementu runbook opublikowane.
1. Uruchamianie elementu runbook **RunAsCertificateToHybridWorker eksportu**, a następnie w **uruchamianie elementu Runbook** bloku, w obszarze opcji **parametrów uruchomieniowych** wybierz opcję  **Hybrydowy proces roboczy** i na liście rozwijanej wybierz utworzony wcześniej w tym scenariuszu grupy hybrydowych procesów roboczych.

    To umożliwia wyeksportowanie certyfikatu do hybrydowego procesu roboczego tak, aby elementy runbook na usługi procesu roboczego mogą uwierzytelniać za pomocą platformy Azure przy użyciu połączenie Uruchom jako, aby można było zarządzać zasobami platformy Azure (w szczególności w tym scenariuszu — Importowanie elementów runbook konta usługi Automation).

1. Na koncie usługi Automation wybierz utworzoną wcześniej grupę hybrydowych procesów roboczych i [Określ konto Uruchom jako](automation-hrw-run-runbooks.md#runas-account) dla grupy hybrydowych procesów roboczych i wybierz opcję Zasób poświadczeń, po prostu lub już został utworzony. Gwarantuje to, że synchronizacji elementu runbook można uruchomić polecenia Git. 
1. Uruchamianie elementu runbook **LocalGitFolderToAutomationAccount synchronizacji**, zapewniają następujące wymagane wartości parametrów wejściowych i **uruchamianie elementu Runbook** bloku, w obszarze opcji **parametrów uruchomieniowych**  wybierz opcję **hybrydowy proces roboczy** i na liście rozwijanej wybierz utworzony wcześniej w tym scenariuszu grupy hybrydowych procesów roboczych:

   * *Grupa zasobów* — Nazwa grupy zasobów skojarzonych z Twoim kontem usługi Automation
   * *AutomationAccountName* -nazwa konta usługi Automation
   * *GitPath* — lokalnego folderu lub pliku w hybrydowym procesie roboczym elementu Runbook, gdzie usługa Git jest skonfigurowana do pobierania najnowszych zmian

    Synchronizuje lokalny folder Git na komputerze hybrydowego procesu roboczego, a następnie importuje plikach .ps1 w katalogu źródłowym do konta usługi Automation.

1. Wyświetl szczegóły podsumowania zadania elementu runbook, wybierając je z **elementów Runbook** bloku konta usługi Automation, a następnie wybierz **zadań** kafelka. Upewnij się, zakończyła się pomyślnie, wybierając **wszystkie dzienniki** kafelków i przeglądania szczegółowego dziennika strumienia.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby uzyskać więcej informacji o funkcji obsługi skryptów programu PowerShell, zobacz [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Obsługa natywnych skryptów programu PowerShell w usłudze Azure Automation).
