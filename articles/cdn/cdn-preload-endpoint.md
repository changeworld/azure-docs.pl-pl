---
title: Wstępne ładowanie zasobów dla punktu końcowego usługi Azure CDN | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wstępne ładowanie pamięci podręcznej zawartości dla punktu końcowego usługi Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: mazha
ms.openlocfilehash: bf3161d756759e4b278e48ad7a49615e4a73d17f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Wstępne ładowanie zasobów w punkcie końcowym usługi Azure CDN
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Domyślnie zasoby są buforowane tylko wtedy, gdy są one wymagane. Ponieważ serwery krawędzi zawartość nie jest jeszcze buforowane i chcesz przesłać żądanie do serwera pochodzenia, pierwsze żądanie z każdego regionu może trwać dłużej niż kolejnych żądań. Aby uniknąć tego opóźnienia trafień pierwszej, wstępne ładowanie zasobów. Oprócz zapewnienia lepszej obsługi klienta, wstępne ładowanie pamięci podręcznej zasobów można ograniczyć ruch sieciowy na serwerze źródłowym.

> [!NOTE]
> Wstępne ładowanie zasobów jest przydatna przy dużych zdarzenia lub zawartości, która staje się jednocześnie dostępna dla wielu użytkowników, takich jak nowe wydanie filmu lub aktualizacji oprogramowania.
> 
> 

W tym samouczku przedstawiono wstępnego ładowania zawartości w pamięci podręcznej na wszystkich węzłach krawędzi Azure CDN.

## <a name="to-pre-load-assets"></a>Aby wstępne ładowanie zasobów
1. W [portalu Azure](https://portal.azure.com), przejdź do profilu CDN zawierający punkt końcowy do wstępnego załadowania. Zostanie otwarte okienko profilu.
    
2. Kliknij punkt końcowy na liście. Zostanie otwarte okienko punktu końcowego.
3. Wybierz z okienka punktu końcowego CDN **obciążenia**.
   
    ![Okienko punktu końcowego CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    **Obciążenia** zostanie otwarte okienko.
   
    ![Okienko obciążenia sieci CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Aby uzyskać **ścieżki zawartości**, wprowadź pełną ścieżkę każdego zasobu do załadowania (na przykład `/pictures/kitten.png`).
   
   > [!TIP]
   > Po rozpoczęciu wprowadzania tekstu, więcej **ścieżki zawartości** pola tekstowe pojawi się umożliwia utworzenie listy wiele zasobów. Aby usunąć zasoby z listy, kliknij przycisk wielokropka (...), a następnie wybierz **usunąć**.
   > 
   > Każda ścieżka zawartości musi być względnym adresem URL, który pasuje do następujących [wyrażeń regularnych](https://msdn.microsoft.com/library/az24scfc.aspx):  
   > - Ładowanie ścieżki pojedynczy plik: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Załaduj pojedynczy plik z ciągu zapytania: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Ponieważ każdy zasobów musi mieć własny ścieżkę, nie ma żadnych funkcji symbolu wieloznacznego wstępnego ładowania zasobów.
   > 
   > 
   
    ![Przycisk obciążenia](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Po zakończeniu wprowadzania ścieżek zawartości wybierz **obciążenia**.
   

> [!NOTE]
> Ma limitu 10 żądań obciążenia na minutę dla profilu sieci CDN i 50 równoczesnych ścieżek, które mogą być przetwarzane w tym samym czasie. Każda ścieżka ma limit długość ścieżki 1024 znaków.
> 
> 

## <a name="see-also"></a>Zobacz także
* [Przeczyszczanie punktu końcowego usługi Azure CDN](cdn-purge-endpoint.md)
* [Azure dokumentacji interfejsu API REST usługi CDN: wstępnie załadować zawartość dla punktu końcowego](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Azure dokumentacji interfejsu API REST usługi CDN: przeczyścić zawartości z punktu końcowego](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

