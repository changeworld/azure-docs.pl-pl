---
title: Sterowanie zachowaniem buforowania usługi Azure CDN za pomocą reguł buforowania | Dokumenty firmy Microsoft
description: Reguły buforowania sieci CDN umożliwiają ustawianie lub modyfikowanie domyślnego zachowania wygaśnięcia pamięci podręcznej zarówno globalnie, jak i z warunkami, takimi jak ścieżka adresu URL i rozszerzenia plików.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: magattus
ms.openlocfilehash: ddd7dc7e1245c2a77e866a454bf6bfa3c1f16f88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74278133"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Sterowanie zachowaniem buforowania w usłudze Azure CDN przy użyciu reguł buforowania

> [!NOTE] 
> Reguły buforowania są dostępne tylko dla **usługi Azure CDN Standard firmy Verizon** i Azure **CDN Standard z profilów Akamai.** W przypadku **usługi Azure CDN z** profilów firmy Microsoft należy użyć aparatu reguł standardowych dla [usługi](cdn-standard-rules-engine-reference.md) **Azure CDN Premium z** profilów Verizon, należy użyć aparatu reguł [Verizon Premium](cdn-rules-engine.md) w portalu **Zarządzanie** dla podobnych funkcji.
 
Usługa Azure Content Delivery Network (CDN) oferuje dwa sposoby kontrolowania sposobu buforowania plików: 

- Reguły buforowania: W tym artykule opisano, jak można używać reguł buforowania sieci dostarczania zawartości (CDN) do ustawiania lub modyfikowania domyślnego zachowania wygasania pamięci podręcznej zarówno globalnie, jak i z niestandardowymi warunkami, takimi jak ścieżka adresu URL i rozszerzenie pliku. Usługa Azure CDN oferuje dwa typy reguł buforowania:

   - Globalne reguły buforowania: można ustawić jedną globalną regułę buforowania dla każdego punktu końcowego w profilu, co wpłynie na wszystkie żądania do punktu końcowego. Globalna reguła buforowania zastępuje wszelkie nagłówki dyrektywy pamięci podręcznej HTTP, jeśli zostały one ustawione.

   - Niestandardowe reguły buforowania: można skonfigurować co najmniej jedną niestandardową regułę buforowania dla każdego punktu końcowego w profilu. Niestandardowe reguły buforowania pasują do określonych ścieżek i rozszerzeń plików, są przetwarzane kolejno i zastępują globalną regułę buforowania, jeśli została ustawiona. 

- Buforowanie ciągów zapytań: można dostosować sposób, w jaki usługa Azure CDN traktuje buforowanie żądań za pomocą ciągów zapytań. Aby uzyskać więcej informacji, zobacz [Kontrolowanie zachowania buforowania usługi Azure CDN za pomocą ciągów zapytań](cdn-query-string.md). Jeśli plik nie jest buforowalny, ustawienie buforowania ciągów zapytań nie ma wpływu na reguły buforowania i domyślne zachowania sieci CDN.

