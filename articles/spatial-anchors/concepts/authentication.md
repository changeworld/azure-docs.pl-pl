---
title: Uwierzytelnianie i autoryzację do kotwic przestrzenne platformy Azure | Dokumentacja firmy Microsoft
description: Poznaj różne sposoby usługi lub aplikacji można uwierzytelniać kotwic przestrzenne platformy Azure i poziomy formant, który trzeba bramy dostępu do platformy Azure przestrzenne kotwic.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2b3f4cf4099459b655fc0e370935ddc8079de810
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073936"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Uwierzytelnianie i autoryzację do kotwic przestrzenne platformy Azure

W tej sekcji omówimy różne sposoby, które można wybrać metodę uwierzytelniania Azure kotwic przestrzennych z aplikacji lub usługi sieci web i sposoby, w którym można użyć kontroli dostępu opartej na rolach w usłudze Azure Directory (Azure AD) możesz kontrolować dostęp do kont przestrzenne zakotwiczenia.  

## <a name="overview"></a>Omówienie

![Omówienie uwierzytelniania kotwic przestrzenne platformy Azure](./media/spatial-anchors-authentication-overview.png)

Aby dostęp do danego konta kotwic przestrzenne platformy Azure, klienci muszą najpierw uzyskać token dostępu z usługi Azure mieszane mogą stać się rzeczywistością Usługa tokenu zabezpieczającego (STS). Tokeny uzyskanymi z STS na żywo przez 24 godziny i zawierają informacje dotyczące usług przestrzenne kotwic do podejmowania decyzji dotyczących autoryzacji na koncie i upewnij się, że tylko autoryzowani podmiotów zabezpieczeń mają dostęp do tego konta. 

Tokeny dostępu można uzyskać w programie exchange z obu kluczy konta lub tokeny wystawione przez usługi AD systemu Azure. 

Klucze konta pozwalają na szybkie rozpoczęcie pracy nad przy użyciu usługi Azure przestrzenne kotwic; Jednak przed wdrożeniem aplikacji do środowiska produkcyjnego, zaleca się, że aktualizujesz aplikację do używania uwierzytelniania w usłudze AD systemu Azure. 

Tokeny uwierzytelniania w usłudze Azure AD można uzyskać na dwa sposoby:

- Jeśli tworzysz aplikację przedsiębiorstwa, a firma korzysta z usługi Azure AD jako jego systemem obsługi tożsamości, można użyć oparte na użytkowniku usługi Azure AD uwierzytelniania w aplikacji i Udziel dostępu do kont przestrzenne kotwic przy użyciu istniejących grup zabezpieczeń usługi Azure AD lub bezpośrednio do użytkowników w Twojej organizacji. 
- W przeciwnym razie zalecane jest, uzyskiwania tokenów usługi Azure AD z usługi sieci web, do obsługi aplikacji. Korzystając z obsługi usługi sieci web jest zalecaną metodę uwierzytelniania dla aplikacji produkcyjnych, ponieważ takie rozwiązanie pomaga uniknąć osadzenia poświadczeń do uzyskania dostępu do kotwic przestrzenne platformy Azure w aplikacji klienckiej. 

## <a name="account-keys"></a>Klucze konta

Za pomocą kluczy konta dla dostępu do konta Azure przestrzenne kotwic jest najprostszym sposobem na rozpoczęcie pracy. Klucze konta można znaleźć w witrynie Azure portal. Przejdź do swojego konta, a następnie wybierz kartę "Klucze".

![Omówienie uwierzytelniania kotwic przestrzenne platformy Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Dwa klucze są udostępniane, służą do obu jednocześnie jest nieprawidłowa dla dostępu kontu przestrzenne zakotwiczenia. Zalecane jest, regularnie aktualizować klucz, którego używasz do uzyskania dostępu do konta; dwa oddzielne klucze Włącz takie aktualizacje bez przestojów; Musisz również zaktualizować, klucz podstawowy i klucz pomocniczy. 

Zestaw SDK ma wbudowaną obsługę uwierzytelniania za pomocą kluczy konta; po prostu musisz ustawić właściwość AccountKey na obiekcie cloudSession. 

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

Po zakończeniu tej operacji, zestaw SDK obsłuży wymiany klucza konta, aby uzyskać token dostępu i konieczne buforowanie tokenów dla aplikacji. 

> [!WARNING] 
> Szybkie proces wdrażania, ale podczas tworzenia/prototypy, zalecane jest użycie kluczy konta. Zdecydowanie zaleca się nie dostarczanie aplikacji przy użyciu klucza konta osadzonych w niej w środowisku produkcyjnym i zamiast tego użyć oparte na użytkowniku lub oparta na usłudze Azure uwierzytelnianie w usłudze AD zbliża się dalej na liście.

## <a name="azure-ad-user-authentication"></a>Uwierzytelnianie użytkownika usługi Azure AD

Dla aplikacji przeznaczonych dla użytkowników usługi Azure Active Directory, zalecanym podejściem jest użycie tokenu usługi Azure AD dla użytkownika, który można uzyskać za pomocą biblioteki ADAL, zgodnie z opisem w następującej dokumentacji: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); użytkownik powinny wykonaj następujące kroki w obszarze "Szybkiego uruchamiania", które obejmują:

