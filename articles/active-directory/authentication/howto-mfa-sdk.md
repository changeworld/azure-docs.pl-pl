---
title: Usługa Azure MFA software development kit dla niestandardowych aplikacji — Azure Active Directory
description: W tym artykule przedstawiono sposób pobierania i Włącz weryfikację dwuetapową dla niestandardowej aplikacji przy użyciu zestawu SDK usługi MFA Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b6f5def70dcb2564e92c04e53b5d2ef5f0631fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414889"
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Tworzenie usługi Multi-Factor Authentication w aplikacjach niestandardowych (SDK)

> [!IMPORTANT]
> Ogłoszono już zakończenie obsługi zestawu SDK usługi Azure Multi-Factor Authentication. Ta funkcja będzie już obsługę nowych klientów. Obecni klienci mogą używać zestawu SDK do 14 listopada 2018 r. Po tym czasie wywołania do zestawu SDK będą kończyć się niepowodzeniem. 

Usługi Azure Multi-Factor Authentication Software Development Kit (SDK) umożliwia tworzenie weryfikacji dwuetapowej bezpośrednio w procesy logowania lub transakcji aplikacji w dzierżawie usługi Azure AD.

Zestaw SDK Multi-Factor Authentication jest dostępna dla języka C#, Visual Basic (platforma .NET), Java, Perl, PHP i Ruby. Zestaw SDK udostępnia alokowania elastycznego otokę weryfikacji dwuetapowej. Zawiera wszystko, czego potrzebujesz do pisania kodu, w tym plików kodu źródłowego z komentarzem, pliki przykładowe i plik ReadMe szczegółowe. Każdy zestaw SDK zawiera również certyfikat i klucz prywatny szyfrowania transakcji, które są unikatowe dla Twojego dostawcę uwierzytelniania Multi-Factor Authentication. Tak długo, jak ma się dostawcy, możesz pobrać zestaw SDK w tyle języków i formatów, według potrzeb.

Struktura interfejsów API w zestawie SDK Multi-Factor Authentication jest proste. Wprowadź funkcję pojedynczego wywołania interfejsu API za pomocą parametrów opcji usługi Multi-Factor Authentication (np. w trybie weryfikacji) i danych użytkownika (na przykład numer telefonu lub numer PIN, aby sprawdzić poprawność). Interfejsy API tłumaczenia wywołanie funkcji do żądań usług sieci web opartej na chmurze platformy Azure usługi Multi-Factor Authentication. Wszystkie wywołania musi zawierać odwołanie do certyfikatu prywatnego, który znajduje się w każdym zestawie SDK.

Ponieważ interfejsy API nie mają dostępu do użytkowników zarejestrowanych w usłudze Azure Active Directory, musisz podać informacje o użytkowniku w pliku lub bazy danych. Interfejsy API oferuje również rejestracji lub użytkownika funkcji zarządzania, dzięki czemu potrzebne do tworzenia tych procesów w aplikacji.

> [!IMPORTANT]
> Aby pobrać zestaw SDK, musisz utworzyć dostawcę usługi Azure Multi-Factor Authentication, nawet jeśli masz licencje usług Azure MFA, AAD Premium lub EMS. Jeśli w tym celu Utwórz dostawcę usługi Azure Multi-Factor Auth i masz już licencje, upewnij się utworzyć dostawcę za pomocą **za każdego włączonego użytkownika** modelu. Następnie połącz dostawcę z katalogiem zawierającym licencje usług Azure MFA, Azure AD Premium lub EMS. Ta konfiguracja gwarantuje, że możesz ponosić dodatkowe opłaty jedynie w przypadku unikatowych użytkowników korzystających z zestawu SDK przekroczy liczbę posiadanych licencji.


## <a name="download-the-sdk"></a>Pobierz zestaw SDK
Pobieranie zestawu SDK usługi Azure Multi-Factor Authentication wymaga [dostawcy usługi Azure Multi-Factor Authentication](concept-mfa-authprovider.md).  Wymaga subskrypcji pełnej platformy Azure, nawet jeśli należą do licencji usługi Azure MFA, Azure AD Premium lub pakietu Enterprise Mobility Suite. Metody publiczne pobierania zestawu SDK ma została zlikwidowana, ponieważ zestaw SDK jest przestarzała. Należy otwierać zgłoszenia do pomocy technicznej z firmą Microsoft, jeśli chcesz pobrać zestaw SDK. Zestaw SDK znajduje się tylko dla klientów, które już używają zestawu SDK. Nowi klienci nie będą dołączone.

## <a name="whats-in-the-sdk"></a>Co nowego w zestawie SDK
Zestaw SDK zawiera następujące elementy:

