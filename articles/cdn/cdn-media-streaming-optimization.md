---
title: Multimediów strumieniowych Optymalizacja przy użyciu usługi Azure CDN
description: Optymalizacja przesyłania strumieniowego plików multimedialnych Zapewnij płynne dostarczanie
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: 9802296170f07bb8599058e230798f647e900d4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636247"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Multimediów strumieniowych Optymalizacja przy użyciu usługi Azure CDN 
 
Korzystanie z wideo w wysokiej rozdzielczości, zwiększa się w Internecie, co stwarza trudności wydajne dostarczanie dużych plików. Klienci oczekują bezproblemowe odtwarzanie wideo na żądanie lub na żywo zasobów wideo na różnych sieci i klientów na całym świecie. Mechanizm szybkie i wydajne dostarczanie multimediów, przesyłanie strumieniowe plików krytyczne jest zapewnienie środowiska płynne i przyjemne konsumenta.  

Multimediów strumieniowych na żywo jest szczególnie trudne do dostarczenia ze względu na duże rozmiary i liczbę współbieżnych osoby przeglądające. Duże opóźnienia, że użytkownicy opuścić. Transmisje strumieniowe na żywo nie można buforować wcześniejsze i duże opóźnienia nie są dopuszczalne osoby oglądające, dlatego fragmentów wideo muszą być dostarczone w odpowiednim czasie. 

Wzorce żądania przesyłania strumieniowego zapewniają również pewne wyzwania. Po wydaniu popularnego strumienia na żywo lub Nowa seria wideo na żądanie, tysięcy do milionów widzów może zażądać strumienia, w tym samym czasie. W tym przypadku konsolidacji żądań inteligentne jest nie przeciąży serwerów źródłowych, kiedy zasoby nie są jeszcze buforowane.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Multimediów strumieniowych Optymalizacja pod kątem wysokiej dostępności treści Azure firmy Microsoft

**Usługa Azure CDN Standard from Microsoft** punktów końcowych dostarczania zasobów przesyłania strumieniowego multimediów bezpośrednio przy użyciu typu optymalizacji dostarczania ogólnych sieci web. 

Przesyłanie strumieniowe optymalizacji dla multimediów **Azure CDN Standard from Microsoft** ma zastosowanie w przypadku na żywo lub wideo na żądanie, strumieniowego przesyłania multimediów, korzystającą z nośnika poszczególne fragmenty dostarczania. Ten proces różni się od pojedynczego zasobu dużych przesyłane przy użyciu pobierania progresywnego lub za pomocą żądania zakresu bajtów. Aby uzyskać informacje dotyczące stylu dostarczanie, zobacz [Optymalizacja pobierania dużych plików za pomocą usługi Azure CDN](cdn-large-file-optimization.md).

Ogólne dostarczanie lub wideo na żądanie media dostarczania optymalizacji typów nośników za pomocą usługi Azure Content Delivery Network (CDN) optymalizacje zaplecza szybsze zasobów multimedialnych. Konfiguracje używane także dla zasobów multimedialnych w oparciu o najlepszymi rozwiązaniami wynikającymi wraz z upływem czasu.

### <a name="partial-cache-sharing"></a>Udostępnianie częściowe pamięci podręcznej
Udostępnianie częściowe pamięci podręcznej umożliwia sieć CDN do udostępniania częściowo buforowanej zawartości na nowe żądania. Na przykład jeśli pierwsze żądanie do usługi CDN w wyniku trafienia pamięci podręcznej, żądanie jest wysyłane do źródła. Mimo że ten niekompletną zawartość jest ładowany do pamięci podręcznej usługi CDN, inne żądania do sieci CDN można uruchomić pobierania tych danych. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Przesyłanie strumieniowe optymalizacje dla usługi Azure CDN from Verizon multimediów

**Usługa Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon** punktów końcowych dostarczania zasobów przesyłania strumieniowego multimediów bezpośrednio przy użyciu typu optymalizacji dostarczania ogólnych sieci web. Kilka funkcji w usłudze CDN bezpośrednio pomagać w dostarczaniu zasobów multimedialnych domyślnie.

### <a name="partial-cache-sharing"></a>Udostępnianie częściowe pamięci podręcznej

Udostępnianie częściowe pamięci podręcznej umożliwia sieć CDN do udostępniania częściowo buforowanej zawartości na nowe żądania. Na przykład jeśli pierwsze żądanie do usługi CDN w wyniku trafienia pamięci podręcznej, żądanie jest wysyłane do źródła. Mimo że ten niekompletną zawartość jest ładowany do pamięci podręcznej usługi CDN, inne żądania do sieci CDN można uruchomić pobierania tych danych. 

### <a name="cache-fill-wait-time"></a>Czas oczekiwania na wypełnienie pamięci podręcznej

 Funkcja czas oczekiwania wypełnienie pamięci podręcznej wymusza na serwerze granicznym na potrzeby przechowywania jakimikolwiek kolejnymi żądaniami dla tego samego zasobu, dopóki nie pojawić się nagłówków odpowiedzi HTTP z serwera pochodzenia. Jeśli nagłówki odpowiedzi HTTP z punktu początkowego pojawić się przed okresu działania czasomierza, wszystkie żądania, które zostały wstrzymane są obsługiwane z rosnącą pamięci podręcznej. W tym samym czasie pamięć podręczna jest wypełniana przez dane ze źródła. Czas oczekiwania wypełnienie pamięci podręcznej domyślnie do 3000 milisekund. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Przesyłanie strumieniowe optymalizacje dla usługi Azure CDN from Akamai multimediów
 