1. Konfiguracja w witrynie Azure portal
    1.  Zarejestruj swoją aplikację w usłudze Azure AD jako **aplikacji natywnej**. W ramach rejestracji należy określić, czy aplikacji powinny być wielodostępne, ani nie zapewniają przekierowania, dozwolone adresy URL aplikacji.  
    2.  Udziel dostępu aplikacji lub użytkowników do zasobu: 
        1.  Przejdź do zasobu przestrzenne kotwice w witrynie Azure portal
        2.  Przełącz się do **kontrola dostępu (IAM)** kartę
        3.  Trafienia **Dodaj przypisanie roli**
            1.  [Wybierz rolę](#role-based-access-control)
            2.  W **wybierz** wprowadź nazwy użytkowników, grup i aplikacji, do którego ma zostać przypisany dostęp. 
            3.  Trafienia **Zapisz**.
2. W kodzie:
    1.  Upewnij się, że używasz **identyfikator aplikacji** i **identyfikator Uri przekierowania** własnych aplikacji usługi Azure AD jako **identyfikator klienta** i **RedirectUri** Parametry w bibliotece ADAL
    2.  Ustawianie informacji o dzierżawie:
        1.  Jeśli aplikacja obsługuje **tylko Moja organizacja**, Zastąp tę wartość za pomocą usługi **identyfikator dzierżawy** lub **nazwa dzierżawy** (na przykład contoso.microsoft.com)
        2.  Jeśli aplikacja obsługuje **kont w dowolnym katalogu organizacji**, Zastąp tę wartość za pomocą **organizacji**
        3.  Jeśli aplikacja obsługuje **użytkowników kont Microsoft wszystkich**, Zastąp tę wartość za pomocą **wspólne**
    3.  Na żądanie tokenu, ustaw **zasobów** do "https://sts.mixedreality.azure.com". To "Zasób" będą wskazywać do usługi Azure AD, czy aplikacja żąda token dla usługi Azure przestrzenne kotwic.  

Dzięki temu aplikacja powinno być możliwe uzyskanie z biblioteki ADAL tokenu usługi Azure AD; można ustawić tokenu usługi Azure AD jako **authenticationToken** na obiekcie konfiguracji sesji chmury. 

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

## <a name="azure-ad-service-authentication"></a>Uwierzytelnianie usługi w usłudze Azure AD

Jest to zalecana opcja, jak wdrażać aplikacje, wykorzystując kotwic przestrzenne platformy Azure do środowiska produkcyjnego można korzystać z usługi zaplecza, która będzie brokera żądań uwierzytelniania. Ogólne schemat powinien być zgodnie z opisem na poniższym diagramie:

![Omówienie uwierzytelniania kotwic przestrzenne platformy Azure](./media/spatial-anchors-aad-authentication.png)

W tym miejscu, zakłada się, że aplikacja używa własny mechanizm (na przykład: Konto Microsoft, platformy PlayFab, Facebook, Google identyfikator, niestandardowe nazwy użytkownika/hasła, itp.) Uwierzytelnianie na jej usługą zaplecza. Po uwierzytelnieniu użytkowników do usługi zaplecza service do pobrania tokenu usługi Azure AD wymiany dla tokenu dostępu, for Azure przestrzenne kotwic i zwrócić go do aplikacji klienckiej.

Token dostępu usługi Azure AD jest pobierany za pomocą biblioteki ADAL, zgodnie z opisem w następującej dokumentacji: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); należy wykonać kroki opisane w obszarze "Szybkiego uruchamiania", które obejmują:

