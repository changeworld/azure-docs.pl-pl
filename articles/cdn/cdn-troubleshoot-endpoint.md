---
title: Rozwiązywanie problemów z punktów końcowych usługi Azure CDN, które zwracają kod stanu 404 | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z 404 kody odpowiedzi z punktów końcowych usługi Azure CDN.
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
ms.openlocfilehash: 1cffef5bbda475032ee7ff07188ab0d9d52846ea
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766108"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Rozwiązywanie problemów z punktów końcowych usługi Azure CDN, które zwracają kod stanu 404
W tym artykule umożliwia rozwiązywanie problemów z punktami końcowymi Azure sieci dostarczania zawartości (CDN), które zwracają kody stanu odpowiedzi HTTP 404.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [MSDN Azure i fora przepełnienie stosu](https://azure.microsoft.com/support/forums/). Alternatywnie można również pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **Get Support**.

## <a name="symptom"></a>Objaw
Po utworzeniu profilu CDN i punktu końcowego, ale wygląda zawartości są dostępne w sieci CDN. Użytkownicy, którzy próba uzyskania dostępu do zawartości za pośrednictwem adresu CDN wyświetlony kod stanu HTTP 404. 

## <a name="cause"></a>Przyczyna
Istnieje kilka możliwych przyczyn, w tym:

* Do pochodzenia pliku nie jest widoczny do sieci CDN.
* Punkt końcowy jest skonfigurowana niepoprawnie powoduje CDN do wyszukiwania w niewłaściwym miejscu.
* Host odrzuca nagłówek hosta z sieci CDN.
* Punkt końcowy nie było Czas propagacji w całej sieci CDN.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
> [!IMPORTANT]
> Po utworzeniu punktu końcowego usługi CDN, nie natychmiast będzie dostępny do użycia, ponieważ czas rejestracji propagację za pośrednictwem sieci CDN:
> - Dla **Azure CDN Standard from Microsoft** profile, propagacji zazwyczaj kończy w ciągu 10 minut. 
> - Aby uzyskać **Azure CDN Standard from Akamai** profile, propagacji zazwyczaj kończy w ciągu jednej minuty. 
> - Dla **Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon** profile, propagacji zazwyczaj kończy w ciągu 90 minut. 
> 
> Wykonaj kroki w tym dokumencie i nadal otrzymujesz odpowiedzi 404, warto rozważyć oczekiwanie na kilka godzin, aby sprawdzić ponownie przed otwarciem biletu pomocy technicznej.
> 
> 

### <a name="check-the-origin-file"></a>Sprawdzanie pliku pierwotnego
Po pierwsze sprawdzenie, czy plik do pamięci podręcznej jest dostępne na serwerze źródłowym i jest dostępny publicznie w Internecie. Otwórz w przeglądarce w sesji prywatne lub incognito i przeglądania bezpośrednio do pliku jest najszybszym sposobem na tym. Wpisz lub wklej adres URL w polu adres i sprawdź jej wynikiem oczekiwanych pliku. Na przykład, załóżmy, że plik na koncie magazynu Azure, dostępny pod https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Czy można pomyślnie załadować zawartości tego pliku, przekazuje testu.

![To wszystko!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Jest najszybszym i Najprostszym sposobem Sprawdź plik jest publicznie dostępna, niektóre konfiguracje sieci w organizacji mógł spowodować, że się, że plik jest publicznie dostępna, jeśli jest on w rzeczywistości widoczne tylko dla użytkowników sieci (nawet jeśli jest ona hostowana w Azure). Aby upewnić się, że nie jest wielkość liter, przetestować go z zewnętrznej przeglądarki, takich jak urządzenia przenośnego, które nie jest podłączony do sieci danej organizacji lub maszynę wirtualną na platformie Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Sprawdź ustawienia źródła
Po upewnieniu się, że plik jest dostępny publicznie w Internecie, sprawdź ustawienia pochodzenia. W [Azure Portal](https://portal.azure.com), przejdź do profilu sieci CDN i wybierz punkt końcowy jest rozwiązywanie problemów. Z powstałe w ten sposób **punktu końcowego** Wybierz źródła.  

![Strona punktu końcowego z wyróżnionym źródła](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

**Pochodzenia** zostanie wyświetlona strona. 

![Początek strony](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Typ źródła i nazwy hosta
Sprawdź, czy wartości **typ źródła** i **nazwę hosta źródła** są poprawne. W tym przykładzie https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, nazwy hosta część adresu URL jest *cdndocdemo.blob.core.windows.net*, która jest poprawna. Ponieważ źródeł magazynu Azure, aplikacji sieci Web i usługi w chmurze Użyj listy rozwijanej wartości dla **nazwę hosta źródła** pole, niepoprawny pisowni nie wystąpił problem. Jednak jeśli używasz źródła niestandardowego, upewnij się, że Twoje nazwa hosta jest poprawna.

#### <a name="http-and-https-ports"></a>Porty HTTP i HTTPS
Sprawdź Twojej **HTTP** i **porty HTTPS**. W większości przypadków 80 i 443 są poprawne, a użytkownik będzie nie wymagają żadnych zmian.  Jednak jeśli serwer pochodzenia nasłuchuje na innym porcie, który należy być reprezentowane w tym miejscu. Jeśli nie masz pewności, należy wyświetlić adres URL dla pliku źródła. Specyfikacje protokołów HTTP i HTTPS korzystania z portów 80 i 443 jako ustawienia domyślne. W adresie URL przykład https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, port nie jest określony, przyjmowana jest wartość domyślna 443 i ustawienia są poprawne.  

Jednak Załóżmy, że adres URL dla pliku pierwotnego, które wcześniej przetestowane, jest protokół http:\//www.contoso.com:8080/file.txt. Uwaga *: 8080* części z końcem segmentu nazwy hosta. Czy numer instruuje przeglądarkę, aby używał portu 8080 do łączenia się z serwerem sieci web w www.contoso.com, dlatego musisz wprowadzić *8080* w **HTTP port** pola. Należy pamiętać, że te ustawienia portu dotyczą tylko jakie port punktu końcowego używa do pobierania informacji ze źródła.

> [!NOTE]
> **Azure CDN Standard from Akamai** punktów końcowych nie zezwalaj na pełny zakres portów TCP dla źródeł.  Lista niedozwolonych portów źródłowych znajduje się w artykule [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx) (Azure CDN from Akamai — dozwolone porty źródłowe).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Sprawdź ustawienia punktu końcowego
Na **punktu końcowego** wybierz pozycję **Konfiguruj** przycisku.

![Strona punktu końcowego o podświetlony przycisk Konfiguruj](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Punkt końcowy CDN **Konfiguruj** zostanie wyświetlona strona.

![Konfigurowanie strony](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoły
Aby uzyskać **protokołów**, sprawdź, czy wybrano protokół używany przez klientów. Ponieważ ten sam protokół używany przez klienta jest używane do dostępu do źródła, należy mieć porty źródła poprawnie skonfigurowane w poprzedniej sekcji. Punkt końcowy CDN nasłuchuje tylko na domyślne portach HTTP i HTTPS (80 i 443), niezależnie od portów pochodzenia.

Teraz wróć do naszym przykładzie hipotetyczny protokołu http:\//www.contoso.com:8080/file.txt.  Jako zapamiętania, Contoso określonego *8080* jako ich HTTP portu, ale również Załóżmy podał *44300* jako ich portu HTTPS.  Jeśli one utworzone punktu końcowego o nazwie *contoso*, będzie ich hosta punktu końcowego CDN *contoso.azureedge.net*.  Żądania http:\//contoso.azureedge.net/file.txt jest żądanie HTTP, więc punktu końcowego użyje HTTP na porcie 8080, aby pobrać go ze źródła.  Bezpieczne żądania za pośrednictwem protokołu HTTPS, https: \/ /contoso.azureedge.net/file.txt, spowodowałoby punktu końcowego do używania protokołu HTTPS na porcie 44300 podczas pobierania pliku ze źródła.

#### <a name="origin-host-header"></a>Nagłówek hosta źródła
**Nagłówka hosta źródła** jest wartość nagłówka hosta wysyłana do źródła z każdym żądaniem.  W większości przypadków ta powinna być taka sama jak **nazwę hosta źródła** możemy zweryfikować wcześniej.  Nieprawidłowa wartość w tym polu zwykle nie będzie powodować stany 404, ale może powodować innych stanów 4xx, w zależności od początkowego oczekuje.

#### <a name="origin-path"></a>Ścieżka do źródła
Ponadto możemy zweryfikować naszych **ścieżki źródła**.  Domyślnie to pole jest puste.  W tym polu należy używać tylko, jeśli chcesz zawęzić zakres hostowanej pochodzenia zasobów, które chcesz udostępnić w sieci CDN.  

W przykładzie punkt końcowy, możemy wszystkich zasobów na koncie magazynu, które mają być dostępne, dlatego **ścieżka do źródła** był pusty.  Oznacza to, że żądanie https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt wynikiem połączenia z punktem końcowym cdndocdemo.core.windows.net żąda */publicblob/lorem.txt*.  Podobnie, żądanie https:\//cdndocdemo.azureedge.net/donotcache/status.png wynikiem żądania punktu końcowego */donotcache/status.png* ze źródła.

Ale co zrobić, jeśli nie chcesz użyć w sieci CDN dla każdej ścieżki na źródła?  Powiedz chcesz udostępnić *publicblob* ścieżki.  Jeśli firma Microsoft wprowadź */publicblob* w **ścieżki źródła** pola, który spowoduje, że punkt końcowy wstawić */publicblob* przed każde żądanie do źródła.  Oznacza to, że żądania dla protokołu https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt teraz potrwa faktycznie żądania część adresu URL, */publicblob/lorem.txt*i Dołącz */publicblob* na początku. Powoduje to żądanie */publicblob/publicblob/lorem.txt* ze źródła.  W przypadku tej ścieżki nie jest rozpoznawane jako rzeczywisty plik, punkt początkowy zwraca stanu 404.  Poprawny adres URL do pobrania lorem.txt w tym przykładzie będzie faktycznie https:\//cdndocdemo.azureedge.net/lorem.txt.  Należy pamiętać, że firma Microsoft nie zawierają */publicblob* ścieżki gwarancja, ponieważ żądanie część adresu URL jest */lorem.txt* i dodaje punkt końcowy */publicblob*, co */publicblob/lorem.txt* żądanie przesyłane do źródła.

