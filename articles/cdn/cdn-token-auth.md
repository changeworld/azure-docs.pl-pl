---
title: Zabezpieczanie zasobów usługi Azure cdn za pomocą uwierzytelniania tokenów| Dokumenty firmy Microsoft
description: Dowiedz się, jak używać uwierzytelniania tokenów do bezpiecznego dostępu do zasobów usługi Azure CDN.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: fa71f472294b91baebc2a6075ddb2b50123e545d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593389"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Zabezpieczanie zasobów usługi Azure cdn za pomocą uwierzytelniania tokenów

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie

Uwierzytelnianie tokenu to mechanizm, który umożliwia uniemożliwienie sieci dostarczania zawartości platformy Azure (CDN) obsługi zasobów nieautoryzowanym klientom. Uwierzytelnianie tokenu jest zazwyczaj wykonywane w celu zapobiegania *hotlinking* zawartości, w którym inna witryna sieci Web, takich jak tablica wiadomości, używa zasobów bez zgody. Hotlinking może mieć wpływ na koszty dostarczania treści. Włączenie uwierzytelniania tokenów w sieci CDN, żądania są uwierzytelniane przez serwer brzegowy usługi CDN, zanim sieć CDN dostarczy zawartość. 

## <a name="how-it-works"></a>Jak to działa

Uwierzytelnianie tokenu sprawdza, czy żądania są generowane przez zaufaną witrynę, wymagając, aby żądania zawierały wartość tokenu zawierającą zakodowane informacje o żądającym. Zawartość jest obsługiwana żądający tylko wtedy, gdy zakodowane informacje spełniają wymagania; w przeciwnym razie żądania są odrzucane. Wymagania można skonfigurować przy użyciu co najmniej jednego z następujących parametrów:

- Kraj: Zezwalaj lub odrzucaj żądania pochodzące z krajów/regionów określonych w [ich kodzie kraju](/previous-versions/azure/mt761717(v=azure.100)).
- ADRES URL: zezwalaj tylko na żądania zgodne z określonym zasobem lub ścieżką.
- Host: Zezwalaj lub odrzucaj żądania korzystające z określonych hostów w nagłówku żądania.
- Odsyłacz: Zezwalaj lub odmów żądania od określonego strony odsyłacza.
- Adres IP: Zezwalaj tylko na żądania pochodzące z określonego adresu IP lub podsieci IP.
- Protokół: Zezwalaj lub odrzucaj żądania na podstawie protokołu używanego do żądania zawartości.
- Czas wygaśnięcia: Przypisz datę i okres, aby upewnić się, że łącze pozostaje ważne tylko przez ograniczony okres czasu.

