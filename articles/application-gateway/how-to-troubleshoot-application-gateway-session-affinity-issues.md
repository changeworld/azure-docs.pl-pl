---
title: Rozwiązywanie problemów z koligacją sesji bramy aplikacji platformy Azure
description: Ten artykuł zawiera informacje na temat rozwiązywania problemów z koligacją sesji w usłudze Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 07165a497e75934a65719e48a9af7d8d6906ee7b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65538332"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Rozwiązywanie problemów z koligacją sesji bramy aplikacji platformy Azure

Dowiedz się, jak Diagnozuj i rozwiązuj problemy koligacji sesji przy użyciu usługi Azure Application Gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

Funkcja koligacji sesji na podstawie plików cookie jest przydatna, gdy chcesz zachować sesję użytkownika na tym samym serwerze. Używając plików cookie zarządzanych przez bramę, usługa Application Gateway może kierować dalszy ruch z sesji użytkownika na ten sam serwer w celu przetwarzania. Jest to ważne w przypadkach, w których stan sesji jest zapisywany lokalnie na serwerze dla sesji użytkownika.

## <a name="possible-problem-causes"></a>Powoduje, że możliwy problem

Problem w utrzymywanie koligacji sesji na podstawie plików cookie może się zdarzyć z następujących głównych przyczyn:

- Ustawienie "koligacji na podstawie plików cookie" nie jest włączone.
- Aplikacja nie może obsługiwać koligacji na podstawie plików cookie
- Aplikacja używa koligacji na podstawie plików cookie, ale żądania nadal odbijania między serwerami zaplecza

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Sprawdź, czy włączone jest ustawienie "koligacji na podstawie plików Cookie"

Czasami z koligacją sesji może wystąpić, gdy zapomnisz włączyć ustawienie "Na podstawie plików Cookie koligacji". Aby ustalić, czy włączono ustawienia "Na podstawie plików Cookie koligacji" na karcie Ustawienia protokołu HTTP w witrynie Azure portal, postępuj zgodnie z instrukcjami:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. W **lewy pasek nawigacyjny** okienku kliknij **wszystkie zasoby**. Kliknij nazwę bramy aplikacji w bloku wszystkie zasoby. Jeśli wybrana subskrypcja zawiera kilka zasobów, można wprowadzić nazwę bramy aplikacji w **Filtruj według nazwy...** aby łatwo uzyskać dostęp do bramy aplikacji.

