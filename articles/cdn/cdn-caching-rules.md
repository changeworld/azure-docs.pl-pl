---
title: Kontrolowanie Azure CDN zachowanie buforowania z buforowaniem reguły | Dokumentacja firmy Microsoft
description: Korzystanie z sieci CDN, buforowanie reguł, można ustawić lub zmodyfikować domyślne zachowanie wygaśnięcia pamięci podręcznej globalnie i warunków, takich jak rozszerzeń adresów URL i ścieżka pliku.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: 4095ed763de378a673908d033d87b2aa6d72f13c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260010"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Formant Azure CDN zachowanie buforowania z buforowaniem reguły

> [!NOTE] 
> Reguły buforowania są dostępne tylko dla **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profilów. Dla **Azure CDN Premium from Verizon** profile, należy użyć [aparatu reguł Azure CDN](cdn-rules-engine.md) w **Zarządzaj** portalu dla podobnych możliwościach.
 
Azure sieci dostarczania zawartości (CDN) oferuje dwa sposoby kontrolowania, jak są buforowane pliki: 

- Buforowanie reguł: w tym artykule opisano, jak można użyć sieci dostarczania zawartości (CDN) buforowanie reguły można ustawić lub zmodyfikować domyślne zachowanie wygaśnięcia pamięci podręcznej globalnie i z warunkami niestandardowych, takich jak ścieżki i rozszerzenia adresu URL. Usługa Azure CDN oferuje dwa typy reguł buforowania:

   - Globalne reguły buforowania: można ustawić jedną globalną regułę buforowania dla każdego punktu końcowego w profilu, co wpłynie na wszystkie żądania do punktu końcowego. Globalna reguła buforowania zastępuje wszelkie nagłówki dyrektywy pamięci podręcznej HTTP, jeśli zostały one ustawione.

   - Niestandardowe reguły buforowania: można skonfigurować co najmniej jedną niestandardową regułę buforowania dla każdego punktu końcowego w profilu. Niestandardowe reguły buforowania pasują do określonych ścieżek i rozszerzeń plików, są przetwarzane kolejno i zastępują globalną regułę buforowania, jeśli została ustawiona. 

- Buforowanie ciągu zapytania: można dostosować, jak usługa Azure CDN traktuje buforowania żądań z ciągami zapytań. Aby uzyskać informacje, zobacz [kontroli usługi Azure CDN buforowanie z ciągami zapytań](cdn-query-string.md). Jeśli plik nie jest buforowalnej, ustawienie buforowania ciągu kwerendy nie ma znaczenia, oparte na buforowanie reguły i zachowania domyślnego CDN.

