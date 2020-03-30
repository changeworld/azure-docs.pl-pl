---
title: Zarządzanie wystąpieniami alertów w usłudze Azure Monitor
description: Zarządzanie wystąpieniami alertów na platformie Azure
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667622"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Zarządzanie wystąpieniami alertów za pomocą ujednoliconych alertów

Dzięki [ujednoliconym środowisko alertów](https://aka.ms/azure-alerts-overview) w usłudze Azure Monitor możesz zobaczyć wszystkie różne typy alertów na platformie Azure. Obejmuje to wiele subskrypcji w jednym okienku. W tym artykule pokazano, jak można wyświetlić wystąpienia alertów i jak znaleźć określone wystąpienia alertów do rozwiązywania problemów.

> [!NOTE]
> Dostęp do alertów generowanych w ciągu ostatnich 30 dni można uzyskać tylko.

## <a name="go-to-the-alerts-page"></a>Przejdź do strony alertów

Możesz przejść do strony alertów w dowolny z następujących sposobów:

- W [witrynie Azure portal](https://portal.azure.com/)wybierz pozycję **Monitor alerty** > **Alerts**.  

     ![Zrzut ekranu przedstawiający alerty monitora](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Użyj kontekstu określonego zasobu. Otwórz zasób, przejdź do sekcji **Monitorowanie** i wybierz pozycję **Alerty**. Strona docelowa jest wstępnie filtrowana pod kątem alertów dotyczących tego określonego zasobu.

     ![Zrzut ekranu przedstawiający alerty monitorowania zasobów](media/alerts-managing-alert-instances/alert-resource.JPG)

- Użyj kontekstu określonej grupy zasobów. Otwórz grupę zasobów, przejdź do sekcji **Monitorowanie** i wybierz pozycję **Alerty**. Strona docelowa jest wstępnie filtrowana pod kątem alertów w tej określonej grupie zasobów.    

     ![Zrzut ekranu przedstawiający alerty monitorowania grupy zasobów](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Znajdowanie wystąpień alertów

Strona **Podsumowanie alertów** zawiera omówienie wszystkich wystąpień alertów na platformie Azure. Widok podsumowania można zmodyfikować, wybierając **wiele subskrypcji** (maksymalnie 5) lub filtrując między **grupami zasobów,** **określonymi zasobami**lub **zakresami czasu.** Wybierz **opcję Łączna liczba alertów**lub dowolny z pasm ważności, aby przejść do widoku listy alertów.     

![Zrzut ekranu przedstawiający stronę Podsumowanie alertów](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Na stronie **Wszystkie alerty** są wyświetlane wszystkie wystąpienia alertów na platformie Azure. Jeśli zbliżasz się do portalu z powiadomienia alertu, możesz użyć filtrów dostępnych do zawężenia w tym określonym wystąpieniu alertu.

> [!NOTE]
> Jeśli na stronie pojawił się jeden z pasm ważności, lista jest wstępnie filtrowana pod kątem tej ważności.

Oprócz filtrów dostępnych na poprzedniej stronie można również filtrować na podstawie usługi monitora (na przykład platformy dla metryk), stanu monitorowania (zwolnionego lub rozwiązanego), ważności, stanu alertu (nowy/potwierdzony/zamknięty) lub identyfikatora grupy inteligentnej.

![Zrzut ekranu przedstawiający stronę Wszystkie alerty](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Jeśli na stronie pojawił się jeden z pasm ważności, lista jest wstępnie filtrowana pod kątem tej ważności.

Wybranie dowolnego wystąpienia alertu powoduje otwarcie strony **Szczegóły alertu,** dzięki czemu można wyświetlić więcej szczegółów na temat tego konkretnego wystąpienia alertu.   

![Zrzut ekranu przedstawiający stronę Szczegóły alertu](media/alerts-managing-alert-instances/alert-details.jpg)  

