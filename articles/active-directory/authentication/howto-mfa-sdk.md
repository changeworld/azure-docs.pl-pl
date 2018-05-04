---
title: Uwierzytelnianie wieloskładnikowe software development kit aplikacji niestandardowych | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób pobranie i użycie zestawu SDK usługi Azure MFA, aby włączyć weryfikację dwuetapową dla niestandardowych aplikacji.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 1c152f67-be02-42a5-a0c7-246fb6b34377
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: joflore
ms.openlocfilehash: 28efd5a5e9e757f3e2eae64fc770393608c9c028
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Tworzenie usługi Multi-Factor Authentication w aplikacje niestandardowe (SDK)

> [!IMPORTANT]
> Ogłoszono już zakończenie obsługi zestawu SDK usługi Azure Multi-Factor Authentication. Ta funkcja nie będą obsługiwane dla nowych klientów. Obecni klienci mogą używać zestawu SDK do 14 listopada 2018 r. Po tym czasie wywołania do zestawu SDK będą kończyć się niepowodzeniem. 

Azure Multi-Factor Authentication Software Development Kit (SDK) umożliwia tworzenie aplikacji w dzierżawie usługi Azure AD włączono weryfikację dwuetapową bezpośrednio do procesów logowania lub transakcji.

SDK usługi Multi-Factor Authentication jest dostępna w C#, Visual Basic (.NET), Java, Perl, PHP i Ruby. Zestaw SDK udostępnia cienką otoką wokół weryfikacji dwuetapowej. Obejmuje on wszystko, co potrzebne do pisania kodu, w tym pliki kodu źródłowego komentarze, przykładowe pliki i szczegółowe plik ReadMe. Każdy zestaw SDK zawiera również certyfikat i klucz prywatny szyfrowania transakcje, które są unikatowe dla dostawcy uwierzytelniania wieloskładnikowego. Tak długo, jak długo ma dostawcy, możesz pobrać zestaw SDK w dowolną liczbę języków i formaty zgodnie z potrzebami.

Struktura interfejsów API w zestawie SDK usługi Multi-Factor Authentication jest proste. Należy jednej funkcji wywołanie interfejsu API za pomocą opcji wieloskładnikowego parametry (takie jak tryb weryfikacji) i danych użytkownika (na przykład numer telefonu lub numer PIN, aby sprawdzić poprawność). Interfejsy API wykonuje wywołanie funkcji do żądania usługi sieci web do usługi uwierzytelniania wieloskładnikowego Azure opartej na chmurze. Wszystkie wywołania musi zawierać odwołanie do certyfikatu prywatnego, który znajduje się w każdym zestawu SDK.

Ponieważ interfejsy API nie mają dostępu do użytkowników zarejestrowanych w usłudze Azure Active Directory, musisz podać informacje o użytkowniku w pliku lub bazy danych. Interfejsy API nie udostępniają również funkcje zarządzania użytkownika lub rejestracji, więc jest potrzebne do tworzenia tych procesów do aplikacji.

> [!IMPORTANT]
> Aby pobrać zestaw SDK, musisz utworzyć dostawcę usługi Azure Multi-Factor Authentication, nawet jeśli masz licencje usług Azure MFA, AAD Premium lub EMS. Jeśli w tym celu należy utworzyć dostawcy uwierzytelniania wieloskładnikowego Azure i mają już licencji, upewnij się, że tworzenie dostawcy z **każdego włączonego użytkownika** modelu. Następnie połącz dostawcę z katalogiem zawierającym licencje usług Azure MFA, Azure AD Premium lub EMS. Taka konfiguracja powoduje, że rozliczenie jest przeprowadzane tylko jeśli masz więcej unikatowych użytkowników przy użyciu zestawu SDK niż liczba licencji użytkownika.


## <a name="download-the-sdk"></a>Pobierz zestaw SDK
Pobieranie zestawu SDK usługi Azure Multi-Factor wymaga [dostawcy uwierzytelniania wieloskładnikowego Azure](concept-mfa-authprovider.md).  Wymaga pełnej Azure subskrypcji, nawet jeśli należą do firmy licencje usługi Azure MFA, Azure AD Premium lub pakietu Enterprise Mobility Suite. Metody publiczne pobierania zestawu SDK ma została zlikwidowana, ponieważ zestawu SDK jest przestarzała. Aby pobrać zestaw SDK, należy otworzyć sprawę pomocy technicznej z firmą Microsoft. Zestaw SDK jest dostępne tylko dla klientów, które już używają zestawu SDK. Nowi klienci nie będą został załadowany.

