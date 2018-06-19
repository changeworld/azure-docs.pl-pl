---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: c10a6367f07bf66f7a0a8968022881b3c616fb74
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "36205607"
---
## <a name="test-your-code"></a>Testowanie kodu

Aby przetestować aplikację w programie Visual Studio, naciśnij klawisz **F5** do uruchomienia projektu. Przeglądarka otworzy http://<span></span>localhost: {port} lokalizacji i zostanie wyświetlony **Zaloguj się przy użyciu Microsoft** przycisku. Wybierz przycisk, aby rozpocząć proces logowania.

Jeśli wszystko jest gotowe do uruchomienia testu, korzystać z konta Microsoft Azure Active Directory (Azure AD) (konta firmowego lub szkolnego) lub osobiste konto Microsoft (<span>na żywo.</span> COM lub <span>programu outlook.</span> com) do logowania.

![Zaloguj się przy użyciu firmy Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Zaloguj się do swojego konta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Wyświetl wyniki aplikacji
Po zalogowaniu użytkownik jest przekierowywany do strony głównej witryny sieci Web. Strona główna jest adres URL HTTPS, określonego w aplikacji informacje rejestracyjne w portalu rejestracji aplikacji firmy Microsoft. Strona główna zawiera komunikat powitalny *"Hello \<użytkownika >,"* łącze, aby się wylogować i link do wyświetlenia oświadczenia użytkownika. Łącze do oświadczenia użytkownika przechodzi do *oświadczeń* kontrolera, który został utworzony wcześniej.

### <a name="browse-to-see-the-users-claims"></a>Przeglądaj, aby wyświetlić oświadczenia użytkownika
Aby wyświetlić oświadczeń użytkownika, wybierz łącze, aby przejść do widoku kontrolera, który jest dostępny tylko dla użytkowników uwierzytelnionych.

#### <a name="view-the-claims-results"></a>Wyświetl wyniki oświadczeń
Po przejściu do widoku kontrolera, powinny być widoczne tabeli zawierającej podstawowe właściwości dla użytkownika:

|Właściwość |Wartość |Opis |
|---|---|---|
|**Nazwa** |Imię i nazwisko użytkownika | Imię i nazwisko użytkownika.
|**Nazwa użytkownika** |Użytkownika<span>@domain.com</span> | Nazwa użytkownika, która jest używana do identyfikacji użytkownika.
|**Temat** |Temat |Ciąg, który unikatowo identyfikuje użytkownika sieci web.|
|**Identyfikator dzierżawy** |Identyfikator GUID | A **guid** który w unikatowy sposób identyfikuje użytkownika usługi Azure AD organizacji.|

Ponadto powinien zostać wyświetlony spis wszystkich oświadczenia, które są w żądaniu uwierzytelnienia. Aby uzyskać więcej informacji, zobacz [lista oświadczeń, które znajdują się w Token identyfikator usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testowanie dostępu do metody, która ma atrybut autoryzacji (opcjonalnie)
Aby przetestować dostępu jako użytkownik anonimowy do kontrolera są chronione przy użyciu `Authorize` atrybutu, wykonaj następujące kroki:
1. Wybierz łącze Wyloguj użytkownika i ukończyć proces wylogowywania.
2. W przeglądarce, wpisz http://<span></span>localhost: {port} / oświadczeń kontrolerze, który jest chroniony za pomocą dostępu do `Authorize` atrybutu.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Oczekiwanych rezultatów po dostęp do chronionych kontrolera
Zostanie wyświetlony monit o uwierzytelniania w celu użycia widoku chronionego kontrolera.

## <a name="advanced-options"></a>Opcje zaawansowane

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Ochrona całej witryny sieci Web
Do ochrony całej witryny sieci Web, w **Global.asax** plików, dodawanie `AuthorizeAttribute` atrybutu `GlobalFilters` Filtruj w `Application_Start` metody:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Ogranicz, który można zalogować się do aplikacji
Domyślnie podczas tworzenia aplikacji utworzonych przez ten przewodnik aplikacji będzie akceptować znak ins osobiste konta (w tym outlook.com, live.com i inne), a także pracować kont służbowych z firmy lub organizacji, który jest zintegrowany z Usługa Azure Active Directory. Jest to zalecana opcja dla aplikacji SaaS.

Aby ograniczyć rejestrowania dostępu użytkowników dla aplikacji, jest dostępnych kilka opcji:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opcja 1: Ograniczyć użytkownikom możliwość wystąpienia usługi Active Directory tylko jednej z organizacji logować się do aplikacji (pojedynczej dzierżawy)

Ta opcja jest typowym scenariuszem dla *aplikacji biznesowych*: Jeśli chcesz zaakceptować logowania tylko z kont należących do określonego wystąpienia usługi Azure Active Directory (w tym *konta gościa*tego wystąpienia) wykonaj następujące czynności:

1. W **web.config** pliku, zmień wartość atrybutu `Tenant` parametru z `Common` nazwy dzierżawy w organizacji, takich jak `contoso.onmicrosoft.com`.
2. W Twojej [klasy początkowej OWIN](#configure-the-authentication-pipeline)ustaw `ValidateIssuer` argument `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Opcja 2: Ograniczanie dostępu do aplikacji dla użytkowników w określonej listy organizacji

Możesz ograniczyć dostęp do logowania do kont użytkowników tylko, które znajdują się w organizacji usługi Azure AD, która znajduje się na liście dozwolonych organizacji:
1. W Twojej [klasy początkowej OWIN](#configure-the-authentication-pipeline)ustaw `ValidateIssuer` argument `true`.
2. Ustaw wartość `ValidIssuers` parametru do listy dozwolonych organizacji.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opcja 3: Umożliwia sprawdzanie poprawności wystawców niestandardowe — metoda
Można zaimplementować niestandardowych metodę do sprawdzania poprawności wystawcy przy użyciu **IssuerValidator** parametru. Aby uzyskać więcej informacji na temat używania tego parametru, przeczytaj o [klasy TokenValidationParameters](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) w witrynie MSDN.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
