---
title: Usługa połączona programu Visual Studio — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Nawiązuj połączenia z interfejsem API przetwarzania obrazów z poziomu aplikacji internetowej ASP.NET Core za pomocą funkcji usługi połączonej programu Visual Studio.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: 148f94410f6acb421d352b68b6f1ecb305a6b16a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235947"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Nawiązywanie połączenia z interfejsem API przetwarzania obrazów za pomocą usług połączonych w programie Visual Studio

W tym artykule i artykułach powiązanych podano szczegółowe informacje na temat używania funkcji usług połączonych programu Visual Studio na potrzeby interfejsu API przetwarzania obrazów usług Cognitive Services. Ta funkcja jest dostępna w programie Visual Studio 2017 15.7 lub nowszym z zainstalowanym rozszerzeniem usługi Cognitive Services.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli jej nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).
- Program **Visual Studio 2017 w wersji 15.7** z zainstalowanym pakietem roboczym **Tworzenie aplikacji internetowych**. [Pobierz go teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Dodawanie obsługi interfejsu API przetwarzania obrazów usług Cognitive Services do projektu

1. Utwórz nowy projekt internetowy ASP.NET Core. Użyj szablonu Pusty projekt. 

1. W **Eksploratorze rozwiązań** wybierz pozycję **Dodaj** > **Usługa połączona**.
   Zostanie wyświetlona strona Usługa połączona zawierająca usługi, które możesz dodać do projektu.

   ![Kliknij prawym przyciskiem myszy menu dla projektu programu Visual Studio: Dodaj > Usługa połączona](../media/vs-common/Connected-Service-Menu.PNG)

1. Z menu dostępnych usług wybierz **Interfejs API przetwarzania obrazów usług Cognitive Services**.

   ![Menu połączonej usługi: Analizuj obrazy... jest opisany](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Jeśli logowanie do programu Visual Studio zostało już wykonane i masz subskrypcję platformy Azure skojarzoną z kontem, zostanie wyświetlona strona z listą rozwijaną zawierającą Twoje subskrypcje.

   ![Okno interfejsu API przetwarzania komputera z wyróżnioną listę rozwijaną subskrypcji](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Wybierz subskrypcję, której chcesz użyć, i nazwę interfejsu API przetwarzania obrazów albo link Edytuj, aby zmodyfikować nazwę wygenerowaną automatycznie oraz wybrać grupę zasobów i warstwę cenową.

   ![Edytowanie szczegółów usługi połączonej](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Skorzystaj z linku, aby wyświetlić szczegóły warstw cenowych.

1. Wybierz przycisk Dodaj, aby dodać obsługę usługi połączonej.
   Program Visual Studio zmodyfikuje projekt, dodając pakiety NuGet i wpisy pliku konfiguracji oraz wprowadzając inne zmiany na potrzeby obsługi połączenia z interfejsem API przetwarzania obrazów. Okno Dane wyjściowe pokazuje dziennik działań związanych z projektem. Powinna zostać wyświetlona zawartość podobna do tej:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Wykrywanie atrybutów obrazu za pomocą interfejsu API przetwarzania obrazów

1. Dodaj następujące instrukcje using w pliku Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Dodaj pole configuration oraz konstruktor, który inicjuje to pole, w klasie `Startup`, aby włączyć konfigurację w programie.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. W folderze wwwroot projektu dodaj folder images i dodaj plik obrazu do folderu wwwroot. Na potrzeby przykładu możesz użyć jednego z obrazów na tej [stronie interfejsu API przetwarzana obrazów](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Kliknij prawym przyciskiem myszy na jeden z obrazów, Zapisz na lokalnym dysku twardym, a następnie w oknie Eksploratora rozwiązań, kliknij prawym przyciskiem myszy na folder Obrazy i wybierz **Dodaj** > **istniejący element** Aby dodać go do projektu. Twój projekt w Eksploratorze rozwiązań powinien wyglądać podobnie do następującego: 
  
   ![Zrzut ekranu przedstawiający widok eksploratora rozwiązań z zaznaczonym plikiem obrazu](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Kliknij prawym przyciskiem myszy plik obrazu, wybierz polecenie Właściwości, a następnie wybierz pozycję **Kopiuj, jeśli nowszy**. 

   ![Okno właściwości obrazu; Kopiuj do katalogu wyjściowego zestawu do skopiowania, jeśli nowszy](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Zastąp metodę Configure następującym kodem, aby uzyskać dostęp do interfejsu API przetwarzania obrazów i przetestować obraz.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        // TODO: Change this to your image's path on your site. 
        string imagePath = @"images/subway.png";

        // Enable static files such as image files. 
        app.UseStaticFiles();

        string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
        string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

        HttpClient client = new HttpClient();

        // Request headers.
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

        // Request parameters. A third optional parameter is "details".
        string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

        // Assemble the URI for the REST API Call.
        string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

        HttpResponseMessage response;

        // Request body. Posts an image you've added to your site's images folder. 
        var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
        byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

        string contentString = string.Empty;
        using (ByteArrayContent content = new ByteArrayContent(byteData))
        {
            // This example uses content type "application/octet-stream".
            // The other content types you can use are "application/json" and "multipart/form-data".
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

            // Execute the REST API call.
            response = client.PostAsync(uri, content).Result;

            // Get the JSON response.
            contentString = response.Content.ReadAsStringAsync().Result;
        }

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
            await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
            await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
            await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
            await context.Response.WriteAsync("<p>");
            await context.Response.WriteAsync(JsonPrettyPrint(contentString));
            await context.Response.WriteAsync("<p>");
        });
    }
   ```

    Ten kod tworzy żądanie HTTP zawierające identyfikator URI oraz obraz jako zawartość binarną na potrzeby wywołania interfejsu API REST przetwarzania obrazów.

1. Dodaj funkcje pomocnicze GetImageAsByteArray i JsonPrettyPrint.

   ```csharp
    /// <summary>
    /// Returns the contents of the specified file as a byte array.
    /// </summary>
    /// <param name="imageFilePath">The image file to read.</param>
    /// <returns>The byte array of the image data.</returns>
    static byte[] GetImageAsByteArray(string imageFilePath)
    {
        FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }

    /// <summary>
    /// Formats the given JSON string by adding line breaks and indents.
    /// </summary>
    /// <param name="json">The raw JSON string to format.</param>
    /// <returns>The formatted JSON string.</returns>
    static string JsonPrettyPrint(string json)
    {
        if (string.IsNullOrEmpty(json))
            return string.Empty;

        json = json.Replace(Environment.NewLine, "").Replace("\t", "");

        string INDENT_STRING = "    ";
        var indent = 0;
        var quoted = false;
        var sb = new StringBuilder();
        for (var i = 0; i < json.Length; i++)
        {
            var ch = json[i];
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case '}':
                case ']':
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    sb.Append(ch);
                    break;
                case '"':
                    sb.Append(ch);
                    bool escaped = false;
                    var index = i;
                    while (index > 0 && json[--index] == '\\')
                        escaped = !escaped;
                    if (!escaped)
                        quoted = !quoted;
                    break;
                case ',':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case ':':
                    sb.Append(ch);
                    if (!quoted)
                        sb.Append(" ");
                    break;
                default:
                    sb.Append(ch);
                    break;
            }
        }
        return sb.ToString();
    }
   ```

1. Uruchom aplikację internetową i zobacz, co interfejs API przetwarzania obrazów znalazł na obrazie.

   ![Obraz interfejsu API przetwarzania obrazów oraz sformatowane wyniki](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie jest już potrzebna, usuń ją. Spowoduje to usunięcie usługi poznawczej i powiązanych zasobów. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat interfejsu API przetwarzania obrazów, czytając [dokumentacji interfejsu API Computer Vision](Home.md).
