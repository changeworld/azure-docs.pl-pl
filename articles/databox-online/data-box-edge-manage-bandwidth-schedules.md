---
title: Usługa Azure Data Box Edge zarządza harmonogramami przepustowości | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać witryny Azure Portal do zarządzania harmonogramami przepustowości w witrynie Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f7b762d5502986c306de240519688aa639f58445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60756889"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Korzystanie z witryny Azure Portal do zarządzania harmonogramami przepustowości w witrynie Azure Data Box Edge  

W tym artykule opisano sposób zarządzania użytkownikami w usłudze Azure Data Box Edge. Harmonogramy przepustowości pozwalają konfigurować użycie przepustowości sieci w wielu harmonogramach dotyczących pory dnia. Te harmonogramy można zastosować do operacji przekazywania i pobierania wykonywanych z urządzenia do chmury.

Harmonogramy przepustowości dla usługi Data Box Edge można dodawać, modyfikować lub usuwać za pośrednictwem witryny Azure portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie harmonogramu
> * Modyfikowanie harmonogramu
> * Usuwanie harmonogramu


## <a name="add-a-schedule"></a>Dodawanie harmonogramu

Wykonaj następujące kroki w witrynie Azure portal, aby dodać harmonogram.

1. W witrynie Azure portal dla zasobu usługi Data Box Edge przejdź do **witryny Bandwidth**.
2. W prawym okienku wybierz pozycję **+ Dodaj harmonogram**.

    ![Wybierz przepustowość](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. W obszarze **Dodaj harmonogram**: 

   1. Podaj **Dzień rozpoczęcia**, **Dzień zakończenia**, **Godzinę rozpoczęcia** i **Godzinę zakończenia** uruchomienia harmonogramu.
   2. Sprawdź opcję **Cały dzień,** jeśli ten harmonogram powinien być uruchamiany przez cały dzień.
   3. **Współczynnik przepustowości** określa w megabitach na sekundę (Mb/s) przepustowość wykorzystywaną przez urządzenie w operacjach dotyczących chmury — zarówno przekazywania, jak i pobierania danych. Podaj liczbę z zakresu od 20 do 1 000 000 007 dla tego pola.
   4. Zaznacz pole **Nieograniczona** przepustowość, jeśli nie chcesz ustalać czasu przekazywania i pobierania.
   5. Wybierz pozycję **Dodaj**.

      ![Dodaj harmonogram](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Harmonogram zostanie utworzony przy użyciu wybranych parametrów. Następnie harmonogram zostanie wyświetlony w portalu na liście harmonogramów przepustowości.

    ![Zaktualizowano listę harmonogramów przepustowości](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Edytowanie harmonogramu

Wykonaj poniższe czynności, aby edytować harmonogram przepustowości.

1. W witrynie Azure portal przejdź do zasobu usługi Data Box Edge, a następnie przejdź do **witryny Bandwidth**. 
2. Z listy harmonogramów przepustowości wybierz i wybierz harmonogram, który chcesz zmodyfikować.
    ![Wybierz harmonogram przepustowości](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Wprowadź i zapisz odpowiednie zmiany.

    ![Modyfikowanie harmonogramu](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Lista harmonogramów zostanie zaktualizowana w celu uwzględnienia zmodyfikowanego harmonogramu.

    ![Modyfikowanie harmonogramu](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Usuwanie harmonogramu

Wykonaj następujące kroki, aby usunąć harmonogram przepustowości skojarzony z urządzeniem Data Box Edge.

1. W witrynie Azure portal przejdź do zasobu usługi Data Box Edge, a następnie przejdź do **witryny Bandwidth**.  

2. Na liście harmonogramów przepustowości wybierz harmonogram, który chcesz usunąć. W **harmonogramie edycji**wybierz pozycję **Usuń**. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **Tak**.

   ![Usuwanie użytkownika](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Po usunięciu harmonogramu lista harmonogramów zostanie zaktualizowana.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać udziałami](data-box-edge-manage-shares.md).
