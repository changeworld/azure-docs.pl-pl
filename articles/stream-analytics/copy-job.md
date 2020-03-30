---
title: Kopiowanie lub kopiowanie kopii zapasowych zadań usługi Azure Stream Analytics
description: W tym artykule opisano sposób kopiowania lub wykonywania kopii zapasowej zadania usługi Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5c8f770855dd8d19a9d313f1b79f9bf8da4b2393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771499"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Kopiowanie lub kopiowanie kopii zapasowych zadań usługi Azure Stream Analytics

Można skopiować lub zrobić kopię zapasową wdrożonych zadań usługi Azure Stream Analytics przy użyciu programu Visual Studio Code lub Visual Studio. 

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Zaloguj się do [Portalu Azure](https://portal.azure.com/).

* Zainstaluj [rozszerzenie usługi Azure Stream Analytics dla programu Visual Studio Code](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension) lub narzędzia Azure Stream Analytics dla programu Visual [Studio.](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension)  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Kliknij ikonę **platformy Azure** na pasku aktywności kodu programu Visual Studio, a następnie rozwiń węzeł Usługi **Stream Analytics.** Zadania powinny być wyświetlane w ramach subskrypcji.

   ![Otwórz Eksploratora analizy strumienia](./media/vscode-explore-jobs/open-explorer.png)

2. Aby wyeksportować zadanie do projektu lokalnego, znajdź zadanie, które chcesz wyeksportować w **Eksploratorze analizy strumienia** w kodzie programu Visual Studio. Następnie wybierz folder dla projektu.

    ![Eksportowanie zadania ASA w programie Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Projekt jest eksportowany do folderu wybranego i dodawany do bieżącego obszaru roboczego.

    ![Eksportowanie zadania ASA w programie Visual Studio Code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Aby opublikować zadanie w innym regionie lub kopii zapasowej przy użyciu innej nazwy, wybierz **wybierz z subskrypcji, aby opublikować** w edytorze zapytań (.asaql)\*i postępuj zgodnie z instrukcjami.

    ![Publikowanie na platformie Azure w programie Visual Studio Code](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Postępuj zgodnie z [eksportem wdrożonego zadania usługi Azure Stream Analytics do instrukcji projektu.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project)

2. Otwórz \*plik .asaql w Edytorze zapytań, wybierz **pozycję Prześlij na platformę Azure** w edytorze skryptów i postępuj zgodnie z instrukcjami, aby opublikować zadanie w innym regionie lub wykonać kopię zapasową przy użyciu nowej nazwy.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu kodu programu Visual Studio](quick-create-vs-code.md)
* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Wdrażanie zadania usługi Azure Stream Analytics z ciągłą integracją/ciągłym wdrażaniem przy użyciu usługi Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
