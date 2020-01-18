---
title: 'Samouczek: połączona usługa czołowa'
titleSuffix: Azure Cognitive Services
description: Utwórz aplikację systemu Windows, która korzysta z usługi Cognitive Services twarzy, aby wykrywać funkcje twarze w obrazie.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: ghogen
ms.openlocfilehash: e0fe92fc7f19c3c899bcccfa9f9cc18029af049c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170235"
---
# <a name="connect-to-the-face-service-by-using-connected-services-in-visual-studio"></a>Nawiązywanie połączenia z usługą kroju przy użyciu usług połączonych w programie Visual Studio

Za pomocą usługi Azure twarzy możesz wykrywać, analizować, organizować i oznakować twarze na zdjęciach.

Ten artykuł i jego artykuły towarzyszące zawierają szczegółowe informacje dotyczące korzystania z funkcji usługi połączonej programu Visual Studio dla usługi. Ta funkcja jest dostępna w programie Visual Studio 2017 15.7 lub nowszym z zainstalowanym rozszerzeniem usługi Cognitive Services.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli jej nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).
- Program Visual Studio 2017 w wersji 15,7 lub nowszej z zainstalowanym obciążeniem **programowaniem w sieci Web** . [Pobierz go teraz](https://www.visualstudio.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-face"></a>Tworzenie projektu i Dodawanie obsługi funkcji rozpoznawania

1. Utwórz nowy projekt internetowy ASP.NET Core. Użyj szablonu Pusty projekt. 

1. W **Eksploratorze rozwiązań** wybierz pozycję **Dodaj** > **Usługa połączona**.
   Zostanie wyświetlona strona Usługa połączona zawierająca usługi, które możesz dodać do projektu.

   ![Element menu Dodaj > Usługa połączona](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. Z menu dostępnych usług wybierz pozycję **Interfejs API rozpoznawania twarzy usług Cognitive Services**.

   ![Wybieranie usługi, z którą ma zostać nawiązane połączenie](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Jeśli logowanie do programu Visual Studio zostało już wykonane i masz subskrypcję platformy Azure skojarzoną z kontem, zostanie wyświetlona strona z listą rozwijaną zawierającą Twoje subskrypcje.

   ![Wybierz swoją subskrypcję](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz nazwę usługi kroju lub wybierz łącze Edytuj, aby zmodyfikować automatycznie wygenerowaną nazwę, wybierz grupę zasobów i warstwę cenową.

   ![Edytowanie szczegółów usługi połączonej](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Skorzystaj z linku, aby wyświetlić szczegóły warstw cenowych.

1. Wybierz przycisk Dodaj, aby dodać obsługę usługi połączonej.
   Program Visual Studio modyfikuje projekt, aby dodać pakiety NuGet, wpisy pliku konfiguracji i inne zmiany w celu obsługi połączenia z usługą kroju.

## <a name="use-the-face-service-to-detect-attributes-of-faces-in-an-image"></a>Korzystanie z usługi twarzy do wykrywania atrybutów powierzchni w obrazie

1. Dodaj następujące instrukcje using w pliku Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Dodaj pole configuration oraz konstruktor, który inicjuje to pole w klasie Startup, aby włączyć konfigurację w programie.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. W folderze wwwroot projektu dodaj folder images i dodaj plik obrazu do folderu wwwroot. Przykładowo można użyć jednego z obrazów na [stronie do](https://azure.microsoft.com/services/cognitive-services/face/) Azure Portal. Kliknij prawym przyciskiem myszy jeden z obrazów, Zapisz na lokalnym dysku twardym, a następnie w Eksplorator rozwiązań kliknij prawym przyciskiem myszy folder obrazy, a następnie wybierz polecenie **dodaj** > **istniejący element** , aby dodać go do projektu. Twój projekt w Eksploratorze rozwiązań powinien wyglądać podobnie do następującego:
 
   ![Folder images z plikiem obrazu](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Kliknij prawym przyciskiem myszy plik obrazu, wybierz polecenie Właściwości, a następnie wybierz pozycję **Kopiuj, jeśli nowszy**.

   ![Kopiuj, jeśli nowszy](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Zastąp metodę Configure następującym kodem, aby uzyskać dostęp do usługi kroju i przetestować obraz. Zmień ciąg imagePath tak, aby wskazywał poprawną ścieżkę i nazwę pliku obrazu twarzy.

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
    Kod w tym kroku tworzy żądanie HTTP z wywołaniem interfejsu API REST platformy Front przy użyciu klucza dodanego podczas dodawania połączonej usługi.

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

1. Uruchom aplikację sieci Web i zobacz, co znajduje się w obrazie.
 
   ![Obraz usługi kroju i sformatowane wyniki](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie jest już potrzebna, usuń ją. Spowoduje to usunięcie usługi poznawczej i powiązanych zasobów. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat usługi kroju, odczytując [dokumentację dotyczącą](Overview.md)programu.
