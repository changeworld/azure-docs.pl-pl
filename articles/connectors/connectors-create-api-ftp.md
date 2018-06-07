---
title: Połącz z serwerem FTP - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie, monitorowanie i zarządzanie plikami na serwerze FTP z usługi Azure Logic Apps
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 07/22/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 3bb8c750846ee14331c749aeece4315d5c16d411
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610569"
---
# <a name="get-started-with-the-ftp-connector"></a>Rozpoczynanie pracy z łącznik FTP
Użyj konektora serwera FTP do monitorowania, zarządzania i utworzyć pliki na serwerze FTP. 

Aby użyć [każdy łącznik](apis-list.md), należy najpierw utworzyć aplikację logiki. Możesz rozpocząć pracę przez [teraz tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-ftp"></a>Nawiązać połączenie FTP
Zanim aplikację logiki można uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenia* do usługi. A [połączenia](connectors-overview.md) udostępnia łączność między aplikacji logiki i innej usługi.  

### <a name="create-a-connection-to-ftp"></a>Utwórz połączenie FTP
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>Użyj wyzwalacz FTP
Wyzwalacz to zdarzenie służy do uruchomienia przepływu pracy zdefiniowanych w aplikacji logiki. [Dowiedz się więcej o wyzwalaczy](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!IMPORTANT]
> Łącznik FTP wymaga, aby serwer FTP jest dostępny z Internetu, który jest skonfigurowany do pracy w trybie PASYWNYM. Ponadto łącznik FTP jest **nie jest zgodne z niejawnych FTPS (FTP za pośrednictwem protokołu SSL)**. Łącznik FTP obsługuje tylko jawne FTPS (FTP za pośrednictwem protokołu SSL).  
> 
> 

W tym przykładzie, I opisano sposób użycia **FTP - podczas dodawania lub modyfikowania pliku** wyzwalacz tak, aby inicjować przepływ pracy aplikacji logiki, po dodaniu pliku lub zmodyfikowane na serwerze FTP. W przykładzie przedsiębiorstwa można użyć tego wyzwalacza do monitorowania folderu FTP dla nowych plików, które reprezentują zamówień od klientów.  Można następnie użyć akcji łącznik FTP takich jak **pobrać zawartość pliku** do uzyskania zawartości zlecenia dla dalszego przetwarzania i przechowywania w bazie danych zamówienia.

1. Wprowadź *ftp* w polu wyszukiwania w Projektancie aplikacji logiki wybiorą **FTP - podczas dodawania lub modyfikowania pliku** wyzwalacza   
   ![Obraz wyzwalacza FTP 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   **Podczas dodawania lub modyfikowania pliku** otwiera formantu  
   ![Wyzwalacz FTP — obraz 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Wybierz **...**  znajdujący się po prawej stronie formantu. Spowoduje to otwarcie formant wyboru folderu  
   ![Obraz wyzwalacza FTP 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Wybierz **>** (Strzałka w prawo) i przejdź do folderu, który chcesz monitorować nowe lub zmodyfikowane pliki. Wybierz folder i zwróć uwagę, folder zostanie wyświetlona w **folderu** formantu.  
   ![Obraz wyzwalacza FTP 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

W tym momencie aplikację logiki została skonfigurowana z wyzwalaczy, które rozpocznie wykonywania innych wyzwalacze i akcje w przepływie pracy zmodyfikowanie lub utworzony w folderze FTP określonego pliku. 

> [!NOTE]
> Dla aplikacji logiki działała musi ona zawierać przynajmniej jeden wyzwalacz i jedną akcję. Wykonaj kroki opisane w następnej sekcji, aby dodać akcję.  
> 
> 

## <a name="use-a-ftp-action"></a>Za pomocą akcji FTP
Akcja jest przeprowadzane przez przepływ pracy zdefiniowanych w aplikacji logiki operacji. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Teraz, wyzwalacz został dodany, wykonaj następujące kroki, aby dodać akcję, która pobierze zawartość pliku nowe lub zmodyfikowane, znalezione przez wyzwalacz.    

1. Wybierz **+ nowy krok** Aby dodać akcję do uzyskania zawartości pliku na serwerze FTP  
2. Wybierz **Dodaj akcję** łącza.  
   ![Obraz akcji FTP 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Wprowadź *FTP* aby wyszukać wszystkie operacje dotyczące FTP.
4. Wybierz **FTP — pobranie zawartości pliku** jako akcję wykonywaną, gdy nowe lub zmodyfikowane pliku znajduje się w folderze FTP.      
   ![Obraz akcji FTP 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   **Pobrać zawartość pliku** sterowania zostanie otwarta. **Uwaga**: pojawi się monit o autoryzowanie aplikację logiki, aby uzyskać dostęp do konta serwera FTP, jeśli nie zostało zrobione to wcześniej.  
   ![Obraz akcji FTP 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Wybierz **pliku** formantu (biały znak znajdujący się poniżej ** pliku ***). W tym miejscu służy różnych właściwości z pliku nowe lub zmodyfikowane na serwerze FTP.  
6. Wybierz **plików zawartości** opcji.  
   ![Obraz akcji FTP 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. Formant jest aktualizowany, co oznacza, że **FTP — pobranie zawartości pliku** akcji uzyskają *plików zawartości* pliku nowe lub zmodyfikowane na serwerze FTP.      
   ![Obraz akcji FTP 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Zapisz swoją pracę, a następnie dodaj plik do folderu FTP, aby przetestować przepływ pracy.    

W tym momencie aplikacji logiki została skonfigurowana z wyzwalaczem monitorowania folderu na serwerze FTP i inicjowania przepływu pracy, gdy znajdzie się nowy plik lub zmodyfikowany plik na serwerze FTP. 

Aplikacja logiki również została skonfigurowana z akcją do uzyskania zawartości pliku nowe lub zmodyfikowane.

Można teraz dodawać innej akcji takich jak [SQL Server — Wstaw wiersz](connectors-create-api-sqlazure.md) akcji, aby wstawić zawartość pliku nowe lub zmodyfikowane do tabeli bazy danych SQL.  

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/ftpconnector/). 

## <a name="next-steps"></a>Następne kroki
[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

