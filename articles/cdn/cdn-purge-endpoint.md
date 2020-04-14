---
title: Czyszczenie punktu końcowego usługi Azure CDN | Dokumenty firmy Microsoft
description: Dowiedz się, jak wyczyścić całą zawartość buforowaną z punktu końcowego usługi Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: allensu
ms.openlocfilehash: ebbb0dd059ce2bcf4a3bc260ed6d426d5be09dfe
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260262"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Czyszczenie punktu końcowego usługi Azure CDN
## <a name="overview"></a>Omówienie
Węzły brzegowe usługi Azure CDN będą buforować zasoby, dopóki nie wygaśnie czas wygaśnięcia (TTL) zasobu.  Po wygaśnięciu czasu wygaśnięcia zasobu, gdy klient zażąda zasobu z węzła krawędzi, węzeł krawędzi pobierze nową zaktualizowaną kopię zasobu, aby obsługiwać żądanie klienta i przechowywać odświeżanie pamięci podręcznej.

Najlepszym rozwiązaniem, aby upewnić się, że użytkownicy zawsze uzyskać najnowszą kopię zasobów jest wersja zasobów dla każdej aktualizacji i opublikować je jako nowe adresy URL.  Usługa CDN natychmiast pobierze nowe zasoby dla następnych żądań klientów.  Czasami można przeczyścić buforowaną zawartość ze wszystkich węzłów brzegowych i wymusić na nich wszystkie pobieranie nowych zaktualizowanych zasobów.  Może to być spowodowane aktualizacjami aplikacji sieci web lub szybkim zaktualizowaniem zasobów zawierających nieprawidłowe informacje.

> [!TIP]
> Należy zauważyć, że przeczyszczanie usuwa tylko buforowaną zawartość na serwerach brzegowych sieci CDN.  Wszystkie podrzędne pamięci podręczne, takie jak serwery proxy i lokalne pamięci podręczne przeglądarki, mogą nadal zawierać buforowaną kopię pliku.  Należy o tym pamiętać podczas ustawiania czasu na żywo pliku.  Można wymusić na kliencie podrzędnym żądanie najnowszej wersji pliku, nadając mu unikatową nazwę przy każdej aktualizacji lub korzystając z [buforowania ciągów zapytań.](cdn-query-string.md)  
> 
> 

W tym samouczku można przejść przez przeczyszczanie zasobów ze wszystkich węzłów krawędzi punktu końcowego.

## <a name="walkthrough"></a>Przewodnik
1. W [witrynie Azure Portal](https://portal.azure.com)przejdź do profilu sieci CDN zawierającego punkt końcowy, który chcesz przeczyścić.
2. W bloku profilu CDN kliknij przycisk przeczyszczanie.
   
    ![Ostrze profilu CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Otwiera się ostrze Odczyszczanie.
   
    ![Ostrze do oczyszczania CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Na przeczyszczanie bloku, wybierz adres usługi, które chcesz przeczyścić z listy rozwijanej adresu URL.
   
    ![Formularz oczyszczania](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Można również uzyskać punkt do bloku Przeczyszczanie, klikając przycisk **Przeczyszczanie** na bloku punktu końcowego sieci CDN.  W takim przypadku pole **adresu URL** zostanie wstępnie wypełnione adresem usługi tego określonego punktu końcowego.
   > 
   > 
4. Wybierz zasoby, które mają być przeczyszczane z węzłów krawędzi.  Jeśli chcesz wyczyścić wszystkie zasoby, kliknij pole wyboru **Przeczyść wszystkie.**  W przeciwnym razie wpisz ścieżkę każdego zasobu, który chcesz przeczyścić w pola tekstowym **Ścieżka.** Poniższe formaty są obsługiwane w ścieżce.
    1. **Przeczyszczanie pojedynczego adresu URL**: Przeczyszczyć poszczególne zasoby, określając pełny`/pictures/strasbourg.png`adres URL, z rozszerzeniem pliku lub bez niego, np.`/pictures/strasbourg`
    2. **Przeczyszczanie symboli wieloznacznych**: Asterisk (\*) może być użyte jako symbol wieloznaczny. Przeczyść wszystkie foldery, podfoldery i pliki `/*` pod punktem końcowym za pomocą ścieżki lub przeczyść wszystkie podfoldery i pliki w określonym folderze, określając `/*`folder, po którym następuje , np.,`/pictures/*`.  Należy zauważyć, że przeczyszczanie symboli wieloznacznych nie jest obecnie obsługiwane przez usługę Azure CDN z usługi Akamai. 
    3. **Przeczyszczanie domeny głównej**: Przeczyść katalog główny punktu końcowego z "/" w ścieżce.
   
   > [!TIP]
   > Ścieżki muszą być określone dla przeczyszczać i muszą być względnym adresem URL, który pasuje do następującego [wyrażenia regularnego](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Przeczyść wszystkie** i **przeczyszczanie symboli wieloznacznych** nie są obecnie obsługiwane przez **usługę Azure CDN z usługi Akamai.**
   > > Przeczyszczanie pojedynczego adresu URL`@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Ciąg zapytania`@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Przeczyszczanie `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`symboli wieloznacznych . 
   > 
   > Więcej pól tekstowych **Ścieżka** pojawi się po wprowadzeniu tekstu, aby umożliwić tworzenie listy wielu zasobów.  Zasoby można usunąć z listy, klikając przycisk wielokropek (...)
   > 
5. Kliknij przycisk **Przeczyszcz.**
   
    ![Przycisk Przeczyść](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Przeczyszczanie żądań trwa około 10 minut, aby przetworzyć za pomocą **usługi Azure CDN firmy Microsoft,** około 2 minuty z **usługą Azure CDN firmy Verizon** (standard i premium) i około 10 sekund z **usługą Azure CDN firmy Akamai.**  Usługa Azure CDN ma limit 50 równoczesnych żądań przeczyścić w danym momencie na poziomie profilu. 
> 
> 

## <a name="see-also"></a>Zobacz też
* [Wstępne ładowanie zasobów w punkcie końcowym usługi Azure CDN](cdn-preload-endpoint.md)
* [Odwołanie interfejsu API REST usługi Azure CDN — przeczyszczanie lub wstępne ładowanie punktu końcowego](/rest/api/cdn/endpoints)

