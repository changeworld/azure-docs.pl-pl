---
title: Przeczyszczanie punktu końcowego usługi Azure CDN | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeczyścić całej zawartości pamięci podręcznej z punktu końcowego usługi Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 262a8f7385ba5f74d21991772599540260a145fc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="purge-an-azure-cdn-endpoint"></a>Przeczyszczanie punktu końcowego usługi Azure CDN
## <a name="overview"></a>Przegląd
Azure CDN krawędzi węzły będą buforowane zasoby, do momentu wygaśnięcia zasobu czas wygaśnięcia (TTL).  Po wygaśnięciu TTL elementu zawartości, gdy klient zażąda zawartości z węzłem krawędzi, węzłem krawędzi pobiera zaktualizowane nową kopię elementu zawartości do obsługi żądań klienta i Magazyn odświeżania pamięci podręcznej.

Najlepszym rozwiązaniem jest upewnij się, że użytkownicy zawsze uzyskać najnowszą kopię zasobów jest wersja zasobów dla każdej aktualizacji i opublikujesz je jako nowe adresy URL.  CDN zostanie natychmiast pobrać nowych zasobów dla następnego żądania klienta.  Czasami możesz przeczyścić zawartości w pamięci podręcznej ze wszystkich węzłów edge i wymuszanie je wszystkie do pobierania nowych, zaktualizowanych trwałych.  Może to być spowodowane aktualizacje do aplikacji sieci web lub aby szybko zasoby aktualizacji, które zawierają nieprawidłowe informacje.

> [!TIP]
> Należy pamiętać, że przeczyszczanie tylko czyści zawartości w pamięci podręcznej na serwerze krawędzi CDN.  Wszystkie podrzędne pamięci podręcznych, takich jak serwery proxy i pamięci podręcznej przeglądarki lokalnego, może nadal posiadają pamięci podręcznej kopię pliku.  Należy pamiętać o tym, gdy ustawiona pliku time-to-live.  Możesz wymusić podrzędne klientowi żądanie najnowszej wersji pliku, nadając jej unikatową nazwę za każdym razem, należy zaktualizować lub korzystając z [buforowanie ciągu zapytania](cdn-query-string.md).  
> 
> 

W tym samouczku przedstawiono przeczyszczanie zasoby ze wszystkich węzłów krawędzi punktu końcowego.

## <a name="walkthrough"></a>Przewodnik
1. W [Azure Portal](https://portal.azure.com), przejdź do profilu CDN zawierający punktu końcowego, które chcesz wyczyścić.
2. Z blok profilu CDN kliknij przycisk Wyczyść.
   
    ![Blok profilu CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Zostanie otwarty blok przeczyszczenia.
   
    ![Blok przeczyszczania CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. W bloku przeczyszczania wybierz adres usługi, które chcesz przeczyścić z listy rozwijanej adresu URL.
   
    ![Przeczyść formularza](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Można także uzyskać bloku przeczyszczania klikając **przeczyścić** przycisk w bloku punktu końcowego CDN.  W takim przypadku **adres URL** pole będzie wstępnie wypełniane przy użyciu adresu tego określonego punktu końcowego usługi.
   > 
   > 
4. Wybierz zasoby, jakie mają zostać przeczyszczone węzłów krawędzi.  Jeśli chcesz wyczyścić wszystkie zasoby, kliknij przycisk **Przeczyść wszystko** wyboru.  W przeciwnym razie wpisz ścieżkę do każdego trwałego chcesz przeczyścić w **ścieżki** pola tekstowego. Poniżej formaty są obsługiwane w ścieżce.
    1. **Pojedynczy adres URL przeczyszczania**: czyszczenie poszczególnych zasobów, określając pełny adres URL z lub bez rozszerzenie pliku, np.`/pictures/strasbourg.png`; `/pictures/strasbourg`
    2. **Symbol wieloznaczny przeczyszczania**: gwiazdki (\*) może być używany jako symbol wieloznaczny. Wyczyść wszystkie foldery, podfoldery i pliki w obszarze punkt końcowy z `/*` przeczyszczania lub ścieżkę wszystkie podfoldery i pliki w określonym folderze, określając folder następuje `/*`, np.,`/pictures/*`.  Należy pamiętać, czyszczenie tego symbolu wieloznacznego nie jest obsługiwany przez usługi Azure CDN from Akamai obecnie. 
    3. **Przeczyszczanie domeny katalogu głównego**: przeczyścić głównego punktu końcowego z "/" w ścieżce.
   
   > [!TIP]
   > Ścieżki musi być określona dla przeczyszczania i musi być względnym adresem URL, który mieści się następujące [wyrażenie regularne](https://msdn.microsoft.com/library/az24scfc.aspx). **Przeczyść wszystko** i **przeczyszczania symbolu wieloznacznego** nie są obsługiwane przez **Azure CDN from Akamai** obecnie.
   > > Pojedynczy adres URL przeczyszczenia `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Ciąg zapytania `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Symbol wieloznaczny przeczyszczania `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > Więcej **ścieżki** pola tekstowe pojawią się po wprowadź tekst, który pozwala na utworzenie listy wiele zasobów.  Zasoby można usunąć z listy, klikając przycisk wielokropka (...).
   > 
5. Kliknij przycisk **przeczyścić** przycisku.
   
    ![Przeczyść przycisku](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Żądania przeczyszczenia zająć około 2 – 3 minuty do przetworzenia z **Azure CDN from Verizon** (warstwy standardowa i premium), a około 7 minut z **Azure CDN from Akamai**.  Usługi Azure CDN ma limit współbieżnych 50 przeczyścić żądań w danym czasie na poziomie profilu. 
> 
> 

## <a name="see-also"></a>Zobacz także
* [Wstępne ładowanie zasobów w punkcie końcowym usługi Azure CDN](cdn-preload-endpoint.md)
* [Azure dokumentacji interfejsu API REST usługi CDN - przeczyścić lub wstępnie załadować punktu końcowego](https://msdn.microsoft.com/library/mt634451.aspx)

