---
title: Kontrolowanie Azure CDN zachowanie buforowania z buforowaniem reguły | Dokumentacja firmy Microsoft
description: Korzystanie z sieci CDN, buforowanie reguł, można ustawić lub zmodyfikować domyślne zachowanie wygaśnięcia pamięci podręcznej globalnie i warunków, takich jak rozszerzeń adresów URL i ścieżka pliku.
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: 735978a0986b2b16b4f96faca78c06d798915002
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Formant Azure CDN zachowanie buforowania z buforowaniem reguły

> [!NOTE] 
> Reguły buforowania są dostępne tylko dla **Azure CDN from Verizon Standard** i **Azure CDN from Akamai Standard**. Dla **Azure CDN from Verizon Premium**, można użyć [aparatu reguł Azure CDN](cdn-rules-engine.md) w **Zarządzaj** portalu dla podobnych możliwościach.
 
Azure sieci dostarczania zawartości (CDN) oferuje dwa sposoby kontrolowania, jak są buforowane pliki: 

- Buforowanie reguł: w tym artykule opisano, jak można użyć sieci dostarczania zawartości (CDN) buforowanie reguły można ustawić lub zmodyfikować domyślne zachowanie wygaśnięcia pamięci podręcznej globalnie i z warunkami niestandardowych, takich jak ścieżki i rozszerzenia adresu URL. Usługi Azure CDN oferuje dwa typy zasad buforowania:
   - Globalny buforowanie reguł: jedną globalnego regułę buforowania dla każdego punktu końcowego można ustawić w Twoim profilu, który ma wpływ na wszystkie żądania do punktu końcowego. Globalna reguła buforowania zastępuje wszelkie nagłówki dyrektywy pamięci podręcznej HTTP, jeśli ustawiona.
   - Niestandardowe reguły buforowania: można skonfigurować jeden lub więcej buforowanie niestandardowe reguły dla każdego punktu końcowego w profilu. Niestandardowe buforowanie reguł dopasowania określonej ścieżki i rozszerzenia plików, są przetwarzane w kolejności i zastąpienie reguły globalnej pamięci podręcznej, jeśli ustawiona. 

- Buforowanie ciągu zapytania: można dostosować, jak usługa Azure CDN traktuje buforowania żądań z ciągami zapytań. Aby uzyskać informacje, zobacz [kontroli usługi Azure CDN buforowanie z ciągami zapytań](cdn-query-string.md). Jeśli plik nie jest buforowalnej, ustawienie buforowania ciągu kwerendy nie ma znaczenia, oparte na buforowanie reguły i zachowania domyślnego CDN.

Informacje domyślne zachowanie buforowania i buforowanie dyrektywy nagłówków, zobacz [działa jak buforowanie](cdn-how-caching-works.md).

## <a name="tutorial"></a>Samouczek

Jak ustawić CDN buforowanie reguł:

