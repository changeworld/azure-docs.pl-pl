---
title: Uruchamianie zadania w menedżerze danych StorSimple za pomocą usługi Azure Automation
description: Dowiedz się, jak używać usługi Azure Automation do wyzwalania zadań programu StorSimple Data Manager
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 034b4996672f0961cf31d342aa6055482f099b9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273991"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Wyzwalanie zadania za pomocą usługi Azure Automation

W tym artykule wyjaśniono, jak można użyć funkcji przekształcania danych w ramach usługi StorSimple Data Manager do przekształcania danych urządzenia StorSimple. Zadanie przekształcania danych można uruchomić na dwa sposoby: 

 - Korzystanie z zestawu SDK dla platformy .NET
 - Korzystanie z podręcznika azure automation
 
W tym artykule opisano sposób tworzenia wiązki uruchomieniu usługi Azure Automation, a następnie użyć go do zainicjowania zadania przekształcania danych. Aby dowiedzieć się więcej o inicjowaniu transformacji danych za pomocą narzędzia .NET SDK, przejdź do [artykułu Użyj narzędzia .NET SDK do wyzwalania zadań przekształcania danych](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:

*   Program Azure PowerShell zainstalowany na komputerze klienckim. [Pobierz program Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   Poprawnie skonfigurowana definicja zadania w usłudze StorSimple Data Manager w grupie zasobów.
*   Pobierz [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) plik z repozytorium GitHub. 
*   Pobierz [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) skrypt z repozytorium GitHub.

## <a name="step-by-step-procedure"></a>Procedura krok po kroku

### <a name="set-up-the-automation-account"></a>Konfigurowanie konta automatyzacji

1. Utwórz konto automatyzacji Azure Run As w witrynie Azure portal. Aby to zrobić, przejdź do **witryny Azure marketplace > wszystko,** a następnie wyszukaj w polu **Automatyzacja**. Wybierz **pozycję Konta automatyzacji**.

    ![Utwórz uruchom jako konto automatyzacji](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Aby dodać nowe konto automatyzacji, kliknij przycisk **+ Dodaj**.

    ![Utwórz uruchom jako konto automatyzacji](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. W dodatku **automatyzacji:**

   1. Podaj **nazwę** swojego konta automatyzacji.
   2. Wybierz **subskrypcję** połączoną z usługą StorSimple Data Manager.
   3. Utwórz nową grupę zasobów lub wybierz z istniejącej grupy zasobów.
   4. Wybierz **lokalizację**.
   5. Pozostaw domyślną opcję **Utwórz uruchom jako konto.**
   6. Aby uzyskać łącze umożliwiające szybki dostęp na pulpicie nawigacyjnym, zaznacz pole **wyboru Przypnij do pulpitu nawigacyjnego**. Kliknij przycisk **Utwórz**.

      ![Utwórz uruchom jako konto automatyzacji](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Po pomyślnym utworzeniu konta automatyzacji użytkownik zostanie o tym powiadomiony.
    
      ![Powiadomienie o wdrożeniu konta automatyzacji](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Aby uzyskać więcej informacji, przejdź do [tematu Utwórz konto Uruchom jako](../automation/automation-create-runas-account.md).

3. Na nowo utworzonym koncie przejdź do **witryny Zasoby udostępnione > moduły** i kliknij przycisk **+ Dodaj moduł**.

    ![Moduł importu 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Przejdź do lokalizacji `DataTransformationApp.zip` pliku z komputera lokalnego, a następnie wybierz i otwórz moduł. Kliknij **przycisk OK,** aby zaimportować moduł.

    ![Moduł importu 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Gdy usługa Azure Automation importuje moduł do konta, wyodrębnia metadane dotyczące modułu. Ta operacja może potrwać kilka minut.

   ![Moduł importu 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Otrzymasz powiadomienie, że moduł jest wdrażany i kolejne powiadomienie po zakończeniu procesu.  Stan w **modules** zmienia się na **Dostępne**.

    ![Moduł importu 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importowanie, publikowanie i uruchamianie systemu runbook automatyzacji

Wykonaj następujące kroki, aby zaimportować, opublikować i uruchomić projekt runbook, aby wyzwolić definicję zadania.

1. W witrynie Azure Portal otwórz konto usługi Automation. Przejdź do **aplikacji Process Automation > Runbooks** i kliknij przycisk + Dodaj system **runbook**.

    ![Dodawanie 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. W **pliku Add runbook**kliknij pozycję **Importuj istniejący element runbook**.

3. Wskaż plik `Trigger-DataTransformation-Job.ps1` skryptu programu Azure PowerShell dla **pliku Runbook**. Typ elementu runbook jest wybierany automatycznie. Podaj nazwę i opcjonalny opis dla wiązki uruchomieniu. Chybień. Kliknij przycisk **Utwórz**.

    ![Dodawanie eksty 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Nowy projekt runbook pojawi się na liście uruchomieniu dla konta automatyzacji. Zaznacz i kliknij ten projekt runbook.

    ![Dodawanie eksty 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Edytuj projekt runbooka i kliknij okienko **testu.**

    ![Dodawanie eksty 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Podaj parametry, takie jak nazwa usługi StorSimple Data Manager, skojarzona grupa zasobów i nazwa definicji zadania. **Rozpocznij** test. Raport jest generowany po zakończeniu uruchamiania. Aby uzyskać więcej informacji, przejdź do sposobu [testowania śmiętu](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Dodawanie eksty 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Sprawdź dane wyjściowe z uruchomieniu w okienku testowym. Jeśli jest spełniony, zamknij okienko. Kliknij **pozycję Publikuj** i po wyświetleniu monitu o potwierdzenie, potwierdź i opublikuj program runbook.

    ![Dodawanie eksty 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Wróć do **śreszt y ekresztów** i wybierz nowo utworzony projekt runbook.

    ![Dodawanie eksty 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Uruchom** program runbook. W **uiwarku początkowego**wprowadź wszystkie parametry. Kliknij **przycisk OK,** aby przesłać i rozpocząć zadanie przekształcania danych.

10. Aby monitorować postęp zadania w witrynie Azure portal, przejdź do **zadań** w usłudze StorSimple Data Manager. Zaznacz i kliknij zadanie, aby wyświetlić szczegóły zadania.

    ![Dodawanie 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Następne kroki

[Aby przekształcić dane, użyj interfejsu użytkownika Programu StorSimple Data Manager.](storsimple-data-manager-ui.md)
