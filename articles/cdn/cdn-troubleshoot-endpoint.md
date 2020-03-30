---
title: Rozwiązywanie problemów z punktami końcowymi usługi Azure CDN — kod stanu 404
description: Rozwiązywanie problemów z kodami odpowiedzi 404 za pomocą punktów końcowych usługi Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c332c6712cdf057491e3039854aa1a29bd54196f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083127"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Rozwiązywanie problemów z punktami końcowymi usługi Azure CDN zwracającymi kod stanu 404
Ten artykuł umożliwia rozwiązywanie problemów z punktami końcowymi usługi Azure Content Delivery Network (CDN), które zwracają 404 kody stanu odpowiedzi HTTP.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [platformie MSDN Azure i forach przepełnienia stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.

## <a name="symptom"></a>Objaw
Utworzono profil sieci CDN i punkt końcowy, ale zawartość nie wydaje się być dostępna w sieci CDN. Użytkownicy, którzy próbują uzyskać dostęp do zawartości za pośrednictwem adresu URL sieci CDN, otrzymają kod stanu HTTP 404. 

## <a name="cause"></a>Przyczyna
Istnieje kilka możliwych przyczyn, w tym:

* Pochodzenie pliku nie jest widoczne dla sieci CDN.
* Punkt końcowy jest nieprawidłowo skonfigurowany, co powoduje, że sieć CDN wygląda w niewłaściwym miejscu.
* Host odrzuca nagłówek hosta z sieci CDN.
* Punkt końcowy nie miał czasu na propagowanie w całej sieci CDN.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
> [!IMPORTANT]
> Po utworzeniu punktu końcowego sieci CDN nie będzie natychmiast dostępny do użycia, ponieważ wymaga czasu, aby rejestracja propagować za pośrednictwem sieci CDN:
> - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
> - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
> - W przypadku profili usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 90 minut. 
> 
> Jeśli wykonasz kroki opisane w tym dokumencie i nadal otrzymujesz 404 odpowiedzi, rozważ odczekanie kilku godzin na ponowne sprawdzenie przed otwarciem biletu pomocy technicznej.
> 
> 

### <a name="check-the-origin-file"></a>Sprawdzanie pliku pochodzenia
Najpierw sprawdź, czy plik do pamięci podręcznej jest dostępny na serwerze pochodzenia i jest publicznie dostępny w Internecie. Najszybszym sposobem, aby to zrobić, jest otwarcie przeglądarki w prywatnej lub incognito sesji i przeglądanie bezpośrednio do pliku. Wpisz lub wklej adres URL w polu adresu i sprawdź, czy powoduje to oczekiwany plik. Załóżmy na przykład, że masz plik na koncie\/usługi Azure Storage, dostępny pod adresem https: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Jeśli można pomyślnie załadować zawartość tego pliku, przechodzi test.

![To wszystko!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Chociaż jest to najszybszy i najprostszy sposób sprawdzenia, czy plik jest publicznie dostępny, niektóre konfiguracje sieci w organizacji mogą sprawić, że plik jest publicznie dostępny, gdy jest w rzeczywistości widoczny tylko dla użytkowników sieci (nawet jeśli jest hostowany w na platformie Azure). Aby upewnić się, że tak nie jest, przetestuj plik za pomocą zewnętrznej przeglądarki, takiej jak urządzenie przenośne, które nie jest połączone z siecią organizacji, lub maszyna wirtualna na platformie Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Sprawdzanie ustawień źródła
Po zweryfikowaniu, że plik jest publicznie dostępny w Internecie, sprawdź ustawienia pochodzenia. W [witrynie Azure Portal](https://portal.azure.com)przejdź do swojego profilu usługi CDN i wybierz punkt końcowy, którego rozwiązujesz. Na wynikowej stronie **punktu końcowego** wybierz początek układu współrzędnych.  

![Strona punktu końcowego z wyróżnioną punktem pochodzenia](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Zostanie wyświetlona strona **Pochodzenie.** 

![Strona pochodzenia](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Typ i nazwa hosta pochodzenia
Sprawdź, czy wartości **typu Origin** i Nazwy **hosta pochodzenia** są poprawne. W tym przykładzie\/https: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, część nazwy hosta adresu URL jest *cdndocdemo.blob.core.windows.net*, co jest poprawne. Ponieważ źródła usługi Azure Storage, Web App i Cloud Service używają wartości listy rozwijanej dla pola **nazwa hosta pochodzenia,** niepoprawne pisownie nie stanowią problemu. Jeśli jednak używasz niestandardowego pochodzenia, upewnij się, że nazwa hosta jest poprawnie zapisana.

#### <a name="http-and-https-ports"></a>Porty HTTP i HTTPS
Sprawdź porty **HTTP** i **HTTPS**. W większości przypadków 80 i 443 są poprawne i nie będzie wymagać żadnych zmian.  Jeśli jednak serwer pochodzenia nasłuchuje na innym porcie, będzie to musiało być reprezentowane w tym miejscu. Jeśli nie masz pewności, wyświetl adres URL pliku źródłowego. Specyfikacje HTTP i HTTPS używają portów 80 i 443 jako wartości domyślnych. W przykładowym adresie\/URL https: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt port nie jest określony, więc zakłada się wartość domyślną 443, a ustawienia są poprawne.  

Załóżmy jednak, że adres URL pliku pochodzenia,\/który został przetestowany wcześniej, to http: /www.contoso.com:8080/file.txt. Zwróć uwagę na część *:8080* na końcu segmentu nazwy hosta. Numer ten nakazuje przeglądarce użycie portu 8080 do\.łączenia się z serwerem www na stronie www contoso.com, dlatego w polu **portu HTTP** należy wprowadzić numer *8080.* Należy pamiętać, że te ustawienia portu mają wpływ tylko na to, jaki port punkt końcowy używa do pobierania informacji ze źródła.

> [!NOTE]
> Standard usługi Azure CDN z punktów końcowych **Akamai** nie zezwala na pełny zakres portów TCP dla źródeł.  Lista niedozwolonych portów źródłowych znajduje się w artykule [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Azure CDN from Akamai — dozwolone porty źródłowe).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Sprawdzanie ustawień punktu końcowego
Na stronie **Punkt końcowy** wybierz przycisk **Konfiguruj.**

![Strona punktu końcowego z wyróżnionym przyciskiem konfiguruj](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Zostanie wyświetlona strona **Konfigurowanie** punktu końcowego sieci CDN.

![Konfigurowanie strony](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoły
W przypadku **protokołów**sprawdź, czy jest wybrany protokół używany przez klientów. Ponieważ ten sam protokół używany przez klienta jest używany do uzyskiwania dostępu do źródła, ważne jest, aby porty pochodzenia zostały poprawnie skonfigurowane w poprzedniej sekcji. Punkt końcowy sieci CDN nasłuchuje tylko domyślnych portów HTTP i HTTPS (80 i 443), niezależnie od portów pochodzenia.

Wróćmy do naszego hipotetycznego przykładu\/z http: /www.contoso.com:8080/file.txt.  Jak zapamiętysz, Contoso określił *8080* jako swój port HTTP, ale załóżmy również, że określono *44300* jako swój port HTTPS.  Jeśli utworzyli punkt końcowy o nazwie *contoso,* ich nazwa hosta punktu końcowego sieci CDN zostanie *contoso.azureedge.net*.  Żądanie http:\//contoso.azureedge.net/file.txt jest żądaniem HTTP, więc punkt końcowy użyje PROTOKOŁU HTTP na porcie 8080, aby pobrać go ze źródła.  Bezpieczne żądanie za pośrednictwem protokołu\/HTTPS, https: /contoso.azureedge.net/file.txt, spowodowałoby, że punkt końcowy do używania protokołu HTTPS na porcie 44300 podczas pobierania pliku ze źródła.

#### <a name="origin-host-header"></a>Nagłówek hosta źródła
**Nagłówek hosta Pochodzenia** jest wartością nagłówka hosta wysyłaną do źródła początkowego przy każdym żądaniu.  W większości przypadków powinno to być takie samo jak **nazwa hosta Origin,** którą zweryfikowaliśmy wcześniej.  Niepoprawna wartość w tym polu zazwyczaj nie powoduje 404 stanów, ale może spowodować inne stany 4xx, w zależności od tego, czego oczekuje pochodzenie.

#### <a name="origin-path"></a>Ścieżka do źródła
Nareszcie, powinniśmy zweryfikować naszą **ścieżkę pochodzenia.**  Domyślnie jest to puste.  Tego pola należy używać tylko wtedy, gdy chcesz zawęzić zakres zasobów hostowanych w źródle pochodzenia, które mają być udostępniane w sieci CDN.  

W przykładowym punkcie końcowym chcieliśmy, aby wszystkie zasoby na koncie magazynu były dostępne, więc **ścieżka pochodzenia** pozostała pusta.  Oznacza to, że żądanie\/https: /cdndocdemo.azureedge.net/publicblob/lorem.txt powoduje połączenie z punktu końcowego do cdndocdemo.core.windows.net, który żąda */publicblob/lorem.txt*.  Podobnie żądanie dla https:\//cdndocdemo.azureedge.net/donotcache/status.png powoduje punkt końcowy żądający */donotcache/status.png* od początku.

Ale co zrobić, jeśli nie chcesz używać CDN dla każdej ścieżki na swoim pochodzeniu?  Powiedzmy, że chcesz tylko odsłonić *ścieżkę publicblob.*  Jeśli wprowadzimy */publicblob* w **polu Ścieżka pochodzenia,** spowoduje to punkt końcowy wstawić */publicblob* przed każdym żądaniem jest do źródła.  Oznacza to, że żądanie\/https: /cdndocdemo.azureedge.net/publicblob/lorem.txt będzie teraz faktycznie wziąć część żądania adresu URL, */publicblob/lorem.txt*i dołączyć */publicblob* na początku. Powoduje to żądanie */publicblob/publicblob/lorem.txt* od źródła.  Jeśli ta ścieżka nie zostanie rozpoznana do rzeczywistego pliku, pochodzenie zwróci stan 404.  Poprawny adres URL do pobrania lorem.txt w tym\/przykładzie będzie rzeczywiście https: /cdndocdemo.azureedge.net/lorem.txt.  Należy zauważyć, że nie *obejmujemy /publicblob* ścieżki w ogóle, ponieważ część żądania adresu URL jest */lorem.txt* i punkt końcowy dodaje */publicblob*, w wyniku */publicblob/lorem.txt* jest żądanie przekazywane do źródła.

