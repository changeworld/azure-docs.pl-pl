---
title: Jak używać usługi poczty e-mail SendGrid (.NET) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysłać wiadomość e-mail z usługi poczty e-mail SendGrid na platformie Azure. Przykłady kodu napisane w języku C# i używają interfejsu API platformy .NET.
services: ''
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: dx@sendgrid.com
ms.openlocfilehash: a5f07d02bfe4032d77a17e5972b88f6530125f28
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2017
ms.locfileid: "27534859"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Sposób wysyłania poczty E-mail przy użyciu SendGrid z platformy Azure
## <a name="overview"></a>Przegląd
W tym przewodniku przedstawiono sposób wykonywania typowych zadań programowania usługi poczty e-mail SendGrid na platformie Azure. Przykłady są napisane w języku C\# i obsługuje standardowe 1.3 .NET. Omówione scenariusze obejmują konstruowania poczty e-mail, wysyłania wiadomości e-mail, dodawanie załączników i włączanie różnych poczty i ustawienia śledzenia. Aby uzyskać więcej informacji na SendGrid i wysyłania wiadomości e-mail, zobacz [następne kroki] [ Next steps] sekcji.

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa SendGrid poczty E-mail?
Jest SendGrid [e-mail opartej na chmurze usługa] zapewnia niezawodne [dostarczania transakcyjnych wiadomości e-mail], skalowalności i analiz w czasie rzeczywistym oraz elastyczne interfejsów API, które umożliwiają łatwe niestandardowej integracji. Typowe przypadki użycia SendGrid obejmują:

* Automatycznego wysyłania potwierdzeń lub potwierdzenia zakupu do klientów.
* Administrowanie dystrybucji wymieniono wysyłania klientów miesięczne ulotki i promocji.
* Zbieranie metryk w czasie rzeczywistym dla zablokowanych poczty e-mail i zaangażowania klientów.
* Przekazywanie zapytania klientów.
* Przetwarzanie przychodzących wiadomości e-mail.

