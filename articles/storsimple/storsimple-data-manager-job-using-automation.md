---
title: Uruchamianie zadania usługi StorSimple Data Manager przy użyciu usługi Azure Automation | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Automation do wyzwalania zadania usługi StorSimple Data Manager
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b837aab871827c468295a365727a282f6c8a1a4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634254"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Używanie usługi Azure Automation do wyzwalania zadania

W tym artykule wyjaśniono, jak funkcja transformacji danych w ramach usługi StorSimple Data Manager można użyć do przekształcania danych urządzenia StorSimple. Można uruchomić zadania przekształcania danych na dwa sposoby: 

 - Korzystanie z zestawu SDK dla platformy .NET
 - Użyj elementu runbook usługi Azure Automation
 
W tym artykule opisano, jak utworzyć element runbook usługi Automation, a następnie użyć go, aby zainicjować zadanie przekształcania danych. Aby dowiedzieć się więcej o tym, jak zainicjować przekształcania danych za pomocą zestawu SDK platformy .NET, przejdź do [wyzwalanie zadań przekształcania danych, zestaw SDK platformy .NET użyj](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:

*   Na komputerze klienckim zainstalowany program Azure PowerShell. [Pobieranie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   Definicja zadania prawidłowo skonfigurowaną w usłudze StorSimple Data Manager w grupie zasobów.
*   Pobierz [ `DataTransformationApp.zip` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) plik z repozytorium GitHub. 
*   Pobierz [ `Trigger-DataTransformation-Job.ps1` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) skryptu z repozytorium GitHub.

## <a name="step-by-step-procedure"></a>Procedura krok po kroku

### <a name="set-up-the-automation-account"></a>Konfigurowanie konta usługi Automation

1. Utwórz konto Uruchom jako platformy Azure automation w witrynie Azure portal. Aby to zrobić, przejdź do **portalu Azure marketplace > wszystko** a następnie wyszukaj **automatyzacji**. Wybierz **kont usługi Automation**.

    ![Utwórz konto usługi automation Uruchom jako](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Aby dodać nowe konto usługi automation, kliknij **+ Dodaj**.

    ![Utwórz konto usługi automation Uruchom jako](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. W **Dodawanie automatyzacji**:

   1. Podaj **nazwa** konta usługi automation.
   2. Wybierz **subskrypcji** połączonego z usługą StorSimple Data Manager.
   3. Utwórz nową grupę zasobów lub wybierz istniejącą grupę zasobów.
   4. Wybierz **lokalizację**.
   5. Pozostaw wartość domyślną **Utwórz konto Uruchom jako** wybraną opcją.
   6. Aby uzyskać link, aby uzyskać szybki dostęp na pulpicie nawigacyjnym, zapoznaj się z **Przypnij do pulpitu nawigacyjnego**. Kliknij pozycję **Utwórz**.

      ![Utwórz konto usługi automation Uruchom jako](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Po pomyślnym utworzeniu konta usługi automation, otrzymasz powiadomienie.
    
      ![Powiadomienie dla wdrożenia dla konta usługi automation](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Aby uzyskać więcej informacji, przejdź do [Tworzenie konta Uruchom jako](../automation/automation-create-runas-account.md).

3. W nowo utworzonego konta, przejdź do **zasoby udostępnione > modułów** i kliknij przycisk **+ Dodaj moduł**.

    ![Zaimportuj moduł 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Przejdź do lokalizacji `DataTransformationApp.zip` pliku z komputera lokalnego i wybierz i otwórz moduł. Kliknij przycisk **OK** Aby zaimportować moduł.

    ![Zaimportuj moduł 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Gdy usługa Azure Automation importuje moduł do swojego konta, wyodrębnia metadane dotyczące modułu. Ta operacja może potrwać kilka minut.

   ![Zaimportuj moduł 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Otrzymasz powiadomienie, że moduł jest wdrażana i kolejne powiadomienie po zakończeniu procesu.  Stan w **modułów** zmieni się na **dostępne**.

    ![Zaimportuj moduł 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importowanie i publikowanie i uruchamianie elementu runbook usługi Automation

Wykonaj poniższe kroki, aby zaimportować, publikowanie i uruchamianie elementu runbook w celu wyzwolenia definicji zadania.

1. W witrynie Azure Portal otwórz konto usługi Automation. Przejdź do **automatyzacji procesów > elementy Runbook** i kliknij przycisk **+ Dodaj element runbook**.

    ![Dodaj element runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. W **Dodaj runbook**, kliknij przycisk **importowanie istniejącego elementu runbook**.

3. Wskaż plik skryptu programu Azure PowerShell `Trigger-DataTransformation-Job.ps1` dla **plikowi elementu Runbook**. Typ elementu runbook są wybierane automatycznie. Podaj nazwę i opcjonalny opis dla elementu runbook. Kliknij pozycję **Utwórz**.

    ![Dodaj element runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Nowy element runbook zostanie wyświetlony na liście elementów runbook dla konta usługi Automation. Wybierz i kliknij ten element runbook.

    ![Dodaj element runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Edytuj element runbook, a następnie kliknij przycisk **testu** okienka.

    ![Dodaj element runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Podaj parametry, takie jak nazwa usługi StorSimple Data Manager, skojarzonej grupy zasobów i Nazwa definicji zadania. **Rozpocznij** testu. Raport jest generowany, gdy uruchomienie zostanie zakończone. Aby uzyskać więcej informacji, przejdź do sposobu [przetestować element runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Dodaj element runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Sprawdź dane wyjściowe z elementu runbook w okienku testu. Jeśli spełnione, należy zamknąć okienko. Kliknij przycisk **Publikuj** i po wyświetleniu monitu o potwierdzenie, upewnij się i publikowanie elementu runbook.

    ![Dodaj element runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Wróć do **elementów Runbook** i wybierz nowo utworzony element runbook.

    ![Dodaj element runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Rozpocznij** elementu runbook. W **uruchamianie elementu runbook**, wprowadź wszystkie parametry. Kliknij przycisk **OK** do przesyłania i uruchomić zadanie przekształcania danych.

10. Aby monitorować postęp zadania w witrynie Azure portal, przejdź do **zadania** w usłudze StorSimple Data Manager. Wybierz i kliknij zadanie, aby wyświetlić szczegóły zadania.

    ![Dodaj element runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Kolejne kroki

[Użyj usługi StorSimple Data Manager interfejsu użytkownika w celu przekształcania danych](storsimple-data-manager-ui.md).