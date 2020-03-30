---
title: Jak monitorować udostępnianie danych platformy Azure
description: Dowiedz się, jak monitorować stan zaproszenia, współużytkować subskrypcje i historię migawek w usłudze Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490512"
---
# <a name="monitor-azure-data-share"></a>Monitorowanie usługi Azure Data Share  

W tym artykule wyjaśniono, jak można monitorować udziały danych przy użyciu usługi Azure Data Share. Jako dostawca danych możesz monitorować różne aspekty relacji udostępniania danych. Szczegóły, takie jak to, czy konsumenci danych zaakceptowali twoje zaproszenie do udziału danych, a także czy utworzyli subskrypcję udostępniania i zaczęli używać twoich danych, są dostępne do monitorowania. 

Jako konsument danych możesz monitorować migawki, które zostały wyzwolone w ramach subskrypcji platformy Azure. 

## <a name="monitor-invitation-status"></a>Monitoruj stan zaproszenia

Wyświetl stan zaproszeń do udostępniania danych, przechodząc do folderu Wysłane udziały -> Zaproszenia. 

![Stan zaproszenia](./media/invitation-status.png "Stan zaproszenia") 

Istnieją trzy stany, w których zaproszenie może być dostępne:

* Oczekujące — odbiorca udostępniania danych nie zaakceptował jeszcze zaproszenia.
* Zaakceptowane — odbiorca udostępniania danych zaakceptował zaproszenie.
* Odrzucone — odbiorca udziału danych odrzucił zaproszenie.

> [!IMPORTANT]
> Jeśli usuniesz zaproszenie po jego zaakceptowaniu, nie jest to równoznaczne z odwołaniem dostępu. Jeśli chcesz zatrzymać przyszłe migawki przed kopiowaniem do konta magazynu danych konsumentów, należy odwołać dostęp za pośrednictwem karty *Udostępnij subskrypcje.* 

## <a name="monitor-share-subscriptions"></a>Monitorowanie subskrypcji udziałów

Wyświetl stan subskrypcji udziałów, przechodząc do sekcji Wysłane udziały -> Subskrypcje akcji. Spowoduje to podanie szczegółowych informacji o aktywnych subskrypcjach utworzonych przez konsumentów danych po zaakceptowaniu zaproszenia. Możesz zatrzymać przyszłe aktualizacje dla konsumenta danych, wybierając subskrypcję udziału i wybierając *pozycję Odwołaj*. 

## <a name="snapshot-history"></a>Historia migawek 

Na karcie historia można wyświetlić migawki, które zostały skopiowane do dzierżawy konsumenta danych. Można monitorować częstotliwość i czas trwania każdego interwału migawki. 

![Historia migawek](./media/sent-shares.png "Historia migawek") 

Możesz wyświetlić więcej szczegółów na temat każdej migawki uruchomionej, klikając datę rozpoczęcia biegu. 

Domyślnie wyświetlana jest do 30 dni historii migawek. Jeśli chcesz zobaczyć historię o wartości ponad 30 dni, przejdź do pozycji Monitorowanie -> Ustawienia diagnostyczne i wybierz **pozycję Dodaj ustawienie diagnostyczne**. Musisz wybrać konto magazynu, aby przechowywać te dzienniki. 

![Historia migawek](./media/diagnostic-settings.png "Ustawienia diagnostyczne") 

## <a name="next-steps"></a>Następne kroki 

Dowiedz się więcej o [terminologii udostępniania danych platformy Azure](terminology.md)