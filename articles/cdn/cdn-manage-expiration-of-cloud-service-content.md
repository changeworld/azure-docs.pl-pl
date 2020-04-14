---
title: Zarządzanie wygasaniem zawartości sieci Web w usłudze Azure CDN | Dokumenty firmy Microsoft
description: Dowiedz się, jak zarządzać wygaśnięciem zawartości usługi Azure Web Apps/Cloud Services, ASP.NET lub IIS w usłudze Azure CDN.
services: cdn
documentationcenter: .NET
author: asudbring
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: allensu
ms.openlocfilehash: 4598e6cee6ffbaaeb2a99727842fcd17fe0046c7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260568"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Zarządzanie wygasaniem zawartości internetowej w usłudze Azure CDN
> [!div class="op_single_selector"]
> * [Zawartość sieci Web platformy Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Magazyn obiektów Blob platformy Azure](cdn-manage-expiration-of-blob-content.md)
> 

Pliki z publicznie dostępnych serwerów sieci web pochodzenia mogą być buforowane w usłudze Azure Content Delivery Network (CDN) do czasu ich upływu czasu wygaśnięcia (TTL). Czas wygaśnięcia jest `Cache-Control` określany przez nagłówek w odpowiedzi HTTP z serwera pochodzenia. W tym artykule `Cache-Control` opisano sposób ustawiania nagłówków dla funkcji aplikacji sieci Web usługi Microsoft Azure App Service, usług w chmurze Azure, aplikacji ASP.NET i witryn internetowych usług informacyjnych (IIS), z których wszystkie są skonfigurowane w podobny sposób. `Cache-Control` Nagłówek można ustawić za pomocą plików konfiguracyjnych lub programowo. 

Ustawienia pamięci podręcznej można również kontrolować za pomocą portalu Azure, ustawiając [reguły buforowania sieci CDN](cdn-caching-rules.md). Jeśli utworzysz jedną lub więcej reguł buforowania i ustawisz ich zachowanie buforowania na **Zastąpal** lub **Pomiń pamięć podręczną,** ustawienia buforowania podane przez źródło, omówione w tym artykule, są ignorowane. Aby uzyskać informacje na temat ogólnych pojęć buforowania, zobacz [Jak działa buforowanie](cdn-how-caching-works.md).

