---
title: Galeria elementów Runbook i modułów usługi Azure Automation
description: Elementy Runbook i modułów firmy Microsoft i społeczności są dostępne do zainstalowania i używania w danym środowisku usługi Azure Automation.  W tym artykule opisano, jak można pobrać tych zasobów i przyczynić się do galerii elementów runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fcf48cfdb43473f99b139f0a0a07a5ace9a91642
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476960"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galeria elementów Runbook i modułów usługi Azure Automation

Zamiast tworzenia własnych elementów runbook i modułów w usłudze Azure Automation, możesz uzyskać dostęp do scenariuszy, które zostały już utworzone przez firmę Microsoft i społeczność.

Zawiera elementy runbook programu PowerShell i [modułów](#modules-in-powershell-gallery) z galerii programu PowerShell i [elementy runbook języka Python](#python-runbooks) z galerii Centrum skryptów. Możesz także współtworzyć społeczności, udostępniając scenariusze, które tworzysz, zobacz Dodawanie elementu runbook do galerii

## <a name="runbooks-in-powershell-gallery"></a>Elementy Runbook w galerii programu PowerShell

[Galerii programu PowerShell](https://www.powershellgallery.com/packages) firmy Microsoft i społeczności, który można zaimportować do usługi Azure Automation jest dostępnych wiele elementów runbook. Aby użyć jednego, Pobierz element runbook z galerii lub bezpośrednio można zaimportować elementy runbook z galerii lub na koncie usługi Automation w witrynie Azure portal.

Można importować tylko bezpośrednio z galerii programu PowerShell przy użyciu witryny Azure portal. Nie można wykonać tej funkcji przy użyciu programu PowerShell.

> [!NOTE]
> Należy sprawdzić, czy zawartość wszelkie elementy runbook pobierany z galerii programu PowerShell i zachować szczególną ostrożność przy instalacji i uruchamiania ich w środowisku produkcyjnym.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Aby zaimportować element runbook programu PowerShell z galerii elementów Runbook za pomocą witryny Azure portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W obszarze **automatyzacji procesów**, kliknij pozycję **Galeria elementów Runbook**
3. Wybierz **źródła: Galeria programu PowerShell**.
4. Znajdź element galerii ma i wybierz ją, aby wyświetlić jego szczegóły. Po lewej stronie możesz wprowadzić parametry dodatkowe wyszukiwania dla wydawcy i typu.

   ![Przeglądaj galerię](media/automation-runbook-gallery/browse-gallery.png)

5. Kliknij pozycję **Wyświetl projekt źródłowy** do wyświetlania elementu w [Centrum skryptów TechNet](https://gallery.technet.microsoft.com/).
6. Aby zaimportować element, kliknij go, aby wyświetlić jego szczegóły, a następnie kliknij przycisk **zaimportować** przycisku.

   ![Przycisk Importuj](media/automation-runbook-gallery/gallery-item-detail.png)

7. Opcjonalnie można zmienić nazwy elementu runbook, a następnie kliknij przycisk **OK** można zaimportować elementu runbook.
8. Element runbook jest wyświetlany na **elementów Runbook** karcie dla konta usługi Automation.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Dodawanie elementu runbook programu PowerShell do galerii

Firma Microsoft zaleca, aby dodać elementy runbook do galerii programu PowerShell, które uważasz, że powinien być przydatny dla innych klientów. Galeria programu PowerShell akceptuje modułów programu PowerShell i skryptów programu PowerShell. Można dodać elementu runbook przez [przekazać go do galerii programu PowerShell](/powershell/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Graficzne elementy runbook nie są obsługiwane w galerii programu PowerShell.

## <a name="modules-in-powershell-gallery"></a>Moduły w galerii programu PowerShell

Moduły programu PowerShell zawierają polecenia cmdlet, które można użyć w elementach runbook, a istniejące moduły, które można zainstalować w usłudze Azure Automation są dostępne w [galerii programu PowerShell](https://www.powershellgallery.com). Można uruchomić tej galerii w witrynie Azure portal i zainstalować je bezpośrednio do usługi Azure Automation. Można je pobrać i zainstalować je ręcznie.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Aby zaimportować moduł za pomocą galerii modułu usługi Automation w witrynie Azure portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz **modułów** w obszarze **zasoby udostępnione** aby otworzyć listę modułów.
3. Kliknij przycisk **Przeglądaj galerię** w górnej części strony.

   ![Moduł galerii](media/automation-runbook-gallery/modules-blade.png)

4. Na **Przeglądaj galerię** strony, możesz wyszukiwać według następujących pól:

   * Nazwa modułu
   * `Tags`
   * Autor
   * Nazwa zasobu DSC/polecenia cmdlet

5. Znajdź moduł, który chcesz wziąć i wybierz ją, aby wyświetlić jego szczegóły.  

   Po przejściu do szczegółów do określonego modułu, można wyświetlić więcej informacji. Informacje te obejmują linkiem z powrotem do galerii programu PowerShell, wszystkie wymagane zależności i wszystkie polecenia cmdlet i zasobów DSC, które zawiera moduł.

   ![Szczegóły modułu programu PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Aby zainstalować moduł bezpośrednio do usługi Azure Automation, kliknij przycisk **importu** przycisku.
7. Po kliknięciu przycisk Importuj **zaimportować** okienko, zobaczysz nazwę modułu, który zamierzasz zaimportować. Jeśli wszystkie zależności są zainstalowane, **OK** zostanie aktywowany przycisk. W przypadku braku zależności, należy zaimportować te zależności, zanim będzie można zaimportować tego modułu.
8. Na **zaimportować** kliknij **OK** Aby zaimportować moduł. Gdy usługa Azure Automation importuje moduł do swojego konta, wyodrębnia metadane dotyczące modułu i poleceń cmdlet. Ta akcja może potrwać kilka minut, ponieważ każde działanie ma zostać wyodrębniony.
9. Otrzymasz powiadomienie zainicjowane przez moduł jest wdrażana i kolejne powiadomienie po zakończeniu.
10. Po zaimportowaniu modułu zobaczysz dostępne działania. Możesz użyć jej zasoby w elementów runbook i Desired State Configuration.

> [!NOTE]
> Moduły, które obsługują tylko program PowerShell core nie są obsługiwane w usłudze Azure Automation i nie mogą być importowane w witrynie Azure portal lub wdrażany bezpośrednio z galerii programu PowerShell.

## <a name="python-runbooks"></a>Elementy Runbook języka Python

Elementy Runbook języka Python są dostępne w [galerii Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Możesz współtworzyć elementów runbook galerii Centrum skryptów języka Python, klikając **Przekaż wkład**. Po użytkownik upewnij się, dodać tag **Python** podczas przekazywania Twojego wkładu.

> [!NOTE]
> Aby przekazać zawartość do [Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter) wymagany jest co najmniej 100 punktów. 

## <a name="requesting-a-runbook-or-module"></a>Żądanie runbook lub modułu

Można wysyłać żądania do [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Jeśli potrzebujesz pomocy przy użyciu zapisywania elementu runbook lub masz pytanie dotyczące programu PowerShell, należy zadać pytanie na naszym [forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Kolejne kroki

* Aby rozpocząć pracę z elementami runbook, zobacz [Zarządzanie elementu runbook w usłudze Azure Automation](manage-runbooks.md)
* Aby zrozumieć różnice między programu PowerShell i przepływie pracy programu PowerShell przy użyciu elementów runbook, zobacz [przepływu pracy programu PowerShell nauki](automation-powershell-workflow.md)