3. Wybierz **ustawienia HTTP** karcie **ustawienia**.

   ![troubleshoot-session-affinity-issues-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Kliknij przycisk **appGatewayBackendHttpSettings** po prawej stronie, aby sprawdzić, czy wybrano **włączone** dla koligacji na podstawie plików Cookie.

   ![troubleshoot-session-affinity-issues-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Możesz również sprawdzić wartość "**CookieBasedAffinity**" jest ustawiona na *włączone*w obszarze "**backendHttpSettingsCollection**" przy użyciu jednej z następujących metod:

- Uruchom [Get AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) w programie PowerShell
- Przejrzyj plik JSON przy użyciu szablonu usługi Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Aplikacja nie może obsługiwać koligacji na podstawie plików cookie

#### <a name="cause"></a>Przyczyna

Application gateway może wykonywać tylko koligacji na podstawie sesji za pomocą pliku cookie.

#### <a name="workaround"></a>Obejście

Jeśli aplikacja nie może obsługiwać koligacji na podstawie plików cookie, należy użyć modułu równoważenia obciążenia platformy azure zewnętrznego lub wewnętrznego lub innego rozwiązania innych firm.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Aplikacja używa koligacji na podstawie plików cookie, ale żądania nadal odbijania między serwerami zaplecza

#### <a name="symptom"></a>Objaw

Gdy uzyskujesz dostęp do bramy aplikacji krótkiej nazwy adresu URL w przeglądarce Internet Explorer, na przykład przy użyciu włączono ustawienie koligacji na podstawie plików Cookie: [ http://website ](http://website/) , żądanie jest nadal odbijania między serwerami zaplecza.

Aby zidentyfikować ten problem, postępuj zgodnie z instrukcjami:

1. Podejmuj śledzenia debugera w sieci web "Client", który nawiązuje połączenie z aplikacji znajdującej się za Gateway(We are using Fiddler in this example) aplikacji.
    **Porada** Jeśli nie wiesz, jak używać narzędzia Fiddler, zaznacz opcję "**chcesz zbierać ruchu sieciowego i analizować je przy użyciu debugera w sieci web**" u dołu.

2. Sprawdź i analizować dzienniki sesji, w celu określenia, czy pliki cookie, dostarczonych przez klienta mają szczegóły ARRAffinity. Jeśli nie możesz znaleźć ARRAffinity uzyskać szczegółowe informacje, takie jak "**ARRAffinity =** *ARRAffinityValue*" w zestawie plików cookie, który oznacza, że klient nie jest odpowiedzi z plikiem cookie Tabelaryzuj, który jest dostarczany przez Usługa Application Gateway.
    Na przykład:

    ![troubleshoot-session-affinity-issues-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

        ![troubleshoot-session-affinity-issues-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

Aplikacja w dalszym ciągu próbuje ustawić plik cookie na każde żądanie, dopóki nie zostanie osiągnięty odpowiedzi.

#### <a name="cause"></a>Przyczyna

Ten problem występuje, ponieważ program Internet Explorer i w innych przeglądarkach może przechowuje ani nie używaj plików cookie z adresem URL krótką nazwę.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy uzyskać dostęp do bramy aplikacji przy użyciu nazwy FQDN. Na przykład użyć [ http://website.com ](https://website.com/) lub [ http://appgw.website.com ](http://appgw.website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Dodatkowe dzienniki rozwiązywania problemów z

Możesz zbierać dodatkowe dzienniki i analizować je rozwiązywać problemy z koligacji powiązane opartą na plikach cookie sesji problemów

### <a name="analyze-application-gateway-logs"></a>Analizowanie dzienników usługi Application Gateway

Aby zbierać dzienniki bramy aplikacji, postępuj zgodnie z instrukcjami:

Włączanie rejestrowania za pośrednictwem witryny Azure Portal

1. W [witryny Azure portal](https://portal.azure.com/), Znajdź zasób, a następnie kliknij przycisk **dzienniki diagnostyczne**.

   W usłudze Application Gateway dostępne są trzy dzienniki: Dostęp do dziennika zapory, dziennik zadania, dziennik wydajności

2. Aby rozpocząć zbieranie danych, kliknij przycisk **Włącz diagnostykę**.

   ![troubleshoot-session-affinity-issues-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. **Ustawień diagnostycznych** blok zawiera ustawienia dla dzienników diagnostycznych. W tym przykładzie usługi Log Analytics są przechowywane dzienniki. Kliknij przycisk **Konfiguruj** w obszarze **usługi Log Analytics** można ustawić obszaru roboczego. Na potrzeby zapisywania dzienników diagnostycznych można także skorzystać z usługi Event Hubs i konta magazynu.

   ![troubleshoot-session-affinity-issues-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Potwierdź ustawienia a następnie kliknij przycisk **Zapisz**.

   ![troubleshoot-session-affinity-issues-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Wyświetlanie i analizowanie dzienników dostępu usługa Application Gateway

1. W witrynie Azure portal w widoku zasobu bramy aplikacji, wybierz **dzienniki diagnostyczne** w **monitorowanie** sekcji.

   ![troubleshoot-session-affinity-issues-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Po prawej stronie wybierz pozycję "**ApplicationGatewayAccessLog**" na liście rozwijanej w obszarze **kategorie dziennika.**  

   ![troubleshoot-session-affinity-issues-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Na liście dziennik bramy Application Gateway dostępu kliknij dziennik, który chcesz analizować i wyeksportować, a następnie wyeksportowania pliku JSON.

4. Konwertuj plik JSON, który został wyeksportowany w kroku 3 do pliku CSV i wyświetlić je w programie Excel, Power BI lub innego narzędzia wizualizacji danych.

5. Sprawdź następujące dane:

- **ClientIP**— jest to adres IP klienta z klienta nawiązującego połączenie.
- **ClientPort** — jest to port źródłowy z klienta nawiązującego połączenie dla żądania.
- **RequestQuery** — oznacza to, czy Odebrano żądanie serwera docelowego.
- **Serwer routingu**: Wystąpienie puli zaplecza, czy Odebrano żądanie.
- **X-AzureApplicationGateway-LOG-ID**: Identyfikator korelacji użyta dla żądania. Może służyć do rozwiązywania problemów ruch na serwerach zaplecza. Na przykład: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **STAN SERWERA**: Kod odpowiedzi HTTP bramy Application Gateway otrzymany z zaplecza.

  ![troubleshoot-session-affinity-issues-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Jeśli widać, dwa elementy pochodzą z tej samej ClientIP i portu klienta, a te są wysyłane na ten sam serwer zaplecza, oznacza to Application Gateway, poprawnie skonfigurowany.

Jeśli widać, dwa elementy pochodzą z tej samej ClientIP i portu klienta i są one wysyłane do różnych serwerów zaplecza, oznacza to żądanie jest odbijania między serwerami wewnętrznej bazy danych, wybierz opcję "**koligacji na podstawie plików cookie, ale żądania jest używany przez aplikację nadal odbijania między serwerami zaplecza**"u dołu, aby rozwiązać problemy.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Debuger sieci web umożliwia przechwytywanie i analizowanie traffics protokołu HTTP lub HTTPS

Sieci Web narzędzia debugowania, takich jak Fiddler, ułatwia debugowanie aplikacji sieci web przez Przechwytywanie ruchu sieciowego między komputerami Internet i testowania. Te narzędzia umożliwiają sprawdzanie danych przychodzących i wychodzących, jak przeglądarka odbiera/wysyła je. Narzędzie fiddler, w tym przykładzie ma opcję powtarzania HTTP, które mogą ułatwić rozwiązywanie problemów po stronie klienta z aplikacji sieci web, szczególnie w przypadku uwierzytelniania rodzaju problemu.

Za pomocą debugera web wybranych przez użytkownika. W tym przykładzie używamy narzędzia Fiddler do przechwytywania i analizowania traffics protokołu http lub https, postępuj zgodnie z instrukcjami:

1. Pobierz narzędzie Fiddler w <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Wybierz Fiddler4, jeśli przechwytywania komputer ma zainstalowany program .NET 4. W przeciwnym razie wybierz Fiddler2.

2. Kliknij prawym przyciskiem myszy plik wykonywalny Instalatora i Uruchom jako administrator, aby zainstalować.

            ![troubleshoot-session-affinity-issues-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Po otwarciu programu Fiddler, automatycznie początek Przechwytywanie ruchu (Uwaga: Przechwytywanie w lewej dolnym rogu). Naciśnij klawisz F12, aby rozpocząć lub zatrzymać przechwytywanie ruchu sieciowego.

        ![troubleshoot-session-affinity-issues-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Najprawdopodobniej będą zainteresowani odszyfrowania ruchu HTTPS i odszyfrowywania protokołu HTTPS można włączyć, zaznaczając **narzędzia** > **opcje programu Fiddler**i zaznacz pole wyboru " **odszyfrowywania Ruch HTTPS**".

        ![troubleshoot-session-affinity-issues-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Możesz usunąć poprzednich sesjach niepowiązanych przed odtworzenia problemu, klikając **X** (ikonę) > **Usuń wszystkie** jako wykonaj zrzut ekranu: 

        ![troubleshoot-session-affinity-issues-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Po mają odtworzyć problem, Zapisz plik do przeglądu, wybierając **pliku** > **Zapisz** > **wszystkie sesje...** . 

        ![troubleshoot-session-affinity-issues-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Sprawdź i analizować dzienniki sesji, aby ustalić, co to jest problem.

    Przykłady:

- **Przykład Odp.:** Znajdowanie dziennika sesji, żądanie zostanie wysłane przez klienta, która zostanie wprowadzona publiczny adres IP bramy aplikacji, kliknij ten dziennik, aby wyświetlić szczegóły.  Po prawej stronie w dolnym polu jest Application Gateway zwróceniem do klienta. Wybierz kartę "RAW" i określić, czy klient otrzymuje "**Set-Cookie: ARRAffinity =** *ARRAffinityValue*. " Nie ma żadnych plików cookie, koligacja sesji nie jest ustawiona, czy Application Gateway nie jest stosowanie plików cookie do klienta.

   > [!NOTE]
   > Ta wartość ARRAffinity jest identyfikator pliku cookie, określająca Application Gateway dla klienta w celu wysłania do określonego serwera zaplecza.

    ![Rozwiązywanie problemów z sesji koligacji — problemy-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Przykład B:** Dalej dziennika sesji następuje poprzedni jest klient odpowiada powrót do bramy aplikacji, która została ustawiona ARRAAFFINITY. Jeśli identyfikator pliku cookie ARRAffinity jest zgodny, pakiet powinny być przesyłane na ten sam serwer zaplecza, który został wcześniej użyty. Sprawdź dalej kilka wierszy komunikacji http, aby zobaczyć, czy plik cookie ARRAffinity klienta ulegnie zmianie.

    ![troubleshoot-session-affinity-issues-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> W tej samej sesji komunikacji plik cookie ma nie należy zmieniać. Sprawdź polu u góry po prawej stronie, wybierz kartę "Plików cookie", aby zobaczyć, czy klient jest przy użyciu pliku cookie i wysłanie go do bramy aplikacji. W przeciwnym razie w przeglądarce klienta nie jest przechowywanie i za pomocą plików cookie dla konwersacji. Czasami może znajdować się klient.

 

## <a name="next-steps"></a>Kolejne kroki

Jeśli poprzednie kroki nie rozwiązują ten problem, otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).
