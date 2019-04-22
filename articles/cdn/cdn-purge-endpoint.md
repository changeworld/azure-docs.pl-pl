---
title: Przeczyszczanie punktu końcowego usługi Azure CDN | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeczyścić wszystkie buforowanej zawartości z punktu końcowego usługi Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: magattus
ms.openlocfilehash: 76e7817be81a97c8d1a0b9ca2fea8378c3c733e1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916483"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Przeczyszczanie punktu końcowego usługi Azure CDN
## <a name="overview"></a>Omówienie
Węzły brzegowe w usłudze Azure CDN będzie zasobów w pamięci podręcznej do momentu wygaśnięcia zasobu czas wygaśnięcia (TTL).  Po wygaśnięciu czasu wygaśnięcia zasobu, gdy klient zażąda zawartości z węzłem krawędzi, w węźle brzegowym powoduje pobranie zaktualizowanych nową kopię zasobu ma obsługiwać żądania klienta i magazynu odświeżyć pamięć podręczną.

Najlepszym rozwiązaniem jest, aby upewnić się, że użytkownicy zawsze uzyskać najnowszą kopię zawartości jest wersja zasobów dla każdej aktualizacji i opublikować je jako nowe adresy URL.  Sieci CDN pobierze natychmiast nowe zasoby dla następnego żądania klienta.  Czasami chcesz przeczyścić pamięci podręcznej zawartość ze wszystkich węzłów brzegowych i wymusić na nich wszystkich pobrać nowe zaktualizowane zasoby.  Może to być spowodowane aktualizacje do aplikacji sieci web lub szybko zasobów aktualizacji, które zawierają nieprawidłowe informacje.

> [!TIP]
> Należy zauważyć, że tylko przeczyszczanie czyści buforowanej zawartości na serwerach granicznych usługi CDN.  Wszelkie pomięci podręczne podrzędne, takie jak serwery proxy i pamięci podręcznej lokalnej przeglądarki, może nadal przechowywać pamięci podręcznej kopię pliku.  Należy pamiętać o tym, po ustawieniu pliku time-to-live.  Możesz wymusić podrzędnego klienta do żądania najnowszą wersję pliku, polega na przekazaniu mu unikatową nazwę, za każdym razem, gdy go zaktualizować lub wykorzystując [buforowanie ciągu zapytania](cdn-query-string.md).  
> 
> 

Ten samouczek przeprowadzi Cię przez czyszczenie zasobów ze wszystkich węzłów brzegowych punktu końcowego.

## <a name="walkthrough"></a>Przewodnik
1. W [witryny Azure Portal](https://portal.azure.com), przejdź do profilu usługi CDN zawierającego punkt końcowy, które chcesz przeczyścić.
2. Z poziomu bloku profil CDN kliknij przycisk Wyczyść.
   
    ![Blok profilu CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Zostanie otwarty blok przeczyszczania.
   
    ![Blok przeczyszczanie usługi CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. W bloku przeczyszczania wybierz adres usługi, które chcesz przeczyścić z listy rozwijanej adresu URL.
   
    ![Przeczyść formularza](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Można także uzyskać blok przeczyszczania, klikając **przeczyścić** przycisku w bloku punktu końcowego usługi CDN.  W takim przypadku **adresu URL** pole jest wstępnie wypełniony adres usługi tego określonego punktu końcowego.
   > 
   > 
4. Wybierz zasoby, które chcesz przeczyścić z węzłów krawędzi.  Jeśli chcesz wyczyścić wszystkie zasoby, kliknij przycisk **Przeczyść wszystkie** pola wyboru.  W przeciwnym razie wpisz ścieżkę do każdego zasobu, które chcesz przeczyścić w **ścieżki** pola tekstowego. Poniższe formaty są obsługiwane w ścieżce.
    1. **Pojedynczy adres URL przeczyszczania**: Przeczyść poszczególnych zasobów, określając pełny adres URL, z lub bez rozszerzenia pliku, np.`/pictures/strasbourg.png`; `/pictures/strasbourg`
    2. **Symbol wieloznaczny przeczyszczania**: Gwiazdka (\*) może być używana jako symbol wieloznaczny. Wyczyść wszystkie foldery, podfoldery i pliki w obszarze punkt końcowy z `/*` przeczyszczania lub ścieżkę wszystkie podfoldery i pliki w określonym folderze, określając folder następuje `/*`, np.,`/pictures/*`.  Należy pamiętać, Przeczyść tego symbolu wieloznacznego nie jest obsługiwana przez usługę Azure CDN from Akamai obecnie. 
    3. **Przeczyszczanie domeny katalogu głównego**: Przeczyszczanie punktu końcowego "/" w ścieżce katalogu głównego.
   
   > [!TIP]
   > Ścieżki musi być określona dla przeczyszczania i musi być względnym adresem URL, który mieści się następujące [wyrażenia regularnego](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Przeczyść wszystkie** i **przeczyszczania symboli wieloznacznych** nie są obsługiwane przez **Azure CDN from Akamai** obecnie.
   > > Pojedynczy adres URL przeczyszczania `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Ciąg zapytania `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Symbol wieloznaczny przeczyszczania `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > Więcej **ścieżki** pola tekstowe pojawią się po wprowadzeniu tekstu, aby możliwe było tworzenie listy wielu zasobów.  Zasoby można usuwać z listy, klikając przycisk wielokropka (...).
   > 
5. Kliknij przycisk **przeczyścić** przycisku.
   
    ![Przycisk Wyczyść](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Przeczyszczanie żądań trwa około 2 – 3 minuty do przetworzenia z **Azure CDN from Verizon** (standard i premium) i około 7 minut za pomocą **Azure CDN from Akamai**.  Usługa Azure CDN jest objęta limitem 50 współbieżnych przeczyścić żądań w danym momencie na poziomie profilu. 
> 
> 

## <a name="see-also"></a>Zobacz także
* [Wstępne ładowanie zasobów w punkcie końcowym usługi Azure CDN](cdn-preload-endpoint.md)
* [Dokumentacja interfejsu API REST usługi CDN Azure - przeczyszczania i wstępne ładowanie punktu końcowego](/rest/api/cdn/endpoints)

