---
title: Zabezpieczanie zasobów usługi Azure CDN przy użyciu tokenu uwierzytelniania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć dostęp do zasobów usługi Azure CDN za pomocą tokenu uwierzytelniania.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: 640c65b1f6995a6c5fb7a3a1fcfeb580aecf5c43
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869405"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Zabezpieczanie zasobów usługi Azure CDN przy użyciu tokenu uwierzytelniania

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie

Uwierzytelnianie przy użyciu tokenów to mechanizm, który pozwala na zapobieganie obsługująca elementy zawartości nieautoryzowanym klientom usługi Azure Content Delivery Network (CDN). Token uwierzytelniania jest zazwyczaj wykonywane, aby zapobiec *hotlinking* zawartości, w którym innej witryny sieci Web, takich jak na tablicy komunikatów używa zasobów bez uprawnienia. Hotlinking może mieć wpływ na koszty dostarczania zawartości. Włączając token uwierzytelniania w usłudze CDN, żądania są uwierzytelniani przez serwer krawędzi sieci CDN, zanim usługa CDN dostarcza zawartość na. 

## <a name="how-it-works"></a>Jak to działa

Uwierzytelnianie przy użyciu tokenów sprawdza, czy żądania są generowane przez zaufanych witryn, wymagając żądania zawiera wartość tokenu, że przechowuje zakodowany informacji na temat osoby żądającej. Zawartość są dostarczane do żądającego tylko wtedy, gdy dane zakodowane spełnia wymagania dotyczące programu w przeciwnym razie żądania są odrzucane. Wymagania można skonfigurować przy użyciu co najmniej jeden z następujących parametrów:

- Kraj: Akceptować lub odrzucać żądania, które pochodzą z krajów/regionów, określonego przez ich [numer kierunkowy kraju](/previous-versions/azure/mt761717(v=azure.100)).
- Adres URL: Zezwalaj na tylko te żądania, które odpowiadają określonym zasobie lub ścieżki.
- Host: Zezwalaj lub Odmów żądań korzystających z określonych hostach w nagłówku żądania.
- Odwołania: Zezwolenie lub zablokowanie żądania z określonego odwołania.
- Adres IP: Zezwalaj na tylko te żądania, które pochodzą z określonego adresu IP lub podsieć IP.
- Protokół: Zezwalaj lub Odmów żądań, w oparciu o protokół używany do żądania zawartości.
- Czas wygaśnięcia: Przypisz okres daty i godziny, aby upewnić się, że łącze jest ważny tylko ograniczony okres.

