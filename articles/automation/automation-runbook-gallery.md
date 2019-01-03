---
title: Galeria elementów Runbook i modułów usługi Azure Automation
description: Elementy Runbook i modułów firmy Microsoft i społeczności są dostępne do zainstalowania i używania w danym środowisku usługi Azure Automation.  W tym artykule opisano, jak można pobrać tych zasobów i przyczynić się do galerii elementów runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5b87d04466a2c94ed233edf4069ec1a30b10d03a
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53634324"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galeria elementów Runbook i modułów usługi Azure Automation
Zamiast tworzenia własnych elementów runbook i modułów w usłudze Azure Automation, możesz uzyskać dostęp do różnych scenariuszy, które zostały już utworzone przez firmę Microsoft i społeczność.  Możesz użyć tych scenariuszy, bez żadnych modyfikacji lub można ich używać jako punkt początkowy i edytować je do swoich specyficznych wymagań.

> [!NOTE]
> Nowy [modułu Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azurermps-6.13.0) nie są obsługiwane w usłudze Azure Automation. Wszystkie skrypty, pobrany z galerii programu PowerShell, za pomocą tych poleceń cmdlet nie będzie działać w usłudze Azure Automation.

Można uzyskać elementów runbook z [galerii elementów Runbook](#runbooks-in-runbook-gallery) i moduły z [galerii programu PowerShell](#modules-in-powerShell-gallery).  Można również przyczyniają się do społeczności, udostępniając scenariusze, które tworzysz, zobacz [Dodawanie elementu runbook do galerii](automation-runbook-gallery.md#adding-a-runbook-to-the-runbook-gallery)

## <a name="runbooks-in-runbook-gallery"></a>Elementy Runbook w galerii elementów Runbook
[Galerii elementów Runbook](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) firmy Microsoft i społeczności, który można zaimportować do usługi Azure Automation jest dostępnych wiele elementów runbook. Możesz pobrać elementu runbook z galerii, która jest hostowana w [Centrum skryptów TechNet](https://gallery.technet.microsoft.com/scriptcenter/site/upload), lub bezpośrednio można zaimportować elementy runbook z galerii w witrynie Azure portal.

Można importować tylko bezpośrednio z poziomu galerii elementów Runbook przy użyciu witryny Azure portal. Nie można wykonać tej funkcji przy użyciu programu Windows PowerShell.

> [!NOTE]
> Należy sprawdzić, czy zawartość wszelkie elementy runbook pobierany z galerii elementów Runbook i zachować szczególną ostrożność przy instalacji i uruchamiania ich w środowisku produkcyjnym.
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Aby zaimportować element runbook z galerii elementów Runbook za pomocą witryny Azure portal
1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W obszarze **automatyzacji procesów**, kliknij pozycję **Galeria elementów Runbook**
3. Znajdź element galerii ma i wybierz ją, aby wyświetlić jego szczegóły. Po lewej stronie możesz wprowadzić parametry dodatkowe wyszukiwania dla wydawcy i typu.
   
    ![Przeglądaj galerię](media/automation-runbook-gallery/browse-gallery.png)
5. Kliknij pozycję **Wyświetl projekt źródłowy** do wyświetlania elementu w [Centrum skryptów TechNet](https://gallery.technet.microsoft.com/).
6. Aby zaimportować element, kliknij go, aby wyświetlić jego szczegóły, a następnie kliknij przycisk **zaimportować** przycisku.
   
    ![Przycisk Importuj](media/automation-runbook-gallery/gallery-item-detail.png)
7. Opcjonalnie można zmienić nazwy elementu runbook, a następnie kliknij przycisk **OK** można zaimportować elementu runbook.
8. Element runbook jest wyświetlany na **elementów Runbook** karcie dla konta usługi Automation.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Dodawanie elementu runbook do galerii elementów runbook
Firma Microsoft zaleca, aby dodać elementy runbook do galerii elementów Runbook, które uważasz, że powinien być przydatny dla innych klientów.  Można dodać elementu runbook przez [przekazać go do Centrum skryptów](https://gallery.technet.microsoft.com/site/upload) biorąc pod uwagę następujące informacje.

* Należy określić *Windows Azure* dla **kategorii** i *automatyzacji* dla **podkategorii** dla elementu runbook, które mają być wyświetlane w Kreator.  
* Przekazywanie musi być pojedynczym pliku .ps1 lub graphrunbook.  Jeśli element runbook wymaga żadnych modułów podrzędnych elementów runbook i zasoby, następnie pomysłem jest wystawienie w opisie przesyłania, a także w sekcji komentarzy elementu runbook.  W przypadku scenariusza wymagające wiele elementów runbook przekazać każdy oddzielnie i wyświetlić listę nazw powiązanych elementów runbook we wszystkich ich opisy. Upewnij się, że używane te same znaczniki, dzięki czemu są one wyświetlane w tej samej kategorii. Użytkownik będzie miał odczytać opis, aby dowiedzieć się, że inne elementy runbook są wymagane scenariusz do pracy.
* Dodaj tag "GraphicalPS", w przypadku publikowania **graficzny element runbook** (nie graficzny przepływ pracy). 
* Wstaw fragment kodu programu PowerShell lub przepływie pracy programu PowerShell do opisu przy użyciu **Wstaw sekcję kodu** ikony.
* Podsumowanie dla przekazywania jest wyświetlana w wynikach galerii elementów Runbook, dlatego należy zadbać o szczegółowe informacje, które pomaga w identyfikacji funkcji elementu runbook użytkownika.
* Jednego do trzech z następujących tagów, należy przypisać do przekazywania.  Element runbook jest wymieniony w Kreatorze w obszarze kategorii, które odpowiada jego tagów.  Dowolne tagi nie na tej liście są ignorowane przez kreatora. Jeśli nie określisz żadnych znaczników pasującego elementu runbook znajduje się w innych kategorii.
  
  * Backup
  * Zarządzanie pojemnością
  * Zmień kontrolę
  * Zgodność
  * Dev / Test środowisk
  * Odzyskiwanie po awarii
  * Monitorowanie
  * Poprawianie
  * Inicjowanie obsługi
  * Korygowanie
  * Zarządzanie cyklem życia maszyn wirtualnych
* Automation aktualizacji galerii raz na godzinę, więc przesłane materiały nie będą widoczne natychmiast.

## <a name="modules-in-powershell-gallery"></a>Moduły w galerii programu PowerShell
Moduły programu PowerShell zawierają polecenia cmdlet, które można użyć w elementach runbook, a istniejące moduły, które można zainstalować w usłudze Azure Automation są dostępne w [galerii programu PowerShell](https://www.powershellgallery.com).  Można uruchomić tej galerii w witrynie Azure portal i zainstalować je bezpośrednio do usługi Azure Automation, lub można je pobrać i zainstalować je ręcznie.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Aby zaimportować moduł za pomocą galerii modułu usługi Automation w witrynie Azure portal
1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz **modułów** w obszarze **zasoby udostępnione** aby otworzyć listę modułów.
4. Kliknij przycisk **Przeglądaj galerię** w górnej części strony.
   
    ![Moduł galerii](media/automation-runbook-gallery/modules-blade.png) <br>
5. Na **Przeglądaj galerię** strony, możesz wyszukiwać według następujących pól:
   
   * Nazwa modułu
   * Tagi
   * Autor
   * Nazwa zasobu DSC/polecenia cmdlet
6. Znajdź moduł, który chcesz wziąć i wybierz ją, aby wyświetlić jego szczegóły.  
   Po przejściu do szczegółów do określonego modułu, można wyświetlić więcej informacji o module, w tym linkiem z powrotem do galerii programu PowerShell wszystkie wymagane zależności i wszystkich poleceń cmdlet i/lub zasobów DSC, które zawiera moduł.
   
    ![Szczegóły modułu programu PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. Aby zainstalować moduł bezpośrednio do usługi Azure Automation, kliknij przycisk **importu** przycisku.
8. Po kliknięciu przycisk Importuj **zaimportować** okienko, zobaczysz nazwę modułu, który chcesz zaimportować. Jeśli wszystkie zależności są zainstalowane, **OK** zostanie aktywowany przycisk. Jeśli użytkownik nie ma zależności, musisz zaimportować je, zanim będzie można zaimportować tego modułu.
9. Na **zaimportować** kliknij **OK** Aby zaimportować moduł. Gdy usługa Azure Automation importuje moduł do swojego konta, wyodrębnia metadane dotyczące modułu i poleceń cmdlet. Może to potrwać kilka minut, ponieważ każde działanie ma zostać wyodrębniony.
10. Otrzymasz powiadomienie zainicjowane przez moduł jest wdrażana i kolejne powiadomienie po zakończeniu.
11. Po zaimportowaniu modułu zobaczysz dostępne działania, a następnie użyć jej zasobów elementów runbook i Desired State Configuration.

> [!NOTE]
> Moduły, które obsługują tylko program PowerShell core nie są obsługiwane w usłudze Azure Automation i nie mogą być importowane w witrynie Azure portal lub wdrażany bezpośrednio z galerii programu PowerShell.

## <a name="python-runbooks"></a>Elementy Runbook języka Python

Elementy Runbook języka Python są dostępne w [galerii Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Elementy runbook języka Python, aby Galeria Centrum skryptów może współtworzyć zawartość. Po użytkownik upewnij się, dodać tag **Python** podczas przekazywania Twojego wkładu.

## <a name="requesting-a-runbook-or-module"></a>Żądanie runbook lub modułu
Można wysyłać żądania do [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Jeśli potrzebujesz pomocy, zapisywania elementu runbook lub masz pytanie dotyczące programu PowerShell, należy zadać pytanie na naszym [forum](https://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Następne kroki
* Aby rozpocząć pracę z elementami runbook, zobacz [Tworzenie lub importowanie elementu runbook w usłudze Azure Automation](automation-creating-importing-runbook.md)
* Aby zrozumieć różnice między programu PowerShell i przepływie pracy programu PowerShell przy użyciu elementów runbook, zobacz [przepływu pracy programu PowerShell nauki](automation-powershell-workflow.md)