1.  Konfiguracja w witrynie Azure portal:
    1.  Rejestrowanie aplikacji w usłudze Azure AD:
        1.  W witrynie Azure portal, przejdź do **usługi Azure Active Directory**i wybierz **rejestracje aplikacji**
        2.  Wybierz **rejestrowanie nowej aplikacji**
        3.  Wprowadź nazwę aplikacji, wybierz pozycję **aplikacji sieci Web / interfejs API** jako typ aplikacji, a następnie wprowadź adres URL uwierzytelniania dla usługi. Następnie naciśnij klawisz **Utwórz**.
        4.  Na tę aplikację, trafienia **ustawienia**, a następnie wybierz **klucze** kartę. Wprowadź nazwę klucza, wybierz czas trwania i trafień **Zapisz**. Upewnij się zapisać wartości klucza, który jest wyświetlany w danym momencie, ponieważ będzie potrzebny, które mają zostać objęte kodu usługi sieci web.
    2.  Udzielanie dostępu do zasobu usługi aplikacji i/lub użytkowników:
        1.  Przejdź do zasobu przestrzenne kotwice w witrynie Azure portal
        2.  Przełącz się do **kontrola dostępu (IAM)** kartę
        3.  Trafienia **Dodaj przypisanie roli**
        1.  [Wybierz rolę](#role-based-access-control)
        2.  W **wybierz** pole, wprowadź nazwę aplikacji został utworzony i do której ma zostać przypisany dostęp. Aby użytkownicy twojej aplikacji pełnią różne role względem konta kotwic przestrzennych, należy zarejestrować wiele aplikacji w usłudze Azure AD i przypisać do poszczególnych osobnych ról. Następnie można zaimplementować logikę autoryzacji do użycia odpowiednią rolę dla użytkowników.  
    3.  Trafienia **Zapisz**.
2.  W kodzie (Uwaga: możesz użyć przykładu usługi uwzględnione w serwisie GitHub):
    1.  Upewnij się, że należy użyć Identyfikatora aplikacji, klucz tajny aplikacji i identyfikator Uri własną aplikację usługi Azure AD, identyfikatora klienta przekierowania wpisem tajnym i parametry RedirectUri w bibliotece ADAL
    2.  Ustaw identyfikator dzierżawy własne AAAzure Dodaj identyfikator dzierżawy w parametrze urząd w bibliotece ADAL
    3.  Na żądanie tokenu, ustaw **zasobów** do "https://sts.mixedreality.azure.com" 

Dzięki temu usługi wewnętrznej bazy danych można pobrać tokenu usługi Azure AD. Go może następnie wymienić dla tokenu MR, to zostanie zwrócona do klienta. Odbywa się przy użyciu tokenu usługi Azure AD do pobrania tokenu MR za pośrednictwem wywołania REST. Oto przykładowe wywołanie:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Jeżeli nagłówek autoryzacji jest sformatowany w następujący sposób: `Bearer <accoundId>:<accountKey>`

I odpowiedzi zawiera token MR w postaci zwykłego tekstu.
 
Ten token MR jest zwracana do klienta. Aplikacja klienta można ustawić ją jako jej token dostępu w konfiguracji sesji chmury.

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Które ułatwiają sterowanie tym poziom dostępu przyznane aplikacji, usług lub użytkowników usługi Azure AD, usługi, przypisywanie stosownie do potrzeb dotyczących kont usługi Azure przestrzenne kotwic zostały utworzone następujące role:

- **Przestrzenne właściciela konta kotwic**: aplikacje lub użytkowników, którzy mają tej roli mogą tworzyć kotwic przestrzennych, zapytania dla niego i je usunąć. Podczas uwierzytelniania Twojego konta za pomocą kluczy konta **przestrzenne właściciela konta kotwic** rola jest przypisywana do uwierzytelniony podmiot zabezpieczeń. 
- **Współautor konta usługi przestrzenne kotwic**: aplikacje lub użytkowników, którzy mają tej roli będą mogli tworzyć kotwic przestrzennych, zapytanie dla nich, ale nie można ich usuwać. 
- **Przestrzenne kotwic konta czytnika**: aplikacje lub użytkowników, którzy mają tej roli mogą tylko zakotwiczenia przestrzennych, wykonanie kwerendy, ale nie można utworzyć nowe, usunąć istniejące lub zaktualizować metadanych na przestrzenne kotwic. To jest zwykle używana dla aplikacji gdy niektórzy użytkownicy selekcjonowanie środowiska, a inne tylko przywołanie kotwic wcześniej umieszczone w tym środowisku.

## <a name="next-steps"></a>Kolejne kroki

Utwórz swoją pierwszą aplikację przy użyciu Azure przestrzenne kotwic.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
