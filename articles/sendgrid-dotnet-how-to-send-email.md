---
title: Jak używać usługi e-mail SendGrid (.NET) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać wiadomości e-mail za pomocą usługi e-mail SendGrid na platformie Azure. Przykłady kodu napisane w języku C# i za pomocą interfejsu API platformy .NET.
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
ms.openlocfilehash: 91d28802b4af23da5b8060fa7c8f9a7e843a7dab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444883"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Jak wysyłać pocztę E-mail przy użyciu platformy Azure za pomocą usługi SendGrid
## <a name="overview"></a>Omówienie
Ten przewodnik pokazuje sposób wykonywania typowych zadań programistycznych za pomocą usługi e-mail SendGrid na platformie Azure. Przykłady są napisane w języku C\# i obsługuje .NET Standard 1.3. Omówione scenariusze obejmują konstruowanie poczty e-mail, wysyłania wiadomości e-mail, dodawanie załączników i włączanie różnych poczty i ustawienia śledzenia. Aby uzyskać więcej informacji na temat usługi SendGrid i wysyłania wiadomości e-mail, zobacz [następne kroki] [ Next steps] sekcji.

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa SendGrid poczty E-mail?
Usługa SendGrid jest [usługa oparta na chmurze poczty e-mail] zapewniająca niezawodne [dostarczania wiadomości e-mail dotyczącej transakcji], skalowalność i analizę w czasie rzeczywistym oraz udostępnia elastyczne interfejsy API, które umożliwiają łatwe niestandardową integrację. Typowe przypadki użycia usługi SendGrid, obejmują:

* Automatyczne wysyłanie potwierdzenia lub potwierdzenia zakupu do klientów.
* Administrowanie dystrybucji wymieniono wysyłania klientom miesięczne rozsyłanych materiałach marketingowych firmowych i promocji.
* Zbieranie metryk czasu rzeczywistego, takich jak zablokowane wiadomości e-mail i zaangażowania użytkowników.
* Przekazywanie zapytania klientów.
* Przetwarzanie przychodzących wiadomości e-mail.

