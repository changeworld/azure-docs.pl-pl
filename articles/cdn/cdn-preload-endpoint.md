---
title: Wstępne ładowanie elementów zawartości w punkcie końcowym usługi Azure CDN | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i wstępne ładowanie pamięci podręcznej zawartości w punkcie końcowym usługi Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: magattus
ms.openlocfilehash: 0d34985c8d83e8adad43aeec36ead939d8b22132
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918369"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Wstępne ładowanie zasobów w punkcie końcowym usługi Azure CDN
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Domyślnie zasoby są buforowane, tylko wtedy, gdy są one wymagane. Ponieważ serwery krawędzi zawartość nie jest jeszcze buforowane i chcesz przekazywać żądania do serwera pochodzenia, pierwsze żądanie z każdego regionu może trwać dłużej niż kolejnych żądań. Aby uniknąć tego opóźnienia trafiony pierwszy, Załaduj wstępnie zasoby. Ponadto, aby zapewnić lepsze środowisko klienta, wstępnego ładowania zawartości pamięci podręcznej można zmniejszyć ruch sieciowy na serwerze źródłowym.

> [!NOTE]
> Wstępne ładowanie zasobów jest przydatne w przypadku dużych wydarzeń lub zawartości, która staje się równocześnie jest dostępna dla wielu użytkowników, takie jak nowe wydanie filmu lub aktualizacji oprogramowania.
> 
> 

Ten samouczek przeprowadzi Cię przez wstępne ładowanie zawartości pamięci podręcznej we wszystkich węzłach brzegowych usługi Azure CDN.

## <a name="to-pre-load-assets"></a>Wstępne ładowanie zasobów
1. W [witryny Azure portal](https://portal.azure.com), przejdź do profilu usługi CDN zawierającego punkt końcowy do wstępnego załadowania. Zostanie otwarte okienko profilu.
    
2. Kliknij punkt końcowy na liście. Zostanie otwarte okienko punktu końcowego.
3. W okienku punktu końcowego usługi CDN wybierz **obciążenia**.
   
    ![Okienko punktu końcowego usługi CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    **Obciążenia** zostanie otwarte okienko.
   
    ![Okienko obciążenia sieci CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Aby uzyskać **ścieżki zawartości**, wprowadź pełną ścieżkę każdego zasobu do załadowania (na przykład `/pictures/kitten.png`).
   
   > [!TIP]
   > Po rozpoczęciu wprowadzania tekstu, więcej **ścieżki zawartości** pól tekstowych, pojawi się umożliwia tworzenie listy wielu zasobów. Aby usunąć zasoby z listy, kliknij przycisk wielokropka (...), a następnie wybierz **Usuń**.
   > 
   > Każda ścieżka zawartości musi być względnym adresem URL, która pasuje do następujących [wyrażeń regularnych](/dotnet/standard/base-types/regular-expression-language-quick-reference):  
   > - Ładuj ścieżkę pojedynczego pliku: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Załaduj pojedynczy plik z ciągu zapytania: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Każdy zasób musi mieć swój własny ścieżki, nie istnieje żadne funkcje symboli wieloznacznych wstępne ładowanie zasobów.
   > 
   > 
   
    ![Przycisk Załaduj](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Po zakończeniu wprowadzania ścieżki zawartości, wybierz **obciążenia**.
   

> [!NOTE]
> Obowiązuje limit 10 żądań obciążenia na minutę na profil usługi CDN i 50 współbieżnych ścieżek, które mogą być przetwarzane w tym samym czasie. Każda ścieżka ma limit długość ścieżki 1024 znaków.
> 
> 

## <a name="see-also"></a>Zobacz także
* [Przeczyszczanie punktu końcowego usługi Azure CDN](cdn-purge-endpoint.md)
* [Dokumentacja interfejsu API REST usługi CDN Azure: Wstępne ładowanie zawartości w punkcie końcowym](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Dokumentacja interfejsu API REST usługi CDN Azure: Przeczyszczanie zawartości z punktu końcowego](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