## <a name="whats-in-the-sdk"></a>Co to jest w zestawie SDK
Zestaw SDK zawiera następujące elementy:

* **PLIK README**. Opisano sposób korzystania z interfejsów API uwierzytelniania wieloskładnikowego w nowej lub istniejącej aplikacji.
* **Pliki źródłowe** uwierzytelnianie wieloskładnikowe
* **Certyfikat klienta** używanej do komunikacji z usługą Multi-Factor Authentication
* **Klucz prywatny** certyfikatu
* **Wyniki wywołania.** Lista kody rezultatów połączeń. Aby otworzyć ten plik, za pomocą aplikacji formatowanie tekstu, takich jak program WordPad. Kody rezultatów połączeń umożliwia testowanie i rozwiązywanie problemów stosowania uwierzytelniania wieloskładnikowego w aplikacji. Nie są one kodów stanu uwierzytelniania.
* **Przykłady.** Przykładowy kod Podstawowa implementacja pracy usługi Multi-Factor Authentication.

> [!WARNING]
> Certyfikat klienta jest unikatowy certyfikat prywatny wygenerowany specjalnie dla Ciebie. Udostępnij lub nie utracić tego pliku. Jest kluczem do zapewnienia bezpieczeństwa komunikacji z usługą Multi-Factor Authentication.

## <a name="code-sample"></a>Przykład kodu
Ten przykładowy kod przedstawia sposób dodawania Tryb standardowy głosu wywołania weryfikacji do aplikacji przy użyciu interfejsów API w zestawie SDK Azure Multi-Factor Authentication. Tryb standardowy jest telefon użytkownika reaguje na naciśnięcie przycisku #.

W tym przykładzie użyto C# .NET 2.0 SDK usługi Multi-Factor Authentication w podstawowej aplikacji ASP.NET w logice C# po stronie serwera, ale ten proces przypomina w innych językach. Ponieważ zestaw SDK zawiera pliki źródłowe, pliki wykonywalne nie można skompilować plików i ich odwołania lub umieścić je bezpośrednio w aplikacji.

> [!NOTE]
> Podczas wdrażania usługi Multi-Factor Authentication, należy użyć dodatkowe metody (Rozmowa telefoniczna lub wiadomości tekstowej) jako dodatkowej lub trzeciorzędny weryfikacji uzupełnienie metodę uwierzytelniania podstawowego (nazwa użytkownika i hasło). Te metody nie są zaprojektowane jako metody uwierzytelniania podstawowego.

### <a name="code-sample-overview"></a>Omówienie przykładowy kod
Ten przykładowy kod dla prostą aplikację sieci web pokaz używa połączenia telefonicznego z odpowiedzią klucza # do weryfikacji uwierzytelniania użytkownika. Współczynnik ten telefon jest określany w usłudze Multi-Factor Authentication jako tryb standardowy.

Kod po stronie klienta nie ma żadnych elementów specyficznych dla usługi Multi-Factor Authentication. Czynniki dodatkowego uwierzytelniania są niezależne od podstawowego uwierzytelniania, dlatego można dodać je bez zmiany istniejącego interfejsu logowania jednokrotnego. Interfejsy API w zestawie SDK usługi Multi-Factor umożliwiają dostosowanie środowiska użytkownika, ale nie może być konieczne wprowadzanie zmian w ogóle.

Kod po stronie serwera dodaje Tryb standardowy uwierzytelniania w kroku 2. Tworzy obiekt PfAuthParams z parametrami, które są wymagane do weryfikacji Tryb standardowy: nazwa_użytkownika, telefonu numer oraz trybu i ścieżkę do certyfikatu klienta (CertFilePath), który jest wymagany w każdym wywołaniu. Pokaz wszystkich parametrów w PfAuthParams na ten temat można znaleźć w pliku przykładzie w zestawie SDK.

Następnie kod przekazuje obiekt PfAuthParams funkcji pf_authenticate(). Zwracana wartość wskazuje powodzenie lub niepowodzenie uwierzytelniania. Parametry, callStatus oraz identyfikator błędu zawierają informacje wynik wywołania dodatkowe. Kody rezultatów połączeń są rejestrowane w pliku wyników wywołania w zestawie SDK.

Ta implementacja minimalnego można pisać w zaledwie kilku wierszach. Jednak w kodzie produkcyjnym, można dołączyć dokładniejsze obsługi błędów kodu dodatkowej bazy danych i lepszą obsługę użytkowników.

### <a name="web-client-code"></a>Kod klienta sieci Web
Poniżej znajduje się kod klienta sieci web dla strony demonstracyjnej.

    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="\_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
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