1. Otwórz Azure portal, wybierz profil CDN, a następnie wybierz punkt końcowy.
2. W lewym okienku w obszarze Ustawienia zaznacz **buforowanie reguły**.

   ![Przycisk reguły buforowania CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

3. Utwórz regułę buforowania globalne w następujący sposób:
   1. W obszarze **globalnej pamięci podręcznej zasad**ustaw **zachowanie buforowania ciągu kwerendy** do **ignorować ciągi kwerendy**.
   2. Ustaw **zachowanie buforowania** do **Jeśli brak**.
       
   3. Dla **pamięci podręcznej Czas wygaśnięcia**, wprowadź 10 w **dni** pola.

       Globalna reguła buforowania ma wpływ na wszystkie żądania do punktu końcowego. Ta zasada będzie honorować pochodzenia nagłówki dyrektywy pamięci podręcznej, jeśli istnieją (`Cache-Control` lub `Expires`); w przeciwnym razie, jeśli nie są one określone, ustawia pamięć podręczną do 10 dni. 

     ![Globalne reguły buforowania](./media/cdn-caching-rules/cdn-global-caching-rules.png)

4. Utwórz niestandardową regułę buforowania w następujący sposób:
    1. W obszarze **niestandardowe reguły buforowania**ustaw **dopasować stan** do **ścieżki** i **odpowiada wartości** do `/images/*.jpg`.
    2. Ustaw **zachowanie buforowania** do **zastąpienia** , a następnie wprowadź 30 w **dni** pola.
       
       Ta niestandardową regułę buforowania ustawia czas buforowania, 30 dni na dowolnym `.jpg` pliki obrazów w `/images` folderu punktu końcowego. Zastępuje ona żadnego `Cache-Control` lub `Expires` nagłówków HTTP, które są wysyłane przez serwer pochodzenia.

    ![Niestandardowe reguły buforowania](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
> [!NOTE] 
> Pliki, które są buforowane przed zmianą reguły Obsługa ustawienie czasu trwania pamięci podręcznej ich źródła. Aby zresetować ich czas trwania pamięci podręcznej, należy najpierw [przeczyścić pliku](cdn-purge-endpoint.md). Aby uzyskać **Azure CDN from Verizon** punktów końcowych, może potrwać do 90 minut buforowanie zasady zaczęły obowiązywać.

## <a name="reference"></a>Informacje ogólne

### <a name="caching-behavior-settings"></a>Ustawienia zachowania buforowania
Globalne i niestandardowych reguł buforowania, można określić następujące **zachowanie buforowania** ustawienia:

- **Obejście pamięci podręcznej**: nie pamięci podręcznej i Ignoruj nagłówków pamięci podręcznej dyrektywy dostarczane do źródła.
- **Zastąpienie**: Ignoruj udostępnione źródła nagłówków pamięci podręcznej dyrektywy; zamiast niej użyj czas buforowania podana.
- **Jeśli brakuje**: Honoruj dostarczane do pochodzenia nagłówki dyrektywy pamięci podręcznej, jeśli istnieją; w przeciwnym razie użyj czas buforowania podana.

### <a name="cache-expiration-duration"></a>Czas wygasania pamięci podręcznej
Globalne i niestandardowych reguł buforowania można określić czas trwania wygasania pamięci podręcznej w dni, godziny, minuty i sekundy:

- Dla **zastąpienia** i **Jeśli brak** **zachowanie buforowania** ustawień, prawidłową pamięć podręczną czasów trwania zakresu od 0 sekund do 366 dni. Wartości 0 sekund sieć CDN buforuje zawartość, ale musi ponownie zatwierdzać każdego żądania w serwerze źródłowym.
- Dla **obejścia pamięci podręcznej** ustawienie, czas trwania pamięci podręcznej jest automatycznie ustawiana na 0 sekund i nie można zmienić.

### <a name="custom-caching-rules-match-conditions"></a>Reguły niestandardowe buforowania warunki dopasowania

Reguły niestandardowe pamięci podręcznej dostępne są niezgodne dwa warunki:
 
- **Ścieżka**: ten stan zgodna ze ścieżką URL, z wyjątkiem nazwy domeny i obsługuje od symbolu wieloznacznego (\*). Na przykład `/myfile.html`, `/my/folder/*`, i `/my/images/*.jpg`. Maksymalna długość to 260 znaków.

- **Rozszerzenie**: ten stan pasuje do rozszerzenia pliku żądanego pliku. Można podać listę rozszerzeń plików rozdzielanych przecinkami do dopasowania. Na przykład `.jpg`, `.mp3`, lub `.png`. Maksymalna liczba rozszerzeń wynosi 50, a maksymalna liczba znaków w rozszerzeniu wynosi 16. 

### <a name="global-and-custom-rule-processing-order"></a>Kolejność przetwarzania reguły globalne i niestandardowych
Reguły buforowania globalne i niestandardowe są przetwarzane w następującej kolejności:

- Globalne reguły buforowania pierwszeństwo nad CDN buforowania domyślnie (ustawienia pamięci podręcznej dyrektywy nagłówka HTTP). 

- Niestandardowe reguły buforowania pierwszeństwo globalne reguły buforowania, gdy mają one zastosowanie. Reguły buforowania niestandardowe są przetwarzane w kolejności od góry do dołu. Oznacza to gdy żądanie spełnia oba warunki, reguły w dolnej części listy pierwszeństwo reguły na początku listy. W związku z tym należy umieścić bardziej szczegółowych reguł niższe na liście.

**Przykład**:
- Globalna reguła buforowania: 
   - Zachowanie buforowania: **zastąpienia**
   - Buforuj czas wygaśnięcia: 1 dzień

- Niestandardowe reguły #1 buforowania:
   - Zgodne warunkiem: **ścieżki**
   - Dopasuj wartości: `/home/*`
   - Zachowanie buforowania: **zastąpienia**
   - Buforuj czas wygaśnięcia: 2 dni

- Niestandardowe reguły #2 buforowania:
   - Zgodne warunkiem: **rozszerzenia**
   - Dopasuj wartości: `.html`
   - Zachowanie buforowania: **Jeśli brak**
   - Buforuj czas wygaśnięcia: 3 dni

Gdy te zasady zostały ustawione, żądanie `<endpoint>.azureedge.net/home/index.html` wyzwalaczy buforowanie niestandardowe reguły #2, która jest ustawiona na: **Jeśli brak** i 3 dni. W związku z tym jeśli `index.html` plik ma `Cache-Control` lub `Expires` nagłówki HTTP są go uznać; w przeciwnym razie jeśli te nagłówki nie są ustawione, plik jest buforowany przez 3 dni.

