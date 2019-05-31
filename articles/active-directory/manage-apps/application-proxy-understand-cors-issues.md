---
title: Opisy problemów i rozwiązywanie problemów z CORS serwera Proxy aplikacji w usłudze Azure AD
description: Zawiera opis mechanizmu CORS w serwera Proxy aplikacji usługi Azure AD oraz sposobach identyfikacji i rozwiązywaniu problemów mechanizmu CORS.
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
ms.openlocfilehash: 2b6adcf4231aa44a4f28d277e963efa16de8af81
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399344"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Opisy problemów i rozwiązywanie problemów z usługi Azure Active Directory aplikacji serwera Proxy mechanizmu CORS

[Współużytkowanie zasobów między źródłami (cors)](http://www.w3.org/TR/cors/) czasami stanowi wyzwanie dla aplikacji i interfejsów API została opublikowania za pośrednictwem usługi Azure Active Directory serwera Proxy aplikacji. W tym artykule omówiono problemy CORS serwera Proxy aplikacji w usłudze Azure AD i ich rozwiązania.

Poziom zabezpieczeń przeglądarki zwykle uniemożliwia strony sieci web dzięki czemu wysyłanie żądań AJAX do innej domeny. To ograniczenie jest nazywany *zasadami tego samego źródła*i zapobiega złośliwych witryn odczytywanie poufnych danych z innej lokacji. Jednak czasami możesz chcieć umożliwić innych witryn, wywołania interfejsu API sieci web. CORS to standard W3C, która umożliwia serwer może Poluzować zasady tego samego źródła i zezwolić na niektóre żądania między źródłami, jednocześnie odrzucając inne.

## <a name="understand-and-identify-cors-issues"></a>Zrozumieć i zidentyfikować problemy mechanizmu CORS

Dwa adresy URL mają tego samego źródła, jeśli mają one hostów, porty i schematy identyczne ([RFC 6454](https://tools.ietf.org/html/rfc6454)), takich jak:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Następujące adresy URL są źródła innego niż poprzednie dwa:

-   http:\//contoso.net — różne domeny
-   http:\//contoso.com:9000/foo.html - innego portu
-   protokół https:\//contoso.com/foo.html - innego schematu
-   http:\//www.contoso.com/foo.html — różne domeny podrzędnej

Zasadami jednego źródła, uniemożliwia dostęp do zasobów z innych źródeł, chyba, że używają nagłówki control prawidłowy dostęp. W przypadku nieobecne lub niepoprawne nagłówki CORS żądań cross-origin zakończyć się niepowodzeniem. 

Mechanizm CORS problemy można zidentyfikować za pomocą narzędzi debugowania w przeglądarce:

1. Uruchom przeglądarkę i przejdź do aplikacji sieci web.
1. Naciśnij klawisz **F12** do konsoli debugowania.
1. Spróbuj odtworzyć transakcji i zapoznaj się z komunikatem konsoli. Naruszenie zasad CORS generuje błąd konsoli, dotyczące źródła.

Poniższy zrzut ekranu wybierając **wypróbuj** przycisk spowodowane komunikat o błędzie CORS tego protokołu https:\//corswebclient-contoso.msappproxy.net nie można znaleźć w nagłówku Access-Control-Allow-Origin.

![Problem z mechanizmu CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Wyzwania dotyczące mechanizmu CORS w usłudze serwera Proxy aplikacji

W poniższym przykładzie przedstawiono typowy scenariusz CORS serwera Proxy aplikacji w usłudze Azure AD. Hosty serwerów wewnętrznych **CORSWebService** kontroler internetowego interfejsu API i **CORSWebClient** wywołująca **CORSWebService**. Jest żądaniem AJAX z **CORSWebClient** do **CORSWebService**.

![Żądanie do tego samego źródła w środowisku lokalnym](./media/application-proxy-understand-cors-issues/image1.png)

Aplikacja CORSWebClient działa hostowanie jej w środowisku lokalnym, ale kończy się niepowodzeniem, aby załadować albo występuje gdy opublikowane za pośrednictwem serwera Proxy aplikacji usługi Azure AD. Jeśli oddzielnie opublikowane aplikacje CORSWebClient i CORSWebService jako różnych aplikacji za pośrednictwem serwera Proxy aplikacji dwie aplikacje znajdują się w różnych domenach. Żądanie AJAX z CORSWebClient CORSWebService jest żądania między źródłami a zakończy się niepowodzeniem.

![Żądania CORS serwera Proxy aplikacji](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Rozwiązania problemów CORS serwera Proxy aplikacji

Można naprawić poprzedni problem mechanizmu CORS w jednym z kilku sposobów.

### <a name="option-1-set-up-a-custom-domain"></a>Opcja 1: Konfigurowanie domeny niestandardowej

Użyj serwera Proxy aplikacji usługi Azure AD [domeny niestandardowej](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) publikować z tego samego źródła, bez konieczności wprowadzać żadnych zmian źródła aplikacji, kodu lub nagłówków. 

### <a name="option-2-publish-the-parent-directory"></a>Opcja 2: Publikowanie katalogu nadrzędnego

Publikowanie katalogu nadrzędnego obie aplikacje. Rozwiązanie działa to szczególnie dobrze, jeśli masz tylko dwie aplikacje na serwerze sieci web. Zamiast publikowanie oddzielnie każdą aplikację, możesz opublikować wspólnego katalogu nadrzędnego, co skutkuje to samo źródło.

W poniższych przykładach pokazano portal strony serwera Proxy aplikacji usługi Azure AD dla aplikacji CORSWebClient.  Gdy **wewnętrzny adres URL** ustawiono *contoso.com/CORSWebClient*, aplikacja nie może wykonać żądania zakończone powodzeniem, aby *contoso.com/CORSWebService* katalogu, ponieważ są one cross-origin. 

![Publikowanie aplikacji indywidualnie](./media/application-proxy-understand-cors-issues/image4.png)

Zamiast tego należy ustawić **wewnętrzny adres URL** do opublikowania katalogu nadrzędnego, który zawiera oba *CORSWebClient* i *CORSWebService* katalogi:

![Publikowanie katalogu nadrzędnego](./media/application-proxy-understand-cors-issues/image5.png)

Wynikowy adresy URL aplikacji skutecznie rozwiązać problem CORS:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Opcja 3: Aktualizowanie nagłówków HTTP

Dodawanie niestandardowego nagłówka odpowiedzi HTTP w usłudze sieci web, aby dopasować żądania origin. Dla witryny sieci Web działające w Internet Information Services (IIS) należy użyć Menedżera usług IIS do modyfikowania nagłówka:

![Dodaj nagłówek niestandardową odpowiedź w Menedżerze usług IIS](./media/application-proxy-understand-cors-issues/image6.png)

Ta modyfikacja nie wymaga żadnych zmian w kodzie. Można jej zweryfikować w śladach programu Fiddler:

**Po dodaniu nagłówka**\
HTTP/1.1 200 OK\
Cache-Control: nie-cache\
Pragma: nie-cache\
Content-Type: text/plain; charset = utf-8\
Wygasa:-1\
Różnią się: Zaakceptuj Encoding\
Serwer: Microsoft — usługi IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin: https://corswebclient-contoso.msappproxy.net** \
X-AspNet-Version: 4.0.30319\
X obsługiwane przez: ASP.NET\
Długość zawartości: 17

### <a name="option-4-modify-the-app"></a>Opcja 4: Modyfikowanie aplikacji

Możesz zmienić aplikację do obsługi mechanizmu CORS przez dodanie nagłówka Access-Control-Allow-Origin odpowiednimi wartościami. Sposób, aby dodać nagłówek zależy od języka kodu aplikacji. Zmiana kodu jest to najmniej zalecana opcja, ponieważ wymaga większość nakładu pracy.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Option 5: Wydłużać jego okresu istnienia tokenu dostępu

Niektóre problemy dotyczące mechanizmu CORS nie można rozpoznać, np. gdy aplikacja wykonuje przekierowanie do *login.microsoftonline.com* w celu uwierzytelnienia i wygaśnięcia ważności tokenu dostępu. CORS wywołań, a następnie kończy się niepowodzeniem. Obejście dla tego scenariusza jest wydłużać jego okresu istnienia tokenu dostępu, aby zapobiec wygaśnięciu podczas sesji użytkownika. Aby uzyskać więcej informacji na temat jak to zrobić, zobacz [okresów istnienia tokenu można skonfigurować w usłudze Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Zobacz także
- [Samouczek: Dodawanie aplikacji w środowisku lokalnym dostępu zdalnego za pośrednictwem serwera Proxy aplikacji w usłudze Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Planowanie wdrożenia serwera Proxy aplikacji usługi Azure AD](application-proxy-deployment-plan.md) 
- [Dostęp zdalny do aplikacji lokalnych za pośrednictwem serwera Proxy usługi Azure Active Directory aplikacji](application-proxy.md) 
