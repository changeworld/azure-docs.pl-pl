---
title: Dowiedz się, jak zapewnić opcjonalnych oświadczeń do aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Przewodnik Dodawanie niestandardowych lub dodatkowe oświadczenia na tokeny SAML 2.0 i tokenów sieci Web JSON (JWT) wystawione przez usługi Azure Active Directory.
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: ffd774477881be6b7f46dd38bbc88c8d019223aa
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317208"
---
# <a name="optional-claims-in-azure-ad-preview"></a>Opcjonalne oświadczenia w usłudze Azure AD (wersja zapoznawcza)

Ta funkcja jest używana przez deweloperów aplikacji, aby określić, które oświadczenia, że w tokeny wysłane do swojej aplikacji. Można opcjonalnie oświadczenia do:
-   Wybierz dodatkowe oświadczenia do uwzględnienia w tokenach aplikacji.
-   Zmień zachowanie niektórych oświadczenia, które zwraca usługi Azure AD w tokenach.
-   Dodaj i oświadczenia niestandardowe dla aplikacji dostępu. 

> [!Note]
> Ta funkcja jest obecnie w wersji zapoznawczej. Przygotuj się do przywrócenia lub Usuń wszystkie zmiany. Funkcja jest dostępna w żadnych subskrypcji usługi Azure AD w publicznej wersji zapoznawczej. Gdy funkcja stanie się ogólnie dostępna, niektórych aspektów funkcji mogą jednak wymagać subskrypcję usługi Azure AD premium.

Listę standardowych oświadczeń i sposób ich użycia w tokenach, zobacz [podstawy tokenów wystawionych przez usługę Azure AD](active-directory-token-and-claims.md). 

Jednym z celów [punktu końcowego v2.0 usługi Azure AD](active-directory-appmodel-v2-overview.md) jest mniejszy rozmiar tokenu, aby zapewnić optymalną wydajność przez klientów.  W związku z tym kilka oświadczeń dawniej zawarty w dostępu i tokeny Identyfikatora nie są już dostępne w wersji 2.0 tokeny i musi zostać wyświetlony monit o podanie szczegółowe informacje dotyczące poszczególnych aplikacji, co.  

**Tabela 1: zastosowania**

| Typ konta | Punkt końcowy w wersji 1.0                      | Punktu końcowego v2.0  |
|--------------|------------------------------------|----------------|
| MSA          | N/d - użyty RPS biletów | Obsługa pochodzących |
| AAD          | Obsługiwane                          | Obsługiwane      |