> [!TIP]
> Można ustawić brak czasu wygaśnięcia w pliku. W takim przypadku usługa Azure CDN automatycznie stosuje domyślny czas wygaśnięcia z siedmiodniowym kursem, chyba że skonfigurowane są reguły buforowania w witrynie Azure portal. Ten domyślny czas wygaśnięcia ma zastosowanie tylko do ogólnych optymalizacji dostarczania w sieci Web. W przypadku optymalizacji dużych plików domyślny czas wygaśnięcia wynosi jeden dzień, a w przypadku optymalizacji przesyłania strumieniowego multimediów domyślny czas wygaśnięcia wynosi jeden rok.
> 
> Aby uzyskać więcej informacji na temat działania usługi Azure CDN w celu przyspieszenia dostępu do plików i innych zasobów, zobacz [Omówienie sieci dostarczania zawartości platformy Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ustawianie nagłówków cache-control przy użyciu reguł buforowania sieci CDN
Preferowaną metodą ustawiania nagłówka `Cache-Control` serwera sieci web jest użycie reguł buforowania w witrynie Azure portal. Aby uzyskać więcej informacji na temat reguł buforowania sieci CDN, zobacz [Kontrolowanie zachowania buforowania usługi Azure CDN za pomocą reguł buforowania](cdn-caching-rules.md).

> [!NOTE] 
> Reguły buforowania są dostępne tylko dla **usługi Azure CDN Standard firmy Verizon** i Azure **CDN Standard z profilów Akamai.** W przypadku profilów **usługi Azure CDN Premium z verizon** należy użyć aparatu reguł usługi Azure [CDN](cdn-rules-engine.md) w portalu **zarządzania** dla podobnych funkcji.

**Aby przejść do strony reguł buforowania sieci CDN:**

1. W witrynie Azure portal wybierz profil usługi CDN, a następnie wybierz punkt końcowy serwera sieci web.

1. W lewym okienku w obszarze Ustawienia zaznacz pole **Reguły buforowania**.

   ![Przycisk reguł buforowania sieci CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Zostanie wyświetlona strona **Reguły buforowania**.

   ![Strona buforowania sieci CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Aby ustawić nagłówki cache-control serwera sieci web przy użyciu globalnych reguł buforowania:**

1. W obszarze **Globalne reguły buforowania**ustaw **zachowanie buforowania ciągów zapytań** tak, aby **ignoruj ciągi zapytań** i ustaw **zachowanie buforowania** na **Zastąpalanie**.
      
1. W przypadku **czasu wygaśnięcia pamięci podręcznej**wprowadź 3600 w polu **Sekundy** lub 1 w polu **Godziny.** 

   ![Przykład globalnych reguł buforowania sieci CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Ta globalna reguła buforowania ustawia czas trwania pamięci podręcznej jednej godziny i wpływa na wszystkie żądania do punktu końcowego. Zastępuje wszystkie `Cache-Control` nagłówki `Expires` lub HTTP, które są wysyłane przez serwer pochodzenia określony przez punkt końcowy.   

1. Wybierz **pozycję Zapisz**.

**Aby ustawić nagłówki cache-control pliku serwera sieci web przy użyciu niestandardowych reguł buforowania:**

1. W obszarze **Niestandardowe reguły buforowania**utwórz dwa warunki dopasowania:

     a. Dla pierwszego warunku dopasowania ustaw warunek `/webfolder1/*` **dopasowania** na **Ścieżkę** i wprowadź wartość **Dopasowania**. Ustaw **zachowanie buforowania,** aby **zastąpić** i wprowadź 4 w polu **Godziny.**

     b. W przypadku drugiego warunku dopasowania ustaw `/webfolder1/file1.txt` warunek **dopasowania** na **Ścieżkę** i wprowadź wartość **Dopasowania**. Ustaw **zachowanie buforowania,** aby **zastąpić** i wprowadź 2 w polu **Godziny.**

    ![Przykład niestandardowych reguł buforowania usługi CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Pierwsza niestandardowa reguła buforowania ustawia czas trwania pamięci `/webfolder1` podręcznej wynoszący cztery godziny dla wszystkich plików w folderze na serwerze pochodzenia określonym przez punkt końcowy. Druga reguła zastępuje pierwszą regułę `file1.txt` tylko dla pliku i ustawia czas trwania pamięci podręcznej wynoszący dwie godziny.

1. Wybierz **pozycję Zapisz**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Ustawianie nagłówków cache-control przy użyciu plików konfiguracyjnych
W przypadku zawartości statycznej, takiej jak obrazy i arkusze stylów, można kontrolować częstotliwość aktualizacji, modyfikując pliki konfiguracyjne **applicationHost.config** lub **Web.config** dla aplikacji sieci web. Aby ustawić `Cache-Control` nagłówek zawartości, użyj `<system.webServer>/<staticContent>/<clientCache>` elementu w każdym pliku.

### <a name="using-applicationhostconfig-files"></a>Korzystanie z plików ApplicationHost.config
**Plik ApplicationHost.config** jest plikiem głównym systemu konfiguracji usług IIS. Ustawienia konfiguracji w pliku **ApplicationHost.config** mają wpływ na wszystkie aplikacje w witrynie, ale są zastępowane przez ustawienia wszystkich plików **Web.config,** które istnieją dla aplikacji sieci web.

### <a name="using-webconfig-files"></a>Korzystanie z plików Web.config
Za pomocą pliku **Web.config** można dostosować sposób zachowania całej aplikacji sieci web lub określonego katalogu w aplikacji sieci web. Zazwyczaj w folderze głównym aplikacji sieci web znajduje się co najmniej jeden plik **Web.config.** Dla każdego pliku **Web.config** w określonym folderze ustawienia konfiguracji mają wpływ na wszystko w tym folderze i jego podfolderach, chyba że zostaną zastąpione na poziomie podfolderu przez inny plik **Web.config.** 

Na przykład można ustawić `<clientCache>` element w pliku **Web.config** w folderze głównym aplikacji sieci web, aby buforować całą zawartość statyczną w aplikacji sieci web przez trzy dni. Można również dodać plik **Web.config** w podfolderze z bardziej `\frequent`zmienną `<clientCache>` zawartością (na przykład) i ustawić jego element do buforowania zawartości podfolderu przez sześć godzin. Wynik netto jest taki, że zawartość w całej witrynie sieci web `\frequent` jest buforowana przez trzy dni, z wyjątkiem dowolnej zawartości w katalogu, która jest buforowana tylko przez sześć godzin.  

W poniższym przykładzie pliku konfiguracyjnego XML pokazano, jak ustawić element, `<clientCache>` aby określić maksymalny wiek trzech dni:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Aby użyć atrybutu **cacheControlMaxAge,** należy ustawić wartość atrybutu **cacheControlMode** na `UseMaxAge`. To ustawienie spowodowało, że `Cache-Control: max-age=<nnn>`nagłówek HTTP i dyrektywa mają zostać dodane do odpowiedzi. Format wartości przedziału czasu dla atrybutu **cacheControlMaxAge** to `<days>.<hours>:<min>:<sec>`. Jego wartość jest konwertowana na sekundy `Cache-Control` `max-age` i jest używana jako wartość dyrektywy. Aby uzyskać więcej `<clientCache>` informacji na temat elementu, zobacz [Client Cache \<klientaCache>](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Programowo ustawianie nagłówków cache-control
W przypadku aplikacji ASP.NET można sterować zachowaniem buforowania sieci CDN programowo, ustawiając właściwość **HttpResponse.Cache** interfejsu API platformy .NET. Aby uzyskać informacje na temat właściwości **HttpResponse.Cache,** zobacz [HttpResponse.Cache Właściwości](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) i [HttpCachePolicy Class](/dotnet/api/system.web.httpcachepolicy).  

Aby programowo buforować zawartość aplikacji w ASP.NET, wykonaj następujące kroki:
   1. Sprawdź, czy zawartość jest oznaczona `HttpCacheability` `Public`jako buforowa, ustawiając na . 
   1. Ustaw walidator pamięci podręcznej, `HttpCachePolicy` wywołując jedną z następujących metod:
      - Wywołanie, `SetLastModified` aby ustawić wartość `Last-Modified` sygnatury czasowej dla nagłówka.
      - Wywołanie, `SetETag` aby ustawić `ETag` wartość nagłówka.
   1. Opcjonalnie należy określić czas wygaśnięcia pamięci podręcznej, wywołując, `SetExpires` aby ustawić wartość nagłówka. `Expires` W przeciwnym razie stosuje się domyślną heurystykę pamięci podręcznej opisaną wcześniej w tym dokumencie.

Na przykład do buforowania zawartości przez jedną godzinę, dodaj następujący kod Języka C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testowanie nagłówka Cache-Control
Możesz łatwo zweryfikować ustawienia czasu wygaśnięcia zawartości sieci Web. Za pomocą [narzędzi programistycznych](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)przeglądarki sprawdź, czy `Cache-Control` zawartość sieci Web zawiera nagłówek odpowiedzi. Można również użyć narzędzia, takiego jak **wget**, [Postman](https://www.getpostman.com/)lub [Fiddler,](https://www.telerik.com/fiddler) aby sprawdzić nagłówki odpowiedzi.

## <a name="next-steps"></a>Następne kroki
* [Przeczytaj szczegółowe informacje o elementów **clientCache**](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Przeczytaj dokumentację właściwości **HttpResponse.Cache**](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Przeczytaj dokumentację **klasy HttpCachePolicy**](/dotnet/api/system.web.httpcachepolicy)  
* [Dowiedz się więcej o pojęciach dotyczących buforowania](cdn-how-caching-works.md)
