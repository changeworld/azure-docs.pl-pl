---
title: Wstępne ładowanie zasobów w punkcie końcowym usługi Azure CDN | Dokumenty firmy Microsoft
description: Dowiedz się, jak wstępnie załadować zawartość buforowaną w punkcie końcowym usługi Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: allensu
ms.openlocfilehash: c45d0a9195a719d830753a9614cfa7efb6f1c23d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260279"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Wstępne ładowanie zasobów w punkcie końcowym usługi Azure CDN
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Domyślnie zasoby są buforowane tylko wtedy, gdy są wymagane. Ponieważ serwery brzegowe nie zostały jeszcze buforowane zawartości i trzeba przesłać dalej żądanie do serwera pochodzenia, pierwsze żądanie z każdego regionu może trwać dłużej niż kolejne żądania. Aby uniknąć tego opóźnienia pierwszego trafienia, wstępnie załaduj zasoby. Oprócz zapewnienia lepszej obsługi klienta, wstępne ładowanie buforowanych zasobów może zmniejszyć ruch sieciowy na serwerze pochodzenia.

> [!NOTE]
> Wstępne ładowanie zasobów jest przydatne w przypadku dużych zdarzeń lub zawartości, które stają się jednocześnie dostępne dla wielu użytkowników, takie jak nowa wersja filmu lub aktualizacja oprogramowania.
> 
> 

W tym samouczku można przejść przez wstępne ładowanie zawartości w pamięci podręcznej we wszystkich węzłach brzegowych usługi Azure CDN.

## <a name="to-pre-load-assets"></a>Aby wstępnie załadować zasoby
1. W [witrynie Azure portal](https://portal.azure.com)przejdź do profilu sieci CDN zawierającego punkt końcowy, który chcesz wstępnie załadować. Zostanie otwarte okienko profilu.
    
2. Kliknij punkt końcowy na liście. Zostanie otwarte okienko punktu końcowego.
3. W okienku punktu końcowego sieci CDN wybierz pozycję **Załaduj**.
   
    ![Okienko punktu końcowego sieci CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Zostanie otwarte okienko **Wczytywnie.**
   
    ![Okienko ładowania sieci CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. W przypadku **ścieżki zawartości**wprowadź pełną ścieżkę każdego zasobu, `/pictures/kitten.png`który chcesz załadować (na przykład ).
   
   > [!TIP]
   > Po rozpoczęciu wprowadzania tekstu pojawi się więcej pól tekstowych **Ścieżka zawartości,** aby umożliwić tworzenie listy wielu zasobów. Aby usunąć zasoby z listy, wybierz przycisk wielokropka (...) a następnie wybierz pozycję **Usuń**.
   > 
   > Każda ścieżka zawartości musi być względnym adresem URL, który pasuje do następujących [wyrażeń regularnych:](/dotnet/standard/base-types/regular-expression-language-quick-reference)  
   > - Załaduj pojedynczą ścieżkę pliku:`^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Załaduj pojedynczy plik z ciągiem zapytania:`^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Ponieważ każdy zasób musi mieć własną ścieżkę, nie ma funkcji symboli wieloznacznych dla wstępnie ładowania zasobów.
   > 
   > 
   
    ![Przycisk Załaduj](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Po zakończeniu wprowadzania ścieżek zawartości wybierz pozycję **Załaduj**.
   

> [!NOTE]
> Istnieje limit 10 żądań obciążenia na minutę na profil sieci CDN i 50 równoczesnych ścieżek mogą być przetwarzane w tym czasie. Każda ścieżka ma limit długości ścieżki 1024 znaków.
> 
> 

## <a name="see-also"></a>Zobacz też
* [Czyszczenie punktu końcowego usługi Azure CDN](cdn-purge-endpoint.md)
* [Odwołanie do interfejsu API rest usługi Azure CDN: wstępnie wczytać zawartość do punktu końcowego](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Odwołanie do interfejsu API REST usługi Azure CDN: przeczyszczanie zawartości z punktu końcowego](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

