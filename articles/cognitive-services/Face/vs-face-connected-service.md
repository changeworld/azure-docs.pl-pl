---
title: Samouczek interfejsu API języka C# twarzy | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Utwórz prostą aplikację Windows, która używa Cognitive Services Face API, aby wykryć funkcji twarzy na obrazie.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ghogen
ms.openlocfilehash: b51760f889db27aa25e54582070ee7d3adcf66f8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665241"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Łączenie z usługą Cognitive Services Face API przy użyciu usług połączonych programu Visual Studio

Za pomocą Cognitive Services Face API, można wykryć, analizowanie, organizowanie i tagowanie twarzy na zdjęciach.

W tym artykule i umieszczanych tam artykułach pomocnika należy podać szczegóły dotyczące korzystania z funkcji usługi Visual Studio połączonych dla Cognitive Services Face API. Ta funkcja jest dostępna w obu 15.7 2017 usługi Visual Studio lub nowszej, za pomocą zainstalowane rozszerzenie usług Cognitive Services.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli jej nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 w wersji 15.7** z **programowania dla sieci Web** zainstalowanym obciążeniem. [Pobierz teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>Tworzenie projektu i dodanie obsługi usługi Cognitive Services Face API

1. Utwórz nowy projekt sieci web platformy ASP.NET Core. Użyj szablonu pusty projekt. 

1. W **Eksploratora rozwiązań**, wybierz **Dodaj** > **podłączoną usługę**.
   Na stronie usługi połączonej pojawia się z usługami, które można dodać do projektu.

   ![Dodawanie pozycji menu usługi połączonej](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. W menu dostępnych usług wybierz **Cognitive Services Face API**.

   ![Wybierz usługę, aby nawiązać połączenie](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Jeśli po zalogowaniu się do programu Visual Studio i subskrypcji platformy Azure skojarzony z Twoim kontem, zostanie wyświetlona strona, która za pomocą listy rozwijanej z subskrypcjami.

   ![Wybierz swoją subskrypcję](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz nazwę interfejsu API rozpoznawania twarzy, lub wybierz link edycji, aby zmodyfikować automatycznie wygenerowaną nazwę, wybierz grupę zasobów i warstwy cenowej.

   ![Edytuj szczegóły usługi połączonej](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Kliknij link, aby szczegóły na warstw cenowych.

1. Kliknij przycisk Dodaj, aby dodać obsługiwane usługi połączone.
   Visual Studio modyfikuje projekt, aby dodać pakiety NuGet, wpisy w pliku konfiguracji i inne zmiany do obsługi połączenia interfejsu API rozpoznawania twarzy.

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>Wykrywaj atrybutów twarzy w obrazie za pomocą interfejsu API rozpoznawania twarzy

1. Dodaj następujące instrukcje using w pliku Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Dodaj pole konfiguracji, a następnie dodaj konstruktor, który inicjuje w polu configuration w klasie uruchamiania, aby włączyć konfigurację w programie.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. W folderze wwwroot w projekcie Dodaj folder obrazów, a następnie dodaj plik obrazu do folderu wwwroot. Na przykład można użyć jednego z obrazów w tym [strony interfejsu API rozpoznawania twarzy](https://azure.microsoft.com/services/cognitive-services/face/). Kliknij prawym przyciskiem myszy na jeden z obrazów, Zapisz na lokalnym dysku twardym, a następnie w oknie Eksplorator rozwiązań kliknij prawym przyciskiem myszy folder obrazów i choosee **Dodaj** > **istniejący element** Aby dodać go do projektu. Projekt powinien wyglądać następująco w Eksploratorze rozwiązań:
 
   ![folder obrazów z pliku obrazu](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Kliknij prawym przyciskiem myszy plik obrazu, wybierz polecenie Właściwości, a następnie wybierz **Kopiuj Jeśli nowszy**.

   ![Kopiuj Jeśli nowszy](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Zastąp metodę Konfiguruj następujący kod, aby dostęp do interfejsu API rozpoznawania twarzy i testowanie obrazu. Zmień ciąg imagePath poprawną ścieżkę i nazwę pliku obrazu twarzy.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    Kod w tym kroku tworzy żądanie HTTP przy użyciu wywołania interfejsu API REST rozpoznawania twarzy, za pomocą klucza, którą dodałeś po dodaniu usługi połączonej.

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

1. Uruchom aplikację sieci web i zobacz, co znaleziono interfejsu API rozpoznawania twarzy na obrazie.
 
   ![Interfejs API rozpoznawania twarzy, obrazu i sformatowane wyniki](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów. Spowoduje to usunięcie usługi cognitive Services i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat interfejsu API rozpoznawania twarzy, czytając [dokumentacji interfejsu API rozpoznawania twarzy](Overview.md).
