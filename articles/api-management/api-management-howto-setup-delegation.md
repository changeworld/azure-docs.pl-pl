---
title: Jak delegować subskrypcji użytkownika rejestracji i produktu
description: Dowiedz się, jak delegować rejestracji i produktu subskrypcji użytkownika do innej firmy w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 02c3a3d996fa253cf56e551a37e098639bf73533
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Jak delegować subskrypcji użytkownika rejestracji i produktu
Delegowanie umożliwia przy użyciu istniejącej witryny sieci Web do obsługi projektanta logowania — w/tworzenia konta i subskrypcji do produktów, a nie za pomocą wbudowanej funkcji w portalu dla deweloperów. Dzięki temu witryny sieci Web do własnych danych użytkownika i przeprowadzania weryfikacji tych kroków w niestandardowy sposób.

## <a name="delegate-signin-up"> </a>Delegowanie developer znak w i rejestrowania
Aby delegować developer logowania i rejestrowania do istniejącej witryny sieci Web, należy utworzyć delegowanie specjalne punkt końcowy w swojej witrynie, który działa jako punkt wejścia dla takiego żądania inicjowanych z poziomu portalu dla deweloperów zarządzanie interfejsami API.

Końcowe przepływ pracy będzie wyglądać następująco:

1. Deweloper kliknięć łącze logowania lub tworzenia konta w portalu dla deweloperów zarządzanie interfejsami API
2. Przeglądarka jest przekierowywany do punktu końcowego delegowania
3. Punkt końcowy delegowania w zamian przekierowuje do lub przedstawia interfejsu użytkownika prośba użytkownika do podpisania w lub zapisywania
4. W przypadku powodzenia zostanie przekierowany użytkownik w wróć do strony portalu deweloperów zarządzanie interfejsami API uruchomienia z

Aby rozpocząć, umożliwia pierwszy zarządzanie interfejsami API konfiguracji do rozsyłania żądań za pośrednictwem punktu końcowego delegowania. W portalu wydawcy interfejsu API zarządzania kliknij polecenie **zabezpieczeń** , a następnie kliknij przycisk **delegowania** kartę. Kliknij pole wyboru, aby włączyć "Delegate logowania & rejestracji".

![Strony delegowania][api-management-delegation-signin-up]

* Zdecyduj, co adres URL punktu końcowego specjalne delegowania zostanie i wprowadź go w **adres URL punktu końcowego delegowania** pola. 
* W polu klucza uwierzytelniania delegowania wprowadź klucz tajny, który będzie używany do obliczenia podpisu dostarczone do weryfikacji upewnić się, czy żądanie jest rzeczywiście pochodzi z usługi Azure API Management. Możesz kliknąć **Generowanie** przycisk, aby mieć interfejsu API zarządzania losowego generowania klucza.

Teraz musisz utworzyć **punktu końcowego delegowania**. Musi przeprowadzić szereg akcji:

1. Otrzyma żądanie w następującym formacie:
   
   > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL Źródło strony} & ziarna = {ciąg} & sig = {ciąg}*
   > 
   > 
   
    Parametry dla logowania w zapytania / Zarejestruj się w przypadku:
   
   * **Operacja**: Określa jaki typ żądania delegowania jest — może być tylko **SignIn** w takim przypadku
   * **returnUrl**: adres URL strony, gdy użytkownik kliknął Link logowania lub zapisywania
   * **ziarna**: specjalne ciąg zaburzający używane do obliczania skrótu zabezpieczeń
   * **SIG**: skrót obliczona zabezpieczeń do użycia w porównaniu do własnych obliczoną wartość mieszania
2. Sprawdź, czy żądanie jest pochodzi z usługi Azure API Management (opcjonalne, lecz zdecydowanie zalecane dla zabezpieczeń)
   
   * Obliczeniowe skrótu HMAC SHA512 ciągu na podstawie **returnUrl** i **soli** parametry zapytania ([przykładowy kod podany poniżej]):
     
     > Metoda HMAC (**soli** + "\n" + **returnUrl**)
     > 
     > 
   * Porównanie obliczonego powyżej skrót z wartością **sig** parametr zapytania. Jeśli dwa skróty są zgodne, przejdź do następnego kroku, odrzuciła żądanie, w przeciwnym razie wartość.
3. Sprawdź, czy otrzymują żądanie logowania w/logowania — w górę: **operacji** parametru zapytania zostanie ustawiony na "**rejestrowanie**".
4. Przedstawia znak w lub tworzenia konta użytkownika przy użyciu interfejsu użytkownika
5. Jeśli użytkownik jest zarejestrowanie się należy utworzyć odpowiednie konta dla nich w usłudze API Management. [Utwórz użytkownika] przy użyciu interfejsu API REST zarządzania interfejsu API. Po tej czynności upewnij się, Ustaw identyfikator użytkownika, takie same, który znajduje się w magazynie użytkownika lub identyfikator, który użytkownik może przechowywać ścieżkę.
6. Po pomyślnym uwierzytelnieniu użytkownika:
   
   * [żądania tokenu (rejestracji jednokrotnej SSO) single-sign-on] za pośrednictwem interfejsu API REST zarządzania interfejsu API
   * Dołącz returnUrl parametru zapytania do adresu URL logowania jednokrotnego otrzymane od wywołania interfejsu API powyżej:
     
     > Na przykład https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * przekierowuje użytkownika do utworzonych powyższy adres URL

