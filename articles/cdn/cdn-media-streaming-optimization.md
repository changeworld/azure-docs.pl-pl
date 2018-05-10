---
title: Multimediów strumieniowych optymalizację za pomocą usługi Azure CDN
description: Optymalizacja strumieniowego przesyłania plików multimedialnych w celu dostarczania smooth
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: v-deasim
ms.openlocfilehash: 8a2b69aaa601e1d00152f57841a4d67f98680181
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Multimediów strumieniowych optymalizację za pomocą usługi Azure CDN 
 
Użyj wideo wysokiej rozdzielczości zwiększa się w Internecie, co powoduje trudności dla dostarczania dużych plików. Klienci oczekują smooth odtwarzania wideo na żądanie lub live wideo zasobów na różnych klientów i sieci na całym świecie. Mechanizm dostarczania szybkich i wydajnych multimediów strumieniowych plików jest krytyczne zapewnienie obsługi płynne i jest przyjemne konsumenta.  

Multimediów strumieniowych na żywo jest szczególnie trudne do dostarczania ze względu na duże rozmiary i liczba równoczesnych przeglądarki. Długie opóźnienia, że użytkownicy pozostawić. Ponieważ strumienie na żywo nie można buforować wcześniejsze i duże opóźnienia nie są akceptowane przeglądarki, należy dostarczyć fragmenty wideo w odpowiednim czasie. 

Wzorce żądań strumieniowych zapewniają również pewne wyzwania. Po wydaniu popularnego strumień na żywo lub utworzenie nowej serii wideo na żądanie, tysięcy do milionów osób przeglądających może zażądać strumienia, w tym samym czasie. W takim przypadku żądanie inteligentnych konsolidacji jest nie przeciąży serwerów pochodzenia, kiedy zasoby nie są jeszcze buforowane.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Multimediów strumieniowych optymalizacji dla usługi Azure CDN firmy Microsoft

**Azure CDN Standard from Microsoft** punkty końcowe dostarczania przesyłania strumieniowego multimediów zasoby bezpośrednio, za pomocą typu optymalizacji dostarczania ogólne sieci web. 

Multimediów strumieniowych Optymalizacja **Azure CDN Standard from Microsoft** ma zastosowanie w przypadku na żywo lub nośnika, który używa w celu dostarczania multimediów poszczególne fragmenty strumieniowe przesyłanie wideo na żądanie. Ten proces różni się od pojedynczego zasobu dużych przesyłane przy użyciu pobierania progresywnego lub za pomocą żądania zakresu bajtów. Dla informacji o stylu dostarczania multimediów, zobacz [optymalizację pobierania dużych plików za pomocą usługi Azure CDN](cdn-large-file-optimization.md).

Ogólne nośnika dostarczania lub wideo na żądanie dostarczania optymalizacji typów nośników za pomocą Azure sieci dostarczania zawartości (CDN) optymalizacje zaplecza do dostarczania multimediów zasoby szybciej. Konfiguracje korzysta również trwałych nośnika, na podstawie najlepszych rozwiązań rozpoznane w czasie.

### <a name="partial-cache-sharing"></a>Udostępnianie częściowe pamięci podręcznej
Udostępnianie częściowe pamięci podręcznej umożliwia CDN z obsługą częściowo buforowane zawartości do nowych żądań. Na przykład jeśli pierwsze żądanie do sieci CDN powoduje Chybienie pamięci podręcznej, żądanie jest wysyłana do źródła. Chociaż ten niekompletną zawartość jest ładowany do pamięci podręcznej CDN, inne żądania do sieci CDN można uruchomić pobierania tych danych. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Przesyłanie strumieniowe optymalizacji dla usługi Azure CDN from Verizon multimediów

**Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon** punkty końcowe dostarczania przesyłania strumieniowego multimediów zasoby bezpośrednio, za pomocą typu optymalizacji dostarczania ogólne sieci web. Kilka funkcji w sieci CDN bezpośrednio pomagają w dostarczaniu zasoby nośnika domyślnie.

### <a name="partial-cache-sharing"></a>Udostępnianie częściowe pamięci podręcznej

Udostępnianie częściowe pamięci podręcznej umożliwia CDN z obsługą częściowo buforowane zawartości do nowych żądań. Na przykład jeśli pierwsze żądanie do sieci CDN powoduje Chybienie pamięci podręcznej, żądanie jest wysyłana do źródła. Chociaż ten niekompletną zawartość jest ładowany do pamięci podręcznej CDN, inne żądania do sieci CDN można uruchomić pobierania tych danych. 

### <a name="cache-fill-wait-time"></a>Czas oczekiwania wypełnienie pamięci podręcznej

 Funkcja czas oczekiwania wypełnienie pamięci podręcznej wymusza serwer graniczny, aby pomieścić wszystkie kolejne żądania dla tego samego zasobu, do momentu osiągnięcia nagłówków odpowiedzi HTTP z serwera pochodzenia. Jeśli nagłówki odpowiedzi HTTP z punktu początkowego napływają przed wygaśnięciem, wszystkie żądania, które zostały wstrzymane są obsługiwane z rosnącym pamięci podręcznej. W tym samym czasie pamięci podręcznej jest wypełniane przez dane ze źródła. Domyślnie czas oczekiwania wypełnienie pamięci podręcznej wynosi 3 000 milisekund. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Przesyłanie strumieniowe optymalizacji dla usługi Azure CDN from Akamai multimediów
 