Aby uzyskać więcej informacji, zobacz przykłady szczegółową konfigurację dla każdego parametru [konfigurowania uwierzytelniania tokenu](#setting-up-token-authentication).

>[!IMPORTANT] 
> Jeśli token autoryzacji jest włączona dla dowolnej ścieżki dla tego konta, tryb pamięci podręcznej standardu jest jedynym trybem, który może służyć do buforowania ciągu kwerendy. Aby uzyskać więcej informacji, zobacz [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md) (Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań).

## <a name="reference-architecture"></a>Architektura referencyjna

Poniższy diagram przepływu pracy opisuje, jak usługa CDN używa uwierzytelniania tokenu do pracy z aplikacją sieci web.

![Przepływ pracy uwierzytelniania tokenu usługi CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Logika walidacji tokenów w punkcie końcowym usługi CDN
    
Następujące schemat blokowy opisano, jak usługi Azure CDN weryfikuje żądanie klienta, gdy token uwierzytelniania jest skonfigurowany w punkcie końcowym usługi CDN.

![Logika walidacji tokenów usługi CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Konfigurowanie uwierzytelniania tokenu

1. Z [witryny Azure portal](https://portal.azure.com), przejdź do swojego profilu CDN, a następnie wybierz **Zarządzaj** do uruchomienia portalu dodatkowego.

    ![Przycisk Zarządzaj w profilu CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Umieść kursor nad **HTTP dużych**, a następnie wybierz **tokenu uwierzytelniania** w menu wysuwane. Możesz następnie skonfigurować klucz szyfrowania i parametry szyfrowania w następujący sposób:

   1. Utwórz co najmniej jeden klucz szyfrowania. Klucz szyfrowania jest uwzględniana wielkość liter i może zawierać dowolną kombinację znaków alfanumerycznych. Inne typy znaków, łącznie ze spacjami, nie są dozwolone. Maksymalna długość to 250 znaków. Aby upewnić się, że klucze szyfrowania są losowe, zaleca się, że można je utworzyć za pomocą [narzędzie OpenSSL](https://www.openssl.org/). 

      Narzędzie OpenSSL ma następującą składnię:

      ```rand -hex <key length>```

      Na przykład:

      ```OpenSSL> rand -hex 32``` 

      Aby uniknąć przestoju, Utwórz podstawowego i zapasowego klucza. Klucza kopii zapasowej zapewnia nieprzerwany dostęp do zawartości po zaktualizowaniu klucz podstawowy.
    
   2. Wprowadź unikatowy klucz szyfrowania w **klucza podstawowego** i opcjonalnie wpisz klucza kopii zapasowej w **klucza kopii zapasowej** pole.

   3. Wybierz wersję minimalną szyfrowania dla każdego klucza z jego **minimalna wersja szyfrowania** , a następnie wybierz **aktualizacji**:
      - **V2**: Wskazuje, że klucz może służyć do generowania tokenów w wersji 2.0 i 3.0. Użyj tej opcji tylko wtedy, gdy cleardb ze starszą wersją 2.0 klucz szyfrowania do klucza wersji 3.0 lub nowszej.
      - **V3**: (Zalecane) Wskazuje, że klucz należy używać tylko do generowania tokenów w wersji 3.0 lub nowszej.

      ![Klucz konfiguracji token uwierzytelniania usługi CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Narzędzie Szyfruj ustawienie szyfrowania parametrów do generowania tokenu. Za pomocą narzędzia Szyfruj możesz można akceptować lub odrzucać żądania na podstawie czasu wygaśnięcia, kraj/region, odwołania, protokół i adres IP klienta (w dowolnej kombinacji). Mimo że nie ma żadnego limitu liczby i kombinacji parametrów, które mogą być połączone do utworzenia tokenu, całkowita długość token jest ograniczona do 512 znaków. 

      ![Sieci CDN szyfrowania narzędzia](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Wprowadź wartości dla co najmniej jednej z następujących parametrów szyfrowania w **szyfrowania narzędzie** sekcji: 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Nazwa parametru</th> 
      >   <th>Opis</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Przypisuje czasu wygaśnięcia tokenu, po którym token jest ważny. Żądania przesłane po odmowie czas wygaśnięcia. Ten parametr używa sygnatura czasowa systemu Unix, która jest oparta na liczba sekund od standardowego epoka systemu Unix `1/1/1970 00:00:00 GMT`. (Można użyć narzędzia online do konwersji między (czas standardowy) i time systemu Unix.)> 
      >    Na przykład, jeśli chcesz, aby token wygaśnie o `12/31/2016 12:00:00 GMT`, wprowadź wartość sygnatura czasowa systemu Unix, `1483185600`. 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Umożliwia dostosowywanie tokenów do określonego zasobu lub ścieżki. Jego ogranicza dostęp do określonej ścieżki względnej zaczynać się którego adres URL żądania. Adresy URL jest rozróżniana wielkość liter. Dane wejściowe wiele ścieżek, Oddziel poszczególne ścieżki z przecinkiem na końcu; nie należy dodawać spacji. W zależności od wymagań można skonfigurować różne wartości, aby zapewnić różne poziomy dostępu.> 
      >    Na przykład adres URL `http://www.mydomain.com/pictures/city/strasbourg.png`, te żądania są dozwolone w przypadku następujących wartości wejściowe: 
      >    <ul>
      >       <li>Wartość wejściowa `/`: Wszystkie żądania są dozwolone.</li>
      >       <li>Wartość wejściowa `/pictures`, dozwolone są następujące żądania: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Wartość wejściowa `/pictures/`: Tylko żądania zawierające `/pictures/` ścieżki są dozwolone. Na przykład `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Wartość wejściowa `/pictures/city/strasbourg.png`: Tylko żądania dla tej konkretnej ścieżki i zasobów są dozwolone.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Umożliwia tylko żądania, które pochodzą z co najmniej jeden określony krajów/regionów. Żądania, które pochodzą od wszystkich innych krajach/regionach są odrzucane. Użyj dwuliterowych [ISO 3166 numer kierunkowy kraju](/previous-versions/azure/mt761717(v=azure.100)) dla każdego kraju i oddziel je przecinkami; nie należy dodawać spacji. Na przykład, jeśli chcesz zezwolić na dostęp w Stanach Zjednoczonych i (Francja), wprowadź `US,FR`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Umożliwia odrzucanie żądań, które pochodzą z co najmniej jeden określony krajów/regionów. Żądania, które pochodzą od wszystkich innych krajach/regionach są dozwolone. Implementacja jest taka sama jak <b>ec_country_allow</b> parametru. Jeśli kod kraju znajduje się w obu <b>ec_country_allow</b> i <b>ec_country_deny</b> parametrów, <b>ec_country_allow</b> parametr ma pierwszeństwo.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Umożliwia tylko żądania z określonego odwołania. Odwołania określa adres URL strony sieci web, która jest połączona z żądanych zasobów. Nie dołączaj protokołu w wartości parametru.> 
      >    Dopuszczalne są następujące typy danych wejściowych:
      >    <ul>
      >       <li>Nazwa hosta lub nazwy hosta i ścieżkę.</li>
      >       <li>Wiele odwołań. Aby dodać wiele odwołań, oddziel każdy odwołania przecinkami; nie należy dodawać spacji. Jeśli określono wartość odwołania, ale informacje odwołania nie są wysyłane żądania z powodu konfiguracji przeglądarki, żądanie zostanie odrzucone domyślnie.</li> 
      >       <li>Żądania z informacjami o odnośnika brakujące lub puste. Domyślnie <b>ec_ref_allow</b> parametru blokuje tego rodzaju żądań. Aby zezwolić na te żądania, wprowadź tekst, "Brak", lub wartość pusta (przy użyciu przecinkiem).</li> 
      >       <li>Subdomains. Aby umożliwić poddomeny, wprowadź znak gwiazdki (\*). Na przykład, aby zezwolić na wszystkie poddomeny `contoso.com`, wprowadź `*.contoso.com`.</li>
      >    </ul> 
      >    Na przykład, aby zezwolić na dostęp dla żądań z `www.contoso.com`, wszystkimi domenami podrzędnymi w `contoso2.com`, i wprowadź żądań z puste lub brakuje miejsca odwołania, `www.contoso.com,*.contoso.com,missing`.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Umożliwia odrzucanie żądań z określonego odwołania. Implementacja jest taka sama jak <b>ec_ref_allow</b> parametru. Jeśli odwołującym jest obecny w obu <b>ec_ref_allow</b> i <b>ec_ref_deny</b> parametrów, <b>ec_ref_allow</b> parametr ma pierwszeństwo.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Umożliwia tylko żądania od wybranego protokołu. Prawidłowe wartości to `http`, `https`, lub `http,https`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Umożliwia odrzucanie żądań z wybranego protokołu. Implementacja jest taka sama jak <b>ec_proto_allow</b> parametru. Jeśli protokół znajduje się w obu <b>ec_proto_allow</b> i <b>ec_proto_deny</b> parametrów, <b>ec_proto_allow</b> parametr ma pierwszeństwo.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Ogranicza dostęp do określonej osoby żądającej adresu IP. Obsługiwane są protokoły IPV4 i IPV6. Można określić adresu IP pojedynczego żądania lub adresy IP skojarzone z określonej podsieci. Na przykład `11.22.33.0/22` zezwala na żądania z adresów IP 11.22.32.1 do 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Po wprowadzeniu wszystkich wartości parametrów szyfrowania, wybierz klawisz aby zaszyfrować (jeśli zostały utworzone podstawowego i zapasowego klucza) z **szyfrowanie klucza** listy.
    
   6. Wybierz wersję szyfrowania z **wersja szyfrowania** listy: **W wersji 2** w wersji 2 lub **V3** w wersji 3 (zalecane). 

   7. Wybierz **Szyfruj** do wygenerowania tokenu.

      Po wygenerowaniu tokenu, jest on wyświetlany w **wygenerowany Token** pole. Korzystanie z tokenu, należy dołączyć go jako ciąg zapytania do końca pliku w ścieżce adresu URL. Na przykład `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Opcjonalnie można sprawdzić za pomocą narzędzia odszyfrowywania tokenu, aby wyświetlić parametry Twojego tokenu. Wklej wartość tokenu w **tokenu do odszyfrowywania** pole. Wybierz klucz szyfrowania do użycia z **odszyfrować klucz** , a następnie wybierz **odszyfrować**.

      Po token zostanie odszyfrowany, jego parametry są wyświetlane w **oryginalnych parametrów** pole.

   9. Opcjonalnie można dostosować typ kod odpowiedzi, który jest zwracany, jeśli żądanie zostanie odrzucone. Wybierz **włączone**, następnie wybierz kod odpowiedzi odebrany od **kod odpowiedzi** listy. **Nazwa nagłówka** jest automatycznie ustawiana na **lokalizacji**. Wybierz **Zapisz** wdrożyć nowy kod odpowiedzi. W przypadku niektórych kodów odpowiedzi, to należy także podać adres URL strony błędu w **wartość nagłówka** pole. **403** kod odpowiedzi (Dostęp zabroniony) jest zaznaczona domyślnie. 

3. W obszarze **HTTP dużych**, wybierz opcję **aparat reguł**. Aparat reguł służy do definiowania ścieżek, aby zastosować tę funkcję, włączyć funkcję uwierzytelnianie przy użyciu tokenów i włączyć dodatkowe funkcje tokenu związane z uwierzytelnianiem. Aby uzyskać więcej informacji, zobacz [dokumentacja aparatu reguł](cdn-rules-engine-reference.md).

   1. Wybierz istniejącą regułę, lub Utwórz nową regułę, aby zdefiniować zasób lub ścieżki, dla której chcesz zastosować uwierzytelnianie przy użyciu tokenów. 
   2. Aby włączyć uwierzytelnianie przy użyciu tokenów związany z regułą, zaznacz **[tokenu uwierzytelniania](cdn-rules-engine-reference-features.md#token-auth)** z **funkcji** , a następnie wybierz **włączone**. Wybierz **aktualizacji** aktualizowania reguły lub **Dodaj** w przypadku tworzenia reguły.
        
      ![Przykład włączyć uwierzytelnianie przy użyciu tokenów aparatu reguł sieci CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Aparat reguł można również włączyć dodatkowe funkcje tokenu związane z uwierzytelnianiem. Aby włączyć dowolne z następujących funkcji, wybierz go z **funkcji** , a następnie wybierz **włączone**.
    
   - **[Token uwierzytelniania typu "odmowa" kodu](cdn-rules-engine-reference-features.md#token-auth-denial-code)**: Określa typ odpowiedzi, która jest zwracana do użytkownika, jeśli żądanie zostanie odrzucone. Regułami ustawionymi tutaj zastąpić kod odpowiedzi w **niestandardowe odmowa obsługi** sekcji na stronie uwierzytelniania opartego na tokenach.

   - **[Token uwierzytelniania ignorowanie wielkości liter adresu URL](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: Określa, czy adres URL używany do sprawdzania poprawności tokenu jest rozróżniana wielkość liter.

   - **[Token uwierzytelniania parametr](cdn-rules-engine-reference-features.md#token-auth-parameter)**: Zmienia nazwę parametru ciągu zapytania tokenu uwierzytelniania wyświetlany w żądany adres URL. 
        
     ![Przykład ustawień uwierzytelniania tokenu aparatu reguł sieci CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Twój token można dostosować, uzyskując dostęp do kodu źródłowego w [GitHub](https://github.com/VerizonDigital/ectoken).
   Dostępne języki:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Usługi Azure CDN funkcje i dostawcy ceny

Aby uzyskać informacje o funkcjach, zobacz [funkcje produktu Azure CDN](cdn-features.md). Aby uzyskać informacje o cenach, zobacz [cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).
