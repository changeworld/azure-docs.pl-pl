---
title: Dostosowywanie interfejsu użytkownika — usługi Azure AD B2C | Dokumentacja firmy Microsoft
description: Temat dotyczący funkcje dostosowywania interfejsu użytkownika w usłudze Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: e6d1e093fafc6ea74dfcdfa498810ff33d27d89f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Usługa Azure Active Directory B2C: Dostosowywanie interfejsu użytkownika (UI) usługi Azure AD B2C

Środowisko użytkownika jest podstawowym w aplikacji klientów.  Zwiększ bazę swoich klientów przez obsługuje tworzenie użytkownika, korzystając z wygląd i działanie oznakowanie. Usługa Azure Active Directory B2C (Azure AD B2C) umożliwia dostosowanie edycji profilu rejestracji, logowania, i stron z formantem pikseli idealny resetowania hasła.

> [!NOTE]
> Funkcja dostosowania interfejsu użytkownika strony opisane w tym artykule nie dotyczy tylko zasad logowania, jego towarzyszący stronę resetowania hasła i sprawdzania wiadomości e-mail.  Użyj tych funkcji [firmowe funkcji](../active-directory/customize-branding.md) zamiast tego.
>
> Podobnie jeśli użytkownik intiates edytowanie profilu zasad *przed* zalogowaniu użytkownik zostanie przekierowany do strony, który można dostosować za pomocą [firmowe funkcji](../active-directory/customize-branding.md).

W tym artykule omówiono następujące tematy:

* Funkcja dostosowania interfejsu użytkownika strony.
* Narzędzie do przekazywania zawartości HTML do magazynu obiektów Blob Azure do użycia z funkcją dostosowania interfejsu użytkownika strony.
* Elementy interfejsu użytkownika używane przez usługę Azure AD B2C, który można dostosować przy użyciu kaskadowych arkuszy stylów (CSS).
* Najlepsze rozwiązania podczas wykonywania tej funkcji.

## <a name="the-page-ui-customization-feature"></a>Dostosowywanie funkcji interfejsu użytkownika strony

Można dostosować wygląd i działanie odbiorcy rejestrację, logowanie (zobacz powyżej Uwaga wyjątki dotyczące znakowania), resetowania hasła i edytowanie profilu stron (konfigurując [zasady](active-directory-b2c-reference-policies.md)). Klienci uzyskać bezproblemowe podczas nawigowania między aplikacji i strony obsługiwanych przez usługę Azure AD B2C.

W przeciwieństwie do innych usług, których opcji interfejsu użytkownika, usługi Azure AD B2C używa proste i nowoczesne podejścia do dostosowania interfejsu użytkownika.

