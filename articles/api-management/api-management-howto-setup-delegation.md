---
title: Jak delegować rejestrację użytkownika i subskrypcję produktu
description: Dowiedz się, jak delegować rejestrację użytkowników i subskrypcję produktów do innej firmy w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: c28872e6cffa973f01b3f5a87c423d9dd93a2aa5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259106"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Jak delegować rejestrację użytkownika i subskrypcję produktu

Delegowanie umożliwia korzystanie z istniejącej witryny sieci Web do obsługi dewelopera logowania/rejestracji i subskrypcji produktów, w przeciwieństwie do korzystania z wbudowanej funkcji w portalu dla deweloperów. Umożliwia to witrynie sieci Web posiadanie danych użytkownika i wykonywanie sprawdzania poprawności tych kroków w sposób niestandardowy.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delegowanie logowania dewelopera i rejestracji

Aby delegować dewelopera, zaloguj się i zarejestruj się w istniejącej witrynie sieci Web, musisz utworzyć specjalny punkt końcowy delegowania w witrynie. Musi działać jako punkt wejścia dla każdego takiego żądania zainicjowanego z portalu dewelopera usługi API Management.

Ostateczny przepływ pracy będzie następujący:

1. Deweloper klika łącze logowania lub rejestracji w portalu deweloperskim api management
2. Przeglądarka jest przekierowywane do punktu końcowego delegowania
3. Delegowanie punktu końcowego w zamian przekierowuje lub przedstawia interfejsu użytkownika z prośbą o zalogowanie się lub rejestrację
4. Po sukcesie użytkownik jest przekierowywał z powrotem do strony portalu dla deweloperów zarządzania interfejsami API, od których rozpoczęto

Aby rozpocząć, najpierw skonfigurujmy zarządzanie interfejsami API do kierowania żądań za pośrednictwem punktu końcowego delegowania. W witrynie Azure portal wyszukaj **zabezpieczenia** w zasobie zarządzania interfejsami API, a następnie kliknij element **Delegowanie.** Kliknij to pole wyboru, aby włączyć opcję "Deluj zaloguj się & zarejestruj się".

![Strona delegowania][api-management-delegation-signin-up]

* Zdecyduj, jaki będzie adres URL punktu końcowego delegowania specjalnego i wprowadź go w polu **Adres URL punktu końcowego delegowania.** 
* W polu Klucz uwierzytelniania delegowania wprowadź klucz tajny, który będzie używany do obliczania podpisu dostarczonego do weryfikacji, aby upewnić się, że żądanie rzeczywiście pochodzi z usługi Azure API Management. Możesz kliknąć przycisk **generowania,** aby zarządzanie interfejsami API losowo wygenerować klucz dla Ciebie.

Teraz musisz utworzyć **punkt końcowy delegowania**. Musi wykonać szereg działań:

1. Otrzymuj żądanie w następującym formularzu:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL strony źródłowej}&salt={string}&sig={string}*
   > 
   > 
   
    Parametry kwerendy dla sprawy logowania /rejestracji:
   
   * **operacja**: określa, jaki rodzaj żądania delegowania jest - może to być tylko **SignIn** w tym przypadku
   * **returnUrl**: adres URL strony, na której użytkownik kliknął link logowania lub rejestracji
   * **sól:** specjalny sznurek soli używany do obliczania skrótu zabezpieczającego
   * **sig**: obliczony skrót zabezpieczeń, który ma być używany do porównania z własnym obliczonym hashem
2. Sprawdź, czy żądanie pochodzi z usługi Azure API Management (opcjonalne, ale wysoce zalecane ze względów bezpieczeństwa)
   
   * Obliczanie skrótu HMAC-SHA512 ciągu na podstawie parametrów **kwerendy returnUrl** i **salt** [(przykładowy kod podany poniżej):]
     
     > HMAC(**sól** + '\n' + **returnUrl**)
     > 
     > 
   * Porównaj powyższy obliczony skrót z wartością parametru **zapytania sig.** Jeśli dwa skróty są zgodne, przejdź do następnego kroku, w przeciwnym razie odmów żądania.
3. Sprawdź, czy otrzymujesz żądanie logowania/rejestracji: parametr kwerendy **operacyjnej** zostanie ustawiony na "**SignIn**".
4. Prezentowanie użytkownika z interfejsem użytkownika w celu zalogowania się lub zarejestrowania się
5. Jeśli użytkownik rejestruje się, musisz utworzyć odpowiednie konto dla nich w usłudze API Management. [Utwórz użytkownika za] pomocą interfejsu API REST zarządzania interfejsem API. W takim przypadku upewnij się, że ustawisz identyfikator użytkownika na taką samą wartość jak w magazynie użytkowników lub na identyfikator, który można śledzić.
6. Gdy użytkownik zostanie pomyślnie uwierzytelniony:
   
   * [żądanie tokenu logowania jednokrotnego] za pośrednictwem interfejsu API REST zarządzania interfejsem API
   * dołącz parametr kwerendy returnUrl do adresu URL usługi SSO otrzymanego z powyższego wywołania interfejsu API:
     
     > na przykład,https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * przekierowanie użytkownika do wyżej opracowanego adresu URL

