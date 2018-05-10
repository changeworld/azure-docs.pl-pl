---
title: Monitorowanie zadań w usłudze Azure Data Lake Analytics przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: 'Dowiedz się, jak rozwiązywać problemy z zadań usługi Data Lake Analytics przy użyciu portalu Azure. '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 14b1f4ec9dff78e4b5d2480755a4b1f2579ec135
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitorowanie zadań w usłudze Azure Data Lake Analytics przy użyciu portalu Azure

**Aby wyświetlić wszystkie zadania**

1. W portalu Azure kliknij **Microsoft Azure** w lewym górnym rogu.
2. Kliknij kafelek z nazwą konta usługi Data Lake Analytics.  Zadanie podsumowania jest wyświetlany na **zadania zarządzania** kafelka.

    ![Zarządzanie zadaniami usługi Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Zadania zarządzania zapewnia podstawowe informacje o stanie zadania. Należy zauważyć, że zadania nie powiodło się.
3. Kliknij przycisk **zadania zarządzania** Kafelek, aby wyświetlić zadania. Zadania są podzielone na **systemem**, **w kolejce**, i **zakończone**. Zostanie wyświetlona zadania nie powiodło się **zakończone** sekcji. Jest ona pierwsza z nich na liście. Jeśli masz wiele zadań, można kliknąć **filtru** ułatwiające można znaleźć zadania.

    ![Usługa Azure Data Lake Analytics filtrowania zadań](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Kliknij zadanie zakończone niepowodzeniem z listy, aby otworzyć szczegóły zadania:

    ![Azure Data Lake Analytics zadania nie powiodło się](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Powiadomienie **ponownie prześlij** przycisku. Po rozwiązaniu problemu należy ponownie przesłać zadania.
5. Kliknij część wyróżnione na poprzednim zrzucie ekranu, aby otworzyć szczegóły błędu.  Zostanie wyświetlona wyglądać mniej więcej tak:

    ![Azure Data Lake Analytics szczegóły zadania nie powiodło się](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Określa, że nie znaleziono folderu źródłowego.
6. Kliknij przycisk **zduplikowane skryptu**.
7. Aktualizacja **FROM** ścieżkę do:

    "/ Samples/Data/SearchLog.tsv"
8. Kliknij przycisk **Prześlij zadanie**.

## <a name="see-also"></a>Zobacz także
* [Omówienie programu Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Wprowadzenie do usługi Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-manage-use-portal.md)
