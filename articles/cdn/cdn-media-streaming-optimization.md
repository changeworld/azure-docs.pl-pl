---
title: Optymalizacja przesyłania strumieniowego multimediów za pomocą usługi Azure CDN
description: Optymalizacja przesyłania strumieniowego plików multimedialnych w celu sprawnego dostarczania
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: 2931dffaaab2d06b2c06f03770a66d78d6466787
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260483"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Optymalizacja przesyłania strumieniowego multimediów za pomocą usługi Azure CDN 
 
Korzystanie z wideo w wysokiej rozdzielczości rośnie w Internecie, co stwarza trudności w wydajnym dostarczaniu dużych plików. Klienci oczekują płynnego odtwarzania wideo na żądanie lub zasobów wideo na żywo w różnych sieciach i klientach na całym świecie. Szybki i wydajny mechanizm dostarczania plików strumieniowych multimediów ma kluczowe znaczenie dla zapewnienia płynnego i przyjemnego doświadczenia konsumenta.  

Transmisja na żywo jest szczególnie trudna do dostarczenia ze względu na duże rozmiary i liczbę równoczesnych widzów. Duże opóźnienia powodują, że użytkownicy odchodzą. Ponieważ strumienie na żywo nie mogą być buforowane z wyprzedzeniem, a duże opóźnienia nie są akceptowane przez widzów, fragmenty wideo muszą być dostarczane w odpowiednim czasie. 

Wzorce żądań przesyłania strumieniowego zapewniają również pewne nowe wyzwania. Gdy popularna transmisja na żywo lub nowa seria zostanie wydana na potrzeby wideo na żądanie, tysiące milionów widzów może poprosić o transmisję w tym samym czasie. W takim przypadku konsolidacja żądań inteligentnych jest niezbędna, aby nie przeciąć serwerów pochodzenia, gdy zasoby nie są jeszcze buforowane.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Optymalizacje przesyłania strumieniowego multimediów dla usługi Azure CDN firmy Microsoft

**Usługa Azure CDN Standard z** punktów końcowych firmy Microsoft dostarcza zasoby multimediów strumieniowych bezpośrednio przy użyciu ogólnego typu optymalizacji dostarczania w sieci Web. 

Optymalizacja przesyłania strumieniowego **multimediów** dla usługi Azure CDN Standard firmy Microsoft jest skuteczna w przypadku multimediów strumieniowych na żywo lub wideo na żądanie, które używają poszczególnych fragmentów multimediów do dostarczania. Ten proces różni się od pojedynczego dużego zasobu przesyłanego za pośrednictwem pobierania progresywnego lub przy użyciu żądań zakresu bajtów. Aby uzyskać informacje na temat tego stylu dostarczania multimediów, zobacz [Optymalizacja pobierania dużych plików za pomocą usługi Azure CDN](cdn-large-file-optimization.md).

Typy optymalizacji dostarczania nośników ogólnych lub wideo na żądanie korzystają z usługi Azure Content Delivery Network (CDN) z optymalizacjami zaplecza w celu szybszego dostarczania zasobów multimediów. Używają również konfiguracji dla zasobów multimedialnych na podstawie najlepszych rozwiązań wyuczonych w czasie.

### <a name="partial-cache-sharing"></a>Częściowe udostępnianie pamięci podręcznej
Częściowe udostępnianie pamięci podręcznej umożliwia sieci CDN obsługiwanie częściowo buforowanej zawartości dla nowych żądań. Na przykład jeśli pierwsze żądanie do sieci CDN powoduje pominięcie pamięci podręcznej, żądanie jest wysyłane do źródła. Mimo że ta niekompletna zawartość jest ładowana do pamięci podręcznej sieci CDN, inne żądania do sieci CDN mogą rozpocząć uzyskiwanie tych danych. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Optymalizacje przesyłania strumieniowego multimediów dla usługi Azure CDN firmy Verizon

**Usługa Azure CDN Standard firmy Verizon** i **Azure CDN Premium z** punktów końcowych Verizon dostarcza zasoby multimediów strumieniowych bezpośrednio przy użyciu ogólnego typu optymalizacji dostarczania w sieci Web. Kilka funkcji sieci CDN domyślnie pomaga w dostarczaniu zasobów multimedialnych.

### <a name="partial-cache-sharing"></a>Częściowe udostępnianie pamięci podręcznej

Częściowe udostępnianie pamięci podręcznej umożliwia sieci CDN obsługiwanie częściowo buforowanej zawartości dla nowych żądań. Na przykład jeśli pierwsze żądanie do sieci CDN powoduje pominięcie pamięci podręcznej, żądanie jest wysyłane do źródła. Mimo że ta niekompletna zawartość jest ładowana do pamięci podręcznej sieci CDN, inne żądania do sieci CDN mogą rozpocząć uzyskiwanie tych danych. 

### <a name="cache-fill-wait-time"></a>Czas oczekiwania wypełnienia pamięci podręcznej

 Funkcja czasu oczekiwania wypełnienia pamięci podręcznej wymusza na serwerze brzegowym przechowywanie kolejnych żądań dla tego samego zasobu, dopóki nagłówki odpowiedzi HTTP nie pojawią się z serwera pochodzenia. Jeśli nagłówki odpowiedzi HTTP ze źródła docierają przed wygaśnięciem czasomierza, wszystkie żądania, które zostały wstrzymane, są obsługiwane z rosnącej pamięci podręcznej. W tym samym czasie pamięć podręczna jest wypełniona przez dane z pochodzenia. Domyślnie czas oczekiwania wypełnienia pamięci podręcznej jest ustawiony na 3000 milisekund. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Optymalizacje przesyłania strumieniowego multimediów dla usługi Azure CDN firmy Akamai
 