Oto jak to działa: usługi Azure AD B2C kod w przeglądarce klienta, korzysta z podejścia nowoczesnych o nazwie [udostępniania zasobów między źródłami (CORS)](http://www.w3.org/TR/cors/).  W czasie wykonywania zawartość została załadowana z adresu URL, który określisz w zasadach. Możesz określić inny adres URL do różnych stron. Po zawartości załadowana z adresu URL jest scalany z fragment kodu HTML z usługi Azure AD B2C, ta strona jest wyświetlana dla klienta. Wszystko, co należy zrobić to:

1. Tworzenie zawartości z pustą poprawnie sformułowanym HTML5 `<div id="api"></div>` element znajduje się gdzieś w `<body>`. Znaczniki ten element gdzie dodaje się zawartość usługi Azure AD B2C.
1. Udostępnić zawartość w punkcie końcowym HTTPS (z CORS dozwolone). Należy pamiętać, zarówno GET i opcje żądania metody musi być włączona podczas konfigurowania CORS.
1. Użyj CSS do określania stylu usługi Azure AD B2C Wstawia elementy interfejsu użytkownika.

### <a name="a-basic-example-of-customized-html"></a>Podstawowy przykład dostosowany HTML

Poniższy przykład jest najbardziej podstawowa zawartość HTML, którego można używać do testowania tej możliwości. Użyj [Narzędzie Pomocnik](active-directory-b2c-reference-ui-customization-helper-tool.md) Aby przekazać i skonfigurować tę zawartość w magazynie obiektów Blob platformy Azure. Następnie można sprawdzić, czy podstawowe, stylized przycisków i pola formularza na każdej stronie są wyświetlane i funkcjonalności.

```HTML
<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
    </body>
</html>
```

## <a name="test-out-the-ui-customization-feature"></a>Przetestowanie dostosowywanie funkcji interfejsu użytkownika

Czy chcesz wypróbować dostosowywanie funkcji interfejsu użytkownika za pomocą naszej próbki kodu HTML i CSS zawartości?  Udostępniliśmy [Narzędzie Pomocnik](active-directory-b2c-reference-ui-customization-helper-tool.md) który przekazuje i konfiguruje przykładowej zawartości w magazynie obiektów Blob platformy Azure.

> [!NOTE]
> Można udostępnić zawartość interfejsu użytkownika dowolnym: na serwerach sieci web, CDN, usług AWS S3, systemy udostępniania plików itp. Tak długo, jak zawartość znajduje się na publicznie dostępny punkt końcowy HTTPS z włączonym mechanizmem CORS, możesz są gotowe. Użyto magazynu obiektów Blob platformy Azure tylko w celach ilustracyjnych.
>

## <a name="the-ui-fragments-embedded-by-azure-ad-b2c"></a>Fragmenty interfejsu użytkownika osadzone przez usługę Azure AD B2C

W poniższych sekcjach wymieniono fragmenty HTML5, które usługi Azure AD B2C scala `<div id="api"></div>` element znajduje się w treści. **Nie wstawiono tych fragmentów w zawartości HTML 5.** Usługę Azure AD B2C wstawia je w czasie wykonywania. Użyj tych fragmentów jako odwołanie podczas projektowania własnych kaskadowych arkuszy stylów (CSS).

### <a name="fragment-inserted-into-the-identity-provider-selection-page"></a>Fragment do "tożsamości dostawcy wybór page"

Ta strona zawiera listę dostawców tożsamości, które użytkownik może wybrać podczas tworzenia konta lub logowania. Przyciski te obejmują dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych (w oparciu nazwa użytkownika lub adres e-mail).

```HTML
<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-local-account-sign-up-page"></a>Fragment do "stronę tworzenia konta lokalnego konta"

Ta strona zawiera formularza dla lokalnego konta tworzenia konta na podstawie adresu e-mail lub nazwę użytkownika. Formularz może zawierać różne kontrolki wejściowe, takich jak pola do wprowadzania tekstu, pole wprowadzania hasła przycisk radiowy, jednokrotnym zaznaczeniem pola listy rozwijanej i pól wyboru wielokrotnego wyboru.

```HTML
<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-social-account-sign-up-page"></a>Fragment do "" kont społecznościowych strony zapisów"

Ta strona może pojawić się podczas rejestracji przy użyciu istniejącego konta od dostawcy tożsamości społecznościowych, takich jak Facebook lub Google +.  Jest używany podczas dodatkowe informacje muszą zostać pobrane od użytkownika końcowego za pomocą formularza tworzenia konta. Ta strona jest podobne do konta lokalnego strony zapisów (wyświetlone w poprzedniej sekcji) z wyjątkiem pól wprowadzania hasła.

### <a name="fragment-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Fragment do "Unified rejestracji i logowania strony"

Ta strona obsługuje zarówno rejestracji i logowania klientów, którzy mogą używać dostawców tożsamości społecznościowych, takich jak Facebook lub Google + lub kont lokalnych.

```HTML
<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>
```

### <a name="fragment-inserted-into-the-multi-factor-authentication-page"></a>Fragment do "page usługi Multi-Factor authentication"

Na tej stronie użytkowników można sprawdzić ich numery telefonów (przy użyciu tekstowych lub głosowych) podczas tworzenia konta lub logowania.

```HTML
<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-error-page"></a>Fragment do "" strona błędu"

```HTML
<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>
```

## <a name="localizing-your-html-content"></a>Lokalizacja zawartości HTML

Można lokalizować zawartość HTML, włączając ["Języka dostosowania"](active-directory-b2c-reference-language-customization.md).  Włączenie tej funkcji umożliwia usłudze Azure AD B2C do przekazywania parametru Open ID Connect `ui-locales`, do punktu końcowego.  Serwer zawartości można Użyj tego parametru, aby zapewnić dostosowanych stron HTML, które są specyficzne dla języka.

## <a name="things-to-remember-when-building-your-own-content"></a>Warto zapamiętać podczas tworzenia własnych zawartości

Jeśli zamierzasz korzystać z funkcji dostosowywania interfejsu użytkownika strony, przejrzyj poniższe najlepsze rozwiązania:

* Nie skopiuj zawartość domyślny usługi Azure AD B2C i próbuje zmodyfikować go. Najlepiej tworzenia zawartości HTML5 od początku i użyj domyślnej zawartości jako odwołanie.
* Ze względów bezpieczeństwa firma Microsoft nie pozwala na obejmują wszystkie JavaScript w zawartości. Większość potrzebnych powinien być dostępny poza pole. Jeśli nie, użyj [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) żądania nowych funkcji.
* Obsługiwane wersje przeglądarki:
  * Program Internet Explorer 11, 10, krawędzi
  * Ograniczona obsługa programu Internet Explorer 9, 8
  * Google Chrome 42.0 i powyżej.
  * Mozilla Firefox 38.0 i powyżej.
* Upewnij się, że nie zawierają `<form>` tagów w kodzie HTML, jak to będzie zakłócać operacji POST, generowanych przez wprowadzony kod HTML z usługi Azure AD B2C.
