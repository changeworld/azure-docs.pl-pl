---
title: Usługa Azure Edge pole danych zarządzania harmonogramami przepustowości | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia witryny Azure portal do zarządzania harmonogramami przepustowości na krawędzi sieci Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f7b762d5502986c306de240519688aa639f58445
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60756889"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Użyj witryny Azure portal do zarządzania harmonogramami przepustowości na krawędzi sieci Azure Data Box  

W tym artykule opisano, jak zarządzać użytkownikami na krawędzi sieci Azure Data Box. Harmonogramy przepustowości pozwalają konfigurować użycie przepustowości sieci w wielu harmonogramach dotyczących pory dnia. Te harmonogramy można zastosować do operacji przekazywania i pobierania wykonywanych z urządzenia do chmury.

Możesz dodawać, modyfikowanie lub usuwanie harmonogramów przepustowości dla usługi Edge pola danych za pośrednictwem witryny Azure portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie harmonogramu
> * Modyfikowanie harmonogramu
> * Usuwanie harmonogramu


## <a name="add-a-schedule"></a>Dodawanie harmonogramu

Wykonaj następujące czynności w witrynie Azure portal, aby dodać zgodnie z harmonogramem.

1. W witrynie Azure portal dla zasobu krawędź pola danych, przejdź do **przepustowości**.
2. W okienku po prawej stronie wybierz **+ Dodaj harmonogram**.

    ![Wybierz przepustowość](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. W obszarze **Dodaj harmonogram**: 

   1. Podaj **Dzień rozpoczęcia**, **Dzień zakończenia**, **Godzinę rozpoczęcia** i **Godzinę zakończenia** uruchomienia harmonogramu.
   2. Sprawdź **cały dzień** opcję, jeśli ten harmonogram powinno być ono uruchomione cały dzień.
   3. **Współczynnik przepustowości** określa w megabitach na sekundę (Mb/s) przepustowość wykorzystywaną przez urządzenie w operacjach dotyczących chmury — zarówno przekazywania, jak i pobierania danych. Podaj liczbę z zakresu od 20 i 1,000,000,007 dla tego pola.
   4. Zaznacz pole **Nieograniczona** przepustowość, jeśli nie chcesz ustalać czasu przekazywania i pobierania.
   5. Wybierz pozycję **Dodaj**.

      ![Dodaj harmonogram](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Harmonogram zostanie utworzony przy użyciu wybranych parametrów. Następnie harmonogram zostanie wyświetlony w portalu na liście harmonogramów przepustowości.

    ![Zaktualizowaną listę harmonogramy przepustowości](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Edytowanie harmonogramu

Wykonaj poniższe czynności, aby edytować harmonogram przepustowości.

1. W witrynie Azure portal przejdź do zasobu krawędź pola danych, a następnie przejdź do **przepustowości**. 
2. Z listy harmonogramy przepustowości wybierz, a następnie wybierz harmonogram, który chcesz zmodyfikować.
    ![Wybierz harmonogram przepustowości](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Wprowadź i zapisz odpowiednie zmiany.

    ![Modyfikowanie harmonogramu](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Lista harmonogramów zostanie zaktualizowana w celu uwzględnienia zmodyfikowanego harmonogramu.

    ![Modyfikowanie harmonogramu](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Usuwanie harmonogramu

Wykonaj poniższe kroki, aby usunąć harmonogram przepustowości, skojarzone z urządzenia usługi Edge pola danych.

1. W witrynie Azure portal przejdź do zasobu krawędź pola danych, a następnie przejdź do **przepustowości**.  

2. Na liście harmonogramów przepustowości wybierz harmonogram, który chcesz usunąć. W **Edycja harmonogramu**, wybierz opcję **Usuń**. Po wyświetleniu monitu o potwierdzenie, wybierz **tak**.

   ![Usuwanie użytkownika](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Po usunięciu harmonogramu lista harmonogramów zostanie zaktualizowana.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Zarządzanie udziałami](data-box-edge-manage-shares.md).
