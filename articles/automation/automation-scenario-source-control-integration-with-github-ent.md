---
title: Integracja kontroli źródła usługi Automatyzacja Azure z systemem GitHub Enterprise
description: W tym artykule opisano szczegóły dotyczące sposobu konfigurowania integracji z systemem GitHub Enterprise dla kontroli źródła elementów runbook automatyzacji.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 685d434affd0561658ae99c50bbe7b1fc27a5572
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Scenariusz automatyzacji Azure - automatyzacji integracji kontroli źródła z systemem GitHub Enterprise

Automatyzacja obsługuje obecnie integracji kontroli źródła, dzięki czemu można skojarzyć elementów runbook do Twojego konta automatyzacji do repozytorium GitHub kontroli źródła. Jednakże klienci, którzy wdrożyli [systemem GitHub Enterprise](https://enterprise.github.com/home) do obsługi swoje praktyki DevOps, również chcesz jej używać do zarządzania cyklem życia elementów runbook, które są opracowywane automatyzować procesy biznesowe i obsługę operacji zarządzania.

W tym scenariuszu masz komputerem z systemem Windows w centrum danych skonfigurowany jako hybrydowy proces roboczy elementu Runbook z modułów usługi Azure Resource Manager i zainstalować narzędzia Git. Maszynie hybrydowego procesu roboczego ma Sklonowanie lokalnego repozytorium Git. Po uruchomieniu elementu runbook na hybrydowy proces roboczy jest zsynchronizowany katalog Git i zawartość pliku elementu runbook są importowane do konta automatyzacji.

W tym artykule opisano sposób konfigurowania tej konfiguracji w danym środowisku usługi Automatyzacja Azure. Możesz uruchomić konfigurowanie przy użyciu poświadczeń zabezpieczeń wymagane do obsługi tego scenariusza i wdrażania hybrydowego procesu roboczego elementu Runbook w centrum danych do uruchamiania elementów runbook i dostępu do repozytorium GitHub Enterprise, aby zsynchronizować elementów runbook elementy runbook automatyzacji z Twoim kontem automatyzacji.

## <a name="getting-the-scenario"></a>Uzyskiwanie scenariusza

W tym scenariuszu składa się z dwóch elementów runbook programu PowerShell, które można importować bezpośrednio z [galerię elementów Runbook](automation-runbook-gallery.md) w portalu Azure lub pobrać z [galerii programu PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Elementy Runbook

Element Runbook | Opis|
--------|------------|
Export-RunAsCertificateToHybridWorker | Element Runbook umożliwia wyeksportowanie RunAs certyfikatu z konta automatyzacji do hybrydowy proces roboczy, aby mógł uwierzytelnić elementów runbook w procesie roboczym, z platformy Azure, aby można było zaimportować elementy runbook do konta automatyzacji.|
LocalGitFolderToAutomationAccount synchronizacji | Element Runbook synchronizuje folder lokalny Git na maszynie hybrydowych, a następnie zaimportować pliki runbook (*.ps1) do konta automatyzacji.|

### <a name="credentials"></a>Poświadczenia

Poświadczenie | Opis|
-----------|------------|
GitHRWCredential | Zasób poświadczeń, możesz utworzyć zawierać nazwę użytkownika i hasło dla użytkownika z uprawnieniami do hybrydowy proces roboczy.|

## <a name="installing-and-configuring-this-scenario"></a>Instalowanie i konfigurowanie scenariusza

### <a name="prerequisites"></a>Wymagania wstępne

1. Element runbook LocalGitFolderToAutomationAccount synchronizacji jest uwierzytelniany przy użyciu [konta Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).

2. Wymagany jest również obszar roboczy analizy dzienników z rozwiązania Automatyzacja Azure, włączona i skonfigurowana. Jeśli nie masz, który jest skojarzony z kontem automatyzacji używane do instalowania i konfigurowania tego scenariusza, jest tworzony i skonfigurowana za użytkownika podczas wykonywania **OnPremiseHybridWorker.ps1 nowy** skryptu z hybrydowy proces roboczy elementu runbook.

    > [!NOTE]
    > Obecnie w następujących regionach obsługują tylko automatyzacji Integracja z usługą analizy dzienników - **południowo-wschodnia Australia**, **wschodnie stany USA 2**, **Azja południowo-wschodnia**, i  **Europa Zachodnia**.

3. Komputer, który może służyć jako dedykowanych procesów roboczych Runbook hybrydowych, obsługującym oprogramowania GitHub i obsługa plików elementu runbook (*runbook*.ps1) w katalogu źródłowym, w systemie plików, które umożliwia synchronizację między GitHub i Twoje automatyzacji konto.

### <a name="import-and-publish-the-runbooks"></a>Importowanie i publikować elementy runbook

Aby zaimportować *RunAsCertificateToHybridWorker eksportu* i *LocalGitFolderToAutomationAccount synchronizacji* elementów runbook z galerii elementu Runbook z konta usługi Automatyzacja w portalu Azure, wykonaj procedury opisane w [importowanie elementu Runbook z galerii Runbook](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal). Elementy runbook należy opublikować po ich zostały pomyślnie zaimportowane do konta automatyzacji.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Wdrażanie i konfigurowanie hybrydowy proces roboczy elementu Runbook

Jeśli nie masz już wdrożone w centrum danych hybrydowego Runbook Worker, należy zapoznać się z wymaganiami i postępuj zgodnie z instrukcjami instalacji zautomatyzowanej przy użyciu procedury w Azure automatyzacji hybrydowe procesy robocze elementu Runbook - automatyzacji instalacji i konfiguracji [Windows](automation-windows-hrw-install.md#automated-deployment) lub [Linux](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker). Po pomyślnym zainstalowaniu hybrydowy proces roboczy na komputerze, wykonaj następujące kroki, aby ukończyć jej Konfigurowanie tego scenariusza.

1. Zaloguj się komputerze, na którym rola hybrydowy proces roboczy elementu Runbook przy użyciu konta, które ma lokalne uprawnienia administracyjne i Utwórz katalog do przechowywania plików Git elementu runbook. Klonowanie wewnętrzny repozytorium Git do katalogu.
1. Jeśli nie masz już konto Uruchom jako tworzone lub chcesz utworzyć nową jedną dedykowanego dla tego celu, w portalu Azure przejdź do konta automatyzacji, wybierz konto automatyzacji i utworzyć [zasób poświadczeń](automation-credentials.md) który zawiera nazwę użytkownika i hasło dla użytkownika z uprawnieniami do hybrydowy proces roboczy.
1. Z Twojego konta automatyzacji [edytować element runbook](automation-edit-textual-runbook.md)**RunAsCertificateToHybridWorker eksportu** i zmodyfikować wartość zmiennej *$Password* silnym hasłem.  Po zmodyfikowaniu wartość, kliknij przycisk **publikowania** ma wersję roboczą elementu runbook opublikowane.
1. Uruchamiania elementu runbook **RunAsCertificateToHybridWorker eksportu**, a następnie w **Uruchom element Runbook** bloku, w obszarze opcji **parametrów uruchomieniowych** wybierz opcję **hybrydowy proces roboczy** i na liście rozwijanej wybierz utworzony wcześniej w tym scenariuszu grupy hybrydowych procesów roboczych.

    Eksportuje ten certyfikat do hybrydowy proces roboczy, aby elementy runbook na proces roboczy może uwierzytelnić się przy użyciu platformy Azure przy użyciu połączenia Uruchom jako w celu zarządzania zasobami Azure (w szczególności w tym scenariuszu - import konta automatyzacji elementów runbook).

1. Wybierz wcześniej utworzoną grupę hybrydowych procesów roboczych z konta automatyzacji i [Określ konto Uruchom jako](automation-hrw-run-runbooks.md#runas-account) grupy hybrydowych procesów roboczych i wybranego zasobu poświadczeń tylko lub już został utworzony. Gwarantuje to, że synchronizacja elementu runbook można uruchomić polecenia usługi Git. 
1. Uruchomić element runbook **LocalGitFolderToAutomationAccount synchronizacji**, podaj następujące wymagane wartości parametru wejściowego i w **Uruchom element Runbook** bloku, w obszarze opcji **parametrów uruchomieniowych** wybierz opcję **hybrydowy proces roboczy** i na liście rozwijanej wybierz utworzony wcześniej w tym scenariuszu grupy hybrydowych procesów roboczych:

   * *Grupa zasobów* — Nazwa grupy zasobów skojarzonych z Twoim kontem automatyzacji
   * *AutomationAccountName* — nazwa konta automatyzacji
   * *GitPath* — lokalny folder lub plik na hybrydowy proces roboczy elementu Runbook, gdzie Git jest skonfigurowany do pobierania najnowszych zmian

    Synchronizuje folder lokalny Git na komputerze hybrydowego procesu roboczego, a następnie importowanie plików .ps1 w katalogu źródłowym na koncie automatyzacji.

1. Wyświetl szczegóły podsumowania zadania dla elementu runbook, wybierając ją z **elementów Runbook** bloku w konta automatyzacji, a następnie wybierz **zadania** kafelka. Upewnij się, zakończyła się pomyślnie, wybierając **wszystkie dzienniki** kafelków i przeglądanie strumienia szczegółowy dziennik.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat typów elementów Runbook, ich zalet i ograniczeń, zobacz [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).
* Aby uzyskać więcej informacji o funkcji obsługi skryptów programu PowerShell, zobacz [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Obsługa natywnych skryptów programu PowerShell w usłudze Azure Automation).