Aby uzyskać więcej informacji, zobacz szczegółowe przykłady konfiguracji dla każdego parametru w [konfigurowaniu uwierzytelniania tokenów](#setting-up-token-authentication).

>[!IMPORTANT] 
> Jeśli autoryzacja tokenu jest włączona dla dowolnej ścieżki na tym koncie, tryb standardowej pamięci podręcznej jest jedynym trybem, który może być używany do buforowania ciągów zapytań. Aby uzyskać więcej informacji, zobacz [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md) (Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań).

## <a name="reference-architecture"></a>Architektura referencyjna

Na poniższym diagramie przepływu pracy opisano, jak sieć CDN używa uwierzytelniania tokenu do pracy z aplikacją sieci web.

![Przepływ pracy uwierzytelniania tokenów sieci CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Logika sprawdzania poprawności tokenu w punkcie końcowym usługi CDN
    
Poniższy schemat blokowy opisuje, jak usługa Azure CDN sprawdza poprawność żądania klienta, gdy uwierzytelnianie tokenu jest skonfigurowane w punkcie końcowym usługi CDN.

![Logika sprawdzania poprawności tokenu usługi CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Konfigurowanie uwierzytelniania tokenu

1. Z [witryny Azure portal](https://portal.azure.com)przejdź do profilu usługi CDN, a następnie wybierz pozycję **Zarządzaj,** aby uruchomić dodatkowy portal.

    ![Przycisk Zarządzanie profilem CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Umieść wskaźnik myszy na **dużym obszarze HTTP,** a następnie wybierz pozycję **Auth tokenu** w wysu na stronie wysuwu. Następnie można skonfigurować klucz szyfrowania i parametry szyfrowania w następujący sposób:

   1. Utwórz jeden lub więcej kluczy szyfrowania. W kluczu szyfrowania rozróżniana jest wielkość liter i może zawierać dowolną kombinację znaków alfanumerycznych. Inne typy znaków, w tym spacje, nie są dozwolone. Maksymalna długość wynosi 250 znaków. Aby upewnić się, że klucze szyfrowania są losowe, zaleca się ich utworzenie za pomocą [narzędzia OpenSSL](https://www.openssl.org/). 

      Narzędzie OpenSSL ma następującą składnię:

      ```rand -hex <key length>```

      Przykład:

      ```OpenSSL> rand -hex 32``` 

      Aby uniknąć przestojów, należy utworzyć klucz podstawowy i klucz kopii zapasowej. Klucz kopii zapasowej zapewnia nieprzerwany dostęp do zawartości podczas aktualizacji klucza podstawowego.
    
   2. Wprowadź unikatowy klucz szyfrowania w polu **Klucz podstawowy** i opcjonalnie wprowadź klucz kopii zapasowej w polu Klucz **kopii zapasowej.**

   3. Wybierz minimalną wersję szyfrowania dla każdego klucza z listy **Minimalna wersja szyfrowania,** a następnie wybierz pozycję **Aktualizuj:**
      - **Wersja 2:** Wskazuje, że klucz może służyć do generowania tokenów w wersji 2.0 i 3.0. Tej opcji należy używać tylko w przypadku przejścia ze starszego klucza szyfrowania w wersji 2.0 do klucza w wersji 3.0.
      - **V3**: (Zalecane) Wskazuje, że klucz może być używany tylko do generowania tokenów w wersji 3.0.

      ![Klucz konfiguracji tokenu CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Użyj narzędzia szyfrowania, aby skonfigurować parametry szyfrowania i wygenerować token. Za pomocą narzędzia do szyfrowania można zezwalać lub odrzucać żądania na podstawie czasu wygaśnięcia, kraju/regionu, strony odsyłacza, protokołu i adresu IP klienta (w dowolnej kombinacji). Chociaż nie ma limitu liczby i kombinacji parametrów, które mogą być łączone w celu utworzenia tokenu, całkowita długość tokenu jest ograniczona do 512 znaków. 

      ![Narzędzie szyfrowania sieci CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Wprowadź wartości co najmniej jednego z następujących parametrów szyfrowania w sekcji **Narzędzie szyfrowania:** 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Nazwa parametru</th> 
      >   <th>Opis</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Przypisuje czas wygaśnięcia do tokenu, po którym token wygasa. Żądania przesłane po upływie czasu są odrzucane. Ten parametr używa sygnatury czasowej Uniksa, która jest oparta na `1/1/1970 00:00:00 GMT`liczbie sekund od standardowej epoce Uniksa . (Za pomocą narzędzi online można konwertować czas standardowy i czas Uniksa).> 
      >    Na przykład, jeśli chcesz, aby `12/31/2016 12:00:00 GMT`token wygaśnie w `1483185600`, wprowadź wartość sygnatury czasowej Uniksa, . 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Umożliwia dostosowanie tokenów do określonego zasobu lub ścieżki. Ogranicza dostęp do żądań, których adres URL zaczyna się od określonej ścieżki względnej. W adresach URL rozróżniana jest wielkość liter. Wprowadź wiele ścieżek, oddzielając każdą ścieżkę przecinkiem; nie należy dodawać spacji. W zależności od wymagań można skonfigurować różne wartości, aby zapewnić inny poziom dostępu.> 
      >    Na przykład dla `http://www.mydomain.com/pictures/city/strasbourg.png`adresu URL te żądania są dozwolone dla następujących wartości wejściowych: 
      >    <ul>
      >       <li>Wartość `/`wejściowa: Wszystkie żądania są dozwolone.</li>
      >       <li>Wartość `/pictures`wejściowa, dozwolone są następujące żądania: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Wartość `/pictures/`wejściowa: Dozwolone `/pictures/` są tylko żądania zawierające ścieżkę. Na przykład `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Wartość `/pictures/city/strasbourg.png`wejściowa: Dozwolone są tylko żądania dla tej konkretnej ścieżki i zasobu.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Zezwala tylko na żądania pochodzące z jednego lub więcej określonych krajów/regionów. Żądania pochodzące ze wszystkich innych krajów/regionów są odrzucane. Użyj dwuliterowego [kodu kraju ISO 3166](/previous-versions/azure/mt761717(v=azure.100)) dla każdego kraju i oddziel każdy z nich przecinkiem; nie należy dodawać spacji. Na przykład, jeśli chcesz zezwolić na dostęp tylko `US,FR`ze Stanów Zjednoczonych i Francji, wprowadź .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Odrzuca żądania, które pochodzą z jednego lub więcej określonych krajów/regionów. Żądania pochodzące ze wszystkich innych krajów/regionów są dozwolone. Implementacja jest taka sama jak <b>parametr ec_country_allow.</b> Jeśli kod kraju jest obecny zarówno w parametrach <b>ec_country_allow,</b> jak i <b>ec_country_deny,</b> pierwszeństwo ma parametr <b>ec_country_allow.</b></td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Zezwala tylko na żądania od określonego strony odsyłacza. Strona odsyłacza identyfikuje adres URL strony sieci web połączonej z żądanym zasobem. Nie należy dołączać protokołu do wartości parametru.> 
      >    Dozwolone są następujące typy danych wejściowych:
      >    <ul>
      >       <li>Nazwa hosta lub nazwa hosta i ścieżka.</li>
      >       <li>Wiele osób odsyłaczy. Aby dodać wiele odsyłaczy, należy oddzielić każdy odsyłający przecinkiem; nie należy dodawać spacji. Jeśli określisz wartość odsyłacza, ale informacje odsyłający nie są wysyłane w żądaniu ze względu na konfigurację przeglądarki, żądanie jest domyślnie odrzucane.</li> 
      >       <li>Żądania z brakującymi lub pustymi informacjami odsyłającymi. Domyślnie parametr <b>ec_ref_allow</b> blokuje tego typu żądania. Aby zezwolić na te żądania, wprowadź tekst "brak" lub wprowadź pustą wartość (używając przecinka końcowego).</li> 
      >       <li>Poddomen. Aby zezwolić na poddomeny,\*wprowadź gwiazdkę ( ). Na przykład, aby zezwolić na `contoso.com`wszystkie `*.contoso.com`poddomeny , wprowadź .</li>
      >    </ul> 
      >    Na przykład, aby zezwolić `www.contoso.com`na dostęp do `contoso2.com`żądań z , wszystkie poddomeny w obszarze , i żądania z pustymi lub brakującymi punktami odsyłającymi, wprowadź `www.contoso.com,*.contoso.com,missing`.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Odrzuca żądania od określonego strony odsyłacza. Implementacja jest taka sama jak <b>parametr ec_ref_allow.</b> Jeśli polecenie jest obecny zarówno w <b>ec_ref_allow</b> i <b>ec_ref_deny</b> parametrów, <b>parametr ec_ref_allow</b> ma pierwszeństwo.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Zezwala tylko na żądania z określonego protokołu. Prawidłowe `http`wartości `https`to `http,https`, lub .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Odrzuca żądania z określonego protokołu. Implementacja jest taka sama jak <b>parametr ec_proto_allow.</b> Jeśli protokół jest obecny zarówno w <b>parametrach ec_proto_allow,</b> jak i <b>ec_proto_deny,</b> pierwszeństwo ma parametr <b>ec_proto_allow.</b></td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Ogranicza dostęp do adresu IP określonego żądania. Obsługiwane są zarówno IPV4, jak i IPV6. Można określić adres IP pojedynczego żądania lub adresy IP skojarzone z określoną podsiecią. Na przykład `11.22.33.0/22` zezwala na żądania z adresów IP 11.22.32.1 do 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Po zakończeniu wprowadzania wartości parametrów szyfrowania wybierz klucz do szyfrowania (jeśli utworzono klucz podstawowy i klucz kopii zapasowej) z listy **Klucz do szyfrowania.**
    
   6. Wybierz wersję szyfrowania z listy **Wersja szyfrowania:** **V2** dla wersji 2 lub **V3** dla wersji 3 (zalecane). 

   7. Wybierz **przycisk Szyfruj,** aby wygenerować token.

      Po wygenerowaniu tokenu jest on wyświetlany w polu **Wygenerowany token.** Aby użyć tokenu, dołącz go jako ciąg zapytania na końcu pliku w ścieżce adresu URL. Na przykład `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Opcjonalnie należy przetestować token za pomocą narzędzia odszyfrowywania, dzięki czemu można wyświetlić parametry tokenu. Wklej wartość tokenu w polu **Token do odszyfrowania.** Wybierz klucz szyfrowania używany z listy **Klucz do odszyfrowania,** a następnie wybierz pozycję **Odszyfruj**.

      Po odszyfrowaniu tokenu jego parametry są wyświetlane w polu **Parametry oryginalne.**

   9. Opcjonalnie dostosuj typ kodu odpowiedzi, który jest zwracany po odrzuceniu żądania. Wybierz **pozycję Włączone**, a następnie wybierz kod odpowiedzi z listy Kod **odpowiedzi.** **Nazwa nagłówka** jest automatycznie ustawiana na **Lokalizacja**. Wybierz **pozycję Zapisz,** aby zaimplementować nowy kod odpowiedzi. W przypadku niektórych kodów odpowiedzi należy również wprowadzić adres URL strony błędu w polu **Wartość nagłówka.** Domyślnie wybierany jest kod odpowiedzi **403** (Zabronione). 

3. W obszarze **Duży HTTP**wybierz pozycję **Aparat reguł**. Aparat reguł służy do definiowania ścieżek, aby zastosować funkcję, włączyć funkcję uwierzytelniania tokenu i włączyć dodatkowe możliwości związane z uwierzytelnianiem tokenu. Aby uzyskać więcej informacji, zobacz [Odwołanie do aparatu reguł](cdn-rules-engine-reference.md).

   1. Wybierz istniejącą regułę lub utwórz nową regułę, aby zdefiniować zasób lub ścieżkę, do której chcesz zastosować uwierzytelnianie tokenu. 
   2. Aby włączyć uwierzytelnianie tokenu w regule, wybierz pozycję **[Auth tokenu](cdn-verizon-premium-rules-engine-reference-features.md#token-auth)** z listy **Funkcje,** a następnie wybierz pozycję **Włączone**. Wybierz **opcję Aktualizuj,** jeśli aktualizujesz regułę, lub **Dodaj** regułę.
        
      ![Zasady cdn uwierzytelniania tokenu aparatu włączyć przykład](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. W engine reguł można również włączyć dodatkowe funkcje związane z uwierzytelnianiem tokenu. Aby włączyć dowolną z następujących funkcji, wybierz ją z listy **Funkcje,** a następnie wybierz pozycję **Włączone**.
    
   - **[Kod odmowy auth token:](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-denial-code)** Określa typ odpowiedzi, która jest zwracana do użytkownika, gdy żądanie zostanie odrzucone. Reguły ustawione w tym miejscu zastępują kod odpowiedzi ustawiony w sekcji **Niestandardowa obsługa odmowy** na stronie uwierzytelniania opartego na tokenie.

   - **[Wielkość adresu URL auth ignoruje token:](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-ignore-url-case)** Określa, czy adres URL używany do sprawdzania poprawności tokenu jest rozróżniany.

   - **[Parametr Auth Token:](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-parameter)** Zmienia nazwę parametru ciągu zapytania auth tokenu, który pojawia się w żądanym adresie URL. 
        
     ![Przykład ustawień uwierzytelniania tokenu aparatu CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Token można dostosować, uzyskując dostęp do kodu źródłowego w [usłudze GitHub.](https://github.com/VerizonDigital/ectoken)
   Dostępne języki obejmują:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Funkcje usługi Azure CDN i ceny dostawców

Aby uzyskać informacje o funkcjach, zobacz [Funkcje produktu usługi Azure CDN](cdn-features.md). Aby uzyskać informacje o cenach, zobacz [Cennik sieci dostarczania zawartości](https://azure.microsoft.com/pricing/details/cdn/).
