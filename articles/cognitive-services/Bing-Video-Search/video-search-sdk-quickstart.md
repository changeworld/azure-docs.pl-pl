---
title: Wideo wyszukiwania szybkiego startu zestawu SDK C# | Dokumentacja firmy Microsoft
description: Instalator wideo wyszukiwania zestawu SDK aplikacji konsoli.
titleSuffix: Azure cognitive services setup News search SDK C# console application
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 01/29/2018
ms.author: v-gedod
ms.openlocfilehash: f53e2d0f0052ccfabb6d750556cb532f069c9121
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349480"
---
# <a name="video-search-sdk-c-quickstart"></a>Wideo wyszukiwania zestawu SDK C# Szybki Start

Zestaw SDK wyszukiwania usługi Bing wideo zawiera funkcje interfejsu API REST dla żądań sieci web i wyniki analizy.

[Źródła kodu dla C# Bing wideo wyszukiwania zestawu SDK przykłady](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) jest dostępna w Centrum Git.

## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsoli przy użyciu zestawu SDK wyszukiwania usługi Bing wideo, przejdź do `Manage NuGet Packages` opcji z Eksploratora rozwiązań w programie Visual Studio.  Dodaj `Microsoft.Azure.CognitiveServices.Search.VideoSearch` pakietu.

Instalowanie [[pakiet NuGet wideo wyszukiwania SDK]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) instaluje również zależności, w tym:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json


## <a name="video-search-client"></a>Wideo klienta wyszukiwania
Aby utworzyć wystąpienie `VideoSearchAPI` klienta, dodawanie dyrektywy using:
```
using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;

```
Następnie można utworzyć wystąpienia klienta:
```
var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
Korzystając z klienta do wyszukiwania tekstu zapytania "SwiftKey" dla plików wideo.
```
var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
Console.WriteLine("Search videos for query \"SwiftKey\"");

```

Analizowanie wyników, a następnie sprawdź liczbę wyników i wydrukowania Identyfikatora, nazwy i adresu url pierwszego wyniku wideo.
```
if (videoResults == null)
{
    Console.WriteLine("Didn't see any video result data..");
}
else
{
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value.First();

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
}

```
## <a name="complete-console-application"></a>Aplikacja konsolowa ukończone

Następującej aplikacji konsoli wykonuje wcześniej zdefiniowane zapytanie i analizuje wyniki.

```
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;

namespace VideoSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("19aa718a79d6444daaa415981d9f54ad"));

            try
            {
                var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
                Console.WriteLine("Search videos for query \"SwiftKey\"");

                if (videoResults == null)
                {
                    Console.WriteLine("Didn't see any video result data..");
                }
                else
                {
                    if (videoResults.Value.Count > 0)
                    {
                        var firstVideoResult = videoResults.Value.First();

                        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
                        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
                        Console.WriteLine($"First video name: {firstVideoResult.Name}");
                        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find video results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            // Include these calls to use queries defined under the following headings.
            //VideoSearchWithFilters(client);
            //VideoDetail(client);
            //VideoTrending(client);


            Console.WriteLine("Any key to exit...");
            Console.ReadKey();

        }

```
## <a name="url-parameters"></a>Parametry adresu URL

Wyszukaj tekst zapytania "Bellevue przyczepy" pliki wideo, które zostały zmienione, krótki i rozdzielczości 1080p.  Sprawdź liczbę wyników, a następnie wydrukuj Identyfikatora, nazwy i adresu url pierwszego wyniku wideo.

```
        public static void VideoSearchWithFilters(VideoSearchAPI client)
        {
            try
            {
                var videoResults = client.Videos.SearchAsync(query: "Bellevue Trailer", pricing: VideoPricing.Free, length: VideoLength.Short, resolution: VideoResolution.HD1080p).Result;
                Console.WriteLine("Search videos for query \"Bellevue Trailer\" that is free, short and 1080p resolution");

                if (videoResults == null)
                {
                    Console.WriteLine("Didn't see any video result data..");
                }
                else
                {
                    if (videoResults.Value.Count > 0)
                    {
                        var firstVideoResult = videoResults.Value.First();

                        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
                        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
                        Console.WriteLine($"First video name: {firstVideoResult.Name}");
                        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find video results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

        }


```
## <a name="trending-videos"></a>Popularne klipy wideo
Wyszukaj trendów wideo, a następnie sprawdź Kafelki transparent i kategorii.
```
        public static void VideoTrending(VideoSearchAPI client)
        {
            try
            {
                var trendingResults = client.Videos.TrendingAsync().Result;
                Console.WriteLine("Search trending videos");

                if (trendingResults == null)
                {
                    Console.WriteLine("Didn't see any trending video data..");
                }
                else
                {
                    // Banner Tiles
                    if (trendingResults.BannerTiles?.Count > 0)
                    {
                        var firstBannerTile = trendingResults.BannerTiles[0];
                        Console.WriteLine($"\r\nBanner tile count: {trendingResults.BannerTiles.Count}");
                        Console.WriteLine($"First banner tile text: {firstBannerTile.Query.Text}");
                        Console.WriteLine($"First banner tile url: {firstBannerTile.Query.WebSearchUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find banner tiles!");
                    }

                    // Categories
                    if (trendingResults.Categories?.Count > 0)
                    {
                        var firstCategory = trendingResults.Categories[0];
                        Console.WriteLine($"Category count: {trendingResults.Categories.Count}");
                        Console.WriteLine($"First category title: {firstCategory.Title}");

                        if (firstCategory.Subcategories?.Count > 0)
                        {
                            var firstSubCategory = firstCategory.Subcategories[0];
                            Console.WriteLine($"SubCategory count: {firstCategory.Subcategories.Count}");
                            Console.WriteLine($"First sub category title: {firstSubCategory.Title}");

                            if (firstSubCategory.Tiles?.Count > 0)
                            {
                                var firstTile = firstSubCategory.Tiles[0];
                                Console.WriteLine($"Tile count: {firstSubCategory.Tiles.Count}");
                                Console.WriteLine($"First tile text: {firstTile.Query.Text}");
                                Console.WriteLine($"First tile url: {firstTile.Query.WebSearchUrl}");
                            }
                            else
                            {
                                Console.WriteLine("Couldn't find tiles!");
                            }
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find subcategories!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find categories!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

        }


```
## <a name="details"></a>Szczegóły
Wyszukaj pliki wideo "Bellevue przyczepy", a następnie wyszukaj szczegółowe informacje pierwszy wideo.
```
        public static void VideoDetail(VideoSearchAPI client)
        {
            try
            {
                var videoResults = client.Videos.SearchAsync(query: "Bellevue Trailer").Result;

                var firstVideo = videoResults?.Value?.FirstOrDefault();

                if (firstVideo != null)
                {
                    var modules = new List<VideoInsightModule?>() { VideoInsightModule.All };
                    var videoDetail = client.Videos.DetailsAsync(query: "Bellevue Trailer", id: firstVideo.VideoId, modules: modules).Result;
                    Console.WriteLine($"Search detail for video id={firstVideo.VideoId}, name={firstVideo.Name}");

                    if (videoDetail != null)
                    {
                        if (videoDetail.VideoResult != null)
                        {
                            Console.WriteLine($"\r\nExpected video id: {videoDetail.VideoResult.VideoId}");
                            Console.WriteLine($"Expected video name: {videoDetail.VideoResult.Name}");
                            Console.WriteLine($"Expected video url: {videoDetail.VideoResult.ContentUrl}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find expected video!");
                        }

                        if (videoDetail?.RelatedVideos?.Value?.Count > 0)
                        {
                            var firstRelatedVideo = videoDetail.RelatedVideos.Value[0];
                            Console.WriteLine($"Related video count: {videoDetail.RelatedVideos.Value.Count}");
                            Console.WriteLine($"First related video id: {firstRelatedVideo.VideoId}");
                            Console.WriteLine($"First related video name: {firstRelatedVideo.Name}");
                            Console.WriteLine($"First related video url: {firstRelatedVideo.ContentUrl}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find any related video!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find detail about the video!");
                    }
                }
                else
                {
                    Console.WriteLine("Couldn't find video results!");
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }

```

## <a name="next-steps"></a>Kolejne kroki

[Usługi kognitywnych przykłady zestawu .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)