**Usługa Azure CDN Standard firmy Akamai** oferuje funkcję, która zapewnia efektywne przesyłanie strumieniowe zasobów multimedialnych użytkownikom na całym świecie na dużą skalę. Funkcja zmniejsza opóźnienia, ponieważ zmniejsza obciążenie serwerów pochodzenia. Ta funkcja jest dostępna w standardowej warstwie cenowej Akamai. 

Optymalizacja przesyłania strumieniowego **multimediów dla usługi Azure CDN Standard firmy Akamai** jest skuteczna w przypadku multimediów strumieniowych na żywo lub wideo na żądanie, które używają poszczególnych fragmentów multimediów do dostarczania. Ten proces różni się od pojedynczego dużego zasobu przesyłanego za pośrednictwem pobierania progresywnego lub przy użyciu żądań zakresu bajtów. Aby uzyskać informacje na temat tego stylu dostarczania multimediów, zobacz [Optymalizacja dużych plików](cdn-large-file-optimization.md).

Ogólne typy dostarczania multimediów lub optymalizacji dostarczania multimediów wideo na żądanie używają sieci CDN z optymalizacjami zaplecza, aby szybciej dostarczać zasoby multimedialne. Używają również konfiguracji dla zasobów multimedialnych na podstawie najlepszych rozwiązań wyuczonych w czasie.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Konfigurowanie punktu końcowego Akamai CDN w celu optymalizacji przesyłania strumieniowego multimediów
 
Można skonfigurować punkt końcowy sieci dostarczania zawartości (CDN), aby zoptymalizować dostarczanie dla dużych plików za pośrednictwem witryny Azure portal. Można również użyć interfejsów API REST lub dowolnego z sdk klienta, aby to zrobić. Następujące kroki pokazują proces za pośrednictwem witryny Azure portal dla **standardu usługi Azure CDN z profilu Akamai:**

1. Aby dodać nowy punkt końcowy, na stronie **profilu sieci CDN** Akamai wybierz pozycję **Punkt końcowy**.
  
    ![Nowy punkt końcowy](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. Z listy **rozwijanej Zoptymalizowano** pod kątem opcji Wideo **na żądanie dla** zasobów wideo na żądanie. Jeśli wykonasz kombinację przesyłania strumieniowego na żywo i wideo na żądanie, wybierz opcję **Ogólne przesyłanie strumieniowe multimediów**.

    ![Wybrano przesyłanie strumieniowe](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Po utworzeniu punktu końcowego stosuje optymalizację dla wszystkich plików, które odpowiadają określonym kryteriom. W poniższej sekcji opisano ten proces. 

### <a name="caching"></a>Buforowanie

Jeśli **standard usługi Azure CDN firmy Akamai** wykryje, że zasób jest manifestem lub fragmentem przesyłania strumieniowego, używa różnych czasów wygaśnięcia buforowania z ogólnego dostarczania sieci Web. (Zobacz pełną listę w poniższej tabeli). Jak zawsze, cache-control lub Wygasa nagłówki wysyłane z pochodzenia są honorowane. Jeśli zasób nie jest zasobem multimedialnym, buforuje się przy użyciu czasu wygaśnięcia dla ogólnego dostarczania w sieci Web.

Krótki ujemny czas buforowania jest przydatne dla odciążania pochodzenia, gdy wielu użytkowników żąda fragmentu, który jeszcze nie istnieje. Przykładem jest strumień na żywo, w którym pakiety nie są dostępne od początku tego drugiego. Dłuższy interwał buforowania pomaga również odciążać żądania od źródła, ponieważ zawartość wideo nie jest zazwyczaj modyfikowana.
 

|   | Ogólne dostarczanie w sieci Web | Ogólne przesyłanie strumieniowe multimediów | Przesyłanie strumieniowe multimediów na żądanie  
--- | --- | --- | ---
Buforowanie: Pozytywne <br> HTTP 200, 203, 300, <br> 301, 302 i 410 | 7 dni |365 dni | 365 dni   
Buforowanie: Negatywne <br> HTTP 204, 305, 404, <br> i 405 | Brak | 1 sekunda | 1 sekunda
 
### <a name="deal-with-origin-failure"></a>Radzenie sobie z awarią pochodzenia  

Ogólne dostarczanie multimediów i dostarczanie multimediów wideo na żądanie mają również limity czasu pochodzenia i dziennik ponawiania prób na podstawie najlepszych rozwiązań dotyczących typowych wzorców żądań. Na przykład ponieważ ogólne dostarczanie multimediów jest przeznaczone do dostarczania multimediów na żywo i wideo na żądanie, używa krótszego limitu czasu połączenia ze względu na czas zależny charakter przesyłania strumieniowego na żywo.

Po przesiądeniu limitu czasu połączenia, usługa CDN ponawia kilka razy ponawia proces, zanim wyśle do klienta błąd "504 - limit czasu bramy". 

Gdy plik pasuje do listy warunków typu pliku i rozmiaru, sieć CDN używa zachowania do przesyłania strumieniowego multimediów. W przeciwnym razie używa ogólnego dostarczania w sieci Web.
   
### <a name="conditions-for-media-streaming-optimization"></a>Warunki optymalizacji przesyłania strumieniowego multimediów 

W poniższej tabeli wymieniono zestaw kryteriów, które należy spełnić w celu optymalizacji przesyłania strumieniowego multimediów: 
 
Obsługiwane typy przesyłania strumieniowego | Rozszerzenia plików  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, klucz, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Struktura adresów URL Seg-Frag <br> (pasujący regex: ^(/.*)Seq(\d+)-Frag(\d+)
Dash | mpd, kreska, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Płynna transmisja strumieniowa | /manifest/, /QualityLevels/Fragmenty/
  
 
