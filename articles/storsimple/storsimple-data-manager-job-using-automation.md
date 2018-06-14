---
title: Używanie automatyzacji Azure można uruchomić zadania w Menedżerze danych StorSimple | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Automatyzacja Azure służącą do wyzwalania zadania Menedżer StorSimple danych
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
ms.openlocfilehash: 1e5fcbee664271058ac1c7fa80bb285e09b8579a
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862200"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Używanie automatyzacji Azure do wyzwalania zadania

W tym artykule opisano sposób korzystania z funkcji przekształcania danych w usłudze Menedżer StorSimple danych do przekształcania danych urządzenia StorSimple. Można uruchomić zadania przekształcania danych na dwa sposoby: 

 - Korzystanie z zestawu SDK dla platformy .NET
 - Użyj elementu runbook usługi Automatyzacja Azure
 
W tym artykule szczegółowo przedstawiają, jak utworzyć element runbook usługi Automatyzacja Azure, a następnie użyć do zainicjowania zadania przekształcania danych. Aby dowiedzieć się więcej o tym, jak zainicjować transformacji danych za pomocą zestawu .NET SDK, przejdź do [Użyj zestawu SDK .NET do zadania przekształcania danych wyzwalacza](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:

*   Program Azure PowerShell jest zainstalowane na komputerze klienckim. [Pobieranie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
*   Definicji zadania poprawnie skonfigurowane w usłudze Menedżer StorSimple danych w grupie zasobów.
*   Pobierz [ `DataTransformationApp.zip` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) plik z repozytorium GitHub. 
*   Pobierz [ `Trigger-DataTransformation-Job.ps1` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) skryptu z repozytorium GitHub.

## <a name="step-by-step-procedure"></a>Procedury krok po kroku

### <a name="set-up-the-automation-account"></a>Skonfiguruj konto automatyzacji

1. Utwórz konto Uruchom jako platformy Azure automatyzacji w portalu Azure. Aby to zrobić, przejdź do **witrynę Azure marketplace > wszystko** , a następnie wyszukaj **automatyzacji**. Wybierz **kont automatyzacji**.

    ![Utwórz konto automatyzacji Uruchom jako](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Aby dodać nowe konto usługi Automatyzacja, kliknij przycisk **+ Dodaj**.

    ![Utwórz konto automatyzacji Uruchom jako](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. W **dodać automatyzacji**:

    1. Podaj **nazwa** Twojego konta automatyzacji.
    2. Wybierz **subskrypcji** połączone z usługą Menedżer StorSimple danych.
    3. Utwórz nową grupę zasobów lub wybierz istniejącą grupę zasobów.
    4. Wybierz **lokalizację**.
    5. Pozostaw wartość domyślną **utworzyć Uruchom jako konta** wybraną opcją.
    6. Aby uzyskać link do szybkiego dostępu na pulpicie nawigacyjnym, sprawdź **Przypnij do pulpitu nawigacyjnego**. Kliknij przycisk **Utwórz**.

    ![Utwórz konto automatyzacji Uruchom jako](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
    Po pomyślnym utworzeniu konta automatyzacji, użytkownik jest powiadamiany.
    
    ![Powiadomienie dla wdrożenia konta automatyzacji](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

    Aby uzyskać więcej informacji, przejdź do [Utwórz konto Uruchom jako](../automation/automation-create-runas-account.md).

3. W nowo utworzonych kont, przejdź do **udostępnione zasoby > modułów** i kliknij przycisk **+ Dodaj moduł**.

    ![Zaimportuj moduł 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Przejdź do lokalizacji `DataTransformationApp.zip` pliku z komputera lokalnego i wybierz i otwórz moduł. Kliknij przycisk **OK** zaimportować modułu.

    ![Zaimportuj moduł 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Automatyzacja Azure importuje moduł do swojego konta, umożliwia wyodrębnianie metadanych dotyczących modułu. Ta operacja może potrwać kilka minut.

   ![Zaimportuj moduł 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Pojawi się powiadomienie jest wdrażany modułu i kolejne powiadomienie po zakończeniu procesu.  Stan w **modułów** zmienia się na **dostępne**.

    ![Zaimportuj moduł 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importuj, publikowanie i uruchomić element runbook automatyzacji

Wykonaj poniższe kroki, aby zaimportować, publikowanie i uruchamiania elementu runbook, aby wyzwolić definicji zadania.

1. W witrynie Azure Portal otwórz konto usługi Automation. Przejdź do **automatyzacji procesu > elementów Runbook** i kliknij przycisk **+ Dodaj element runbook**.

    ![Dodaj element runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. W **dodać runbook**, kliknij przycisk **importowanie istniejącego elementu runbook**.

3. Wskaż plik skryptu programu PowerShell Azure `Trigger-DataTransformation-Job.ps1` dla **pliku Runbook**. Typ elementu runbook jest automatycznie wybierany. Podaj nazwę i opcjonalny opis dla elementu runbook. Kliknij przycisk **Utwórz**.

    ![Dodaj element runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Nowy element runbook zostanie wyświetlony na liście elementów runbook dla konta automatyzacji. Wybierz i kliknij ten element runbook.

    ![Dodaj element runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Edytowanie elementu runbook, a następnie kliknij przycisk **testu** okienka.

    ![Dodaj element runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Podaj parametry, takie jak nazwa usługi Menedżer StorSimple danych i grupy skojarzone zasób Nazwa definicji zadania. **Uruchom** testu. Raport jest generowany po zakończeniu uruchom. Aby uzyskać więcej informacji, przejdź do sekcji [przetestować element runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Dodaj element runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Sprawdź dane wyjściowe z elementu runbook w okienku testu. Jeśli spełnione, należy zamknąć okienko. Kliknij przycisk **publikowania** i po wyświetleniu monitu o potwierdzenie, upewnij się i opublikować elementu runbook.

    ![Dodaj element runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Wróć do **elementów Runbook** i wybierz nowo utworzony element runbook.

    ![Dodaj element runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Uruchom** elementu runbook. W **uruchomić elementu runbook**, wprowadź wszystkie parametry. Kliknij przycisk **OK** do przesyłania i uruchomić zadanie transformacji danych.

10. Aby monitorować postęp zadania w portalu Azure, przejdź do **zadania** w usłudze Menedżer StorSimple danych. Wybierz, a następnie kliknij pozycję zadania, aby wyświetlić szczegóły zadania.

    ![Dodaj element runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Następne kroki

[Użyj Menedżera danych StorSimple interfejsu użytkownika Służącego do przekształcenia danych](storsimple-data-manager-ui.md).