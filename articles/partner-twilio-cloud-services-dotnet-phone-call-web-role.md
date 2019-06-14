---
title: Jak nawiązać połączenie telefoniczne z usługi Twilio (.NET) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązywanie połączeń telefonicznych i wysyłanie wiadomości SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w języku .NET.
services: ''
documentationcenter: .net
author: devinrader
manager: timlt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: jeconnoc
ms.openlocfilehash: c41057203da949e371f62332e938feb92e84534f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60422808"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Jak nawiązać połączenie telefoniczne w roli sieci web na platformie Azure za pomocą usługi Twilio
Ten przewodnik pokazuje, jak wykonać wywołanie ze strony sieci web hostowanych na platformie Azure za pomocą usługi Twilio. Wynikowa aplikacja monituje o nawiązać połączenie za pomocą podanej liczbie i wiadomość, jak pokazano na poniższym zrzucie ekranu.

![Formularz wywołania platformy Azure za pomocą usługi Twilio i platformy ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Wymagania wstępne
Należy wykonać następujące czynności, aby użyć kodu, w tym temacie:

1. Uzyskiwanie konta usługi Twilio i uwierzytelnianie tokenu z [konsoli Twilio][twilio_console]. Aby rozpocząć pracę z usługą Twilio, zarejestruj się pod adresem [ https://www.twilio.com/try-twilio ] [ try_twilio]. Możesz ocenić ceny na [ https://www.twilio.com/pricing ] [ twilio_pricing]. Aby uzyskać informacji na temat interfejsów API dostarczonych przez usługi Twilio, zobacz [ https://www.twilio.com/voice/api ] [ twilio_api].
2. Dodaj *biblioteki Twilio .NET* do roli sieci web. Zobacz **Dodawanie bibliotek usługi Twilio do Twojego projektu roli sieć web**w dalszej części tego tematu.

Należy zapoznać się z tworzeniem prostej [roli sieci Web na platformie Azure][azure_webroles_get_started].

## <a name="howtocreateform"></a>Jak: Tworzenie formularza sieci web nawiązywania połączenia
<a id="use_nuget"></a>Aby dodać biblioteki usługi Twilio, do projektu roli sieci web:

1. Otwórz swoje rozwiązanie w programie Visual Studio.
2. Kliknij prawym przyciskiem myszy **odwołania**.
3. Kliknij przycisk **Zarządzaj pakietami NuGet**.
4. Kliknij przycisk **Online**.
5. W polu wyszukiwania online, wpisz *twilio*.
6. Kliknij przycisk **zainstalować** w pakiecie usługi Twilio.

Poniższy kod przedstawia sposób tworzenia formularza sieci web do pobierania danych użytkownika do nawiązywania połączenia. W tym przykładzie rolę sieci Web programu ASP.NET o nazwie **TwilioCloud** zostanie utworzony.

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a id="howtocreatecode"></a>Jak: Tworzenie kodu do wykonania wywołania
Następujący kod, który jest wywoływana, gdy użytkownik kończy formularz, tworzy komunikat wywołania i generuje wywołanie. W tym przykładzie kodu jest uruchamiane w program obsługi zdarzeń przycisku w formularzu. (Użyj swojego konta usługi Twilio i uwierzytelniania tokenu zamiast wartości symboli zastępczych, przypisany do `accountSID` i `authToken` w poniższym kodzie.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

Zostanie nawiązane połączenie, a punkt końcowy usługi Twilio, wersja interfejsu API i stan wywołania są wyświetlane. Poniższy zrzut ekranu przedstawia dane wyjściowe przykładowego uruchomienia.

![Odpowiedź wywołania platformy Azure za pomocą usługi Twilio i platformy ASP.NET][twilio_dotnet_basic_form_output]

Więcej informacji na temat TwiML znajduje się w temacie [ https://www.twilio.com/docs/api/twiml ] [ twiml]. Więcej informacji na temat &lt;Say&gt; i innych poleceń usługi Twilio, można znaleźć w folderze [ https://www.twilio.com/docs/api/twiml/say ] [ twilio_say].

## <a id="nextsteps"></a>Następne kroki
Ten kod podano Wam podstawową funkcjonalność w roli sieci web platformy ASP.NET na platformie Azure za pomocą usługi Twilio. Przed wdrożeniem na platformie Azure w środowisku produkcyjnym, można dodać więcej obsługę błędów lub innych funkcji. Na przykład:

* Zamiast przy użyciu formularza sieci web, można użyć usługi Azure Blob storage, czy oddzielnego wystąpienia usługi Azure SQL Database do przechowywania numerów telefonów i wywołać tekstu. Aby dowiedzieć się, jak za pomocą obiektów blob na platformie Azure, zobacz [jak używać usługi Azure Blob storage na platformie .NET][howto_blob_storage_dotnet]. Aby uzyskać informacje dotyczące korzystania z bazy danych SQL, zobacz [jak używać usługi Azure SQL Database w aplikacjach .NET][howto_sql_azure_dotnet].
* Można użyć `RoleEnvironment.getConfigurationSettings` można pobrać usługi Twilio identyfikator konta i uwierzytelniania tokenu z ustawień konfiguracji wdrożenia, a nie zakodowane na stałe wartości w formularzu. Aby uzyskać informacje o `RoleEnvironment` klasy, zobacz [Namespace Microsoft.WindowsAzure.ServiceRuntime][azure_runtime_ref_dotnet].
* Zapoznaj się ze wskazówkami zabezpieczeń Twilio na [ https://www.twilio.com/docs/security ] [ twilio_docs_security].
* Więcej informacji na temat usługi Twilio o [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="seealso"></a>Zobacz też
* [Jak za pomocą usługi Twilio dla połączeń głosowych i SMS możliwości platformy Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
