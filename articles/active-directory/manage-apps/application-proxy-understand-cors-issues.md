---
title: Opis i rozwiązywanie problemów z usługą Azure AD Application Proxy CORS
description: Zapewnia zrozumienie mechanizmu CORS w usłudze Azure AD Application Proxy oraz sposób identyfikowania i rozwiązywania problemów z mechanizmem CORS.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c49535ad11139ac5145d4f283374bf9cc6d71f52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025785"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Opis i rozwiązywanie problemów z usługą Azure Active Directory Application Proxy CORS

[Udostępnianie zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/) może czasami stanowić wyzwanie dla aplikacji i interfejsów API publikowanych za pośrednictwem serwera proxy aplikacji usługi Azure Active Directory. W tym artykule omówiono problemy i rozwiązania dotyczące usługi Azure AD Application Proxy CORS.

Zabezpieczenia przeglądarki zwykle uniemożliwiają stronie internetowej kierowanie żądań AJAX do innej domeny. To ograniczenie jest nazywane *zasadami tego samego źródła*i uniemożliwia złośliwej witrynie odczytywanie poufnych danych z innej witryny. Czasami jednak możesz chcieć pozwolić innym witrynom na wywołanie internetowego interfejsu API. CORS to standard W3C, który pozwala serwerowi rozluźnić zasady tego samego źródła i zezwalać na niektóre żądania cross-origin, jednocześnie odrzucając inne.

## <a name="understand-and-identify-cors-issues"></a>Zrozumienie i identyfikacja problemów z cors

