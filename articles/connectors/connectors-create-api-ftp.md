---
title: Nawiązać połączenie z serwerem FTP — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie, monitorowanie i zarządzanie plikami na serwerze FTP za pomocą usługi Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 07/22/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 4355a767d2ecd500662cdf4522e8a7e12de86b80
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866155"
---
# <a name="get-started-with-the-ftp-connector"></a>Rozpoczynanie pracy z łącznikiem usługi FTP
Łącznik FTP umożliwia monitorowanie, zarządzanie i tworzenia plików na serwerze FTP. 

Aby użyć [każdy łącznik](apis-list.md), najpierw musisz utworzyć aplikację logiki. Możesz rozpocząć od [teraz tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-ftp"></a>Nawiązać połączenie FTP
Zanim aplikacja logiki może uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenia* do usługi. A [połączenia](connectors-overview.md) zapewnia łączność między aplikacją logiki i inną usługę.  

### <a name="create-a-connection-to-ftp"></a>Utwórz połączenie FTP
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>Użyj wyzwalacza FTP
Wyzwalacz to zdarzenie, który może służyć do uruchamiania tego przepływu, zdefiniowane w aplikacji logiki. [Dowiedz się więcej na temat wyzwalaczy](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!IMPORTANT]
> Łącznik FTP wymaga serwer FTP, który jest dostępny z Internetu i jest skonfigurowany do pracy w trybie PASYWNYM. Ponadto łącznik FTP jest **nie jest zgodna z niejawne FTPS (FTP za pośrednictwem protokołu SSL)**. Łącznik FTP obsługuje tylko jawne FTPS (FTP za pośrednictwem protokołu SSL).  
> 
> 

W tym przykładzie I opisano, jak używać **FTP — po dodaniu lub zmodyfikowaniu pliku** wyzwalacz tak, aby zainicjować przepływ pracy aplikacji logiki, gdy plik zostanie dodany do lub zmodyfikowane na serwerze FTP. W przykładzie przedsiębiorstwa można użyć do monitorowania folderu FTP dla nowych plików, które reprezentują zamówień klientów korzystających z tego wyzwalacza.  Możesz także użyć akcji łącznik FTP takich jak **Pobierz zawartość pliku** można pobrać zawartości kolejność potrzeby dalszego przetwarzania i przechowywania w bazie danych zamówień.

1. Wprowadź *ftp* w polu wyszukiwania w Projektancie aplikacji usługi Logic Apps a następnie zaznacz **FTP — po dodaniu lub zmodyfikowaniu pliku** wyzwalacza   
   ![Obraz wyzwalacza FTP 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   **Po dodaniu lub zmodyfikowaniu pliku** otwiera kontrolki  
   ![Obraz wyzwalacza FTP 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Wybierz **...**  znajdujący się po prawej stronie formantu. Spowoduje to otwarcie kontrolkę selektora folderów  
   ![Obraz wyzwalacza FTP 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Wybierz **>** (Strzałka w prawo) i przejdź do folderu, który chcesz monitorować, czy nowe lub zmodyfikowane pliki. Wybierz folder i zwróć uwagę, folder jest teraz wyświetlany w **folderu** kontroli.  
   ![Obraz wyzwalacza FTP 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

W tym momencie skonfigurowano aplikację logiki z wyzwalaczem, który spowoduje rozpoczęcie wykonywania innych wyzwalaczy i akcji w przepływie pracy, gdy plik zostanie zmodyfikowany lub utworzony w określonym folderze FTP. 

> [!NOTE]
> Dla aplikacji logiki działała prawidłowo musi zawierać co najmniej jeden wyzwalacz i jedną akcję. Wykonaj kroki opisane w następnej sekcji, aby dodać akcję.  
> 
> 

## <a name="use-a-ftp-action"></a>Używanie akcji FTP
Akcja jest operacją przeprowadzanych przez przepływ pracy zdefiniowane w aplikacji logiki. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Teraz, że dodano wyzwalacza, wykonaj następujące kroki, aby dodać akcję, która pobierze zawartość pliku nowe lub zmodyfikowane przez wyzwalacz.    

1. Wybierz **+ nowy krok** do dodawania akcji, aby pobrać zawartość pliku na serwerze FTP  
2. Wybierz **Dodaj akcję** łącza.  
   ![Obraz akcji FTP 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Wprowadź *FTP* aby wyszukać wszystkie akcje związane z FTP.
4. Wybierz **FTP — Pobierz zawartość pliku** jako akcję do wykonania, gdy nowy lub zmodyfikowany plik znajduje się w folderze FTP.      
   ![Obraz akcji FTP 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   **Pobierz zawartość pliku** sterowania zostanie otwarta. **Uwaga**: zostanie wyświetlony monit, aby autoryzować aplikację logiki, aby uzyskać dostęp do konta serwera FTP, gdy użytkownik nie zostało zrobione wcześniej.  
   ![Obraz akcji FTP 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Wybierz **pliku** kontroli (biały znajdujący się poniżej ** pliku ***). W tym miejscu służy różne właściwości z pliku nowe lub zmodyfikowane na serwerze FTP.  
6. Wybierz **zawartość pliku** opcji.  
   ![Obraz akcji FTP 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. Kontrolka jest aktualizowany, co oznacza, że **FTP — Pobierz zawartość pliku** akcji otrzymają *zawartość pliku* pliku nowe lub zmodyfikowane na serwerze FTP.      
   ![Obraz akcji FTP 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Zapisz swoją pracę, a następnie dodaj plik do folderu FTP, aby przetestować przepływ pracy.    

W tym momencie skonfigurowano aplikację logiki z wyzwalaczem do monitorowania folderu na serwerze FTP i inicjować przepływ pracy, gdy znajdzie się nowy plik lub zmodyfikowanego pliku na serwerze FTP. 

Aplikacja logiki również została skonfigurowana przy użyciu akcji, aby pobrać zawartość pliku nowe lub zmodyfikowane.

Teraz możesz dodać kolejną akcję taką jak [programu SQL Server — Wstaw wiersz](connectors-create-api-sqlazure.md) akcji, aby wstawić zawartość pliku nowych lub zmodyfikowanych do tabeli bazy danych SQL.  

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/ftpconnector/). 

## <a name="next-steps"></a>Następne kroki
[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

