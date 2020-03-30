---
title: Jak nawiązać połączenie telefoniczne z usługi Twilio (.NET) | Dokumenty firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie telefoniczne i wysłać wiadomość SMS za pomocą usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w .NET.
services: ''
documentationcenter: .net
author: georgewallace
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: gwallace
ms.openlocfilehash: 27b4f3cdd8f622a97cfc0853f79bb77d76673dcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69636152"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Jak nawiązać połączenie telefoniczne przy użyciu usługi Twilio w roli sieci Web na platformie Azure
W tym przewodniku pokazano, jak używać usługi Twilio do nawijania z witryny sieci Web hostowanej na platformie Azure. Wynikowa aplikacja monituje użytkownika o nawiązać połączenie z podanym numerem i komunikatem, jak pokazano na poniższym zrzucie ekranu.

![Formularz wywołania platformy Azure przy użyciu usługi Twilio i ASP.NET][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Wymagania wstępne
Aby użyć kodu w tym temacie, należy wykonać następujące czynności:

1. Uzyskaj konto usługi Twilio i token uwierzytelniania z [konsoli usługi Twilio][twilio_console]. Aby rozpocząć korzystanie z usługi Twilio, zarejestruj się w pliku [https://www.twilio.com/try-twilio][try_twilio]. Ceny można ocenić [https://www.twilio.com/pricing][twilio_pricing]w punkcie . Aby uzyskać informacje na temat interfejsu [https://www.twilio.com/voice/api][twilio_api]API dostarczonego przez program Twilio, zobacz .
2. Dodaj *bibliotekę programu Twilio .NET* do swojej roli sieci Web. Zobacz **Aby dodać biblioteki usługi Twilio do projektu roli sieci Web,** w dalszej części tego tematu.

Należy zapoznać się z tworzeniem podstawowej [roli sieci Web na platformie Azure][azure_webroles_get_started].

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>Jak: Tworzenie formularza internetowego do nawiązywania połączenia
<a id="use_nuget"></a>Aby dodać biblioteki usługi Twilio do projektu roli sieci Web:

1. Otwórz rozwiązanie w programie Visual Studio.
2. Kliknij prawym przyciskiem myszy **pozycję Odwołania**.
3. Kliknij pozycję **Zarządzaj pakietami NuGet**.
4. Kliknij pozycję **Online**.
5. W polu wyszukiwania online wpisz *twilio*.
6. Kliknij **pozycję Zainstaluj** pakiet usługi Twilio.

Poniższy kod pokazuje, jak utworzyć formularz sieci web, aby pobrać dane użytkownika do nawiązywania połączenia. W tym przykładzie tworzony jest ASP.NET roli sieci Web o nazwie **TwilioCloud.**

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

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Jak: Tworzenie kodu, aby nawiązać połączenie
Poniższy kod, który jest wywoływany, gdy użytkownik wypełnia formularz, tworzy komunikat o połączeniu i generuje wywołanie. W tym przykładzie kod jest uruchamiany w programie obsługi zdarzeń onclick przycisku w formularzu. (Użyj konta usługi Twilio i tokenu uwierzytelniania zamiast `accountSID` `authToken` wartości zastępczych przypisanych do i w poniższym kodzie).

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

Wywołanie jest i punkt końcowy usługi Twilio, wersja interfejsu API i stan wywołania są wyświetlane. Poniższy zrzut ekranu przedstawia dane wyjściowe z przebiegu próbki.

![Odpowiedź na wywołanie platformy Azure przy użyciu usługi Twilio i ASP.NET][twilio_dotnet_basic_form_output]

Więcej informacji na temat TwiML można znaleźć na stronie [https://www.twilio.com/docs/api/twiml][twiml]. Więcej informacji &lt;&gt; na temat Say i innych czasowników Twilio można znaleźć na stronie [https://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a name="next-steps"></a><a id="nextsteps"></a>Następne kroki
Ten kod został dostarczony, aby pokazać podstawowe funkcje przy użyciu usługi Twilio w ASP.NET roli sieci web na platformie Azure. Przed wdrożeniem na platformie Azure w procesach produkcyjnych można dodać więcej obsługi błędów lub innych funkcji. Przykład:

* Zamiast używać formularza sieci web, można użyć usługi Azure Blob storage lub wystąpienia usługi Azure SQL Database do przechowywania numerów telefonów i wywoływania tekstu. Aby uzyskać informacje dotyczące korzystania z obiektów blob na platformie Azure, zobacz [Jak korzystać z usługi magazynu obiektów Blob platformy Azure w .NET][howto_blob_storage_dotnet]. Aby uzyskać informacje dotyczące korzystania z bazy danych SQL, zobacz [Jak używać usługi Azure SQL Database w aplikacjach platformy .NET][howto_sql_azure_dotnet].
* Można użyć `RoleEnvironment.getConfigurationSettings` do pobrania identyfikatora konta usługi Twilio i token uwierzytelniania z ustawień konfiguracji wdrożenia, zamiast twardego kodowania wartości w formularzu. Aby uzyskać `RoleEnvironment` informacje o klasie, zobacz [Microsoft.WindowsAzure.ServiceRuntime Namespace][azure_runtime_ref_dotnet].
* Przeczytaj wytyczne dotyczące zabezpieczeń [https://www.twilio.com/docs/security][twilio_docs_security]usługi Twilio pod adresem .
* Dowiedz się więcej o [https://www.twilio.com/docs][twilio_docs]firmie Twilio pod adresem .

## <a name="see-also"></a><a name="seealso"></a>Zobacz też
* [Jak korzystać z funkcji Twilio dla poczty głosowej i programu SMS z platformy Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

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