Aby uzyskać więcej informacji, odwiedź stronę [https://sendgrid.com](https://sendgrid.com) lub jego SendGrid [biblioteki C#] [ sendgrid-csharp] repozytorium GitHub.

## <a name="create-a-sendgrid-account"></a>Utwórz konto SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Odwołanie w bibliotece klas programu .NET SendGrid
[Pakietu SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) jest najprostszym sposobem, można pobrać interfejsu API SendGrid i skonfigurowania aplikacji ze wszystkich zależności. NuGet to rozszerzenie programu Visual Studio, dołączone do programu Microsoft Visual Studio 2015 i powyżej, który ułatwia instalacji i aktualizacji biblioteki i narzędzia.

> [!NOTE]
> Aby zainstalować NuGet, jeśli używasz programu Visual Studio w wersji starszej niż Visual Studio 2015, odwiedź [http://www.nuget.org](http://www.nuget.org)i kliknij przycisk **instalowania NuGet** przycisku.
>
>

Aby zainstalować pakiet NuGet włączenie w aplikacji, wykonaj następujące czynności:

1. Polecenie **nowy projekt** i wybierz **szablonu**.

   ![Tworzenie nowego projektu][create-new-project]
2. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **odwołania**, następnie kliknij przycisk **Zarządzaj pakietami NuGet**.

   ![Pakiet SendGrid NuGet][SendGrid-NuGet-package]
3. Wyszukaj **SendGrid** i wybierz **SendGrid** element na liście wyników.
4. Wybierz z listy rozwijanej wersji, aby mieć możliwość pracy z modelem obiektów najnowsza stabilna wersja pakietu Nuget i interfejsów API przedstawiona w tym artykule.

   ![Pakietu SendGrid][sendgrid-package]
5. Kliknij przycisk **zainstalować** do ukończenia instalacji, a następnie zamknij to okno dialogowe.

Biblioteka klas programu .NET w SendGrid jest nazywany **SendGrid**. Ten przewodnik zawiera następujące przestrzenie nazw:

* **SendGrid** do komunikowania się z jego SendGrid interfejsu API.
* **SendGrid.Helpers.Mail** dla metody pomocnicze łatwe tworzenie SendGridMessage obiektów, które określają sposób wysyłania wiadomości e-mail.

Dodaj następujące deklaracje przestrzeni nazw kod na początku dowolnego pliku C# ma do uzyskania programowego dostępu do usługi poczty e-mail SendGrid.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Porady: Tworzenie wiadomości E-mail
Użyj **SendGridMessage** obiekt, aby utworzyć wiadomości e-mail. Po utworzeniu obiektu wiadomości, można ustawić właściwości i metod, w tym nadawcą wiadomości e-mail adresata poczty e-mail oraz temat i treść wiadomości e-mail.

Poniższy przykład przedstawia sposób tworzenia obiektu całkowicie wypełnione poczty e-mail:

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

Aby uzyskać więcej informacji na temat wszystkich właściwości i metod obsługiwanych przez **SendGrid** typu, zobacz [sendgrid csharp] [ sendgrid-csharp] w witrynie GitHub.

## <a name="how-to-send-an-email"></a>Porady: wysyłanie wiadomości E-mail
Po utworzeniu wiadomości e-mail, możesz wysłać go przy użyciu interfejsu API w SendGrid. Alternatywnie można użyć [. Utworzony przez sieć w bibliotece][NET-library].

Wysyłanie poczty e-mail wymaga podania klucza interfejsu API SendGrid. Aby uzyskać szczegółowe informacje o sposobie konfigurowania klucze interfejsu API, odwiedź stronę klucze interfejsu API w SendGrid [dokumentacji][documentation].

Te poświadczenia mogą być przechowywane za pośrednictwem portalu Azure, klikając ustawienia aplikacji i dodawanie pary klucz wartość w ustawieniach aplikacji.

 ![Ustawienia aplikacji Azure][azure_app_settings]

 Następnie użytkownik może uzyskiwać do nich dostęp w następujący sposób:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Poniższe przykłady przedstawiają sposób wysyłania wiadomości e-mail z aplikacji konsoli przy użyciu interfejsu API sieci Web SendGrid.

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Porady: wysyłanie wiadomości e-mail z ASP .NET Core interfejsu API przy użyciu klasy MailHelper

Poniższym przykładzie może służyć do wysyłania jednej wiadomości e-mail do wielu osób z przy użyciu interfejsu API programu ASP .NET Core `MailHelper` klasy `SendGrid.Helpers.Mail` przestrzeni nazw. W tym przykładzie użyto ASP .NET Core 1.0. 

W tym przykładzie klucz interfejsu API przechowywanych w `appsettings.json` pliku, który może zostać zastąpiona w portalu Azure, jak pokazano w powyższych przykładach.

Zawartość `appsettings.json` plik powinien być podobny do:

    {
       "Logging": {
       "IncludeScopes": false,
       "LogLevel": {
       "Default": "Debug",
       "System": "Information",
       "Microsoft": "Information"
         }
       },
     "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }

Najpierw należy dodać poniższego kodu w `Startup.cs` pliku projektu interfejsu API programu .NET Core. Jest to wymagane, dzięki czemu możemy dostęp `SENDGRID_API_KEY` z `appsettings.json` pliku przy użyciu iniekcji zależności w kontrolerze interfejsu API. `IConfiguration` Interfejsu mogą zostać dodane w Konstruktorze kontrolera po dodaniu go w `ConfigureServices` metody poniżej. Zawartość `Startup.cs` pliku wygląda podobnie do następującej po dodaniu wymagane kodu:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

Na kontrolerze, po wstrzyknięcie `IConfiguration` interfejsu, możemy użyć `CreateSingleEmailToMultipleRecipients` metody `MailHelper` klasę, aby wysłać wiadomość e-mail z jednego do wielu adresatów. Metoda przyjmuje jeden dodatkowy parametr logiczną o nazwie `showAllRecipients`. Ten parametr może służyć do formantu, czy adresatów wiadomości e-mail będą mogli zobaczyć nich adres e-mail w sekcji do nagłówka wiadomości e-mail. Przykładowy kod kontroler powinien być podobny do poniżej 

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using SendGrid;
    using SendGrid.Helpers.Mail;
    using Microsoft.Extensions.Configuration;

    namespace SendgridMailApp.Controllers
    {
        [Route("api/[controller]")]
        public class NotificationController : Controller
        {
           private readonly IConfiguration _configuration;

           public NotificationController(IConfiguration configuration)
           {
             _configuration = configuration;
           }      
        
           [Route("SendNotification")]
           public async Task PostMessage()
           {
              var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
              var client = new SendGridClient(apiKey);
              var from = new EmailAddress("test1@example.com", "Example User 1");
              List<EmailAddress> tos = new List<EmailAddress>
              {
                  new EmailAddress("test2@example.com", "Example User 2"),
                  new EmailAddress("test3@example.com", "Example User 3"),
                  new EmailAddress("test4@example.com","Example User 4")
              };
            
              var subject = "Hello world email from Sendgrid ";
              var htmlContent = "<strong>Hello world with HTML content</strong>";
              var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
              var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
              var response = await client.SendEmailAsync(msg);
          }
       }
    }
    
## <a name="how-to-add-an-attachment"></a>Porady: Dodawanie załącznika
Można dodać załączników do wiadomości przez wywołanie metody **AddAttachment** — metoda i minimalny zestaw określania nazwy pliku i kodowany w standardzie Base64 zawartości możesz chcesz dołączyć. Może zawierać wiele załączników przez wywołanie tej metody, gdy dla każdego pliku chcesz dołączyć lub za pomocą **AddAttachments** metody. W poniższym przykładzie pokazano, dodawanie załączników do wiadomości:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Porady: Użyj ustawienia poczty, aby włączyć stopki, śledzenia i analiza
SendGrid udostępnia funkcje dodatkowe poczty e-mail przy użyciu ustawienia śledzenia i ustawienia poczty. Te ustawienia można dodać do wiadomości e-mail, aby włączyć określonych funkcji, takich jak kliknij śledzenia, Google analytics, subskrypcji, śledzenia i tak dalej. Aby uzyskać pełną listę aplikacji, zobacz [dokumentacji ustawienia][settings-documentation].

Aplikacje mogą być stosowane do **SendGrid** przy użyciu metod zaimplementowanych jako część wiadomości e-mail **SendGridMessage** klasy. Poniższe przykłady pokazują, stopka i kliknij przycisk śledzenia filtrów:

Poniższe przykłady pokazują, stopka i kliknij przycisk śledzenia filtrów:

### <a name="footer-settings"></a>Ustawienia stopki
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Kliknij przycisk śledzenia
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Porady: Użyj SendGrid dodatkowych usług
SendGrid oferuje kilka interfejsów API i elementów webhook, który można wykorzystać dodatkowe funkcje aplikacji Azure. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy usługi poczty E-mail SendGrid, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* SendGrid C\# repozytorium biblioteki: [sendgrid csharp][sendgrid-csharp]
* Dokumentacja interfejsu API SendGrid: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[e-mail opartej na chmurze usługa]: https://sendgrid.com/solutions
[dostarczania transakcyjnych wiadomości e-mail]: https://sendgrid.com/use-cases/transactional-email

