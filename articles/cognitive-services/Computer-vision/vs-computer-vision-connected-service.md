---
title: Samouczek przetwarzania języka C# na komputerze | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Nawiąż połączenie przetwarzania obrazów w usłudze Cognitive Services z aplikacji sieci web programu ASP.NET Core.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 76ca1215144a5caa40971e1eda23f6462f7bf27b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665255"
---
# <a name="connecting-to-cognitive-services-computer-vision-api-by-using-connected-services-in-visual-studio"></a>Łączenie z usługą API przetwarzania obrazów w usłudze Cognitive Services przy użyciu usług połączonych programu Visual Studio

Za pomocą Cognitive Services interfejs API przetwarzania obrazów, możesz wyodrębniaj rozbudowane informacje w celu kategoryzowania i przetwarzania danych wizualnych oraz przeprowadzać wspomaganego maszynowo moderowania obrazów, aby ułatwić nadzorowanie swoich usług.

W tym artykule i umieszczanych tam artykułach pomocnika należy podać szczegóły dotyczące korzystania z funkcji usługi Visual Studio połączonych dla API przetwarzania obrazów w usłudze Cognitive Services. Ta funkcja jest dostępna w obu 15.7 2017 usługi Visual Studio lub nowszej, za pomocą zainstalowane rozszerzenie usług Cognitive Services.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli jej nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 w wersji 15.7** z **programowania dla sieci Web** zainstalowanym obciążeniem. [Pobierz teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Dodanie obsługi do projektu dla API przetwarzania obrazów w usłudze Cognitive Services

1. Utwórz nowy projekt sieci web platformy ASP.NET Core. Użyj szablonu pusty projekt. 

1. W **Eksploratora rozwiązań**, wybierz **Dodaj** > **podłączoną usługę**.
   Na stronie usługi połączonej pojawia się z usługami, które można dodać do projektu.

   ![Dodawanie pozycji menu usługi połączonej](../media/vs-common/Connected-Service-Menu.PNG)

1. W menu dostępnych usług wybierz **API przetwarzania obrazów w usłudze Cognitive Services**.

   ![Wybierz usługę, aby nawiązać połączenie](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Jeśli po zalogowaniu się do programu Visual Studio i subskrypcji platformy Azure skojarzony z Twoim kontem, zostanie wyświetlona strona, która za pomocą listy rozwijanej z subskrypcjami.

   ![Wybierz swoją subskrypcję](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz nazwę interfejsu API przetwarzania obrazów, lub wybierz link edycji, aby zmodyfikować automatycznie wygenerowaną nazwę, wybierz grupę zasobów i warstwy cenowej.

   ![Edytuj szczegóły usługi połączonej](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Kliknij link, aby szczegóły na warstw cenowych.

1. Kliknij przycisk Dodaj, aby dodać obsługiwane usługi połączone.
   Visual Studio modyfikuje projekt, aby dodać pakiety NuGet, wpisy w pliku konfiguracji i inne zmiany do obsługi połączenia interfejsu API przetwarzania obrazów. W oknie danych wyjściowych pokazuje dziennika, co się dzieje z projektem. Powinien zostać wyświetlony podobny do poniższego:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 1.0.2-preview.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Użyj interfejsu API przetwarzania obrazów, aby wykryć atrybuty obrazu

1. Dodaj następujące instrukcje using w pliku Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Dodaj pole konfiguracji i dodaj konstruktor, który inicjuje w polu configuration w `Startup` klasy, aby włączyć konfigurację w programach.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. W folderze wwwroot w projekcie Dodaj folder obrazów, a następnie dodaj plik obrazu do folderu wwwroot. Na przykład można użyć jednego z obrazów w tym [strony interfejsu API Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Kliknij prawym przyciskiem myszy na jeden z obrazów, Zapisz na lokalnym dysku twardym, a następnie w oknie Eksplorator rozwiązań kliknij prawym przyciskiem myszy folder obrazów i choosee **Dodaj** > **istniejący element** Aby dodać go do projektu. Projekt powinien wyglądać następująco w Eksploratorze rozwiązań: 
  
   ![folder obrazów z pliku obrazu](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Kliknij prawym przyciskiem myszy plik obrazu, wybierz polecenie Właściwości, a następnie wybierz **Kopiuj Jeśli nowszy**. 

   ![Kopiuj Jeśli nowszy](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Zastąp metodę Konfiguruj następujący kod, aby dostęp do interfejsu API przetwarzania obrazów i testowanie obrazu.

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
    Tutaj kod tworzy żądanie HTTP przy użyciu identyfikatora URI i obrazu jako zawartość binarna wywołania interfejsu API REST dla przetwarzania obrazów.

1. Dodawanie funkcji pomocnika GetImageAsByteArray i JsonPrettyPrint.

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

1. Uruchom aplikację sieci web i zobacz interfejs API przetwarzania obrazów znalezione w obrazie.

   ![Komputer interfejs API przetwarzania obrazów i sformatowane wyniki](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów. Spowoduje to usunięcie usługi cognitive Services i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat interfejsu API przetwarzania obrazów, czytając [dokumentacja dotycząca interfejsu API przetwarzania obrazów](Home.md).