**Usługa Azure CDN Standard from Akamai** oferuje funkcję, która stanowi przesyłania strumieniowego zasobów multimedialnych efektywnie użytkowników na całym świecie na dużą skalę. Ta funkcja zmniejsza opóźnienia, ponieważ redukuje obciążenie serwerów źródłowych. Ta funkcja jest dostępna z warstwy standard Akamai w warstwie cenowej. 

Przesyłanie strumieniowe Optymalizacja multimediów **Azure CDN Standard from Akamai** ma zastosowanie w przypadku na żywo lub wideo na żądanie, strumieniowego przesyłania multimediów, korzystającą z nośnika poszczególne fragmenty dostarczania. Ten proces różni się od pojedynczego zasobu dużych przesyłane przy użyciu pobierania progresywnego lub za pomocą żądania zakresu bajtów. Aby uzyskać informacje dotyczące stylu dostarczanie, zobacz [Optymalizacja dużych plików](cdn-large-file-optimization.md).

Ogólne dostarczanie lub wideo na żądanie media dostarczania optymalizacji typów nośników za pomocą sieci CDN optymalizacje zaplecza szybsze zasobów multimedialnych. Konfiguracje używane także dla zasobów multimedialnych w oparciu o najlepszymi rozwiązaniami wynikającymi wraz z upływem czasu.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Konfigurowanie punktu końcowego usługi CDN firmy Akamai w celu zoptymalizowania przesyłania strumieniowego multimediów
 
Można skonfigurować punkt końcowy usługi content delivery network (CDN) do optymalizacji dostarczania dla dużych plików za pośrednictwem witryny Azure portal. Aby to zrobić, można użyć interfejsów API REST, ani żadnego z zestawów SDK klienta. Poniższe kroki pokazują proces za pośrednictwem interfejsu witryny Azure portal dla **Azure CDN Standard from Akamai** profilu:

1. Aby dodać nowy punkt końcowy na Akamai **profil CDN** wybierz opcję **punktu końcowego**.
  
    ![Nowy punkt końcowy](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. W **zoptymalizowane pod kątem** listy rozwijanej wybierz **wideo strumieniowe multimediów na żądanie** zasobów wideo na żądanie. Jeśli to zrobisz, połączenie na żywo i przesyłanie strumieniowe wideo na żądanie, wybierz **strumieniowego przesyłania multimediów ogólne**.

    ![Przesyłanie strumieniowe wybrane](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Po utworzeniu punktu końcowego, dotyczy optymalizacji dla wszystkich plików spełniających określone kryteria. W poniższej sekcji opisano tego procesu. 

### <a name="caching"></a>Buforowanie

Jeśli **Azure CDN Standard from Akamai** wykryje, że element zawartości jest manifestu przesyłania strumieniowego lub fragment, używa ona inny czas wygaśnięcia pamięci podręcznej z ogólne dostarczanie w Internecie. (Zobacz pełną listę w poniższej tabeli). Jak zawsze są uznawane cache-control lub Expires nagłówki wysyłane ze źródła. Jeśli zasób nie jest zasób nośnika, pamięci podręczne przy użyciu czasu wygaśnięcia dla ogólne dostarczanie w Internecie.

Krótki czas buforowania ujemna jest przydatna do odciążania źródła, gdy wielu użytkowników żądają fragmentu, który jeszcze nie istnieje. Przykładem jest strumień na żywo, gdzie pakiety nie są dostępne ze źródła tego drugiego. Interwał jest już w pamięci podręcznej pomaga również odciążania żądań ze źródła, ponieważ zawartość wideo zwykle nie jest modyfikowana.
 

|   | Ogólne dostarczanie w sieci Web | Ogólne transmisje strumieniowe multimediów | Przesyłanie strumieniowe multimediów wideo na żądanie  
--- | --- | --- | ---
Pamięć podręczna: Pozytywna <br> HTTP 200, 203, 300, <br> 301, 302 i do 410 | 7 dni |365 dni | 365 dni   
Pamięć podręczna: Negatywna <br> HTTP 204, 305, 404, <br> i 405 | Brak | 1 sekunda | 1 sekunda
 
### <a name="deal-with-origin-failure"></a>Dotyczy błąd źródła  

Ogólne dostarczanie i dostarczanie wideo na żądanie ma również pochodzenia przekroczeń limitu czasu i dziennika ponownych prób, w oparciu o najlepsze rozwiązania dotyczące typowe wzorce. Na przykład ponieważ ogólne dostarczanie jest na żywo i dostarczanie wideo na żądanie używa krótszego limitu czasu połączenia ze względu na charakter zależne od czasu transmisja strumieniowa na żywo.

Jeśli upłynie limit czasu połączenia, sieć CDN ponawia próbę kilkukrotnie przed wysłaniem błąd "504 — Limit czasu bramy" do klienta. 

Gdy plik jest zgodna z listy warunków typu i rozmiaru plików, sieci CDN używa zachowania do przesyłania strumieniowego multimediów. W przeciwnym razie używa ogólne dostarczanie w Internecie.
   
### <a name="conditions-for-media-streaming-optimization"></a>Warunki dla przesyłania strumieniowego Optymalizacja multimediów 

Poniższa lista zawiera zbiór kryteria, które należy spełnić w celu przesyłania strumieniowego Optymalizacja multimediów: 
 
Obsługiwane typy przesyłania strumieniowego | Rozszerzenia plików  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, klucza serwera terminali, adaptacyjnych kontrolek aplikacji
Adobe obr. / min | f4m f4x, drmmeta, bootstrap, f4f,<br>Adres URL Frag seg — struktura <br> (dopasowanie wyrażenia regularnego: ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd, kreska, divx, ismv, m4s, m4v, w formacie mp4, mp4v, <br> sidx webm, mp4a, m4a, isma
Funkcji Smooth streaming | / manifest//QualityLevels/fragmenty /
  
 