Oprócz **SignIn** operacji, można również wykonywać Zarządzanie kontami przez wykonanie poprzednich kroków i przy użyciu jednej z następujących czynności:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Należy podać następujące parametry zapytania dla operacji zarządzania kontem.

* **Operacja**: Określa typ żądania delegowania jest (Element ChangePassword, ChangeProfile lub CloseAccount)
* **Identyfikator userId**: identyfikator użytkownika konta do zarządzania
* **ziarna**: specjalne ciąg zaburzający używane do obliczania skrótu zabezpieczeń
* **SIG**: skrót obliczona zabezpieczeń do użycia w porównaniu do własnych obliczoną wartość mieszania

## <a name="delegate-product-subscription"> </a>Delegowanie subskrypcji produktu
Delegowanie subskrypcji produktu działa podobnie do delegowania użytkownika logowania/w górę. Końcowe przepływu pracy, należy w następujący sposób:

1. Deweloper wybiera produktu w portalu dla deweloperów usługi API Management i kliknie przycisk Subskrybuj
2. Przeglądarka jest przekierowywany do punktu końcowego delegowania
3. Punkt końcowy delegowania wykonuje kroki subskrypcji produktu wymagane — to zależy od użytkownika i może spowodować przekierowanie do innej strony do żądania informacji dotyczących rozliczeń, prosząc dodatkowych pytań lub po prostu przechowywanie informacji i nie wymaga żadnych działań użytkownika

Aby włączyć funkcję, na **delegowania** kliknij **delegować subskrypcji produktu**.

Następnie upewnij się, że punkt końcowy delegowania wykonuje następujące czynności:

1. Otrzyma żądanie w następującym formacie:
   
   > *http://www.yourwebsite.com/apimdelegation?operation={operation}&productId={product Aby subskrybować} & userId = {użytkownika zgłaszającego żądanie} & ziarna = {ciąg} & sig = {ciąg}*
   > 
   > 
   
    Parametry zapytania w przypadku subskrypcji produktu:
   
   * **Operacja**: Określa, jakiego typu żądanie delegowania jest. Dla subskrypcji produktu żądania prawidłowe opcje to:
     * "Subskrypcji": żądanie do subskrybowania użytkownika do danego produktu z podany identyfikator (patrz poniżej)
     * "Anuluj subskrypcję": żądanie anulowania subskrypcji użytkownika z produktu
     * "Odnowić": żądanie odnowienia subskrypcji (na przykład, który może być wygasa)
   * **productId**: identyfikator produktu użytkownik zażądał do subskrybowania
   * **Identyfikator userId**: identyfikator użytkownika, dla którego żądań
   * **ziarna**: specjalne ciąg zaburzający używane do obliczania skrótu zabezpieczeń
   * **SIG**: skrót obliczona zabezpieczeń do użycia w porównaniu do własnych obliczoną wartość mieszania
2. Sprawdź, czy żądanie jest pochodzi z usługi Azure API Management (opcjonalne, lecz zdecydowanie zalecane dla zabezpieczeń)
   
   * Obliczeniowe SHA512 HMAC ciągu na podstawie **productId**, ** userId, i **soli** parametry zapytania:
     
     > Metoda HMAC (**soli** + "\n" + **productId** + "\n" + **userId**)
     > 
     > 
   * Porównanie obliczonego powyżej skrót z wartością **sig** parametr zapytania. Jeśli dwa skróty są zgodne, przejdź do następnego kroku, odrzuciła żądanie, w przeciwnym razie wartość.
3. Przetwarzania żadnych subskrypcji produktów na podstawie typu żądanej w operacji **operacji** — na przykład rozliczeń dalsze pytania itp.
4. W subskrypcji pomyślnie użytkownikowi produktu na Twojej stronie, subskrypcja użytkownikowi produktu interfejsu API zarządzania przez [wywołanie interfejsu API REST dla subskrypcji produktu].

## <a name="delegate-example-code"> </a> Przykładowy kod
Te przykłady pokazują, jak wykonać *klucz sprawdzania poprawności delegowania*, znajduje w ekranie delegowania portalu wydawcy, aby utworzyć HMAC, który jest następnie używany do sprawdzania poprawności podpisu, co dowodzi ważności przekazany returnUrl. Ten sam kod działa w przypadku productId i userId nieznaczne modyfikacji.

**Kod C# do generowania skrótu returnUrl**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**Umożliwia generowanie skrótów returnUrl kodu NodeJS**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na delegowania zobacz poniższe wideo:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[żądania tokenu (rejestracji jednokrotnej SSO) single-sign-on]: https://docs.microsoft.com/rest/api/apimanagement/User/GenerateSsoUrl
[Utwórz użytkownika]: https://docs.microsoft.com/rest/api/apimanagement/user/createorupdate
[wywołanie interfejsu API REST dla subskrypcji produktu]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[przykładowy kod podany poniżej]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
