---
title: Monitorowanie usługi Azure Data Lake Analytics — witryna Azure portal
description: W tym artykule opisano, jak używać witryny Azure Portal do rozwiązywania problemów z zadaniami usługi Azure Data Lake Analytics.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 7fb85f179f1e1e1ac873fc7d6d937b34c1d48ada
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71316588"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitor jobs in Azure Data Lake Analytics using the Azure Portal (Monitorowanie zadań w usłudze Azure Data Lake Analytics przy użyciu witryny Azure Portal)

**Aby wyświetlić wszystkie oferty pracy**

1. W witrynie Azure portal kliknij pozycję **Microsoft Azure** w lewym górnym rogu.
2. Kliknij kafelek z nazwą konta usługi Data Lake Analytics.  Podsumowanie zadania jest wyświetlane na kafelku **Zarządzanie zadaniami.**

    ![Zarządzanie zadaniami usługi Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Zarządzanie zadaniami daje rzut oka na stan zadania. Należy zauważyć, że zadanie nie powiodło się.
3. Kliknij kafelek **Zarządzanie zadaniami,** aby wyświetlić zadania. Zadania są podzielone na kategorie w **liczbach Uruchomionych**, **Kolejkowanych**i **Zakończonych**. Nieudane zadanie zostanie wyświetlone w sekcji **Zakończone.** Jest to pierwszy na liście. Jeśli masz wiele zadań, możesz kliknąć **przycisk Filtruj,** aby ułatwić znajdowanie zadań.

    ![Zadania filtrowania usługi Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Kliknij zadanie, które nie powiodło się z listy, aby otworzyć szczegóły zadania:

    ![Niepowodzenie zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Zwróć uwagę na przycisk **Prześlij ponownie.** Po rozwiązaniu problemu można ponownie przesłać zadanie.
5. Kliknij wyróżnioną część z poprzedniego zrzutu ekranu, aby otworzyć szczegóły błędu.  Zobaczysz coś takiego:

    ![Szczegóły zadania usługi Azure Data Lake Analytics nie powiodły się](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Informuje, że folder źródłowy nie został znaleziony.
6. Kliknij pozycję **Zduplikowany skrypt**.
7. Zaktualizuj ścieżkę **FROM** do:

    "/Przykłady/Dane/SearchLog.tsv"
8. Kliknij przycisk **Prześlij zadanie**.

## <a name="see-also"></a>Zobacz też
* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Wprowadzenie do pracy z usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-manage-use-portal.md)
