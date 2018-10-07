---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 3a141bcde75872f2384aedf982ffef5cba9666a3
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48842852"
---
## <a name="test-your-code"></a>Testowanie kodu

Aby przetestować aplikację w programie Visual Studio, naciśnij klawisz **F5** Aby uruchomić projekt. Zostanie otwarta przeglądarka http://<span></span>localhost: {port} lokalizacji i **Zaloguj się przy użyciu Microsoft** przycisku. Wybierz przycisk aby rozpocząć proces logowania.

Gdy wszystko będzie gotowe uruchomić test, użyj konta Microsoft Azure Active Directory (Azure AD) (konto służbowe) lub osobistego konta Microsoft (<span>na żywo.</span> COM lub <span>programu outlook.</span> com) do logowania.

![Zaloguj się przy użyciu konta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Zaloguj się do swojego konta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Wyświetl wyniki aplikacji
Po zalogowaniu użytkownik jest przekierowany do strony głównej witryny sieci Web. Strona główna jest adres URL HTTPS, który jest określony w aplikacji informacje rejestracyjne w portalu rejestracji aplikacji firmy Microsoft. Strona główna zawiera komunikat powitalny *"Hello \<użytkownika >,"* łącze, aby się wylogować i link do wyświetlenia oświadczenia użytkownika. Link do oświadczenia użytkownika przejdzie do *oświadczeń* kontrolera, który został utworzony wcześniej.

### <a name="browse-to-see-the-users-claims"></a>Wyszukaj, aby zobaczyć oświadczenia użytkownika
Aby wyświetlić oświadczeń użytkownika, wybierz link umożliwiający przejście do widoku kontrolera, który jest dostępny tylko dla uwierzytelnionych użytkowników.

#### <a name="view-the-claims-results"></a>Wyświetlanie wyników oświadczeń
Po przejściu do widoku kontrolera, powinny zostać wyświetlone tabeli, która zawiera podstawowe właściwości dla użytkownika:

|Właściwość |Wartość |Opis |
|---|---|---|
|**Nazwa** |Imię i nazwisko użytkownika | Użytkownik na imię i nazwisko.
|**Nazwa użytkownika** |Użytkownik<span>@domain.com</span> | Nazwa użytkownika, który służy do identyfikowania użytkownika.
|**Temat** |Podmiot |Ciąg, który unikatowo identyfikuje użytkownika w sieci Web.|
|**Identyfikator dzierżawy** |Identyfikator GUID | A **guid** który w unikatowy sposób identyfikuje użytkownika usługi Azure AD organizacja.|

Ponadto powinien zostać wyświetlony spis wszystkich oświadczeń, które znajdują się w żądaniu uwierzytelnienia. Aby uzyskać więcej informacji, zobacz [lista oświadczeń, które znajdują się w usłudze Azure AD identyfikator tokenu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testowanie dostępu do metody, która ma atrybut autoryzacji (opcjonalnie)
Aby przetestować dostęp jako użytkownik anonimowy do kontrolera jest chroniony za pomocą `Authorize` atrybutu, wykonaj następujące kroki:
1. Wybierz łącze do wylogowania użytkownika i ukończyć proces wylogowywania.
2. W przeglądarce, należy wpisać http://<span></span>localhost: {port} / oświadczeń dostęp do Twojego kontrolera, który jest chroniony za pomocą `Authorize` atrybutu.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Oczekiwane wyniki po dostęp do chronionych kontrolera
Zostanie wyświetlony monit uwierzytelnienia w celu użycia widoku chronionym kontrolera.

## <a name="advanced-options"></a>Opcje zaawansowane

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Ochrona całej witryny sieci Web
Do ochrony całej witryny sieci Web, w **Global.asax** Dodaj `AuthorizeAttribute` atrybutu `GlobalFilters` filtr `Application_Start` metody:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Ogranicz, kto może się zalogować do aplikacji
Domyślnie podczas tworzenia aplikacji utworzone przez tego przewodnika, aplikacja będzie akceptować logowania konta osobiste (w tym outlook.com, live.com i inne) oraz pracy kont służbowych z firmy lub organizacji, która została zintegrowana z Usługa Azure Active Directory. Jest to zalecana opcja dla aplikacji SaaS.

Do ograniczenia rejestrowania dostępu użytkowników dla aplikacji, dostępnych jest kilka opcji:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opcja 1: Ograniczyć użytkownikom możliwość wystąpienia usługi Active Directory tylko jednej z organizacji, aby zalogować się do aplikacji (pojedyncza dzierżawa)

Ta opcja jest typowym scenariuszem dla *aplikacje LOB*: Jeśli chcesz, aby Twoja aplikacja będzie akceptować logowania tylko z kont, które należą do konkretnego wystąpienia usługi Azure Active Directory (w tym *konta gościa*danego wystąpienia) wykonaj następujące czynności:

1. W **web.config** plików, zmień wartość `Tenant` parametr `Common` nazwę dzierżawy organizacji, takich jak `contoso.onmicrosoft.com`.
2. W swojej [Klasa początkowa OWIN](#configure-the-authentication-pipeline)ustaw `ValidateIssuer` argument `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Opcja 2: Ograniczanie dostępu do aplikacji dla użytkowników na określonej liście organizacji

Można ograniczyć dostęp do logowania do kont użytkowników tylko, które znajdują się w organizacji usługi Azure AD, w której znajduje się na liście dozwolonych organizacji:
1. W swojej [Klasa początkowa OWIN](#configure-the-authentication-pipeline)ustaw `ValidateIssuer` argument `true`.
2. Ustaw wartość `ValidIssuers` parametru do listy dozwolonych organizacji.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opcja 3: Użyj niestandardowej metody do sprawdzania poprawności wystawcy
Możesz zaimplementować niestandardowy metodę do sprawdzania poprawności wystawcy przy użyciu **IssuerValidator** parametru. Aby uzyskać więcej informacji o tym, jak używać tego parametru, przeczytaj temat [klasy TokenValidationParameters](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) w witrynie MSDN.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
