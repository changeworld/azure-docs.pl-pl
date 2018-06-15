---
title: Zarządzaj wygasaniem zawartości sieci web w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać wygasaniem zawartości usług Azure Web Apps/Cloud Services, ASP.NET lub usługi IIS w usłudze Azure CDN.
services: cdn
documentationcenter: .NET
author: dksimpson
manager: akucer
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: mazha
ms.openlocfilehash: ec5470587454a35bc7606a3518d61bd3491d653b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33765545"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Zarządzaj wygasaniem zawartości sieci web w usłudze Azure CDN
> [!div class="op_single_selector"]
> * [Zawartość sieci Web platformy Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Mogą być buforowane pliki z serwerów sieci web publicznie dostępnego źródła w Azure Content Delivery Network (CDN), dopóki nie upłynie ich czas wygaśnięcia (TTL). Czas wygaśnięcia jest określany przez `Cache-Control` nagłówka odpowiedzi HTTP z serwera pochodzenia. W tym artykule opisano sposób ustawiania `Cache-Control` nagłówki dla funkcji aplikacji sieci Web Microsoft Azure App Service, usługi w chmurze Azure, aplikacji ASP.NET oraz witryny usług Internet Information Services (IIS), które są skonfigurowane podobnie. Można ustawić `Cache-Control` nagłówka przy użyciu plików konfiguracyjnych lub programowo. 

Ustawienia pamięci podręcznej z portalu Azure można też kontrolować przez ustawienie [CDN buforowanie reguły](cdn-caching-rules.md). Jeśli utworzenie jednego lub więcej buforowanie reguły i ustawić ich zachowanie buforowania **zastąpienia** lub **obejścia pamięci podręcznej**, wprowadzone do pochodzenia ustawień buforowania omówione w tym artykule są ignorowane. Informacje ogólne koncepcje buforowania, zobacz [działa jak buforowanie](cdn-how-caching-works.md).

> [!TIP]
> Można ustawić nie TTL w pliku. W takim przypadku Azure CDN automatycznie stosuje się domyślny czas wygaśnięcia wynosi siedem dni, chyba, że po skonfigurowaniu buforowania reguł w portalu Azure. To ustawienie domyślne TTL dotyczy tylko optymalizacji ogólne sieci web. Dla optymalizacji dużych plików domyślny czas wygaśnięcia wynosi jeden dzień, a dla multimediów strumieniowych optymalizacji, domyślny czas wygaśnięcia wynosi 1 rok.
> 
> Aby uzyskać więcej informacji na temat działania usługi Azure CDN do Przyspieszanie dostępu do plików i innych zasobów, zobacz [Omówienie usługi Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ustawienie nagłówki Cache-Control za pomocą zasad buforowania w sieci CDN
Preferowaną metodą ustawienie serwera sieci web `Cache-Control` nagłówka jest użycie zasad buforowania w portalu Azure. Aby uzyskać więcej informacji o CDN buforowanie reguły, zobacz [kontroli usługi Azure CDN zachowanie buforowania z buforowaniem reguły](cdn-caching-rules.md).

> [!NOTE] 
> Reguły buforowania są dostępne tylko dla **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profilów. Dla **Azure CDN Premium from Verizon** profile, należy użyć [aparatu reguł Azure CDN](cdn-rules-engine.md) w **Zarządzaj** portalu dla podobnych możliwościach.

**Aby przejść do strony reguł buforowania CDN**:

1. W portalu Azure wybierz profil CDN, a następnie wybierz punkt końcowy dla serwera sieci web.

2. W lewym okienku w obszarze Ustawienia zaznacz **buforowanie reguły**.

   ![Przycisk reguły buforowania CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   **Buforowanie reguły** zostanie wyświetlona strona.

   ![Strona buforowania CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Można ustawić nagłówki Cache-Control serwera sieci web, przy użyciu globalne reguły buforowania:**

1. W obszarze **globalnej pamięci podręcznej zasad**ustaw **zachowanie buforowania ciągu kwerendy** do **ignorować ciągi kwerendy** i ustaw **zachowanie buforowania** do  **Zastąpienie**.
      
2. Dla **pamięci podręcznej Czas wygaśnięcia**, wprowadź 3600 w **sekund** pola lub 1 w **godziny** pole. 

   ![Przykład globalnej reguły buforowania CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   To globalna reguła buforowania ustawia czas buforowania, godzinę i ma wpływ na wszystkie żądania do punktu końcowego. Zastępuje ona żadnego `Cache-Control` lub `Expires` nagłówków HTTP, które są wysyłane przez serwer pochodzenia określony przez punkt końcowy.   

3. Wybierz pozycję **Zapisz**.

**Aby ustawić serwera sieci web nagłówki Cache-Control pliku za pomocą niestandardowych zasad buforowania:**

1. W obszarze **niestandardowe reguły buforowania**, Utwórz dwa warunki dopasowania:

     a. Pierwszy warunek dopasowania, można ustawić **dopasować stan** do **ścieżki** , a następnie wprowadź `/webfolder1/*` dla **odpowiada wartości**. Ustaw **zachowanie buforowania** do **zastąpienia** , a następnie wprowadź 4 w **godziny** pole.

     b. Drugi warunek dopasowania, można ustawić **dopasować stan** do **ścieżki** , a następnie wprowadź `/webfolder1/file1.txt` dla **odpowiada wartości**. Ustaw **zachowanie buforowania** do **zastąpienia** , a następnie wprowadź 2 w **godziny** pole.

    ![Przykład reguły buforowania niestandardowej CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Pierwszy niestandardową regułę buforowania ustawia czas trwania czterech godzin, wszystkie pliki w pamięci podręcznej `/webfolder1` folderu na serwerze źródłowym, określony przez punkt końcowy. Drugi reguła zastępuje regułę pierwszy dla `file1.txt` tylko plików i ustawia czas trwania pamięci podręcznej dwóch godzin.

2. Wybierz pozycję **Zapisz**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Ustawienie nagłówki Cache-Control za pomocą plików konfiguracji
Dla zawartości statycznej, takich jak obrazy i arkusze stylów, możesz kontrolować częstotliwość aktualizacji przez zmodyfikowanie **applicationHost.config** lub **Web.config** pliki konfiguracji aplikacji sieci web. Aby ustawić `Cache-Control` nagłówek dla zawartości, użyj `<system.webServer>/<staticContent>/<clientCache>` element w każdym pliku.

### <a name="using-applicationhostconfig-files"></a>Przy użyciu pliku ApplicationHost.config plików
**ApplicationHost.config** plik jest plikiem głównego systemu konfiguracji usług IIS. Ustawienia konfiguracji w **ApplicationHost.config** pliku wpływają na wszystkie aplikacje w lokacji, ale są zastępowane przez ustawienia dowolnej **Web.config** plików, które istnieją dla aplikacji sieci web.

### <a name="using-webconfig-files"></a>Przy użyciu plików Web.config
Z **Web.config** plików, można dostosować sposób zachowania aplikacji sieci web całego lub określonego katalogu w aplikacji sieci web. Zazwyczaj mają co najmniej jeden **Web.config** pliku w katalogu głównym aplikacji sieci web. Dla każdego **Web.config** plików w określonym folderze, ustawienia konfiguracji mają wpływ na wszystkie elementy w tym folderze i jego podfolderach chyba że są one zastąpione na poziomie podfolder przez inną **Web.config** plik. 

Na przykład można ustawić `<clientCache>` element **Web.config** pliku w katalogu głównym aplikacji sieci web do pamięci podręcznej wszystkie zawartość statyczną w aplikacji sieci web dla trzech dni. Można również dodać **Web.config** plik w podfolderze o więcej zmiennych zawartości (na przykład `\frequent`) i ustawić jej `<clientCache>` elementu do pamięci podręcznej zawartości podfolder przez 6 godzin. Wynikiem jest tej zawartości w całej witryny sieci web jest buforowana przez trzy dni, z wyjątkiem bez zawartości `\frequent` katalogu, który jest buforowana przez tylko 6 godzin.  

W poniższym przykładzie plik XML konfiguracji przedstawiono sposób ustawiania `<clientCache>` element, aby określić maksymalny wiek trzy dni:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Aby użyć **cacheControlMaxAge** atrybutu, należy ustawić wartość **cacheControlMode** atrybutu `UseMaxAge`. To ustawienie spowodował nagłówka HTTP i dyrektywy `Cache-Control: max-age=<nnn>`, mają zostać dodane do odpowiedzi. Format wartości timespan dla **cacheControlMaxAge** atrybutu `<days>.<hours>:<min>:<sec>`. Wartość jest konwertowany na sekund i jest używany jako wartość `Cache-Control` `max-age` dyrektywy. Aby uzyskać więcej informacji na temat `<clientCache>` elementu, zobacz [pamięci podręcznej klienta <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Ustawienie programowo nagłówki Cache-Control
Dla aplikacji ASP.NET, sterowanie CDN buforowanie programowo, ustawiając **HttpResponse.Cache** właściwości interfejsu API programu .NET. Informacje o **HttpResponse.Cache** właściwości, zobacz [właściwości HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) i [HttpCachePolicy klasy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Aby programowo zawartości pamięci podręcznej aplikacji w programie ASP.NET wykonaj następujące kroki:
   1. Sprawdź, czy zawartość jest oznaczony jako buforowalnej przez ustawienie `HttpCacheability` do `Public`. 
   2. Ustaw moduł weryfikacji pamięci podręcznej, wywołując jedną z następujących `HttpCachePolicy` metod:
      - Wywołanie `SetLastModified` można ustawić wartość sygnatury czasowej `Last-Modified` nagłówka.
      - Wywołanie `SetETag` można ustawić wartości dla `ETag` nagłówka.
   3. Opcjonalnie można określić czas wygaśnięcia pamięci podręcznej przez wywołanie metody `SetExpires` można ustawić wartości dla `Expires` nagłówka. W przeciwnym razie domyślny algorytm heurystyczny pamięci podręcznej, opisanych wcześniej w tym dokumencie mają zastosowanie.

Na przykład do pamięci podręcznej zawartości przez jedną godzinę, Dodaj następujący kod C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testowanie nagłówek Cache-Control
Można łatwo sprawdzić ustawienia czas wygaśnięcia zawartości sieci web. Przy użyciu przeglądarki [narzędzia dla deweloperów](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test, który obejmuje zawartości sieci web `Cache-Control` nagłówka odpowiedzi. Można również użyć narzędzia takie jak **wget**, [Postman](https://www.getpostman.com/), lub [Fiddler](http://www.telerik.com/fiddler) Aby sprawdzić nagłówki odpowiedzi.

## <a name="next-steps"></a>Następne kroki
* [Przeczytaj szczegółowe informacje na temat **clientCache** — element](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Przeczytaj dokumentację **HttpResponse.Cache** właściwości](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Przeczytaj dokumentację **HttpCachePolicy — klasa**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)  
* [Dowiedz się więcej o buforowanie pojęcia](cdn-how-caching-works.md)
