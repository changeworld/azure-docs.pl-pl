---
title: Galeria elementów Runbook i modułów dla Azure Automation
description: Elementy Runbook i moduły firmy Microsoft oraz społeczność są dostępne do zainstalowania i użycia w środowisku Azure Automationu.  W tym artykule opisano sposób uzyskiwania dostępu do tych zasobów i współtworzenia elementów Runbook w galerii.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 52a0ab0a31600c1548283c7d899b17e497811b5a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421480"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galeria elementów Runbook i modułów dla Azure Automation

Zamiast tworzyć własne elementy Runbook i moduły w Azure Automation, możesz uzyskać dostęp do scenariuszy, które zostały już utworzone przez firmę Microsoft i społeczność.

Można uzyskać elementy Runbook i [moduły](#modules-in-powershell-gallery) programu PowerShell z poziomu [elementów Runbook](#python-runbooks) Galeria programu PowerShell i Python z poziomu galerii centrum skryptów. Możesz również współtworzyć społeczność, udostępniając scenariusze, które opracowujesz, zobacz Dodawanie elementu Runbook do galerii

## <a name="runbooks-in-powershell-gallery"></a>Elementy Runbook w Galeria programu PowerShell

[Galeria programu PowerShell](https://www.powershellgallery.com/packages) oferuje różne elementy Runbook firmy Microsoft i społeczność, które można zaimportować do Azure Automation. Aby użyć jednego z nich, pobierz element Runbook z galerii lub bezpośrednio Importuj elementy Runbook z galerii lub z konta usługi Automation w Azure Portal.

Można importować tylko bezpośrednio z Galeria programu PowerShell przy użyciu Azure Portal. Nie można wykonać tej funkcji przy użyciu programu PowerShell.

> [!NOTE]
> Należy sprawdzić poprawność zawartości wszystkich elementów Runbook uzyskanych z Galeria programu PowerShell, a także zachować wyjątkową ostrożność instalowania i uruchamiania ich w środowisku produkcyjnym.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Aby zaimportować element Runbook programu PowerShell z galerii elementów Runbook za pomocą Azure Portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W obszarze **Automatyzacja procesów**kliknij pozycję **Galeria elementów Runbook** .
3. Wybierz **Źródło: Galeria programu PowerShell**.
4. Znajdź żądany element galerii i wybierz go, aby wyświetlić jego szczegóły. Po lewej stronie możesz wprowadzić dodatkowe parametry wyszukiwania dla wydawcy i typu.

   ![Galeria przeglądania](media/automation-runbook-gallery/browse-gallery.png)

5. Kliknij pozycję **Wyświetl projekt źródłowy** , aby wyświetlić element w [Centrum skryptów TechNet](https://gallery.technet.microsoft.com/).
6. Aby zaimportować element, kliknij go, aby wyświetlić jego szczegóły, a następnie kliknij przycisk **Importuj** .

   ![Przycisk Importuj](media/automation-runbook-gallery/gallery-item-detail.png)

7. Opcjonalnie Zmień nazwę elementu Runbook, a następnie kliknij przycisk **OK** , aby zaimportować element Runbook.
8. Element Runbook jest wyświetlany na karcie **elementy Runbook** dla konta usługi Automation.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Dodawanie elementu Runbook programu PowerShell do galerii

Firma Microsoft zachęca do dodawania elementów Runbook do Galeria programu PowerShell, które prawdopodobnie będą przydatne dla innych klientów. Galeria programu PowerShell akceptuje modułów programu PowerShell i skryptów programu PowerShell. Element Runbook można dodać, [przekazując go do Galeria programu PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Graficzne elementy Runbook nie są obsługiwane w Galeria programu PowerShell.

## <a name="modules-in-powershell-gallery"></a>Moduły w Galeria programu PowerShell

Moduły programu PowerShell zawierają polecenia cmdlet, których można używać w elementach Runbook, oraz istniejące moduły, które można zainstalować w Azure Automation są dostępne w [Galeria programu PowerShell](https://www.powershellgallery.com). Możesz uruchomić tę galerię z Azure Portal i zainstalować ją bezpośrednio w Azure Automation. Można je również pobrać i zainstalować ręcznie.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Aby zaimportować moduł z galerii modułu automatyzacji przy użyciu Azure Portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz pozycję **moduły** w obszarze **udostępnione zasoby** , aby otworzyć listę modułów.
3. W górnej części strony kliknij pozycję **Przeglądaj Galerię** .

   ![Galeria modułów](media/automation-runbook-gallery/modules-blade.png)

4. Na stronie **Przeglądaj Galerię** można wyszukać następujące pola:

   * Nazwa modułu
   * Tagi
   * Autor
   * Nazwa zasobu polecenia cmdlet/konfiguracji DSC

5. Znajdź interesujący Cię moduł i wybierz go, aby wyświetlić jego szczegóły.

   Podczas przechodzenia do szczegółów konkretnego modułu można wyświetlić więcej informacji. Te informacje obejmują łącze z powrotem do Galeria programu PowerShell, wszystkie wymagane zależności oraz wszystkie polecenia cmdlet lub zasoby DSC, które zawiera moduł.

   ![Szczegóły modułu programu PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Aby zainstalować moduł bezpośrednio w Azure Automation, kliknij przycisk **Importuj** .
7. Po kliknięciu przycisku Importuj w okienku **Importuj** zostanie wyświetlona nazwa modułu, który ma zostać zaimportowany. Jeśli wszystkie zależności są zainstalowane, przycisk **OK** jest aktywowany. W przypadku braku zależności należy zaimportować te zależności przed zaimportowaniem tego modułu.
8. Na stronie **Import** kliknij przycisk **OK** , aby zaimportować moduł. Podczas Azure Automation importuje moduł do konta, wyodrębnia on metadane dotyczące modułu i poleceń cmdlet. Ta akcja może potrwać kilka minut, ponieważ należy wyodrębnić każde działanie.
9. Otrzymujesz początkowe powiadomienie, że moduł jest wdrażany, i inne powiadomienie po jego zakończeniu.
10. Po zaimportowaniu modułu można zobaczyć dostępne działania. Możesz użyć swoich zasobów w elementach Runbook i konfiguracji żądanego stanu.

> [!NOTE]
> Moduły obsługujące tylko rdzeń programu PowerShell nie są obsługiwane w programie Azure Automation i nie można ich zaimportować w Azure Portal ani wdrożyć bezpośrednio w Galeria programu PowerShell.

## <a name="python-runbooks"></a>Elementy Runbook języka Python

Elementy Runbook języka Python są dostępne w [galerii centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Elementy Runbook języka Python można współtworzyć w galerii centrum skryptów, klikając przycisk **Przekaż wkład**. Gdy to zrobisz, upewnij się, że podczas przekazywania Twojego wkładu dodasz tag **Python** .

> [!NOTE]
> Aby można było przekazywać zawartość do [Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter) , wymagany jest co najmniej 100 punktów.

## <a name="requesting-a-runbook-or-module"></a>Żądanie elementu Runbook lub modułu

Możesz wysyłać żądania do [głosu użytkownika](https://feedback.azure.com/forums/246290-azure-automation/).  Jeśli potrzebujesz pomocy przy pisaniu elementu Runbook lub pytania dotyczące programu PowerShell, Opublikuj pytanie na naszym [forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Popularne rozwiązania dostępne w galerii elementów Runbook

Poniższa lista zawiera kilka elementów Runbook, które udostępniają rozwiązania typowym scenariuszom. Aby uzyskać pełną listę elementów Runbook utworzonych przez zespół Azure Automation, zobacz [profil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

* [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) — importuje najnowszą wersję na Galeria programu PowerShell wszystkich modułów na koncie usługi Automation.
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) — ten skrypt służy do konfigurowania Diagnostyka Azure i log Analytics do odbierania dzienników Azure Automation zawierających stan zadania i strumienie zadań.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) — ten element Runbook kopiuje plik zdalny z maszyny wirtualnej platformy Microsoft Azure.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) — ten element Runbook kopiuje plik lokalny na maszynę wirtualną platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z elementami Runbook, zobacz [Zarządzanie elementem Runbook w Azure Automation](manage-runbooks.md)
* Aby zrozumieć różnice między programem PowerShell i przepływem pracy programu PowerShell z elementami Runbook, zobacz temat [uczenie przepływu pracy programu PowerShell](automation-powershell-workflow.md)
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów dokumentacji i uczenia dotyczącej języka, zapoznaj się z dokumentacją programu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