## <a name="standard-optional-claims-set"></a>Zestaw standardowych opcjonalnych oświadczeń
Zestaw oświadczeń opcjonalne domyślnie dostępne do użycia przez aplikacje są wymienione poniżej.  Aby dodać opcjonalne oświadczenia niestandardowe dla aplikacji, zobacz [rozszerzenia katalogów](active-directory-optional-claims.md#Configuring-custom-claims-via-directory-extensions), poniżej. 

> [!Note]
>Większość tych oświadczeń można dołączyć do tokenów Jwt, ale nie tokeny SAML, chyba że zaznaczono inaczej w kolumnie Typ tokenu.  Ponadto podczas opcjonalnych oświadczeń są obsługiwane tylko dla użytkowników usługi AAD obecnie, zarządzanych kont usług pomocy technicznej jest dodawany.  Jeśli MSA opcjonalnych oświadczeń obsługuje dla punktu końcowego v2.0, kolumny typu użytkownika określa, czy roszczenie jest dostępna dla użytkowników usługi AAD lub zarządzanych kont usług.  

**Tabela 2: Zestaw standardowych opcjonalnego roszczenia**

| Name (Nazwa)                     | Opis                                                                                                                                                                                     | Typ tokenu | Typ użytkownika | Uwagi                                                                                                                                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `auth_time`                | Czas, kiedy ostatnio uwierzytelnienia użytkownika.  Specyfikacja protokołu OpenID Connect Zobacz.                                                                                                                                | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_region_scope`      | Region dzierżawy zasobów                                                                                                                                                                   | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `signin_state`             | Zaloguj się w stanie oświadczeń                                                                                                                                                                             | JWT        |           | 6 wartości zwracane, jako flagi:<br> "dvc_mngd": urządzenie jest zarządzane<br> "dvc_cmp": urządzenie jest zgodne<br> "dvc_dmjd": urządzenie jest przyłączony do domeny<br> "dvc_mngd_app": urządzenie jest zarządzane za pośrednictwem zarządzania urządzeniami Przenośnymi<br> "inknownntwk": urządzenie znajduje się wewnątrz znanymi sieciowymi.<br> "kmsi": Zachowaj mnie podpisany w był używany. <br> |
| `controls`                 | Atrybut wielowartościowy elementu oświadczenie zawierające formanty sesji, wymuszane przez zasady dostępu warunkowego.                                                                                                       | JWT        |           | wartości 3:<br> "app_res": aplikacja powinna wymuszać szczegółowe ograniczenia. <br> "ca_enf": Wymuszanie dostępu warunkowego została opóźniona i jest nadal wymagana. <br> "no_cookie": ten token jest niewystarczający do programu exchange dla pliku cookie w przeglądarce. <br>                              |
| `home_oid`                 | Dla gości, identyfikator obiektu użytkownika w dzierżawie macierzystego użytkownika.                                                                                                                           | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `sid`                      | Identyfikator sesji używany dla sesji wylogowanie użytkownika.                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `platf`                    | Platforma urządzeń                                                                                                                                                                                 | JWT        |           | Ograniczone do zarządzanych urządzeń, które można zweryfikować typu urządzenia.                                                                                                                                                                                                                              |
| `verified_primary_email`   | Z PrimaryAuthoritativeEmail użytkownika                                                                                                                                               | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `verified_secondary_email` | Z SecondaryAuthoritativeEmail użytkownika                                                                                                                                             | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `enfpolids`                | Zasady zabezpieczeń identyfikatorów. Lista zasad identyfikatorów, które zostały ocenione pod kątem bieżącego użytkownika.                                                                                                         | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `vnet`                     | Informacje o specyfikator sieci Wirtualnej.                                                                                                                                                                     | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `fwd`                      | Adres IP.  Dodaje oryginalnego adresu IPv4 klienta (w sieci Wirtualnej)                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `ctry`                     | Kraj użytkownika                                                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_ctry`              | Kraj dzierżawy zasobów                                                                                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `acct`    | Stan konta użytkowników w dzierżawie.  Jeśli użytkownik jest członkiem dzierżawcy, wartość jest `0`.  Jeśli są one Gość, wartość jest `1`.  | JWT, SAML | | |
| `upn`                      | Oświadczenie UserPrincipalName.  Mimo że tego oświadczenia jest uwzględniana automatycznie, należy określić go jako opcjonalnego roszczenia można dołączyć dodatkowe właściwości, aby zmodyfikować jego zachowanie w przypadku użytkownika gościa. | JWT, SAML  |           | Dodatkowe właściwości: <br> `include_externally_authenticated_upn` <br> `include_externally_authenticated_upn_without_hash`                                                                                                                                                                 |

### <a name="v20-optional-claims"></a>Opcjonalne oświadczeń w wersji 2.0
Te oświadczenia zawsze znajdują się w wersji 1.0 tokenów, ale są usuwane z tokenów w wersji 2.0, o ile nie jest wymagane.  Oświadczenia te dotyczą tylko tokenów Jwt (tokeny Identyfikatora i tokenów dostępu).  

**Tabela 3: Tylko w wersji 2.0 opcjonalnych oświadczeń**

| Token JWT oświadczeń     | Name (Nazwa)                            | Opis                                                                                                                    | Uwagi |
|---------------|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------|-------|
| `ipaddr`      | Adres IP                      | Adres IP klienta, zalogowany z.                                                                                      |       |
| `onprem_sid`  | Identyfikator zabezpieczeń lokalnych |                                                                                                                                |       |
| `pwd_exp`     | Czas wygaśnięcia hasła        | Element datetime, w którym wygasa hasło.                                                                                    |       |
| `pwd_url`     | Zmień adres URL hasła             | Adres URL, które mogą odwiedzać użytkownika, aby zmienić swoje hasło.                                                                        |       |
| `in_corp`     | Wewnątrz sieci firmowej        | Sygnały, jeśli klient jest loguje się z siecią firmową. Jeśli nie, nie jest dołączana oświadczenia                     |       |
| `nickname`    | Pseudonim                        | Dodatkową nazwę użytkownika, niezależnie od imię lub nazwisko.                                                             |       |                                                                                                                |       |
| `family_name` | Nazwisko                       | Zgodnie z definicją w obiekcie użytkownika usługi Azure AD zapewnia ostatniego nazwy, nazwisko lub nazwisko użytkownika. <br>"family_name":"Miller" |       |
| `given_name`  | Imię                      | Udostępnia pierwszy lub "" Nazwa użytkownika, zgodnie z ustaleniami na obiekt użytkownika usługi Azure AD.<br>"given_name": "Piotr"                   |       |

### <a name="additional-properties-of-optional-claims"></a>Dodatkowe właściwości opcjonalnych oświadczeń

Niektóre opcjonalnych oświadczeń można skonfigurować zmienić sposób zwracane są oświadczenia.  Te dodatkowe właściwości przede wszystkim służą do migracji aplikacji lokalnej za pomocą oczekiwań różnych danych (na przykład `include_externally_authenticated_upn_without_hash` ułatwia klientów, którzy nie może obsłużyć hashmarks (`#`) w nazwy UPN)

**Tabela 4: Wartości związane z konfigurowaniem standardowe opcjonalnych oświadczeń**

| Nazwa właściwości                                     | Nazwa właściwości dodatkowe                                                                                                             | Opis |
|---------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-------------|
| `upn`                                                 |                                                                                                                                      |             |
| | `include_externally_authenticated_upn`              | Obejmuje gościa UPN przechowywanej w dzierżawie zasobów.  Na przykład: `foo_hometenant.com#EXT#@resourcetenant.com`                            |             
| | `include_externally_authenticated_upn_without_hash` | Jak wyżej, z wyjątkiem, że hashmarks (`#`) są zastępowane znakami podkreślenia (`_`), na przykład `foo_hometenant.com_EXT_@resourcetenant.com` |             

> [!Note]
>Określanie nazwy upn opcjonalnego roszczenia bez dodatkowych właściwości nie zmienia żadnych zachowanie — aby zobaczyć nowe oświadczenie wydanych w tokenie, co najmniej jeden z dodatkowych właściwości muszą zostać dodane. 


#### <a name="additional-properties-example"></a>Przykład dodatkowe właściwości:

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

Ten obiekt OptionalClaims powoduje, że token Identyfikatora zwracana do klienta, aby uwzględnić innej nazwy upn z dodatkowe dzierżawy macierzystego i informacje o zasobie dzierżawy.  

## <a name="configuring-optional-claims"></a>Konfigurowanie opcjonalnych oświadczeń

Opcjonalne oświadczeń dla aplikacji można skonfigurować, modyfikując manifest aplikacji (Zobacz przykład poniżej). Aby uzyskać więcej informacji, zobacz [opis artykułu manifestu aplikacji usługi Azure AD](active-directory-application-manifest.md).

**Schemat próbki:**

```json
"optionalClaims":  
   {
       "idToken": [
             { 
                   "name": "upn", 
                   "essential": false, 
                   "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ],
 "accessToken": [ 
             {
                    "name": "auth_time", 
                    "essential": false
              }
        ],
"saml2Token": [ 
              { 
                    "name": "upn", 
                    "essential": true
               },
               { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>Typ OptionalClaims

Deklaruje opcjonalnych oświadczeń żądany przez aplikację. Aplikację można skonfigurować opcjonalne oświadczeń ma zostać zwrócona w każdej z trzech typów tokenów (identyfikator tokenu tokenu SAML 2 tokenu dostępu) może odbierać z usługi tokenu zabezpieczającego. Aplikację można skonfigurować różne zestawy opcjonalnych oświadczeń w każdym typ tokenu. Właściwość OptionalClaims jednostki aplikacji jest obiektem OptionalClaims.

**Tabela 5: Właściwości typu OptionalClaims**

| Name (Nazwa)        | Typ                       | Opis                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Kolekcja (OptionalClaim) | Opcjonalne oświadczenia zwracane w token JWT Identyfikatora.     |
| `accessToken` | Kolekcja (OptionalClaim) | Opcjonalne oświadczeń zwracane w tokenie dostępu JWT. |
| `saml2Token`  | Kolekcja (OptionalClaim) | Zwracane opcjonalne oświadczenia w tokenie SAML.       |


### <a name="optionalclaim-type"></a>Typ OptionalClaim

Zawiera opcjonalnego roszczenia skojarzone z aplikacją lub nazwy głównej usługi. Właściwości idToken, accessToken i saml2Token [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) typ jest kolekcją OptionalClaim.
Jeśli jest to obsługiwane przez określone oświadczenie, można również zmodyfikować zachowanie OptionalClaim, korzystając z pola parametr AdditionalProperties.

**Tabela 6: Właściwości typu OptionalClaim**

| Name (Nazwa)                 | Typ                    | Opis                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Nazwa opcjonalnego roszczenia.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | Źródło (obiektu katalogu) oświadczenia. Istnieją wstępnie zdefiniowane oświadczeń i zdefiniowanych przez użytkownika z właściwości rozszerzenia. Jeśli wartość źródłowa ma wartość null, to wstępnie zdefiniowane oświadczenie opcjonalne. Jeśli wartości źródłowej jest użytkownika, wartość właściwości name jest właściwość rozszerzenia z obiektu użytkownika. |
| `essential`            | Edm.Boolean             | Jeśli ma wartość true, roszczenia określony przez klienta jest niezbędne w celu zapewnienia sprawnego autoryzacji obsługę określonego zadania wymagane przez użytkownika końcowego. Wartość domyślna to false.                                                                                                                 |
| `additionalProperties` | Kolekcja (typem Edm.String.) | Dodatkowe właściwości oświadczenia. Jeśli właściwość istnieje w tej kolekcji, modyfikuje zachowanie opcjonalnego roszczenia określony we właściwości name.                                                                                                                                                   |

## <a name="configuring-custom-claims-via-directory-extensions"></a>Konfigurowanie niestandardowych oświadczeń za pośrednictwem rozszerzenia katalogów

Oprócz zestaw standardowych opcjonalnych oświadczeń tokeny można również skonfigurować do uwzględnienia rozszerzenia schematu katalogu (zobacz [artykułu rozszerzenia schematu katalogu](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) Aby uzyskać więcej informacji).  Ta funkcja jest przydatna do dołączania dodatkowych informacji o nich aplikacji można użyć — na przykład, identyfikator dodatkowych lub opcji konfiguracji ważne, że użytkownik ma ustawiony. 

> [!Note]
> Rozszerzenia schematu katalogu są funkcję tylko do usługi AAD, więc jeśli żądania manifest aplikacji niestandardowego rozszerzenia i użytkownik MSA rejestruje w swojej aplikacji, te rozszerzenia nie zostaną zwrócone. 

### <a name="values-for-configuring-additional-optional-claims"></a>Konfigurowanie dodatkowych opcjonalnych oświadczeń wartości 

Atrybuty rozszerzenia, można użyć w pełnej nazwy rozszerzenia (w formacie: `extension_<appid>_<attributename>`) w manifeście aplikacji. `<appid>` Musi odpowiadać identyfikator aplikacji żąda oświadczeń. 

W ramach JWT, będzie obliczanie tych oświadczeń o następującym formacie nazwy: `extn.<attributename>`.

W ramach tokeny SAML będzie obliczanie tych oświadczeń o następującym formacie identyfikatora URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="optional-claims-example"></a>Przykład opcjonalnych oświadczeń

W tej sekcji można przeprowadzenie scenariusza, aby zobaczyć sposób korzystania z funkcji opcjonalnych oświadczeń dla aplikacji.
Dostępne są opcje wielu aktualizacji właściwości konfiguracji tożsamości aplikacji aby włączyć i skonfigurować opcjonalne oświadczeń:
-   Manifest aplikacji można modyfikować. W poniższym przykładzie zostanie ta metoda umożliwia przeprowadzenie konfiguracji. Odczyt [opis dokumentu manifestu aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) pierwszy wprowadzenie do manifestu.
-   Istnieje również możliwość pisania aplikacji, która używa [interfejsu API programu Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) zaktualizowania aplikacji. [Jednostki i odwołanie do typu złożonego](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) w dokumentacji interfejsu API programu Graph Przewodnik ułatwia przy konfigurowaniu opcjonalnych oświadczeń.

**Przykład:** w poniższym przykładzie użytkownik zmodyfikuje manifest aplikacji, aby dodawać oświadczenia do dostępu, identyfikator i SAML tokeny przeznaczone dla aplikacji.
1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2.  Po uwierzytelniono, wybierz dzierżawy usługi Azure AD, wybierając ją z prawym górnym rogu strony.
3.  Wybierz **rozszerzenia usługi Azure AD** w panelu nawigacji po lewej stronie, kliknij polecenie **rejestracji aplikacji**.
4.  Znajdź aplikację, którą chcesz skonfigurować opcjonalne oświadczenia dla na liście i kliknij go.
5.  Na stronie aplikacji kliknij **manifestu** aby otworzyć Edytor manifestu w tekście. 
6.  Można edytować bezpośrednio manifest przy użyciu tego edytora. Manifest następuje schematu [jednostek aplikacji](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)i formaty automatyczne raz zapisać manifestu. Nowe elemets zostanie dodany do `OptionalClaims` właściwości.

      ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
      "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
      "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }
      ```
      W takim przypadku różne oświadczenia opcjonalne zostały dodane do każdego typu tokenu, który może odbierać aplikacji. Tokeny Identyfikatora teraz będzie zawierać nazwę UPN dla użytkowników federacyjnych w pełnej postaci (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Tokeny dostępu teraz otrzyma oświadczenia auth_time. Tokeny SAML będzie zawierać teraz rozszerzenie schematu katalogu skypeId (w tym przykładzie identyfikator aplikacji dla tej aplikacji jest ab603c56068041afb2f6832e2a17e237).  Tokeny SAML uwidoczni Identyfikator Skype jako `extension_skypeId`.

7.  Po zakończeniu aktualizowania manifest kliknij **zapisać** można zapisać manifestu


## <a name="related-content"></a>Zawartość pokrewna
* Dowiedz się więcej o [standardowe oświadczeń](active-directory-token-and-claims.md) dostarczane przez usługę Azure AD. 
