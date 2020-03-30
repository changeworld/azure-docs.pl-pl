---
title: Szablony stron w usłudze Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak dostosować zawartość stron portalu deweloperów przy użyciu zestawu szablonów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ce56c406c884471c445b25343d5c42f9edcbe4c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249544"
---
# <a name="page-templates-in-azure-api-management"></a>Szablony stron w usłudze Azure API Management
Usługa Azure API Management umożliwia dostosowanie zawartości stron portalu deweloperów przy użyciu zestawu szablonów, które konfigurują ich zawartość. Za pomocą [dotLiquid](http://dotliquidmarkup.org/) składni i edytora do wyboru, takich jak [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), i pod warunkiem, zestaw zlokalizowanych [zasobów ciąg,](api-management-template-resources.md#strings) [zasoby glifów](api-management-template-resources.md#glyphs)i [page formantów,](api-management-page-controls.md)masz dużą elastyczność, aby skonfigurować zawartość stron, jak można uznać za stosowne przy użyciu tych szablonów.  
  
 Szablony w tej sekcji umożliwiają dostosowanie zawartości stron logowania, rejestracji i strony, na której nie znaleziono stron w portalu dla deweloperów.  
  
-   [Zaloguj się](#SignIn)  
  
-   [Zarejestruj się](#SignUp)  
  
-   [Nie znaleziono strony](#PageNotFound)  
  
> [!NOTE]
>  Przykładowe szablony domyślne są zawarte w poniższej dokumentacji, ale mogą ulec zmianie z powodu ciągłych ulepszeń. Domyślne szablony na żywo można wyświetlić w portalu dla deweloperów, przechodząc do żądanych szablonów indywidualnych. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Jak dostosować portal dewelopera zarządzania interfejsami API przy użyciu szablonów](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="sign-in"></a><a name="SignIn"></a>Rejestrowanie  
 Szablon **logowania** umożliwia dostosowanie strony logowania w portalu dewelopera.  
  
 ![Strona logowania](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "Szablony portalu deweloperów logowania do witryny APIM")  
  
### <a name="default-template"></a>Szablon domyślny  
  
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
  
### <a name="controls"></a>Formanty  
 Ten szablon może używać [kontrolek](api-management-page-controls.md)strony poniżej .  
  
-   [podstawowe-signin](api-management-page-controls.md#basic-signin)  
  
-   [Dostawców](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Model danych  
 [Jednostka logowania użytkownika.](api-management-template-data-model-reference.md#UseSignIn)  
  
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
  
##  <a name="sign-up"></a><a name="SignUp"></a>Zarejestruj się  
 Szablon **rejestracji** umożliwia dostosowanie strony rejestracji w portalu dla deweloperów.  
  
 ![Strona zarejestruj się](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "Szablony portalu deweloperów stron rejestracji w interfejsie APIM")  
  
### <a name="default-template"></a>Szablon domyślny  
  
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
  
### <a name="controls"></a>Formanty  
 Ten szablon może używać [kontrolek](api-management-page-controls.md)strony poniżej .  
  
-   [Rejestracji](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Model danych  
 [Jednostka rejestracji użytkownika.](api-management-template-data-model-reference.md#UserSignUp)  
  
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
  
##  <a name="page-not-found"></a><a name="PageNotFound"></a>Nie znaleziono strony  
 **Nie znaleziono** szablonu strony umożliwia dostosowanie strony, której nie znaleziono w portalu dla deweloperów.  
  
 ![Nie znaleziono strony](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "Szablony portalu deweloperów nie znalezionych stron w interfejsie APIM")  
  
### <a name="default-template"></a>Szablon domyślny  
  
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
  
### <a name="controls"></a>Formanty  
 Ten szablon nie może używać żadnych [formantów strony](api-management-page-controls.md).  
  
### <a name="data-model"></a>Model danych  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|referenceCode (Kod referencyjny)|ciąg|Kod wygenerowany, jeśli ta strona została wyświetlona w wyniku błędu wewnętrznego.|  
|Errorcode|ciąg|Kod wygenerowany, jeśli ta strona została wyświetlona w wyniku błędu wewnętrznego.|  
|emailBody (Ciało e-mail)|ciąg|Treść wiadomości e-mail wygenerowana, jeśli ta strona została wyświetlona w wyniku błędu wewnętrznego.|  
|wniosekUrl|ciąg|Adres URL żądany, gdy strona nie została znaleziona.|  
|odsyłaczUrl|ciąg|Adres URL strony odsyłającej do żądanego adresu URL.|  
  
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

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Jak dostosować portal dewelopera zarządzania interfejsami API przy użyciu szablonów](api-management-developer-portal-templates.md).
