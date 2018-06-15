---
title: Debugowanie na języku SAML logowanie jednokrotne do aplikacji w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: 'Dowiedz się, jak można debugować na języku SAML logowanie jednokrotne do aplikacji w usłudze Azure Active Directory '
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: dastrock; smalser
ms.openlocfilehash: 1a33b5ab9e26ed497e3be2d430f66ef41402733d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157898"
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Debugowanie na języku SAML logowanie jednokrotne do aplikacji w usłudze Azure Active Directory

Podczas debugowania integracji aplikacji opartej na SAML, warto często za pomocą narzędzia, takie jak [Fiddler](http://www.telerik.com/fiddler) SAML żądania i odpowiedzi SAML, która zawiera tokenu SAML, który został wystawiony dla aplikacji. 

![Fiddler][1]

Często problemy odnoszą się do konfiguracji w usłudze Azure Active Directory lub na stronie aplikacji. W zależności od tego, gdzie zostanie wyświetlony błąd należy przejrzeć SAML żądania lub odpowiedzi:

- Komunikat w mojej firmy strony logowania [łącza do sekcji]
- Wystąpił błąd na stronie aplikacji jest widoczny po zalogowaniu [łącza do sekcji]

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>Komunikat w mojej firmy strony logowania.

Mapowanie jeden do jednego między kodem błędu i kroki rozwiązania można znaleźć [problemy przy logowaniu do galerii aplikacji skonfigurowana dla federacyjnych logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML).

Jeśli zostanie wyświetlony błąd na stronie logowania firmy należy komunikat o błędzie i żądania SAML, aby debugować ten problem.

### <a name="how-can-i-get-the-error--message"></a>Jak można uzyskać komunikatu o błędzie?

Aby komunikat o błędzie, zobacz prawym dolnym rogu strony. Zostanie wyświetlony błąd, który zawiera:

- CorrelationID
- Sygnatura czasowa
- Komunikat

![Błąd][2] 

 
Identyfikator korelacji i sygnatura czasowa tworzą unikatowy identyfikator, używanego do Znajdź dzienniki zaplecza skojarzono błąd logowania. Te wartości są ważne podczas tworzenia sprawy pomocy technicznej z firmą Microsoft, ponieważ pomagają inżynierów umożliwia szybsze rozwiązanie tego problemu.

Komunikat jest główną przyczynę problemu logowania. 


### <a name="how-can-i-review-the-saml-request"></a>Jak można przejrzeć żądania SAML

Żądanie SAML wysłane przez aplikację do usługi Azure Active Directory zazwyczaj jest ostatnim odpowiedź 200 protokołu HTTP z [ https://login.microsoftonline.com ](https://login.microsoftonline.com).
 
Przy użyciu programu Fiddler, można wyświetlić żądanie SAML tego wiersza, a następnie zaznaczając **inspektorzy > WebForms** kartę na prawym panelu. Kliknij prawym przyciskiem myszy **SAMLRequest** wartość, a następnie wybierz **przesyłają TextWizard**. Następnie wybierz **Base64 z** z **przekształcenie** menu do zdekodowania token i wyświetlić jego zawartość. 

Ponadto również skopiować wartość SAMLrequest i użyj innego dekodera Base64.

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

Z żądaniem SAML zdekodowany sprawdź następujące kwestie:

1. **Docelowy** w żądaniu SAML odpowiada SAML pojedynczy znak na adres URL usługi uzyskane z usługi Azure Active Directory.
 
2. **Wystawca** w SAML żądania jest taki sam **identyfikator** zostały skonfigurowane dla aplikacji w usłudze Azure Active Directory. Usługi Azure AD używa wystawca do znajdowania aplikacji w katalogu.

3. **AssertionConsumerServiceURL** jest, w którym aplikacja oczekuje odebrać tokenu SAML z usługi Azure Active Directory. Tę wartość można skonfigurować w usłudze Azure Active Directory, ale nie jest to konieczne, jeśli jest on częścią żądania SAML.


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>Wystąpił błąd na stronie aplikacji jest widoczny po zalogowaniu

W tym scenariuszu aplikacja nie akceptuje odpowiedzi wystawiony przez usługę Azure AD. Jest ważne, aby zweryfikować odpowiedzi z usługi Azure AD, który zawiera token SAML z dostawcą aplikacji, aby ustalić brakujący lub nieprawidłowy. 

### <a name="how-can-i-get-and-review-the-saml-response"></a>Jak pobrać i przejrzyj odpowiedzi SAML?

Odpowiedź z usługi Azure AD, który zawiera SAML token jest zwykle występujący po przekierowania HTTP 302 z jednym https://login.microsoftonline.com i są wysyłane do skonfigurowanego **adres URL odpowiedzi** aplikacji. 

SAML token można wyświetlić tego wiersza, a następnie zaznaczając **inspektorzy > WebForms** kartę na prawym panelu. Z tego miejsca, kliknij prawym przyciskiem myszy **SAMLResponse** wartość, a następnie wybierz **przesyłają TextWizard**. Następnie wybierz **Base64 z** z **przekształcenie** menu do zdekodowania token i wyświetlić jego zawartość, użyj innego dekodera Base64. 

Można także skopiować wartość **SAMLrequest** i użyj innego dekodera Base64.

Odwiedź stronę [błąd na stronie aplikacji po zalogowaniu się](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML) wskazówki, aby uzyskać więcej informacji, na jakie mogą być brakujące lub nieprawidłowe w odpowiedzi SAML rozwiązywania problemów.

Aby uzyskać informacje dotyczące sposobu przeglądania SAML odpowiedzi Wyświetl artykuł [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response).


## <a name="related-articles"></a>Pokrewne artykuły
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](../active-directory-apps-index.md)
* [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Dostosowywanie oświadczeń wydanych w tokenie SAML dla wstępnie zintegrowanych aplikacji](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png
