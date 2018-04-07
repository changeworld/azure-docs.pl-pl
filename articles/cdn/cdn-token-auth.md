---
title: Zabezpieczanie zasobów Azure CDN z tokenu uwierzytelniania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak bezpieczny dostęp do zasobów usługi Azure CDN za pomocą tokenu uwierzytelniania.
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
ms.openlocfilehash: aaec713a7680aeda8317f5af41b9b99bcbdca4b7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Zabezpieczanie zasobów Azure CDN z tokenu uwierzytelniania

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Przegląd

Token uwierzytelniania jest mechanizm, który pozwala zapobiec obsługująca zasoby do nieautoryzowanego klientów sieci dostarczania zawartości (CDN) platformy Azure. Token uwierzytelniania jest zazwyczaj wykonywane zapobiegające *hotlinking* zawartości, w którym różne witryny sieci Web, takich jak tablica wiadomości, używa zasobów bez uprawnienia. Hotlinking może mieć wpływ na koszty dostarczania zawartości. Przez włączenie token uwierzytelniania w sieci CDN w warstwie, żądania są uwierzytelniani przez serwer graniczny CDN przed CDN oferuje zawartość. 

## <a name="how-it-works"></a>Jak to działa

Token uwierzytelniania sprawdza, czy żądania są generowane przez zaufanych witryn, gdyż żądania zawiera wartość tokenu, że blokad zakodowany informacji na temat obiektu żądającego. Zawartość jest udostępniany element żądający tylko wtedy, gdy dane zakodowane spełnia wymagania; w przeciwnym razie żądania są odrzucane. Wymagania można skonfigurować przy użyciu co najmniej jeden z następujących parametrów:

