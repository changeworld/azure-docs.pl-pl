---
title: Stronie szablonów w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować zawartość portalu dla deweloperów przy użyciu zestawu szablonów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: apimpm
ms.openlocfilehash: 1fbafcdab938a0f8653df48631d7733cc58a3668
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441914"
---
# <a name="page-templates-in-azure-api-management"></a>Szablony stron w usłudze Azure API Management
Usługa Azure API Management zapewnia możliwość dostosować zawartość portalu dla deweloperów przy użyciu zestawu szablonów, które ich zawartość. Przy użyciu [DotLiquid](http://dotliquidmarkup.org/) składni i Edytor wybranych przez użytkownika, takich jak [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), i zlokalizowanych podany zbiór [zasoby ciągu](api-management-template-resources.md#strings), [symbol zasoby](api-management-template-resources.md#glyphs), i [stronie kontrolki](api-management-page-controls.md), masz dużą elastyczność konfigurowania zawartości stron, zgodnie z potrzebami przy użyciu tych szablonów.  
  
 Szablony w tej sekcji umożliwiają dostosowanie zawartość logowanie, logowanie się i strona nie można odnaleźć strony w portalu dla deweloperów.  
  
-   [Rejestrowanie](#SignIn)  
  
-   [Tworzenie konta](#SignUp)  
  
-   [Nie odnaleziono strony](#PageNotFound)  
  
> [!NOTE]
>  Przykładowe szablony domyślne są uwzględnione w poniższej dokumentacji, ale mogą ulec zmianie ze względu na stałe udoskonalenia. Szablony domyślne na żywo można wyświetlić w portalu dla deweloperów, przechodząc do żądanego szablony osobno. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Dostosowywanie portalu dla deweloperów usługi API Management przy użyciu szablonów](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="SignIn"></a> Rejestrowanie  
 **Zaloguj** szablon umożliwia bycie na dostosowywanie logowania strony w portalu dla deweloperów.  
  
 ![Zaloguj się na stronie](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM logowania szablonów portalu dla deweloperów strony")  
  
### <a name="default-template"></a>szablon domyślny  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Kontrolki  
 Ten szablon może korzystać z następujących [stronie kontrolki](api-management-page-controls.md).  
  
-   [Basic — logowanie](api-management-page-controls.md#basic-signin)  
  
-   [dostawcy](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Model danych  
 [Logowanie użytkownika](api-management-template-data-model-reference.md#UseSignIn) jednostki.  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json  
{
    "Email": null,
    "Password": null,
    "ReturnUrl": null,
    "RememberMe": false,
    "RegistrationEnabled": true,
    "DelegationEnabled": false,
    "DelegationUrl": null,
    "SsoSignUpUrl": null,
    "AuxServiceUrl": "https://portal.azure.com/#resource/subscriptions/{subscription ID}/resourceGroups/Api-Default-West-US/providers/Microsoft.ApiManagement/service/contoso5",
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
        ],
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": false
}
```  
  
##  <a name="SignUp"></a> Zarejestruj się  
 **Zarejestruj** szablon umożliwia dostosowanie rejestracji strony w portalu dla deweloperów.  
  
 ![Zarejestruj się Strona](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM rejestracji szablonów portalu dla deweloperów strony")  
  
### <a name="default-template"></a>szablon domyślny  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Kontrolki  
 Ten szablon może korzystać z następujących [stronie kontrolki](api-management-page-controls.md).  
  
-   [Zarejestruj się](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Model danych  
 [Rejestracja użytkownika](api-management-template-data-model-reference.md#UserSignUp) jednostki.  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="PageNotFound"></a> Nie odnaleziono strony  
 **Strony nie można odnaleźć** szablon umożliwia bycie na dostosowywanie strony, nie można odnaleźć strony w portalu dla deweloperów.  
  
 ![Nie można odnaleźć strony](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM nie znaleziono strony dla deweloperów szablonów portalu")  
  
### <a name="default-template"></a>szablon domyślny  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Kontrolki  
 Ten szablon nie może używać dowolnego [stronie kontrolki](api-management-page-controls.md).  
  
### <a name="data-model"></a>Model danych  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|referenceCode|ciąg|Kod generowany, jeśli ta strona została wyświetlona w wyniku błędu wewnętrznego.|  
|errorCode|ciąg|Kod generowany, jeśli ta strona została wyświetlona w wyniku błędu wewnętrznego.|  
|emailBody|ciąg|Wyślij wiadomość e-mail treści generowany, gdy ta strona została wyświetlona w wyniku błędu wewnętrznego.|  
|requestedUrl|ciąg|Adres URL zażądane, gdy nie można odnaleźć strony.|  
|referrerUrl|ciąg|Adres URL odwołania do żądanego adresu URL.|  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Dostosowywanie portalu dla deweloperów usługi API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