Informacje domyślne zachowanie buforowania i buforowanie dyrektywy nagłówków, zobacz [działa jak buforowanie](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Uzyskiwanie dostępu do usługi Azure CDN reguły buforowania

1. Otwórz Azure portal, wybierz profil CDN, a następnie wybierz punkt końcowy.

2. W lewym okienku w obszarze Ustawienia zaznacz pole **Reguły buforowania**.

   ![Przycisk Reguły buforowania usługi CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Zostanie wyświetlona strona **Reguły buforowania**.

   ![Strona Reguły buforowania usługi CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Ustawienia zachowania buforowania
Globalne i niestandardowych reguł buforowania, można określić następujące **zachowanie buforowania** ustawienia:

- **Obejście pamięci podręcznej**: nie pamięci podręcznej i Ignoruj nagłówków pamięci podręcznej dyrektywy dostarczane do źródła.

- **Zastąpienie**: Ignoruj udostępnione źródła nagłówków pamięci podręcznej dyrektywy; zamiast niej użyj czas buforowania podana.

- **Jeśli brakuje**: Honoruj dostarczane do pochodzenia nagłówki dyrektywy pamięci podręcznej, jeśli istnieją; w przeciwnym razie użyj czas buforowania podana.

![Globalne reguły buforowania](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Niestandardowe reguły buforowania](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Czas trwania wygasania pamięci podręcznej
Globalne i niestandardowych reguł buforowania można określić czas trwania wygasania pamięci podręcznej w dni, godziny, minuty i sekundy:

- Dla **zastąpienia** i **Jeśli brak** **zachowanie buforowania** ustawień, prawidłową pamięć podręczną czasów trwania zakresu od 0 sekund do 366 dni. Wartości 0 sekund sieć CDN buforuje zawartość, ale musi ponownie zatwierdzać każdego żądania w serwerze źródłowym.

- Dla **obejścia pamięci podręcznej** ustawienie, czas trwania pamięci podręcznej jest automatycznie ustawiana na 0 sekund i nie można zmienić.

## <a name="custom-caching-rules-match-conditions"></a>Reguły niestandardowe buforowania warunki dopasowania

Reguły niestandardowe pamięci podręcznej dostępne są niezgodne dwa warunki:
 
- **Ścieżka**: ten stan zgodna ze ścieżką URL, z wyjątkiem nazwy domeny i obsługuje od symbolu wieloznacznego (\*). Na przykład _/myfile.html_, _/Moje/folderu / *_, i _/my/images/*.jpg_. Maksymalna długość to 260 znaków.

- **Rozszerzenie**: ten stan pasuje do rozszerzenia pliku żądanego pliku. Można podać listę rozszerzeń plików rozdzielanych przecinkami do dopasowania. Na przykład _.jpg_, _MP3_, lub _.png_. Maksymalna liczba rozszerzeń wynosi 50, a maksymalna liczba znaków w rozszerzeniu wynosi 16. 

## <a name="global-and-custom-rule-processing-order"></a>Kolejność przetwarzania reguły globalne i niestandardowych
Reguły buforowania globalne i niestandardowe są przetwarzane w następującej kolejności:

- Globalne reguły buforowania pierwszeństwo nad CDN buforowania domyślnie (ustawienia pamięci podręcznej dyrektywy nagłówka HTTP). 

- Niestandardowe reguły buforowania pierwszeństwo globalne reguły buforowania, gdy mają one zastosowanie. Reguły buforowania niestandardowe są przetwarzane w kolejności od góry do dołu. Oznacza to gdy żądanie spełnia oba warunki, reguły w dolnej części listy pierwszeństwo reguły na początku listy. W związku z tym należy umieścić bardziej szczegółowych reguł niższe na liście.

**Przykład**:
- Globalna reguła buforowania: 
   - Zachowanie buforowania: **zastąpienia**
   - Buforuj czas wygaśnięcia: 1 dzień

- Niestandardowe reguły #1 buforowania:
   - Zgodne warunkiem: **ścieżki**
   - Odpowiada wartości:   _/home / *_
   - Zachowanie buforowania: **zastąpienia**
   - Buforuj czas wygaśnięcia: 2 dni

- Niestandardowe reguły #2 buforowania:
   - Zgodne warunkiem: **rozszerzenia**
   - Odpowiada wartości: _.html_
   - Zachowanie buforowania: **Jeśli brak**
   - Buforuj czas wygaśnięcia: 3 dni

Gdy te zasady zostały ustawione, żądanie  _&lt;hosta punktu końcowego&gt;_ wyzwalaczy.azureedge.net/home/index.html buforowanie niestandardowe reguły #2, która jest ustawiona na: **Jeśli brak** i 3 Liczba dni. W związku z tym jeśli *index.html* plik ma `Cache-Control` lub `Expires` nagłówki HTTP są go uznać; w przeciwnym razie jeśli te nagłówki nie są ustawione, plik jest buforowany przez 3 dni.

> [!NOTE] 
> Pliki, które są buforowane przed zmianą reguły Obsługa ustawienie czasu trwania pamięci podręcznej ich źródła. Aby zresetować ich czas trwania pamięci podręcznej, należy najpierw [przeczyścić pliku](cdn-purge-endpoint.md). 
>
> Zmiany konfiguracji usługi Azure CDN może zająć trochę czasu na rozpropagowanie za pośrednictwem sieci: 
> - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
> - Aby uzyskać **Azure CDN Standard from Verizon** profile, propagacji zazwyczaj kończy w ciągu 10 minut.  
>

## <a name="see-also"></a>Zobacz także

- [Jak działa buforowanie](cdn-how-caching-works.md)
- [Samouczek: ustawianie reguł buforowania usługi Azure CDN](cdn-caching-rules-tutorial.md)