Dwa adresy URL mają takie same pochodzenie, jeśli mają identyczne schematy, hosty i porty[(RFC 6454),](https://tools.ietf.org/html/rfc6454)takie jak:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Następujące adresy URL mają inne źródła niż poprzednie dwa:

-   http:\//contoso.net - Inna domena
-   http:\//contoso.com:9000/foo.html - Inny port
-   https:\//contoso.com/foo.html - Inny schemat
-   http:\//www.contoso.com/foo.html - Inna subdomena

Zasady tego samego źródła uniemożliwiają aplikacjom uzyskiwanie dostępu do zasobów z innych źródeł, chyba że używają odpowiednich nagłówków kontroli dostępu. Jeśli nagłówki CORS są nieobecne lub niepoprawne, żądania między źródłami nie powiedzie się. 

Problemy z mechanizmem CORS można zidentyfikować za pomocą narzędzi do debugowania przeglądarki:

1. Uruchom przeglądarkę i przejdź do aplikacji internetowej.
1. Naciśnij **klawisz F12,** aby przywołać konsolę debugowania.
1. Spróbuj odtworzyć transakcję i przejrzyj komunikat konsoli. Naruszenie CORS powoduje błąd konsoli o pochodzeniu.

Na poniższym zrzucie ekranu wybranie przycisku **Wypróbuj** spowodowało\/komunikat o błędzie CORS, który https: /corswebclient-contoso.msappproxy.net nie został znaleziony w nagłówku Access-Control-Allow-Origin.

![Problem CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Wyzwania CORS z proxy aplikacji

W poniższym przykładzie przedstawiono typowy scenariusz cors serwera proxy aplikacji usługi Azure AD. Serwer wewnętrzny obsługuje kontroler interfejsu API sieci web **CORSWebService** i **CORSWebClient,** który wywołuje **USŁUGĘ CORSWebService**. Istnieje żądanie AJAX z **CORSWebClient** do **CORSWebService**.

![Lokalne żądanie tego samego pochodzenia](./media/application-proxy-understand-cors-issues/image1.png)

Aplikacja CORSWebClient działa podczas hosta lokalnie, ale nie można załadować lub błędy po opublikowaniu za pośrednictwem usługi Azure AD Application Proxy. Jeśli opublikowano aplikacje CORSWebClient i CORSWebService oddzielnie jako różne aplikacje za pośrednictwem serwera proxy aplikacji, dwie aplikacje są hostowane w różnych domenach. Żądanie AJAX z CORSWebClient do CORSWebService jest żądaniem cross-origin i kończy się niepowodzeniem.

![Żądanie cors serwera proxy aplikacji](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Rozwiązania problemów z usługą APS proxy aplikacji

Poprzedni problem cors można rozwiązać na jeden z kilku sposobów.

### <a name="option-1-set-up-a-custom-domain"></a>Opcja 1: Konfigurowanie domeny niestandardowej

Użyj [domeny niestandardowej](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) serwera proxy aplikacji usługi Azure AD, aby publikować z tego samego źródła, bez konieczności wprowadzania zmian w źródłach aplikacji, kodach lub nagłówkach. 

### <a name="option-2-publish-the-parent-directory"></a>Opcja 2: Publikowanie katalogu nadrzędnego

Opublikuj katalog nadrzędny obu aplikacji. To rozwiązanie działa szczególnie dobrze, jeśli masz tylko dwie aplikacje na serwerze www. Zamiast publikować każdą aplikację oddzielnie, można opublikować wspólny katalog nadrzędny, co powoduje, że tego samego pochodzenia.

Poniższe przykłady pokazują portal Azure AD Application Proxy strony dla aplikacji CORSWebClient.  Gdy **wewnętrzny adres URL** jest ustawiony na *contoso.com/CORSWebClient,* aplikacja nie może wywrzeć pomyślnych żądań do katalogu *contoso.com/CORSWebService,* ponieważ są one cross-origin. 

![Publikowanie aplikacji indywidualnie](./media/application-proxy-understand-cors-issues/image4.png)

Zamiast tego ustaw **wewnętrzny adres URL,** aby opublikować katalog nadrzędny, który obejmuje katalogi *CORSWebClient* i *CORSWebService:*

![Publikowanie katalogu nadrzędnego](./media/application-proxy-understand-cors-issues/image5.png)

Wynikowe adresy URL aplikacji skutecznie rozwiązać problem CORS:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Opcja 3: Aktualizowanie nagłówków HTTP

Dodaj niestandardowy nagłówek odpowiedzi HTTP w usłudze sieci web, aby dopasować żądanie pochodzenia. W przypadku witryn sieci Web działających w internetowych usługach informacyjnych (IIS) użyj Menedżera usług IIS, aby zmodyfikować nagłówek:

![Dodawanie niestandardowego nagłówka odpowiedzi w Menedżerze usług IIS](./media/application-proxy-understand-cors-issues/image6.png)

Ta modyfikacja nie wymaga żadnych zmian kodu. Można to sprawdzić w fiddler ślady:

**Księgowanie dodatku nagłówka**\
HTTP/1.1 200 OK\
Cache-Control: no-cache\
Pragma: no-cache\
Typ zawartości: tekst/zwykły; charset=utf-8\
Wygasa: -1\
Vary: Akceptowanie kodowania\
Serwer: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin: https\://corswebclient-contoso.msappproxy.net**\
X-AspNet-Wersja: 4.0.30319\
X-Powered-By: ASP.NET\
Długość zawartości: 17

### <a name="option-4-modify-the-app"></a>Opcja 4: Modyfikowanie aplikacji

Aplikację można zmienić na obsługę mechanizmu CORS, dodając nagłówek Access-Control-Allow-Origin z odpowiednimi wartościami. Sposób dodawania nagłówka zależy od języka kodu aplikacji. Zmiana kodu jest najmniej zalecaną opcją, ponieważ wymaga największego wysiłku.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Opcja 5: Wydłużenie okresu istnienia tokenu dostępu

Niektórych problemów z mechanizmem CORS nie można rozwiązać, na przykład gdy aplikacja przekierowuje do *login.microsoftonline.com* do uwierzytelniania i token dostępu wygasa. Wywołanie CORS kończy się niepowodzeniem. Obejście tego scenariusza jest przedłużenie okresu istnienia tokenu dostępu, aby zapobiec jego wygaśnięciu podczas sesji użytkownika. Aby uzyskać więcej informacji na temat tego, jak to zrobić, zobacz [Konfigurowalne okresy istnienia tokenu w usłudze Azure AD.](../develop/active-directory-configurable-token-lifetimes.md)

## <a name="see-also"></a>Zobacz też
- [Samouczek: Dodawanie aplikacji lokalnej do zdalnego dostępu za pośrednictwem serwera proxy aplikacji w usłudze Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Planowanie wdrożenia serwera proxy aplikacji usługi Azure AD](application-proxy-deployment-plan.md) 
- [Zdalny dostęp do aplikacji lokalnych za pośrednictwem serwera proxy aplikacji usługi Azure Active Directory](application-proxy.md) 
