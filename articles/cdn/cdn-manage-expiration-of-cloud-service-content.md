---
title: Zarządzanie wygasaniem zawartości sieci web w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać wygasaniem zawartości usługi Azure Web Apps/Cloud Services, ASP.NET lub IIS w usłudze Azure CDN.
services: cdn
documentationcenter: .NET
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: magattus
ms.openlocfilehash: c21ae227d74442be5701dd906180392b1e0fdf8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636709"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Zarządzanie wygasaniem zawartości sieci web w usłudze Azure CDN
> [!div class="op_single_selector"]
> * [Zawartość sieci Web platformy Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Pliki z publicznie dostępnego źródła serwerów sieci web mogą być buforowane w usłudze Azure Content Delivery Network (CDN), dopóki nie upłynie ich time to live (TTL). Czas wygaśnięcia jest określany przez `Cache-Control` nagłówka odpowiedzi HTTP z serwera pochodzenia. W tym artykule opisano sposób ustawiania `Cache-Control` nagłówki dla funkcji Web Apps, Microsoft Azure App Service, usług Azure Cloud Services, aplikacje platformy ASP.NET i Internet Information Services (IIS) witryny, które są skonfigurowane w podobny sposób. Możesz ustawić `Cache-Control` nagłówka za pomocą plików konfiguracji lub programowo. 

Ustawienia pamięci podręcznej w witrynie Azure portal można także kontrolować przez ustawienie [zasady buforowania usługi CDN](cdn-caching-rules.md). Jeśli użytkownik należy utworzyć jedną lub więcej pamięci podręcznej reguły i ustaw ich zachowanie buforowania **zastąpienia** lub **Pomiń pamięć podręczną**, ustawienia buforowania dostarczone do źródła omówionych w tym artykule są ignorowane. Aby uzyskać informacje o ogólnych pojęciach buforowania, zobacz [jak działa buforowanie](cdn-how-caching-works.md).

> [!TIP]
> Można ustawić nie czasu wygaśnięcia na plik. W tym przypadku sieć CDN systemu Azure automatycznie stosuje jako domyślny czas wygaśnięcia siedem dni, chyba, że po skonfigurowaniu reguł w witrynie Azure portal buforowania. To ustawienie domyślne TTL dotyczy tylko optymalizacji dostarczania ogólnych sieci web. Optymalizacja dużych plików, aby uzyskać domyślny czas wygaśnięcia wynosi jeden dzień, a dla multimediów, przesyłanie strumieniowe optymalizacje, domyślny czas wygaśnięcia wynosi jeden rok.
> 
> Aby uzyskać więcej informacji na temat współdziałania usługi Azure CDN do Przyspieszanie dostępu do plików i innych zasobów, zobacz [Omówienie usługi Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ustawienie nagłówki Cache-Control przy użyciu reguł buforowania usługi CDN
Preferowaną metodą ustawienie serwera sieci web `Cache-Control` nagłówek jest użycie reguły buforowania w witrynie Azure portal. Aby uzyskać więcej informacji na temat zasad buforowania usługi CDN, zobacz [kontroli Azure działanie buforowania usługi CDN przy użyciu reguł buforowania](cdn-caching-rules.md).

> [!NOTE] 
> Reguły buforowania są dostępne tylko dla **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profilów. Dla **Azure CDN Premium from Verizon** profile, należy użyć [aparat reguł wysokiej dostępności treści Azure](cdn-rules-engine.md) w **Zarządzaj** portal dla podobne funkcje.

**Aby przejść do strony reguł buforowania usługi CDN**:

1. W witrynie Azure portal wybierz profil usługi CDN, a następnie wybierz punkt końcowy serwera sieci web.

1. W lewym okienku w obszarze Ustawienia zaznacz pole **Reguły buforowania**.

   ![Przycisk reguły z buforowania usługi CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Zostanie wyświetlona strona **Reguły buforowania**.

   ![Strona buforowania usługi CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Aby ustawić nagłówki Cache-Control serwera sieci web, przy użyciu globalne reguły buforowania:**

1. W obszarze **globalne reguły buforowania**ustaw **zachowanie buforowania ciągu kwerendy** do **Ignoruj ciągi zapytań** i ustaw **zachowanie buforowania** do  **Zastąp**.
      
1. Dla **czas wygasania pamięci podręcznej**, wprowadź 3600 w **sekund** pola lub 1 w **godzin** pole. 

   ![Przykład globalne reguły buforowania usługi CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   To globalne reguły buforowania ustawia czas trwania pamięci podręcznej jednej godziny i ma wpływ na wszystkie żądania do punktu końcowego. Ustawienie to zastępuje dowolny `Cache-Control` lub `Expires` nagłówki HTTP, które są wysyłane przez serwer pochodzenia, określony przez punkt końcowy.   

1. Wybierz pozycję **Zapisz**.

**Aby ustawić serwera sieci web nagłówki Cache-Control pliku za pomocą niestandardowych reguł buforowania:**

1. W obszarze **niestandardowe reguły buforowania**, Utwórz dwa warunki dopasowania:

     a. Pierwszy warunek dopasowania, ustaw **dopasować stan** do **ścieżki** i wprowadź `/webfolder1/*` dla **odpowiada wartości**. Ustaw **zachowanie buforowania** do **zastąpienia** i wprowadź 4 w **godzin** pole.

     b. Drugi warunek dopasowania, ustaw **dopasować stan** do **ścieżki** i wprowadź `/webfolder1/file1.txt` dla **odpowiada wartości**. Ustaw **zachowanie buforowania** do **zastąpienia** i wprowadź wartość 2, w **godzin** pole.

    ![Przykład niestandardowych reguł buforowania usługi CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Pierwsze niestandardowe reguły buforowania ustawia czas trwania pamięci podręcznej czterech godzin dla wszystkich plików w `/webfolder1` folderu na serwerze źródłowym, określony przez punkt końcowy usługi. Druga reguła zastępuje pierwszej reguły dla `file1.txt` tylko plik i ustawia czas trwania pamięci podręcznej z 2 godzin.

1. Wybierz pozycję **Zapisz**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Ustawianie nagłówki Cache-Control za pomocą plików konfiguracji
Dla zawartość statyczną, taką jak obrazy i arkusze stylów, możesz kontrolować częstotliwość aktualizacji, modyfikując **applicationHost.config** lub **Web.config** pliki konfiguracji dla aplikacji sieci web. Aby ustawić `Cache-Control` nagłówek dla zawartości, użyj `<system.webServer>/<staticContent>/<clientCache>` element w dowolnym pliku.

### <a name="using-applicationhostconfig-files"></a>Przy użyciu plików ApplicationHost.config
**ApplicationHost.config** plik jest plikiem głównego systemu konfiguracji usług IIS. Ustawienia konfiguracji w **ApplicationHost.config** pliku wpływają na wszystkie aplikacje w witrynie, ale są zastępowane przez ustawienia dowolnej **Web.config** pliki, które istnieją w dla aplikacji sieci web.

### <a name="using-webconfig-files"></a>Korzystanie z plików Web.config
Za pomocą **Web.config** pliku, można dostosować sposób zachowania aplikacji cała sieć web lub określonego katalogu aplikacji sieci web. Zazwyczaj mają co najmniej jeden **Web.config** pliku w folderze głównym aplikacji sieci web. Dla każdego **Web.config** pliku w określonym folderze, ustawienia konfiguracji wpływają na wszystko, co w tym folderze i jego podfolderach, chyba że zostaną zastąpione przez inną na poziomie podfolder **Web.config** plik. 

Na przykład można ustawić `<clientCache>` element **Web.config** pliku w folderze głównym aplikacji sieci web na buforowanie zawartości statycznej, aplikacji sieci web dla trzech dni. Można również dodać **Web.config** plik w podfolderze o większej zmiennej zawartości (na przykład `\frequent`) i ustaw jego `<clientCache>` elementu do tego podfolderu zawartość pamięci podręcznej przez sześć godzin. Wynikiem jest tej zawartości w całej witryny sieci web jest buforowana na 3 dni, z wyjątkiem zawartości `\frequent` katalogu, który jest w pamięci podręcznej tylko sześć godzin.  

W poniższym przykładzie plik konfiguracji XML pokazuje, jak ustawić `<clientCache>` elementu, aby określić maksymalny wiek trzy dni:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Aby użyć **cacheControlMaxAge** atrybutu, należy ustawić wartość **cacheControlMode** atrybutu `UseMaxAge`. To ustawienie spowodowane nagłówka HTTP i dyrektywy, `Cache-Control: max-age=<nnn>`, mają zostać dodane do odpowiedzi. Format wartość timespan odpowiadającą **cacheControlMaxAge** atrybut jest `<days>.<hours>:<min>:<sec>`. Jego wartość jest konwertowana na sekund i jest używana jako wartość `Cache-Control` `max-age` dyrektywy. Aby uzyskać więcej informacji na temat `<clientCache>` elementu, zobacz [pamięci podręcznej klienta \<clientCache >](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Programowe Ustawianie nagłówki Cache-Control
Dla aplikacji ASP.NET, możesz kontrolować zachowanie buforowania programowo przez ustawienie sieci CDN **HttpResponse.Cache** właściwości interfejsu API platformy .NET. Aby uzyskać informacje o **HttpResponse.Cache** właściwości, zobacz [właściwość HttpResponse.Cache](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) i [klasy HttpCachePolicy](/dotnet/api/system.web.httpcachepolicy).  

Aby programowo zawartości pamięci podręcznej aplikacji na platformie ASP.NET wykonaj następujące kroki:
   1. Sprawdź, czy zawartość jest oznaczony jako podlega buforowaniu, ustawiając `HttpCacheability` do `Public`. 
   1. Ustaw walidatorze pamięci podręcznej, wywołując jedną z następujących `HttpCachePolicy` metody:
      - Wywołaj `SetLastModified` można ustawić wartość sygnatury czasowej `Last-Modified` nagłówka.
      - Wywołaj `SetETag` można ustawić wartości dla `ETag` nagłówka.
   1. Opcjonalnie można określić czas wygaśnięcia pamięci podręcznej przez wywołanie metody `SetExpires` można ustawić wartości dla `Expires` nagłówka. W przeciwnym razie zastosować Algorytm heurystyczny pamięci podręcznej domyślne, które zostały opisane wcześniej w tym dokumencie.

Na przykład do pamięci podręcznej zawartości przez jedną godzinę, Dodaj następujący kod C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testowanie nagłówek Cache-Control
Możesz łatwo sprawdzić ustawienia wygaśnięcia zawartości sieci web. Przy użyciu przeglądarki [narzędzi deweloperskich](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test, który obejmuje zawartość sieci web `Cache-Control` nagłówka odpowiedzi. Można również użyć narzędzia takie jak **wget**, [Postman](https://www.getpostman.com/), lub [Fiddler](https://www.telerik.com/fiddler) Aby sprawdzić nagłówki odpowiedzi.

## <a name="next-steps"></a>Następne kroki
* [Przeczytaj szczegółowe informacje na temat **clientCache** — element](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Przeczytaj dokumentację dotyczącą **HttpResponse.Cache** właściwości](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Przeczytaj dokumentację dotyczącą **HttpCachePolicy, klasa**](/dotnet/api/system.web.httpcachepolicy)  
* [Dowiedz się więcej na temat buforowania pojęcia](cdn-how-caching-works.md)
