---
title: Monitorowanie zadań w usłudze Azure Data Lake Analytics przy użyciu witryny Azure portal
description: W tym artykule opisano, jak rozwiązywanie problemów z zadań usługi Azure Data Lake Analytics przy użyciu witryny Azure portal.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 40864bab068659be016161f7dc40243ebbd45174
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60812593"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitorowanie zadań w usłudze Azure Data Lake Analytics przy użyciu witryny Azure Portal

**Aby wyświetlić wszystkie zadania**

1. W witrynie Azure portal, kliknij przycisk **Microsoft Azure** w lewym górnym rogu.
2. Kliknij kafelek z nazwą konta usługi Data Lake Analytics.  W obszarze podsumowania zadania są wyświetlane na **zadań zarządzania** kafelka.

    ![Zarządzanie zadaniami w usłudze Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Zadania zarządzania zapewnia szybkie i łatwe stanu zadania. Należy zauważyć, że to zadanie zakończone niepowodzeniem.
3. Kliknij przycisk **zadań zarządzania** Kafelek, aby wyświetlić zadania. Zadania są podzielone na **systemem**, **kolejce**, i **Zakończono**. Zostanie wyświetlona zadania zakończone niepowodzeniem w **Zakończono** sekcji. Jest on pierwszy z nich na liście. Jeśli masz wiele zadań, możesz kliknąć pozycję **filtru** ułatwiające zlokalizować zadania.

    ![Usługa Azure Data Lake Analytics filtrowania zadań](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Kliknij zadanie zakończone niepowodzeniem z listy, aby otworzyć szczegóły zadania:

    ![Usługa Azure Data Lake Analytics nie powiodło się zadanie](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Zwróć uwagę **ponownie przesłać** przycisku. Po rozwiązaniu problemu możesz ponownie przesłać zadanie.
5. Kliknij wyróżniony część z poprzedniego zrzutu ekranu, można otworzyć szczegóły błędu.  Zostanie wyświetlona mniej więcej tak:

    ![Usługa Azure Data Lake Analytics nie powiodło się szczegóły zadania](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Określa, że nie można odnaleźć folderu źródłowego.
6. Kliknij przycisk **zduplikowane skryptu**.
7. Aktualizacja **FROM** ścieżkę do:

    "/ Samples/Data/SearchLog.tsv"
8. Kliknij przycisk **Prześlij zadanie**.

## <a name="see-also"></a>Zobacz także
* [Usługa Azure Data Lake Analytics — Przegląd](data-lake-analytics-overview.md)
* [Rozpoczynanie pracy z usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-manage-use-portal.md)