Oprócz operacji **SignIn** można również wykonać zarządzanie kontem, wykonując poprzednie kroki i korzystając z jednej z następujących operacji:

* **Changepassword**
* **Zmień profil**
* **Zamknij Konto**

Należy przekazać następujące parametry kwerendy dla operacji zarządzania kontem.

* **operacja**: określa, jaki typ żądania delegowania jest (ChangePassword, ChangeProfile lub CloseAccount)
* **identyfikator użytkownika**: identyfikator użytkownika konta do zarządzania
* **sól:** specjalny sznurek soli używany do obliczania skrótu zabezpieczającego
* **sig**: obliczony skrót zabezpieczeń, który ma być używany do porównania z własnym obliczonym hashem

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delegowanie subskrypcji produktu
Delegowanie subskrypcji produktu działa podobnie do delegowanie logowania/logowania użytkownika. Ostateczny przepływ pracy będzie następujący:

1. Deweloper wybiera produkt w portalu dewelopera zarządzania interfejsami API i klika przycisk Subskrybuj.
2. Przeglądarka jest przekierowywane do punktu końcowego delegowania.
3. Punkt końcowy delegowania wykonuje wymagane kroki subskrypcji produktu. To do Ciebie, aby zaprojektować kroki. Mogą one obejmować przekierowanie na inną stronę w celu żądania informacji rozliczeniowych, zadawanie dodatkowych pytań lub po prostu przechowywanie informacji i niewymaganie żadnych działań użytkownika.

Aby włączyć tę funkcję, na stronie **Delegowanie** kliknij pozycję **Deluj subskrypcję produktu**.

Następnie upewnij się, że punkt końcowy delegowania wykonuje następujące akcje:

1. Otrzymuj żądanie w następującym formularzu:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    Parametry kwerendy dla sprawy subskrypcji produktu:
   
   * **operacja**: określa, jaki rodzaj żądania delegowania jest. W przypadku żądań subskrypcji produktu prawidłowe opcje to:
     * "Subskrybuj": prośba o subskrypcję danego produktu z podanym identyfikatorem (patrz poniżej)
     * "Rezygnacja z subskrypcji": żądanie anulowania subskrypcji produktu
     * "Renew": żądanie odnowienia subskrypcji (na przykład, które mogą wygasać)
   * **productId**: identyfikator produktu, który użytkownik poprosił o subskrypcję
   * **subscriptionId**: na *anulowanie subskrypcji* i *odnów* - identyfikator subskrypcji produktu
   * **userId**: identyfikator użytkownika, dla
   * **sól:** specjalny sznurek soli używany do obliczania skrótu zabezpieczającego
   * **sig**: obliczony skrót zabezpieczeń, który ma być używany do porównania z własnym obliczonym hashem

2. Sprawdź, czy żądanie pochodzi z usługi Azure API Management (opcjonalne, ale wysoce zalecane ze względów bezpieczeństwa)
   
   * Obliczanie parametrów HMAC-SHA512 ciągu na podstawie parametrów **productId**, **userId**i **salt** query:
     
     > HMAC(**sól** + '\n' + **productId** + '\n' + **identyfikator użytkownika**)
     > 
     > 
   * Porównaj powyższy obliczony skrót z wartością parametru **zapytania sig.** Jeśli dwa skróty są zgodne, przejdź do następnego kroku, w przeciwnym razie odmów żądania.
3. Przetwarzaj subskrypcję produktu na podstawie typu operacji żądanej w **działaniu** — na przykład rozliczeń, dalszych pytań itp.
4. Po pomyślnym zasubskrybowaniu użytkownika do produktu po twojej stronie zasubskrybuj użytkownika do produktu api Management, [wywołując interfejs API REST dla subskrypcji].

## <a name="example-code"></a><a name="delegate-example-code"> </a> Przykładowy kod

Te przykłady kodu pokazują, jak:

* Weź *klucz sprawdzania poprawności delegowania,* który jest ustawiony na ekranie Delegowanie portalu wydawcy
* Utwórz HMAC, który jest następnie używany do sprawdzania poprawności podpisu, potwierdzające ważność przekazanych returnUrl.

Ten sam kod działa dla productId i userId z nieznaczną modyfikacją.

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

**Kod NodeJS do generowania skrótu returnUrl**

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

> [!IMPORTANT]
> Należy [ponownie opublikować portal dewelopera,](api-management-howto-developer-portal-customize.md#publish) aby zmiany delegowania zostały wprowadzone.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat delegowania, zobacz następujący film:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[żądanie tokenu logowania jednokrotnego]: https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/User/GenerateSsoUrl
[tworzenie użytkownika]: https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/user/createorupdate
[wywoływanie interfejsu API REST dla subskrypcji]: https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription/createorupdate
[Next steps]: #next-steps
[przykładowy kod podany poniżej]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