Aby uzyskać więcej informacji, odwiedź stronę [ https://sendgrid.com ](https://sendgrid.com) lub SendGrid [bibliotekę języka C#] [ sendgrid-csharp] repozytorium GitHub.

## <a name="create-a-sendgrid-account"></a>Utwórz konto usługi SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Odwołanie do biblioteki klas platformy .NET usługi SendGrid
[Pakietu NuGet usługi SendGrid](https://www.nuget.org/packages/Sendgrid) jest najprostszym sposobem uzyskania interfejsu API usługi SendGrid i skonfigurowania aplikacji ze wszystkimi zależnościami. To rozszerzenie programu Visual Studio, dołączone do programu Microsoft Visual Studio 2015 i powyżej, które można łatwo zainstalować i zaktualizować biblioteki i narzędzia NuGet.

> [!NOTE]
> Aby zainstalować NuGet, jeśli używasz programu Visual Studio w wersji wcześniejszej niż Visual Studio 2015, odwiedź stronę [ https://www.nuget.org ](https://www.nuget.org)i kliknij przycisk **instalacji NuGet** przycisku.
>
>

Aby zainstalować pakiet NuGet usługi SendGrid w aplikacji, wykonaj następujące czynności:

1. Kliknij pozycję **nowy projekt** i wybierz **szablonu**.

   ![Tworzenie nowego projektu][create-new-project]
2. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **odwołania**, następnie kliknij przycisk **Zarządzaj pakietami NuGet**.

   ![Pakiet NuGet usługi SendGrid][SendGrid-NuGet-package]
3. Wyszukaj **SendGrid** i wybierz **SendGrid** elementu na liście wyników.
4. Wybierz najnowszą stabilną wersję pakietu Nuget z listy rozwijanej wersji, aby można było pracować z modelem obiektów i interfejsów API przedstawiona w tym artykule.

   ![Pakiet usługi SendGrid][sendgrid-package]
5. Kliknij przycisk **zainstalować** można ukończyć instalację, a następnie zamknij to okno dialogowe.

Nosi nazwę biblioteki klas platformy .NET usługi SendGrid **SendGrid**. Zawiera on następujące przestrzenie nazw:

* **Usługa SendGrid** do komunikowania się z interfejsem API usługi SendGrid.
* **SendGrid.Helpers.Mail** dla metody pomocnika do łatwego tworzenia SendGridMessage obiekty, które określają sposób wysyłania wiadomości e-mail.

Dodaj następujące deklaracje przestrzeni nazw kod na początku każdego pliku C# w którym chcesz programowego dostępu do usługi e-mail SendGrid.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Instrukcje: Utwórz wiadomość E-mail
Użyj **SendGridMessage** obiekt, aby utworzyć wiadomość e-mail. Po utworzeniu obiektu wiadomości, można ustawić właściwości i metod, takich jak nadawcy wiadomości e-mail adresata poczty e-mail oraz tematu i treści wiadomości e-mail.

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

Aby uzyskać więcej informacji na temat wszystkich właściwości i metod obsługiwanych przez **SendGrid** typu, zobacz [csharp usługi sendgrid] [ sendgrid-csharp] w witrynie GitHub.

## <a name="how-to-send-an-email"></a>Instrukcje: Wyślij wiadomość E-mail
Po utworzeniu wiadomości e-mail, możesz wysłać zawartość przy użyciu interfejsu API usługi SendGrid. Alternatywnie można użyć [. NET firmy wbudowanych w bibliotece][NET-library].

Wysyłanie wiadomości e-mail wymaga podania klucza interfejsu API usługi SendGrid. Aby uzyskać szczegółowe informacje o sposobie konfigurowania klucze interfejsu API, odwiedź stronę klucze interfejsu API usługi SendGrid [dokumentacji][documentation].

Te poświadczenia mogą być przechowywane za pośrednictwem portalu Azure, klikając ustawienia aplikacji i dodawanie pary klucz/wartość w ustawieniach aplikacji.

 ![Ustawienia aplikacji platformy Azure][azure_app_settings]

 Następnie użytkownik może uzyskiwać do nich dostęp w następujący sposób:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Poniższe przykłady pokazują, jak wysyłać wiadomości e-mail za pomocą aplikacji konsolowej przy użyciu internetowego interfejsu API usługi SendGrid.

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
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Instrukcje: Wyślij wiadomość e-mail z ASP .NET Core interfejsu API za pomocą klasy MailHelper

Poniższym przykładzie można wysyłać jedną wiadomość e-mail do wielu osób z interfejsu API programu ASP .NET Core przy użyciu `MailHelper` klasy `SendGrid.Helpers.Mail` przestrzeni nazw. W tym przykładzie używamy ASP .NET Core 1.0. 

W tym przykładzie klucz interfejsu API zostały zapisane w `appsettings.json` pliku, który może zostać przesłonięta w witrynie Azure portal, jak pokazano w powyższych przykładach.

Zawartość `appsettings.json` plik powinien wyglądać podobnie do:

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

Najpierw musimy dodać poniższego kodu w `Startup.cs` pliku projektu interfejsu API platformy .NET Core. Jest to wymagane, dzięki czemu będziemy mogli `SENDGRID_API_KEY` z `appsettings.json` plików za pomocą iniekcji zależności w kontrolerze interfejsu API. `IConfiguration` Interfejs może zostać wprowadzona w Konstruktorze kontrolera po dodaniu go w `ConfigureServices` metody poniżej. Zawartość `Startup.cs` pliku wygląda podobnie do następującej, po dodaniu wymagany kod:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

Na kontrolerze, po wprowadza `IConfiguration` interfejsu, możemy użyć `CreateSingleEmailToMultipleRecipients` metody `MailHelper` klasy, aby wysyłać jedną wiadomość e-mail do wielu odbiorców. Metoda przyjmuje jeden dodatkowy parametr logiczny o nazwie `showAllRecipients`. Ten parametr służy do sterowania czy adresatów wiadomości e-mail będą mogli zobaczyć nich adres e-mail w sekcji na nagłówki wiadomości e-mail. Powinno być przykładowy kod dla kontrolera, takie jak niższe niż 

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
    
## <a name="how-to-add-an-attachment"></a>Instrukcje: Dodaj załącznik
Załączniki można dodawać do wiadomości, wywołując **AddAttachment** metody i co najmniej, określając nazwę pliku i zakodowane w formacie Base64 zawartości możesz chcesz dołączyć. Może zawierać wiele załączników, przez wywołanie tej metody, gdy dla każdego pliku chcesz podłączyć lub za pomocą **AddAttachments** metody. W poniższym przykładzie pokazano, dodawanie załącznika do wiadomości:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Instrukcje: Użyj ustawienia poczty e-mail, aby włączyć stopki, śledzenia i analizy
Usługa SendGrid umożliwia funkcje dodatkowe poczty e-mail przy użyciu ustawień wiadomości e-mail i śledzenia. Te ustawienia można dodać do wiadomości e-mail, aby włączyć określonych funkcji, takich jak śledzenie kliknięć, usługi Google analytics, subskrypcji, śledzenia i tak dalej. Aby uzyskać pełną listę aplikacji, zobacz [dokumentacji ustawienia][settings-documentation].

Aplikacje mogą być stosowane do **SendGrid** przy użyciu metod zaimplementowanych w ramach wiadomości e-mail **SendGridMessage** klasy. Poniższe przykłady pokazują stopki i kliknij śledzenia filtrów:

Poniższe przykłady pokazują stopki i kliknij śledzenia filtrów:

### <a name="footer-settings"></a>Ustawienia stopki
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Śledzenie kliknięć
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Instrukcje: Za pomocą usługi SendGrid dodatkowych usług
Usługa SendGrid oferuje kilka interfejsów API i elementy webhook, które można użyć, aby korzystać z dodatkowych funkcji w aplikacji platformy Azure. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API usługi SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy usługi E-mail SendGrid, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* SendGrid C\# repozytorium biblioteki: [csharp usługi sendgrid][sendgrid-csharp]
* Dokumentacja interfejsu API usługi SendGrid: <https://sendgrid.com/docs>

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

[Usługa oparta na chmurze poczty e-mail]: https://sendgrid.com/solutions
[dostarczania wiadomości e-mail dotyczącej transakcji]: https://sendgrid.com/use-cases/transactional-email

