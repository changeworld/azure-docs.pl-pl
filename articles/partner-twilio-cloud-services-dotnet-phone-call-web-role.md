---
title: 'Porady: tworzenie rozmowy telefonicznej z usługi Twilio (.NET) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak wykonać połączenie telefoniczne i wysłać wiadomość SMS z usługi interfejsu API usługi Twilio na platformie Azure. Przykłady kodu napisane w programie .NET.
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
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: cd9792881182fbe90d9c210130ae8a34b12da363
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2017
ms.locfileid: "26366008"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Jak nawiązać połączenie telefoniczne z rolą sieci web na platformie Azure przy użyciu usługi Twilio
W tym przewodniku pokazano, jak używać usługi Twilio, aby nawiązać połączenie ze strony sieci web hostowanej na platformie Azure. Wynikowa aplikacji monituje użytkownika o nawiązać połączenie za pomocą podanej liczbie i wiadomości, jak pokazano na poniższym zrzucie ekranu.

![Formularz wywołania platformy Azure przy użyciu usługi Twilio i platformy ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Wymagania wstępne
Należy wykonać następujące czynności, aby użyć kodu w tym temacie:

1. Uzyskać konta usługi Twilio i uwierzytelniania tokenu z [usługi Twilio Console][twilio_console]. Aby zacząć korzystać z usługi Twilio, zarejestruj się w [https://www.twilio.com/try-twilio][try_twilio]. Będziesz w stanie ocenić cennik w [http://www.twilio.com/pricing][twilio_pricing]. Aby uzyskać informacje o interfejsie API dostarczonych przez usługi Twilio, zobacz [http://www.twilio.com/voice/api][twilio_api].
2. Dodaj *biblioteki .NET usługi Twilio* do roli sieci web. Zobacz **do dodania do projektu roli sieci web biblioteki usługi Twilio**w dalszej części tego tematu.

Należy się zapoznać z tworzeniem podstawowego [roli sieci Web na platformie Azure][azure_webroles_get_started].

## <a name="howtocreateform"></a>Porady: Tworzenie formularza sieci web nawiązywania połączenia
<a id="use_nuget"></a>Aby dodać biblioteki usługi Twilio do projektu roli sieci web:

1. Otwórz rozwiązanie w programie Visual Studio.
2. Kliknij prawym przyciskiem myszy **odwołania**.
3. Kliknij przycisk **Zarządzaj pakietami NuGet**.
4. Kliknij przycisk **Online**.
5. W polu wyszukiwania online, wpisz *usługi twilio*.
6. Kliknij przycisk **zainstalować** w pakiecie usługi Twilio.

Poniższy kod przedstawia sposób tworzenia formularzy sieci web do pobierania danych użytkownika do nawiązywania połączenia. W tym przykładzie roli sieci Web programu ASP.NET o nazwie **TwilioCloud** jest tworzony.

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

## <a id="howtocreatecode"></a>Porady: tworzenie kodu do wywoływania
Następujący kod, który jest wywoływany, gdy użytkownik zamyka formularz, tworzy komunikat wywołania i generuje wywołania. W tym przykładzie kodu uruchamianego w program obsługi zdarzeń przycisk w formularzu. (Użyj swojego konta usługi Twilio i uwierzytelniania tokenu zamiast wartości symbolu zastępczego, przypisane do `accountSID` i `authToken` w poniższym kodzie.)

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
            // Call porcessing happens here.

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
                var url = $"http://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
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

Połączenie jest nawiązywane, a punkt końcowy usługi Twilio, wersja interfejsu API i stan wywołania są wyświetlane. Poniższy zrzut ekranu przedstawia Uruchom przykładowe dane wyjściowe.

![Odpowiedź wywołania platformy Azure przy użyciu usługi Twilio i platformy ASP.NET][twilio_dotnet_basic_form_output]

Więcej informacji na temat TwiML można znaleźć w folderze [http://www.twilio.com/docs/api/twiml][twiml]. Więcej informacji na temat &lt;powiedzieć&gt; i inne usługi Twilio zlecenia można znaleźć w folderze [http://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a id="nextsteps"></a>Następne kroki
Ten kod został podany pokazanie podstawowe funkcje w roli sieci web platformy ASP.NET na platformie Azure przy użyciu usługi Twilio. Przed wdrożeniem na platformie Azure w środowisku produkcyjnym, możesz dodać więcej obsługi błędów lub innych funkcji. Na przykład:

* Zamiast za pomocą formularza sieci web, można użyć magazynu obiektów Blob platformy Azure lub wystąpienie bazy danych SQL Azure do przechowywania numerów telefonów i wywołać tekstu. Aby dowiedzieć się, jak za pomocą obiektów blob platformy Azure, zobacz [jak używać usługi magazynu obiektów Blob platformy Azure w programie .NET][howto_blob_storage_dotnet]. Aby uzyskać informacje dotyczące korzystania z bazy danych SQL, zobacz [jak używać usługi Azure SQL Database w aplikacjach .NET][howto_sql_azure_dotnet].
* Można użyć `RoleEnvironment.getConfigurationSettings` można pobrać usługi Twilio Identyfikatora konta oraz uwierzytelniania token z danym wdrożeniu ustawień konfiguracji, zamiast kodować wartości w formularzu. Aby uzyskać informacje o `RoleEnvironment` , zobacz [Namespace Microsoft.WindowsAzure.ServiceRuntime][azure_runtime_ref_dotnet].
* Zapoznaj się ze wskazówkami zabezpieczeń usługi Twilio w [https://www.twilio.com/docs/security][twilio_docs_security].
* Dowiedz się więcej na temat usługi Twilio w [https://www.twilio.com/docs][twilio_docs].

## <a name="seealso"></a>Zobacz też
* [Jak używać usługi Twilio głosu i SMS funkcji z platformy Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