**Azure CDN Standard from Akamai** funkcję wydajnie zapewnia przesyłania strumieniowego multimediów zasoby użytkownikom na całym świecie na dużą skalę. Funkcja zmniejsza opóźnienia, ponieważ zmniejsza obciążenie serwerów pochodzenia. Ta funkcja jest dostępna z Akamai standardowe, warstwy cenowej. 

Multimediów strumieniowych Optymalizacja **Azure CDN Standard from Akamai** ma zastosowanie w przypadku na żywo lub nośnika, który używa w celu dostarczania multimediów poszczególne fragmenty strumieniowe przesyłanie wideo na żądanie. Ten proces różni się od pojedynczego zasobu dużych przesyłane przy użyciu pobierania progresywnego lub za pomocą żądania zakresu bajtów. Dla informacji o stylu dostarczania multimediów, zobacz [optymalizacji plików o dużym](cdn-large-file-optimization.md).

Ogólne nośnika dostarczania lub wideo na żądanie dostarczania optymalizacji typów nośników za pomocą CDN optymalizacje zaplecza do dostarczania multimediów zasoby szybciej. Konfiguracje korzysta również trwałych nośnika, na podstawie najlepszych rozwiązań rozpoznane w czasie.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Skonfiguruj punkt końcowy Akamai CDN, aby zoptymalizować przesyłania strumieniowego multimediów
 
Można skonfigurować punktu końcowego (CDN) sieci dostarczania zawartości w celu zoptymalizowania dostarczania dla dużych plików za pośrednictwem portalu Azure. Aby to zrobić, można użyć interfejsów API REST lub dowolny z zestawów SDK klienta. W poniższej procedurze pokazano proces za pośrednictwem portalu Azure **Azure CDN Standard from Akamai** profilu:

1. Aby dodać nowy punkt końcowy na Akamai **profilu CDN** wybierz pozycję **punktu końcowego**.
  
    ![Nowy punkt końcowy](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. W **zoptymalizowane pod kątem** listy rozwijanej wybierz **wideo na żądanie przesyłania strumieniowego multimediów** zasobów wideo na żądanie. Jeśli to zrobisz, kombinację na żywo i przesyłania strumieniowego wideo na żądanie, wybierz **przesyłania strumieniowego multimediów ogólne**.

    ![Wybrane przesyłania strumieniowego](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Po utworzeniu punktu końcowego stosuje optymalizacji dla wszystkich plików spełniających określone kryteria. W poniższej sekcji opisano tego procesu. 

### <a name="caching"></a>Buforowanie

Jeśli **Azure CDN Standard from Akamai** wykryje, że jest zasobu manifestu przesyłania strumieniowego lub fragment, używa innej buforowania czas wygaśnięcia od dostarczania ogólne sieci web. (Zobacz pełną listę w poniższej tabeli). Jak zawsze honorowane cache-control lub Expires nagłówki wysyłane ze źródła. Jeśli element zawartości nie jest zasób nośnika, buforuje jest przy użyciu czas wygaśnięcia w celu dostarczania ogólne sieci web.

Krótki czas buforowania ujemna jest przydatna do odciążania źródła, gdy wielu użytkowników żądają fragmentu, która jeszcze nie istnieje. Przykładem jest strumień na żywo, gdy pakiety nie są dostępne ze źródła tego drugiego. Interwał buforowania już pomaga również w odciążania żądań ze źródła, ponieważ zawartość wideo zwykle nie jest zmodyfikowany.
 

|   | Ogólne dostarczanie w sieci Web | Ogólne transmisje strumieniowe multimediów | Przesyłanie strumieniowe multimediów wideo na żądanie  
--- | --- | --- | ---
Buforowanie: dodatnią <br> HTTP 200, 203, 300, <br> 301, 302 i 410 | 7 dni |365 dni | 365 dni   
Buforowanie: ujemna <br> HTTP 204, 305, 404, <br> i 405 | Brak | 1 sekunda | 1 sekunda
 
### <a name="deal-with-origin-failure"></a>Postępowania w przypadku niepowodzenia źródła  

Dostarczanie multimediów ogólne i dostarczanie multimediów wideo na żądanie mieć również limity czasu pochodzenia i dziennika ponownych prób, na podstawie najlepszych rozwiązań dla żądania typowe wzorce. Na przykład, ponieważ dostarczania ogólne nośnika jest dla na żywo i dostarczania multimediów wideo na żądanie, używa krótszy limit czasu połączenia z powodu harmonogramów rodzaj transmisja strumieniowa na żywo.

Jeśli upłynie limit czasu połączenia, sieć CDN ponowi próbę wiele razy przed wysłaniem ich do klienta błąd "504 — Limit czasu bramy". 

Gdy plik jest zgodna z listy warunków typ i rozmiar pliku, sieć CDN używa zachowania do przesyłania strumieniowego multimediów. W przeciwnym razie używa dostarczania ogólne sieci web.
   
### <a name="conditions-for-media-streaming-optimization"></a>Warunki dotyczące multimediów strumieniowych optymalizacji 

W poniższej tabeli wymieniono zestaw kryteriów dotyczące multimediów strumieniowych optymalizacji: 
 
Obsługiwane typy przesyłania strumieniowego | Rozszerzenia plików  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, klucza usług terminalowych, aac
Adobe obr. / min | f4m, f4x, drmmeta, bootstrap, f4f,<br>Adres URL seg Frag — struktura <br> (dopasowanie wyrażenia regularnego: ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd, kreska, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Funkcji Smooth streaming | / manifest//QualityLevels/fragmenty /
  
 
