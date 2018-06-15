---
title: Debugowanie publikowania usługi z programu Visual Studio i IntelliTrace w chmurze platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można debugować usługi chmury za pomocą programu Visual Studio i IntelliTrace
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/21/2017
ms.author: mikejo
ms.openlocfilehash: 2ca15bd5ffa88d2e8053decf5b81c265b1d9c6e1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
ms.locfileid: "30292561"
---
# <a name="debugging-a-published-azure-cloud-service-with-visual-studio-and-intellitrace"></a>Debugowanie usługi opublikowana chmura Azure za pomocą programu Visual Studio i IntelliTrace
Przy użyciu funkcji IntelliTrace można rejestrować szeroką gamę informacji o debugowaniu dla wystąpienia roli, po uruchomieniu na platformie Azure. Jeśli potrzebujesz ustalić przyczynę problemu, można użyć dzienniki IntelliTrace do wykonania kroków opisanych kodu w programie Visual Studio, tak jakby były uruchomione w systemie Azure. W efekcie rekordów funkcji IntelliTrace klawisz wykonanie kodu i danych środowiska aplikacji platformy Azure działa jako usługa w chmurze na platformie Azure i pozwala odtwarzać zarejestrowanych danych z programu Visual Studio. 

Można użyć funkcji IntelliTrace, jeśli masz zainstalowany program Visual Studio Enterprise i celów aplikacji Azure .NET Framework 4 lub nowszy. IntelliTrace zbiera informacje dotyczące poszczególnych ról platformy Azure. Maszyny wirtualnej dla tych ról są zawsze uruchamiane w 64-bitowych systemach operacyjnych.

Alternatywnie, można użyć [zdalnego debugowania](http://go.microsoft.com/fwlink/p/?LinkId=623041) dołączyć bezpośrednio do usługi w chmurze, która działa na platformie Azure.

> [!IMPORTANT]
> IntelliTrace jest przeznaczona tylko w scenariuszach debugowania i nie powinna być używana w przypadku wdrożenia produkcyjnego.
> 

## <a name="configure-an-azure-application-for-intellitrace"></a>Skonfigurować aplikację Azure dla funkcji IntelliTrace
Aby włączyć IntelliTrace aplikacji platformy Azure, należy utworzyć i opublikować aplikację z projektu programu Visual Studio Azure. Przed opublikowaniem go na platformie Azure, należy skonfigurować IntelliTrace dla aplikacji platformy Azure. W przypadku publikowania aplikacji bez konfigurowania funkcji IntelliTrace, należy ponownie opublikować projekt. Aby uzyskać więcej informacji, zobacz [publikowania Azure cloud services projektów przy użyciu programu Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Gdy wszystko będzie gotowe do wdrożenia aplikacji platformy Azure, sprawdź, czy ustawiono celów kompilacji projektu **debugowania**.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz z menu kontekstowego **publikowania**.
   
1. W **publikowanie aplikacji platformy Azure** okno dialogowe, wybierz subskrypcję platformy Azure i wybierz **dalej**.

1. W **ustawienia** wybierz pozycję **Zaawansowane ustawienia** kartę.

1. Włącz **włączyć IntelliTrace** opcję, aby zbierać dzienniki IntelliTrace dla aplikacji, gdy zostanie opublikowany w chmurze.
   
1. Aby dostosować podstawową konfigurację funkcji IntelliTrace, wybierz opcję **ustawienia** obok **włączyć IntelliTrace**.

    ![Łącze Ustawienia funkcji IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/intellitrace-settings-link.png)
   
1. W **ustawienia IntelliTrace** okna dialogowego, można określić zdarzeń do dziennika, czy do zbierania informacji o wywołaniu, które moduły i procesów do zbierania dzienników dla i ilości wykorzystanego miejsca można przydzielić do zapisu. Aby uzyskać więcej informacji o funkcji IntelliTrace, zobacz [debugowanie przy użyciu funkcji IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).
   
    ![Ustawienia funkcji IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

Dziennika funkcji IntelliTrace jest cyklicznego pliku dziennika maksymalny rozmiar określony w ustawieniach funkcji IntelliTrace (domyślny rozmiar to 250 MB). Dzienniki IntelliTrace są zbierane w pliku w systemie plików maszyny wirtualnej. W przypadku żądania dzienniki, migawka jest podjęte w danym momencie i pobrane na komputer lokalny.

Po opublikowaniu usługi w chmurze Azure do platformy Azure, można określić, jeśli IntelliTrace zostało włączone w węźle Azure w **Eksploratora serwera**, jak pokazano na poniższej ilustracji:

![Eksplorator serwera - IntelliTrace włączone](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="download-intellitrace-logs-for-a-role-instance"></a>Pobierz dzienniki IntelliTrace dla wystąpienia roli
Za pomocą programu Visual Studio, możesz pobrać dzienniki IntelliTrace dla wystąpienia roli wykonaj następujące czynności:

1. W **Eksploratora serwera**, rozwiń węzeł **usługi w chmurze** węzeł, a następnie zlokalizuj wystąpienia roli, których chcesz pobrać dzienniki. 

1. Kliknij prawym przyciskiem myszy wystąpienia roli, a następnie wybierz z menu kontekstowego s **Wyświetl dzienniki IntelliTrace**. 

    ![Wyświetlanie opcji menu Dzienniki IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/view-intellitrace-logs.png)

1. Dzienniki IntelliTrace są pobierane do pliku w katalogu na komputerze lokalnym. Zawsze zażądania IntelliTrace w dziennikach, utworzono nową migawkę. Podczas pobierania dzienników programu Visual Studio Wyświetla postęp operacji w **dziennika aktywności platformy Azure** okna. Jak pokazano na poniższej ilustracji, można rozszerzyć elementu wiersza dla tej operacji zobaczyć więcej szczegółów.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Można kontynuować pracę w programie Visual Studio podczas pobierania dzienników IntelliTrace. Dziennik zakończył pobieranie, zostanie otwarty w programie Visual Studio.

> [!NOTE]
> Dzienniki IntelliTrace może zawierać wyjątki, które w ramach generuje i obsługuje później. Kod wewnętrzny framework generuje tych wyjątków w ramach normalnego uruchamiania roli, więc można je bezpiecznie zignorować.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
- [Opcje debugowania usług w chmurze Azure](vs-azure-tools-debugging-cloud-services-overview.md)
- [Publikowanie usługi w chmurze platformy Azure przy użyciu programu Visual Studio](vs-azure-tools-publishing-a-cloud-service.md)