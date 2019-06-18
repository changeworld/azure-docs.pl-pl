---
title: Rozwiązywanie problemów z punktów końcowych usługi Azure CDN, które zwracają kod stanu 404 | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z 404 kody odpowiedzi z punktami końcowymi usługi Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 66ee211856bb451caad7af02103aa306d76e8f97
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60323711"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Rozwiązywanie problemów z punktów końcowych usługi Azure CDN, które zwracają kod stanu 404
W tym artykule umożliwia rozwiązywanie problemów z punktami końcowymi usługi Azure Content Delivery Network (CDN), które zwracają 404 kodów stanu odpowiedzi HTTP.

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [MSDN Azure i Stack Overflow forów](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz **Uzyskaj pomoc techniczną**.

## <a name="symptom"></a>Objaw
Utworzono profil CDN i punktu końcowego, ale zawartość wydają się być dostępny w sieci CDN. Użytkownicy, którzy spróbują do uzyskiwania dostępu do zawartości za pomocą adresu URL usługi CDN wyświetlony kod stanu HTTP 404. 

## <a name="cause"></a>Przyczyna
Istnieje kilka możliwych przyczyn, takich jak:

* Pochodzenie pliku nie jest widoczny dla usługi CDN.
* Punkt końcowy jest nieprawidłowo skonfigurowana, powodując Szukaj w niewłaściwym miejscu w sieci CDN.
* Host odrzuca nagłówek hosta z sieci CDN.
* Punkt końcowy nie ma czas na propagację w całej sieci CDN.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
> [!IMPORTANT]
> Po utworzeniu punktu końcowego usługi CDN, nie od razu będzie dostępny do użycia, ponieważ zajmuje trochę czasu rejestracji do propagowania za pośrednictwem sieci CDN:
> - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
> - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
> - W przypadku profili usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 90 minut. 
> 
> W przypadku wykonaniu czynności opisanych w tym dokumencie, a nadal otrzymujesz odpowiedzi 404, należy wziąć pod uwagę oczekiwanie na kilka godzin, aby sprawdzić ponownie, przed otwarciem biletu pomocy technicznej.
> 
> 

### <a name="check-the-origin-file"></a>Sprawdź plik pierwotny
Po pierwsze sprawdzenie, czy plik do pamięci podręcznej jest dostępna na serwerze źródłowym i jest dostępny publicznie w Internecie. Najszybszym sposobem wykonania tego zadania jest prywatny lub incognito w sesji i przejdź bezpośrednio do pliku, otwórz przeglądarkę. Wpisz lub wklej adres URL w polu adres i sprawdź, czy powoduje to plików, których oczekujesz. Załóżmy na przykład, posiadasz plik na koncie usługi Azure Storage, dostępny pod adresem https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Jeśli można pomyślnie załadować zawartości tego pliku, przejdzie test.

![To wszystko!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> To najszybszy i najłatwiejszy sposób, aby sprawdzić, czy plik jest publicznie dostępny, niektórych konfiguracji sieci w organizacji można stał się, że plik jest publicznie dostępny, gdy jest to, w rzeczywistości widoczne tylko dla użytkowników sieci (nawet jeśli jest ona hostowana na Platforma Azure). Aby upewnić się, że nie jest to przypadek, przetestować plik z zewnętrznej przeglądarki, takich jak urządzenia przenośnego, które nie jest połączony z siecią organizacji lub maszynę wirtualną na platformie Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Sprawdź ustawienia źródła
Po upewnieniu się, że plik jest publicznie dostępny w Internecie, sprawdź ustawienia źródła. W [witryny Azure Portal](https://portal.azure.com), przejdź do swojego profilu CDN i wybierz punkt końcowy, w przypadku rozwiązywania problemów. Z wynikowy **punktu końcowego** wybierz źródło.  

![Strona punktu końcowego z pochodzenia wyróżniony](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

**Pochodzenia** zostanie wyświetlona strona. 

![Strona źródła](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Typ źródła i nazwy hosta
Upewnij się, że wartości **typ źródła** i **nazwa hosta źródła** są poprawne. W tym przykładzie https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, nazwy hosta część adresu URL *cdndocdemo.blob.core.windows.net*, który jest poprawny. Ponieważ źródła usługi Azure Storage, aplikacji sieci Web i usługi w chmurze Użyj listy rozwijanej wartości dla **nazwa hosta źródła** pisowni pola, niepoprawny nie wystąpił problem. Jednak jeśli używasz źródła niestandardowego, upewnij się, że Twoja nazwa hosta jest poprawna.

#### <a name="http-and-https-ports"></a>Porty HTTP i HTTPS
Sprawdź swoje **HTTP** i **portów HTTPS**. W większości przypadków 80 i 443 są poprawne, i będzie wymagać żadnych zmian.  Jednak jeśli serwer pochodzenia nasłuchuje na innym porcie, który należy może być reprezentowana w tym miejscu. Jeśli nie masz pewności, Wyświetl adres URL pliku źródła. HTTP i HTTPS specyfikacjach zdalne wywoływanie procedur porty 80 i 443 jako ustawienia domyślne. W adresie URL przykład https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, port nie jest określony, przyjmowana jest wartość domyślna 443 i ustawienia są poprawne.  

Jednak Załóżmy, że adres URL dla pliku pierwotnego, który został przetestowany wcześniej, jest protokół http:\//www.contoso.com:8080/file.txt. Uwaga *: 8080* części na końcu segmentu nazwy hosta. Czy liczba powoduje, że przeglądarka łączenia z serwerem sieci web w sieci Web za pomocą portu 8080\.contoso.com, w związku z tym musisz wprowadzić *8080* w **portu HTTP** pola. Należy zauważyć, że te ustawienia portów mieć wpływ na tylko port punktu końcowego używa w celu pobierania informacji ze źródła.

> [!NOTE]
> **Usługa Azure CDN Standard from Akamai** punktów końcowych nie zezwalają na pełny zakres portów TCP dla źródeł.  Lista niedozwolonych portów źródłowych znajduje się w artykule [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Azure CDN from Akamai — dozwolone porty źródłowe).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Sprawdź ustawienia punktu końcowego
Na **punktu końcowego** wybierz opcję **Konfiguruj** przycisku.

![Strona do Endpoint z wyróżnionym przyciskiem Konfiguruj](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Punkt końcowy usługi CDN **Konfiguruj** zostanie wyświetlona strona.

![Konfigurowanie strony](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoły
Aby uzyskać **protokołów**, sprawdź, czy wybrano protokół używany przez klientów. Ponieważ ten sam protokół używany przez klienta jest używana do dostępu do źródła, należy mieć porty źródła poprawnie skonfigurowane w poprzedniej sekcji. Punkt końcowy usługi CDN nasłuchuje tylko na domyślnych portach HTTP i HTTPS (80 i 443), niezależnie od tego, niedozwolonych portów.

Powrócimy do naszych hipotetycznego przykładu za pośrednictwem protokołu http:\//www.contoso.com:8080/file.txt.  Jak zapamiętasz, Contoso określony *8080* co ich HTTP portu, ale również Załóżmy, że podał *44300* jako ich portu HTTPS.  Jeśli zostały utworzone punktu końcowego o nazwie *contoso*, będzie ich nazwę hosta punktu końcowego usługi CDN *contoso.azureedge.net*.  Żądanie http:\//contoso.azureedge.net/file.txt jest żądanie HTTP, więc punkt końcowy użyje protokołu HTTP na porcie 8080, aby pobrać je ze źródła.  Bezpieczne żądania za pośrednictwem protokołu HTTPS-https: \/ /contoso.azureedge.net/file.txt, spowodowałoby punktu końcowego za pośrednictwem protokołu HTTPS na porcie 44300 pobranie pliku ze źródła.

#### <a name="origin-host-header"></a>Nagłówek hosta źródła
**Nagłówek hosta źródła** jest wartość nagłówka hosta wysyłana do źródła z każdym żądaniem.  W większości przypadków ta powinna być taka sama jak **nazwa hosta źródła** możemy zweryfikować wcześniej.  Niepoprawna wartość w tym polu ogólnie nie będzie powodować stany 404, ale może powodować inne stany 4xx, w zależności od początkowego oczekuje.

#### <a name="origin-path"></a>Ścieżka do źródła
Ponadto możemy zweryfikować nasze **ścieżka do źródła**.  Domyślnie to pole jest puste.  W tym polu należy używać tylko, jeśli chcesz zawęzić zakres zasobów hostowanych źródła, który chcesz udostępnić w sieci CDN.  

Punkt końcowy przykład Chcieliśmy wszystkich zasobów na koncie magazynu, który ma być dostępne, dzięki czemu **ścieżka do źródła** był pusty.  Oznacza to, że żądanie https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt powoduje połączenie z punktem końcowym cdndocdemo.core.windows.net, który żąda */publicblob/lorem.txt*.  Analogicznie, żądania dla protokołu https:\//cdndocdemo.azureedge.net/donotcache/status.png wynikiem żądania punktu końcowego */donotcache/status.png* ze źródła.

Ale co zrobić, jeśli nie chcesz używać sieci CDN dla każdej ścieżki w źródle użytkownika?  Powiedz, chcesz udostępnić *publicblob* ścieżki.  Jeśli możemy wprowadzić */publicblob* w **ścieżka do źródła** pola, który spowoduje, że punkt końcowy, aby wstawić */publicblob* przed każdym żądaniem zostaną wprowadzone do źródła.  Oznacza to, że żądania dla protokołu https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt teraz będzie faktycznego ulokowania żądania część adresu URL, */publicblob/lorem.txt*, obiektów blob i uzupełnialnych */publicblob* na początku. Skutkuje to żądanie */publicblob/publicblob/lorem.txt* ze źródła.  Jeśli ścieżka nie jest rozpoznawane jako rzeczywisty plik, pochodzenie zwróci stanu 404.  Poprawny adres URL do pobrania lorem.txt w tym przykładzie będzie faktycznie https:\//cdndocdemo.azureedge.net/lorem.txt.  Należy zauważyć, że firma Microsoft nie uwzględniają */publicblob* ścieżki w ogóle ponieważ żądanie część adresu URL jest */lorem.txt* i dodaje punkt końcowy */publicblob*, dając w efekcie w */publicblob/lorem.txt* żądanie przesyłane do źródła.

