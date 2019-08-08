---
title: Przewodnik Szybki start platformy Azure — Tworzenie elementu runbook usługi Azure Automation | Microsoft Docs
description: Dowiedz się, jak utworzyć element runbook usługi Azure Automation
services: automation
author: csand-msft
ms.author: csand
ms.date: 02/05/2019
ms.topic: quickstart
ms.service: automation
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 3dece2d2bffc7bd2b9e0a3c2931b2038ebc3c77e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850646"
---
# <a name="create-an-azure-automation-runbook"></a>Tworzenie elementu runbook usługi Azure Automation

Elementy runbook usługi Azure Automation można tworzyć za pośrednictwem platformy Azure. Ta metoda zapewnia oparty na przeglądarce interfejs użytkownika przeznaczony do tworzenia elementów runbook usługi Automation. W tym przewodniku Szybki start opisano tworzenie, edytowanie, testowanie i publikowanie elementu runbook programu PowerShell usługi Automation.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure w witrynie https://portal.azure.com

## <a name="create-runbook"></a>Tworzenie elementu runbook

Najpierw utwórz element runbook. Przykładowy element runbook tworzony w tym przewodniku Szybki start domyślnie wyświetla tekst `Hello World`.

1. Otwórz konto usługi Automation.

1. Kliknij pozycję **Elementy runbook** w obszarze **AUTOMATYZACJA PROCESÓW**. Zostanie wyświetlona lista elementów runbook.

1. Kliknij przycisk **Dodaj element runbook** znajdujący się w górnej części listy. Na stronie **Dodawanie elementu runbook** wybierz pozycję **Szybkie tworzenie**.

1. W polu **Nazwa** elementu runbook wpisz „Hello World”, a w obszarze **Typ elementu runbook** wybierz pozycję **PowerShell**. Kliknij przycisk **Utwórz**.

   ![Wprowadzanie na stronie informacji dotyczących elementu runbook usługi Automation](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Element runbook zostanie utworzony i zostanie otworzona strona **Edytuj element runbook programu PowerShell**.

    ![Tworzenie skryptu programu PowerShell w edytorze elementów runbook](./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png)

1. Wpisz lub skopiuj i wklej następujący kod do okienka edycji. Spowoduje to utworzenie dodatkowego parametru wejściowego o nazwie „Name” o domyślnej wartości „World” oraz wyświetlenie ciągu używającego następującej wartości wejściowej:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Kliknij pozycję **Zapisz**, aby zapisać kopię roboczą elementu runbook.

    ![Tworzenie skryptu programu PowerShell w edytorze elementów runbook](./media/automation-quickstart-create-runbook/automation-edit-runbook.png)

## <a name="test-the-runbook"></a>Testowanie elementu runbook

Po utworzeniu elementu runbook należy go przetestować, aby upewnić się, że działa.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć stronę **Testowanie**.

1. Wprowadź wartość w polu **Nazwa** i kliknij pozycję **Uruchom**. Zadanie testowania zostanie uruchomione oraz zostaną wyświetlone stan zadania i danej wyjściowe.

    ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-test-runbook.png)

1. Aby zamknąć okno **Testowanie**, kliknij znak **X** w prawym górnym rogu. Wybierz przycisk **OK** w wyświetlonym oknie podręcznym.

1. Na stronie **Edytuj element runbook programu PowerShell** kliknij pozycję **Publikuj**, aby opublikować element runbook jako oficjalną wersję elementu runboook na koncie.

   ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png)

## <a name="run-the-runbook"></a>Uruchamianie elementu runbook

Po opublikowaniu elementu runbook zostanie wyświetlona strona przeglądu.

1. Na stronie przeglądu elementu runbook kliknij pozycję **Uruchom**, aby otworzyć stronę konfiguracji **Uruchamianie elementu Runbook** dla tego elementu runbook.

   ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png)

1. Zostaw pole **Nazwa** puste, aby została użyta wartość domyślna, i kliknij przycisk **OK**. Element runbook zostanie przesłany i zostanie wyświetlona strona zadania.

   ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-job-page.png)

1. Gdy **Stan zadania** będzie miał wartość **Uruchomione** lub **Ukończone**, kliknij pozycję **Dane wyjściowe**, aby otworzyć okienko **Dane wyjściowe** i wyświetlić dane wyjściowe elementu runbook.

   ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy element runbook nie będzie już potrzebny, usuń go. Aby to zrobić, zaznacz element runbook na liście i kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono, edytowano, przetestowano i opublikowano element runbook oraz uruchomiono zadanie elementu runbook. Aby dowiedzieć się więcej o elementach runbook usługi Automation, przejdź do artykułu opisującego różne typy elementów runbook, które można tworzyć i z nich korzystać w usłudze Automation.

> [!div class="nextstepaction"]
> [Instrukcje dotyczące usługi Automation — typy elementów runbook](./automation-runbook-types.md)
