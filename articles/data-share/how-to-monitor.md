---
title: Jak monitorować wersję zapoznawczą usługi Azure Data Share
description: Dowiedz się, jak monitorować stan zaproszenia, udostępniać subskrypcje i historię migawek w wersji zapoznawczej usługi Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 22c95f1ac541e1288494ed85cc9654d42780ea60
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169118"
---
# <a name="monitor-azure-data-share-preview"></a>Monitorowanie wersji zapoznawczej udziału danych platformy Azure 

W tym artykule wyjaśniono, jak można monitorować udziały danych przy użyciu usługi Azure Data Share Preview. Jako dostawca danych możesz monitorować różne aspekty relacji udostępniania danych. Szczegóły, takie jak to, czy konsumenci danych zaakceptowali zaproszenie do udziału danych, a także czy utworzyły subskrypcję udziału i rozpoczęto korzystanie z danych, są dostępne do monitorowania. 

Jako odbiorca danych możesz monitorować migawki, które zostały wyzwolone w ramach subskrypcji platformy Azure. 

## <a name="monitor-invitation-status"></a>Monitoruj stan zaproszenia

Wyświetl stan zaproszeń udziału danych, przechodząc do obszaru wysłane udziały — > zaproszenia. 

![](./media/invitation-status.png "Status zaproszenia") do stanu zaproszenia 

Istnieją trzy stany, w których może znajdować się zaproszenie:

* Oczekiwanie — odbiorca udziału danych nie zaakceptował jeszcze zaproszenia.
* Zaakceptowano — odbiorca udziału danych zaakceptował zaproszenie.
* Odrzucone — odbiorca udziału danych odrzucił zaproszenie.

> [!IMPORTANT]
> Po usunięciu zaproszenia, które już zostało zaakceptowane, nie jest równoznaczne z cofnięciem dostępu. Jeśli chcesz zatrzymać kopiowanie przyszłych migawek do konta magazynu odbiorców danych, musisz odwołać dostęp za pośrednictwem karty *udostępnianie subskrypcji* . 

## <a name="monitor-share-subscriptions"></a>Monitoruj subskrypcje udziałów

Wyświetl stan subskrypcji udziałów, przechodząc do obszaru wysłane udziały — > udostępnić subskrypcje. Umożliwi to podanie szczegółowych informacji o aktywnych subskrypcjach utworzonych przez odbiorców danych po zaakceptowaniu zaproszenia. Możesz zatrzymać przyszłe aktualizacje dla konsumenta danych, wybierając pozycję Udostępnij subskrypcję i wybierając pozycję *odwołaj*. 

## <a name="snapshot-history"></a>Historia migawek 

Na karcie Historia można wyświetlić migawki, które zostały skopiowane do dzierżawy użytkownika danych. Można monitorować częstotliwość i czas trwania każdego interwału migawek. 

![](./media/sent-shares.png "Historia") migawek historii migawek 

Aby wyświetlić więcej szczegółów na temat poszczególnych migawek, kliknij datę rozpoczęcia przebiegu. 

Domyślnie zostanie wyświetlona historia migawek do 30 dni. Jeśli potrzebujesz więcej niż 30 dni historii, przejdź do obszaru monitorowanie — > Ustawienia diagnostyczne i wybierz pozycję **Dodaj ustawienie diagnostyczne**. Użytkownik musi wybrać konto magazynu, w którym będą przechowywane te dzienniki. 

(./media/diagnostic-settings.png "Ustawienia diagnostyczne") ![historii migawek] 

## <a name="next-steps"></a>Następne kroki 

Dowiedz się więcej o [terminologii dotyczącej udziałów danych platformy Azure](terminology.md)