* **PLIK README**. Wyjaśnia, jak używać interfejsów API uwierzytelniania wieloskładnikowego w nowej lub istniejącej aplikacji.
* **Pliki źródłowe** uwierzytelnianie wieloskładnikowe
* **Certyfikat klienta** używanego do komunikacji z usługą Multi-Factor Authentication
* **Klucz prywatny** certyfikatu
* **Wyniki wywołania.** Lista kody rezultatów połączeń. Aby otworzyć ten plik, za pomocą aplikacji formatowanie tekstu, takich jak program WordPad. Kody rezultatów połączeń umożliwia testowanie i rozwiązywanie problemów implementacja uwierzytelniania wieloskładnikowego w Twojej aplikacji. Nie są one kodów stanu uwierzytelniania.
* **Przykłady.** Przykładowy kod podstawową implementację pracy, uwierzytelnianie wieloskładnikowe.

> [!WARNING]
> Certyfikat klienta jest unikatowy certyfikat prywatny, który został wygenerowany specjalnie dla Ciebie. Nie można udostępniać lub utraty tego pliku. Jest kluczem do zapewnienia bezpieczeństwa komunikacji z usługą Multi-Factor Authentication.

## <a name="code-sample"></a>Przykład kodu
Ten przykład kodu pokazuje, jak dodać weryfikację w drodze połączenia trybu standardowego głosu do aplikacji przy użyciu interfejsów API w zestawie SDK usługi Azure Multi-Factor Authentication. Tryb standardowy jest połączeń telefonicznych, który użytkownik odpowie, naciskając klawisz #.

W tym przykładzie użyto języka C# .NET 2.0 zestawu SDK Multi-Factor Authentication w podstawowej aplikacji ASP.NET przy użyciu logiki po stronie serwera w języku C#, ale proces jest podobny w innych językach. Ponieważ zestaw SDK zawiera pliki źródłowe i pliki wykonywalne nie można utworzyć plików i odwoływać się do nich lub uwzględnić je bezpośrednio w aplikacji.

> [!NOTE]
> Podczas implementowania usługi Multi-Factor Authentication, należy użyć dodatkowe metody (Rozmowa telefoniczna lub wiadomości SMS) jako dodatkowej lub trzeciorzędny weryfikacji Aby uzupełnić swoje metody uwierzytelniania podstawowego (nazwa użytkownika i hasło). Te metody nie są zaprojektowane jako metody uwierzytelniania podstawowego.

### <a name="code-sample-overview"></a>Omówienie przykładowych kodu
Ten przykładowy kod dla prostej aplikacji sieci web pokaz za pomocą połączenia telefonicznego # odpowiedzi klucza do weryfikacji uwierzytelniania użytkownika. Ten współczynnik połączeń telefonicznych jest nazywana w usłudze uwierzytelniania wieloskładnikowego Tryb standardowy.

Kod po stronie klienta nie ma żadnych elementów specyficznych dla uwierzytelniania wieloskładnikowego. Ponieważ czynniki dodatkowego uwierzytelniania są niezależne od podstawowego uwierzytelniania, można dodawać je bez zmieniania istniejącego interfejsu logowania jednokrotnego. Interfejsy API zestawu SDK usługi Multi-Factor Authentication pozwala na dostosowywanie środowiska użytkownika, ale nie może być konieczne wprowadzanie zmian w ogóle.

Kod po stronie serwera dodaje Tryb standardowy uwierzytelniania w kroku 2. Tworzy obiekt PfAuthParams z parametrami, które są wymagane do weryfikacji Tryb standardowy: nazwa_użytkownika, telefonu numer, a tryb i ścieżkę do certyfikatu klienta (CertFilePath), który jest wymagany w każdym wywołaniu. Do pokazania wszystkie parametry w PfAuthParams zobacz przykładowy plik w zestawie SDK.

Następnie kod przekazuje obiekt PfAuthParams funkcji pf_authenticate(). Zwracana wartość wskazuje powodzenie lub niepowodzenie uwierzytelniania. Parametry, callStatus i identyfikator błędu zawiera wywołanie dodatkowe informacje na temat wyników. Kody rezultatów połączeń są udokumentowane w pliku wyników wywołań w zestawie SDK.

Ta implementacja minimalny można pisać w zaledwie kilku wierszach. Jednak w kodzie produkcyjnym należy dołączyć bardziej zaawansowanej obsługi błędów, kod dodatkowej bazy danych i lepszą obsługę użytkowników.

### <a name="web-client-code"></a>Kod klienta sieci Web
Poniżej przedstawiono kod klienta sieci web dla strony pokaz.

    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="\_Default" %>

    <!DOCTYPE html>

    <html xmlns="https://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Kod po stronie serwera
W poniższym kodzie po stronie serwera Multi-Factor Authentication jest skonfigurowany i uruchom w kroku 2. Tryb standardowy (MODE_STANDARD) jest połączeń telefonicznych, do którego użytkownik odpowiada, naciskając klawisz #.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class \_Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "5555555555";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = "Multi-Factor Authentication failed.";
                }
            }

        }
    }