Aby uzyskać informacje na temat domyślnego zachowania buforowania i buforowania nagłówków dyrektywy, zobacz [Jak działa buforowanie](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Uzyskiwanie dostępu do reguł buforowania usługi Azure CDN

1. Otwórz witrynę Azure portal, wybierz profil usługi CDN, a następnie wybierz punkt końcowy.

2. W lewym okienku w obszarze Ustawienia zaznacz pole **Reguły buforowania**.

   ![Przycisk Reguły buforowania usługi CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Zostanie wyświetlona strona **Reguły buforowania**.

   ![Strona Reguły buforowania usługi CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Ustawienia zachowania buforowania
W przypadku globalnych i niestandardowych reguł buforowania można określić następujące ustawienia **zachowania buforowania:**

- **Pomijanie pamięci podręcznej:** Nie buforuj i ignoruj nagłówki dyrektywy cache dostarczone przez źródło.

- **Zastępowanie:** Ignoruj czas trwania pamięci podręcznej dostarczonej przez pochodzenie; zamiast tego użyj podanego czasu trwania pamięci podręcznej. Nie spowoduje to zastąpienia kontroli pamięci podręcznej: no-cache.

- **Ustaw, jeśli brakuje:** Honor origin-provided cache-directive nagłówki, jeśli istnieją; w przeciwnym razie należy użyć podanego czasu trwania pamięci podręcznej.

![Globalne reguły buforowania](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Niestandardowe reguły buforowania](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Czas wygaśnięcia pamięci podręcznej
W przypadku globalnych i niestandardowych reguł buforowania można określić czas wygaśnięcia pamięci podręcznej w dniach, godzinach, minutach i sekundach:

- W przypadku ustawień zachowania Zastępowanie i **Ustawianie,** **jeśli brakuje** **zachowania buforowania,** prawidłowe czasy trwania pamięci podręcznej wahają się od 0 sekund do 366 dni. Dla wartości 0 sekund cdn buforuje zawartość, ale musi ponownie ocenić każde żądanie z serwerem pochodzenia.

- W przypadku ustawienia **Bypass cache** czas trwania pamięci podręcznej jest automatycznie ustawiany na 0 sekund i nie można go zmienić.

## <a name="custom-caching-rules-match-conditions"></a>Niestandardowe reguły buforowania są zgodne z warunkami

W przypadku reguł niestandardowej pamięci podręcznej dostępne są dwa warunki dopasowania:
 
- **Ścieżka:** Ten warunek jest zgodny ze ścieżką adresu URL, z wyłączeniem\*nazwy domeny i obsługuje symbol symbolu wieloznacznego ( ). Na przykład _/myfile.html_, _/my/folder/*_ i _/my/images/*.jpg_. Maksymalna długość wynosi 260 znaków.

- **Rozszerzenie:** Ten warunek jest zgodny z rozszerzeniem pliku żądanego. Można podać listę rozszerzeń plików oddzielonych przecinkami, aby dopasować je do siebie. Na przykład _.jpg_, _.mp3_lub _.png_. Maksymalna liczba rozszerzeń wynosi 50, a maksymalna liczba znaków na rozszerzenie wynosi 16. 

## <a name="global-and-custom-rule-processing-order"></a>Kolejność przetwarzania reguł globalnych i niestandardowych
Globalne i niestandardowe reguły buforowania są przetwarzane w następującej kolejności:

- Globalne reguły buforowania mają pierwszeństwo przed domyślnym zachowaniem buforowania sieci CDN (ustawienia nagłówka http cache-directive). 

- Niestandardowe reguły buforowania mają pierwszeństwo przed globalnymi regułami buforowania, gdzie mają zastosowanie. Niestandardowe reguły buforowania są przetwarzane w kolejności od góry do dołu. Oznacza to, że jeśli żądanie spełnia oba warunki, reguły u dołu listy mają pierwszeństwo przed regułami znajdującymi się na górze listy. W związku z tym należy umieścić bardziej szczegółowe reguły niższe na liście.

**Przykład:**
- Globalna reguła buforowania: 
   - Zachowanie buforowania: **Zastępowanie**
   - Czas wygaśnięcia pamięci podręcznej: 1 dzień

- Niestandardowa reguła buforowania #1:
   - Warunek dopasowania: **Ścieżka**
   - Wartość dopasowania: _/home/*_
   - Zachowanie buforowania: **Zastępowanie**
   - Czas wygaśnięcia pamięci podręcznej: 2 dni

- Niestandardowa reguła buforowania #2:
   - Warunek dopasowania: **Rozszerzenie**
   - Wartość dopasowania: _.html_
   - Zachowanie buforowania: **ustaw, jeśli brakuje**
   - Czas wygaśnięcia pamięci podręcznej: 3 dni

Po ustawieniu tych reguł żądanie nazwy _ &lt;hosta&gt;punktu końcowego_.azureedge.net/home/index.html wyzwala niestandardową regułę buforowania #2, która jest ustawiona na: **Ustaw, jeśli brakuje** i 3 dni. W związku z tym jeśli `Cache-Control` plik `Expires` *index.html* ma lub nagłówki HTTP, są one honorowane; w przeciwnym razie, jeśli te nagłówki nie są ustawione, plik jest buforowany przez 3 dni.

> [!NOTE] 
> Pliki, które są buforowane przed zmianą reguły zachowują ustawienie czasu trwania pamięci podręcznej pochodzenia. Aby zresetować czas trwania pamięci podręcznej, należy [przeczyścić plik](cdn-purge-endpoint.md). 
>
> Zmiany konfiguracji usługi Azure CDN może zająć trochę czasu, aby propagować za pośrednictwem sieci: 
> - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
> - W przypadku **usługi Azure CDN Standard z** profilów Verizon propagacja zwykle kończy się w ciągu 10 minut.  
>

## <a name="see-also"></a>Zobacz też

- [Jak działa buforowanie](cdn-how-caching-works.md)
- [Samouczek: ustawianie reguł buforowania usługi Azure CDN](cdn-caching-rules-tutorial.md)