- Kraju: Akceptować lub odrzucać żądania, które pochodzą z krajów określony przez ich [numer kierunkowy kraju](https://msdn.microsoft.com/library/mt761717.aspx).
- Adres URL: Zezwalaj tylko te żądania, które odpowiadają określonym zasobie lub ścieżki.
- Host: Akceptować lub odrzucać żądania używające określonych hostach w nagłówku żądania.
- Odwołania: Akceptować lub odrzucać żądania od określonego odwołania.
- Adres IP: Zezwalaj tylko żądania, które pochodzą z określonego adresu IP lub podsieci IP.
- Protokół: Akceptować lub odrzucać żądania na podstawie protokołu używane do żądania zawartości.
- Czas wygaśnięcia: przypisywanie okres daty i godziny, aby upewnić się, że łącze pozostaje ważny tylko przez ograniczony czas.

Aby uzyskać więcej informacji, zobacz przykłady szczegółowej konfiguracji dla każdego parametru w [Konfigurowanie uwierzytelniania tokenu](#setting-up-token-authentication).

>[!IMPORTANT] 
> Jeśli token autoryzacji jest włączona dla dowolną ścieżkę na tym koncie, tryb standardowy pamięci podręcznej jest tylko tryb, który może służyć do buforowania ciągu kwerendy. Aby uzyskać więcej informacji, zobacz [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md) (Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań).

## <a name="reference-architecture"></a>Architektura odwołań

Na poniższym diagramie przepływu pracy w tym artykule opisano sposób CDN do pracy z aplikacji sieci web używa tokenu uwierzytelniania.

![Przepływ tokenu uwierzytelniania CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Logikę weryfikacji tokenu na punkt końcowy CDN
    
Poniższy schemat opisano, jak Azure CDN weryfikuje żądanie klienta, gdy token uwierzytelniania jest skonfigurowane na punkt końcowy CDN.

![Logikę weryfikacji tokenu CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Konfigurowanie uwierzytelniania tokenu

1. Z [portalu Azure](https://portal.azure.com), przejdź do swojego profilu CDN, a następnie wybierz **Zarządzaj** można uruchomić portalu dodatkowym.

    ![Przycisk Zarządzaj profil CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Umieść kursor nad **HTTP dużych**, a następnie wybierz pozycję **Token uwierzytelniania** w wysuwane okno. Można następnie ustawić klucza szyfrowania i szyfrowania parametrów w następujący sposób:

    1. Utwórz co najmniej jeden klucz szyfrowania. Klucz szyfrowania jest rozróżniana wielkość liter i może zawierać dowolną kombinację znaków alfanumerycznych. Inne rodzaje znaków, łącznie ze spacjami, nie są dozwolone. Maksymalna długość to 250 znaków. Aby upewnić się, że klucze szyfrowania są losowe, zaleca się, że można je utworzyć za pomocą [narzędzia biblioteki OpenSSL](https://www.openssl.org/). 

       Narzędzie biblioteki OpenSSL ma następującą składnię:

       ```rand -hex <key length>```

       Na przykład:

       ```OpenSSL> rand -hex 32``` 

       Aby uniknąć przestoju, Utwórz podstawowego i zapasowego klucza. Klucz zapasowy zapewnia nieprzerwany dostęp do zawartości po klucz podstawowy jest aktualizowana.
    
    2. Wprowadź unikatowy klucz szyfrowania w **klucza podstawowego** i opcjonalnie wpisz kopii zapasowej klucza w **kopii zapasowej klucza** pole.

    3. Wybrana wersja minimalna szyfrowania dla każdego klucza z jego **minimalna wersja szyfrowania** listy, a następnie wybierz **aktualizacji**:
       - **V2**: wskazuje, że klucz może być używane do generowania tokenów w wersji 2.0 i 3.0. Użyj tej opcji tylko wtedy, gdy są przenoszone z klucza szyfrowania starszych wersji 2.0, do klucza w wersji 3.0.
       - **V3**: (zalecane) wskazuje, że klucz tylko mogą być używane do generowania tokenów w wersji 3.0 lub nowszej.

    ![Klucz tokenu uwierzytelniania CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    4. Narzędzie Szyfruj służy do ustawiania parametrów szyfrowania oraz do generowania tokenu. Za pomocą narzędzia Szyfruj można akceptować lub odrzucać żądania na podstawie czasu wygaśnięcia, kraj, odwołania, protokół i IP klienta (w dowolnej kombinacji). Nie ma żadnego limitu liczby i kombinacja parametrów, które można łączyć, do utworzenia tokenu, ale token całkowita długość jest ograniczona do 512 znaków. 

       ![Narzędzia szyfrowania CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       Wprowadź wartości dla co najmniej jednego z następujących parametrów szyfrowania w **szyfrowania narzędzia** sekcji: 

       > [!div class="mx-tdCol2BreakAll"] 
       > <table>
       > <tr>
       >   <th>Nazwa parametru</th> 
       >   <th>Opis</th>
       > </tr>
       > <tr>
       >    <td><b>ec_expire</b></td>
       >    <td>Przypisuje czas wygaśnięcia tokenu, po którym token jest ważny. Żądań przesłanych po czas wygaśnięcia nie są dozwolone. Sygnatura czasowa systemu Unix, opartym na liczba sekund od standardowego epoka Unix używa tego parametru `1/1/1970 00:00:00 GMT`. (Umożliwia konwersję pomiędzy (czas standardowy) i czas Unix można użyć narzędzia online).> 
       >    Na przykład, jeśli chcesz, aby token do wygaśnięcia na `12/31/2016 12:00:00 GMT`, wprowadź wartość sygnatury czasowej systemu Unix, `1483185600`. 
       > </tr>
       > <tr>
       >    <td><b>ec_url_allow</b></td> 
       >    <td>Umożliwia dostosowanie tokenów do określonego zasobu lub ścieżki. Go ogranicza dostęp do żądania, którego adres URL rozpoczynać się od określonej ścieżki względnej. Adresy URL jest rozróżniana wielkość liter. Dane wejściowe wiele ścieżek, Oddziel poszczególne ścieżki przecinkami; nie należy dodawać spacji. W zależności od wymagań można skonfigurować różne wartości, aby zapewnić różne poziomy dostępu.> 
       >    Na przykład dla adresu URL `http://www.mydomain.com/pictures/city/strasbourg.png`, te żądania są dozwolone w przypadku następujących argumentów: 
       >    <ul>
       >       <li>Wartość wejściowa `/`: wszystkie żądania są dozwolone.</li>
       >       <li>Wartość wejściowa `/pictures`, dozwolone są następujące żądania: <ul>
       >          <li>`http://www.mydomain.com/pictures.png`</li>
       >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
       >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
       >       </ul></li>
       >       <li>Wartość wejściowa `/pictures/`: tylko żądań zawierających `/pictures/` ścieżki są dozwolone. Na przykład `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
       >       <li>Wartość wejściowa `/pictures/city/strasbourg.png`: tylko żądania dla tej określonej ścieżki i zasobów są dozwolone.</li>
       >    </ul>
       > </tr>
       > <tr>
       >    <td><b>ec_country_allow</b></td> 
       >    <td>Zezwala na żądania, które pochodzą z co najmniej jeden określony krajów. Żądania, które pochodzą z innych krajów są odrzucane. Użyj dwuliterowych [normy ISO 3166 numer kierunkowy kraju](https://msdn.microsoft.com/library/mt761717.aspx) dla każdego kraju i oddziel je przecinkami; nie dodają spację. Na przykład, jeśli chcesz zezwolić na dostęp tylko Stany Zjednoczone i Francja, wprowadź `US,FR`.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_country_deny</b></td> 
       >    <td>Odrzucanie żądań, które pochodzą z co najmniej jeden określony krajów. Żądania, które pochodzą z innych krajów są dozwolone. Implementacja jest taka sama jak <b>ec_country_allow</b> parametru. Jeśli kod kraju znajduje się w obu <b>ec_country_allow</b> i <b>ec_country_deny</b> parametrów, <b>ec_country_allow</b> parametr ma pierwszeństwo.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_ref_allow</b></td>
       >    <td>Tylko zezwala na żądania pochodzące z określonej odwołania. Odwołania określa adres URL strony sieci web, który jest połączony z żądanych zasobów. Nie dołączaj protokołu w wartości parametru.>    
       >    Dopuszczalne są następujące typy danych wejściowych:
       >    <ul>
       >       <li>Nazwa hosta lub nazwa hosta i ścieżkę.</li>
       >       <li>Wiele odwołań. Aby dodać wiele odwołań, oddziel każdy odwołania przecinkami; nie należy dodawać spacji. Jeśli wartość odwołania, ale informacje odwołania nie są wysyłane w żądania z powodu konfiguracji przeglądarki, żądanie zostanie odrzucone domyślnie.</li> 
       >       <li>Żądania z informacjami odwołania brakujący lub pusty. Domyślnie <b>ec_ref_allow</b> parametru blokuje tych typów żądań. Aby zezwolić na te żądania, wprowadź tekst, "Brak", lub wartość pusta (przy użyciu przecinek końcowy).</li> 
       >       <li>Domeny podrzędne. Aby umożliwić poddomen, wprowadź znak gwiazdki (\*). Na przykład, aby umożliwić wszystkich poddomen `contoso.com`, wprowadź `*.contoso.com`.</li>
       >    </ul>     
       >    Na przykład, aby zezwolić na dostęp dla żądań z `www.contoso.com`, wszystkimi domenami podrzędnymi w `contoso2.com`, i żądań z pustą lub Brak odwołań, wprowadź `www.contoso.com,*.contoso.com,missing`.</td>
       > </tr>
       > <tr> 
       >    <td><b>ec_ref_deny</b></td>
       >    <td>Odrzucanie żądań z określonego odwołania. Implementacja jest taka sama jak <b>ec_ref_allow</b> parametru. Jeśli odwołania znajduje się w obu <b>ec_ref_allow</b> i <b>ec_ref_deny</b> parametrów, <b>ec_ref_allow</b> parametr ma pierwszeństwo.</td>
       > </tr>
       > <tr> 
       >    <td><b>ec_proto_allow</b></td> 
       >    <td>Zezwala na żądania od wybranego protokołu. Prawidłowe wartości to `http`, `https`, lub `http,https`.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_proto_deny</b></td>
       >    <td>Odrzucanie żądań z wybranego protokołu. Implementacja jest taka sama jak <b>ec_proto_allow</b> parametru. Jeśli protokół znajduje się w obu <b>ec_proto_allow</b> i <b>ec_proto_deny</b> parametrów, <b>ec_proto_allow</b> parametr ma pierwszeństwo.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_clientip</b></td>
       >    <td>Ogranicza dostęp do żądającego określonego adresu IP. Obsługiwane są protokoły IPV4 i IPV6. Można określić adres IP pojedynczego żądania lub adresy IP skojarzone z określonej podsieci. Na przykład `11.22.33.0/22` umożliwia żądań z adresów IP 11.22.32.1 do 11.22.35.254.</td>
       > </tr>
       > </table>

    5. Po zakończeniu wprowadzania wartości parametrów szyfrowania klucza szyfrowania (jeśli zostały utworzone podstawowego i zapasowego klucza) wybierz z **szyfrowania klucza** listy.
    
    6. Wybierz wersję szyfrowania z **wersja szyfrowania** listy: **V2** w wersji 2 lub **V3** w wersji 3 (zalecane). 

    7. Wybierz **Szyfruj** do wygenerowania tokenu.

    Po wygenerowaniu tokenu, jest on wyświetlany w **wygenerowany Token** pole. Korzystanie z tokenu, dołącza go jako ciąg zapytania do końca pliku w ścieżce URL. Na przykład `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
    8. Opcjonalnie można sprawdzić za pomocą narzędzia odszyfrowywania token tak, aby wyświetlić parametry Twojego tokenu. Wklej wartość tokenu w **Token do odszyfrowywania** pole. Wybierz klucz szyfrowania do użycia z **odszyfrować klucza** listy, a następnie wybierz **odszyfrować**.

    Po token jest odszyfrowywany, jego parametrów są wyświetlane w **oryginalnych parametrów** pole.

    9. Opcjonalnie można dostosować typ kod odpowiedzi, który jest zwracany, gdy żądanie zostanie odrzucone. Wybierz **włączone**, następnie wybierz kod odpowiedzi z **kod odpowiedzi** listy. **Nazwa nagłówka** jest automatycznie ustawiana **lokalizacji**. Wybierz **zapisać** do zaimplementowania nowy kod odpowiedzi. Dla niektórych kodów odpowiedzi, to należy także podać adres URL strony błędu w **wartość nagłówka** pole. **403** kod odpowiedzi (zabroniony) jest domyślnie zaznaczona. 

3. W obszarze **HTTP dużych**, wybierz pozycję **aparatu reguł**. Aparat reguł służy do definiowania ścieżki, aby zastosować funkcję, włączyć funkcję uwierzytelniania tokenu i włączyć dodatkowe funkcje tokenu związane z uwierzytelnianiem. Aby uzyskać więcej informacji, zobacz [odwołania aparat reguł](cdn-rules-engine-reference.md).

    1. Wybierz istniejącą regułę lub Utwórz nową regułę do definiowania zasobów lub ścieżka, dla którego chcesz zastosować token uwierzytelniania. 
    2. Aby włączyć uwierzytelnianie tokenu reguł, wybierz **[Token uwierzytelniania](cdn-rules-engine-reference-features.md#token-auth)** z **funkcje** listy, a następnie wybierz **włączone**. Wybierz **aktualizacji** aktualizowania reguły lub **Dodaj** w przypadku tworzenia reguły.
        
    ![Przykład Włącz token uwierzytelniania aparat reguł CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Aparat reguł można również włączyć dodatkowe funkcje tokenu związane z uwierzytelnianiem. Aby włączyć jedną z następujących funkcji, wybierz go z **funkcje** listy, a następnie wybierz **włączone**.
    
    - **[Token uwierzytelniania odmowa kodu](cdn-rules-engine-reference-features.md#token-auth-denial-code)**: Określa typ odpowiedzi, który jest zwracany do użytkownika, gdy żądanie zostanie odrzucone. Ustaw tutaj zastępują kod odpowiedzi w **niestandardowe odmowa obsługi** na stronie uwierzytelniania opartego na tokenie.

    - **[Token uwierzytelniania Ignoruj adresu URL przypadku](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: Określa, czy adres URL używany do sprawdzania poprawności tokenu jest rozróżniana wielkość liter.

    - **[Token uwierzytelniania parametru](cdn-rules-engine-reference-features.md#token-auth-parameter)**: zmienia nazwę parametru ciągu zapytania token uwierzytelniania wyświetlany w żądanym adresie URL. 
        
    ![Przykład ustawienia uwierzytelniania tokena aparat reguł CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Można dostosować token dostępu do kodu źródłowego w [GitHub](https://github.com/VerizonDigital/ectoken).
Dostępne języki:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN funkcji i cenach dostawcy

Aby uzyskać informacje o funkcjach, zobacz [funkcji produktu Azure CDN](cdn-features.md). Aby uzyskać informacje o cenach, zobacz [cennik Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).
