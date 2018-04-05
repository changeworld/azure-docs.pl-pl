---
title: Integrowanie usługi w chmurze Azure z usługą Azure CDN | Dokumentacja firmy Microsoft
description: Sposób wdrażania usługi w chmurze, obejmującej zawartość z punktu końcowego zintegrowane usługi Azure CDN
services: cdn, cloud-services
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: tysonn
ms.assetid: b3c0108f-9ec5-43a8-8fd0-40eafbd32637
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f131eb021d85766f12b0fb6cb8b5a07f965f9c97
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="intro"></a> Integracja z usługą w chmurze z usługą Azure CDN
Usługi w chmurze można zintegrować z usługą Azure CDN, obsługująca zawartość z lokalizacji usługi w chmurze. To podejście zapewnia następujące korzyści:

* Łatwe wdrażanie i aktualizowanie obrazów, skrypty i arkusze stylów w katalogu projektu usługi chmury
* Łatwo przeprowadzić uaktualnienie pakietów NuGet w usługi w chmurze, np. jQuery lub ładowania początkowego wersji
* Zarządzanie aplikacji sieci Web i sieci obsługiwanych CDN wszystkie zawartości z tego samego interfejsu programu Visual Studio
* Przepływ pracy ujednoliconego wdrożenia dla aplikacji sieci Web i zawartości obsługiwanych w sieci CDN
* Integracja z usługą Azure CDN ASP.NET tworzenie pakietów i minimalizowanie

## <a name="what-you-will-learn"></a>Co dowiesz się
Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

* [Integracja punktu końcowego usługi Azure CDN z usługi w chmurze i udostępniać zawartość statyczną na stronach sieci Web z usługi Azure CDN](#deploy)
* [Skonfiguruj ustawienia pamięci podręcznej zawartość statyczną w usługi w chmurze](#caching)
* [Udostępniać zawartość z akcji kontrolera, za pomocą usługi Azure CDN](#controller)
* [Służą powiązane i zminimalizowane zawartości za pomocą usługi Azure CDN przy zachowaniu skryptu debugowanie w programie Visual Studio](#bundling)
* [Skonfigurować powrotu skryptów i CSS z usługi Azure CDN jest w trybie offline](#fallback)

## <a name="what-you-will-build"></a>Zostanie utworzona
Wdrażania roli sieci Web usługi chmury przy użyciu domyślnego szablonu platformy ASP.NET MVC, Dodaj kod, aby obsługiwać zawartość z zintegrowane usługi Azure CDN, takich jak obraz, wyniki akcji kontrolera i domyślne pliki obsługi języka JavaScript i CSS i również napisać kod, aby skonfigurować mechanizm rezerwowy umożliwiający pakiety obsługiwanych w przypadku, gdy element CDN jest w trybie offline.

## <a name="what-you-will-need"></a>Dane będą potrzebne
Ten samouczek ma następujące wymagania wstępne:

* Aktywny [konta Microsoft Azure](/account/)
* Visual Studio 2015 z [Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [!NOTE]
> Do ukończenia tego samouczka jest potrzebne konto platformy Azure.
> 
> * Możesz [otworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/) — otrzymasz kredyt służy do wypróbowania płatnych usług Azure, a nawet po wyczerpaniu kredytu możesz zachować konto i korzystać z bezpłatnych usług platformy Azure, takich jak witryny sieci Web.
> * Możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) -subskrypcji Your MSDN otrzymasz kredyt, co miesiąc, używanego programu płatnych usług Azure.
> 
> 

<a name="deploy"></a>

## <a name="deploy-a-cloud-service"></a>Wdrażanie usługi w chmurze
W tej sekcji zostanie wdrożyć rolę sieci Web usługi w chmurze domyślnego szablonu aplikacji platformy ASP.NET MVC programu Visual Studio 2015 i zintegrować ją z nowy punkt końcowy CDN. Postępuj zgodnie z instrukcjami poniżej:

1. W programie Visual Studio 2015, Utwórz nową usługę w chmurze Azure z paska menu, przechodząc do **Plik > Nowy > Projekt > chmura > usługi w chmurze Azure**. Nadaj mu nazwę, a następnie kliknij przycisk **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)
2. Wybierz **roli sieci Web ASP.NET** i kliknij przycisk **>** przycisku. Kliknij przycisk OK.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)
3. Wybierz **MVC** i kliknij przycisk **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)
4. Teraz Opublikuj tę rolę sieci Web do usługi w chmurze Azure. Kliknij prawym przyciskiem myszy projekt usługi w chmurze i wybierz **publikowania**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)
5. Jeśli dany komputer nie ma jeszcze podpisany w Microsoft Azure, kliknij przycisk **Dodaj konto...**  listy rozwijanej i kliknij przycisk **Dodaj konto** elementu menu.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)
6. Na stronie logowania Zaloguj się przy użyciu konta Microsoft, które zostało użyte do uaktywnienia konta platformy Azure.
7. Gdy użytkownik jest zarejestrowany, kliknij przycisk **dalej**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)
8. Przy założeniu, że nie utworzono konta usługi lub magazynu chmury, Visual Studio pomoże utworzyć jednocześnie. W **Tworzenie usługi w chmurze i konto** okna dialogowego, wpisz nazwę żądanej usługi, a następnie wybierz odpowiedni region. Następnie kliknij pozycję **Utwórz**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)
9. Na stronie ustawień publikowania, sprawdź konfigurację, a następnie kliknij przycisk **publikowania**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)
   
   > [!NOTE]
   > Proces publikowania dla usługi w chmurze zajmuje dużo czasu. Włącz narzędzia Web Deploy dla wszystkich ról opcji ułatwia debugowanie znacznie szybsza, bo usługi w chmurze przy udostępnienia aktualizacji szybkiego (ale tymczasowe) dla poszczególnych ról sieci Web. Aby uzyskać więcej informacji na temat tej opcji, zobacz [publikowania usługi w chmurze przy użyciu narzędzia Azure](http://msdn.microsoft.com/library/ff683672.aspx).
   > 
   > 
   
    Gdy **dziennik aktywności programu Microsoft Azure** pokazuje, że stan publikowania **Ukończono**, spowoduje utworzenie punktu końcowego usługi CDN, który jest zintegrowany z usługą w chmurze.
   
   > [!WARNING]
   > Jeśli po opublikowaniu usługi w chmurze wdrożonej wyświetla komunikat o błędzie, prawdopodobnie ponieważ korzysta z usługi w chmurze wdrożeniu [gościa systemu operacyjnego, który nie obejmuje .NET 4.5.2](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates).  Można obejść ten problem przez [wdrażanie .NET 4.5.2 jako zadanie uruchamiania](../cloud-services/cloud-services-dotnet-install-dotnet.md).
   > 
   > 

## <a name="create-a-new-cdn-profile"></a>Tworzenie nowego profilu CDN
Profil CDN jest kolekcją punktów końcowych usługi CDN.  Każdy profil zawiera jeden lub więcej punktów końcowych usługi CDN.  Możesz używać wielu profilów, aby organizować punkty końcowe usługi CDN według domeny internetowej, aplikacji sieci Web lub innych kryteriów.

> [!TIP]
> Jeśli masz już profil CDN, który ma być używany na potrzeby tego samouczka, przejdź do [utworzyć nowy punkt końcowy CDN](#create-a-new-cdn-endpoint).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Tworzenie nowego punktu końcowego usługi CDN
**Aby utworzyć nowy punkt końcowy CDN dla konta magazynu**

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do swojego profilu CDN.  Być może został on przypięty do pulpitu nawigacyjnego w poprzednim kroku.  Jeśli nie, możesz go znaleźć, klikając pozycję **Przeglądaj**, następnie pozycję **Profile CDN** i klikając profil, do którego planujesz dodać punkt końcowy.
   
    Zostanie wyświetlony blok profilu CDN.
   
    ![Profil CDN][cdn-profile-settings]
2. Kliknij przycisk **Dodaj punkt końcowy**.
   
    ![Przycisk dodawania punktu końcowego][cdn-new-endpoint-button]
   
    Zostanie wyświetlony blok **Dodawanie punktu końcowego**.
   
    ![Blok dodawania punktu końcowego][cdn-add-endpoint]
3. Wprowadź **nazwę** dla tego punktu końcowego usługi CDN.  Ta nazwa będzie używana do uzyskiwania dostępu do buforowanych zasobów w domenie `<EndpointName>.azureedge.net`.
4. W **typ źródła** listy rozwijanej wybierz *usługi w chmurze*.  
5. W **nazwę hosta źródła** listy rozwijanej wybierz usługi w chmurze.
6. Pozostaw wartości domyślne dla **ścieżki źródła**, **nagłówka hosta źródła**, i **port protokołu/źródła**.  Należy określić co najmniej jeden protokół (HTTP lub HTTPS).
7. Kliknij przycisk **Dodaj**, aby utworzyć nowy punkt końcowy.
8. Po utworzeniu punktu końcowego zostanie on wyświetlony na liście punktów końcowych dla profilu. Widok listy zawiera adres URL służący do uzyskiwania dostępu do zawartości buforowanej (w pamięci podręcznej), a także domeny źródła.
   
    ![Punkt końcowy usługi CDN][cdn-endpoint-success]
   
   > [!NOTE]
   > Punkt końcowy nie natychmiast będą dostępne do użycia.  Może potrwać do 90 minut rejestracji propagację za pośrednictwem sieci CDN. Użytkownicy, którzy spróbują użyć nazwy domeny usługi CDN natychmiast może zostać wyświetlony kod stanu 404, dopóki zawartość jest dostępna za pośrednictwem sieci CDN.
   > 
   > 

## <a name="test-the-cdn-endpoint"></a>Testowanie punktu końcowego usługi CDN
Gdy stan publikowania jest **Ukończono**, Otwórz okno przeglądarki i przejdź do **http://<cdnName>*.azureedge.net/Content/bootstrap.css**. Moje ustawienia ten adres URL jest:

    http://camservice.azureedge.net/Content/bootstrap.css

Które odpowiada następujący adres URL źródła na punkt końcowy CDN:

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

Po przejściu do **http://*&lt;cdnName >*.azureedge.net/Content/bootstrap.css**, w zależności od przeglądarki, pojawi się monit do pobierania lub otwierania bootstrap.css, dołączone z opublikowanych aplikacji sieci Web.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Podobnie można uzyskać dostępu do dowolny publicznie dostępny adres URL w **http://*&lt;serviceName >*.cloudapp.net/**skorelowane z punktu końcowego CDN. Na przykład:

* Plik ze ścieżki/Script js
* Dowolny plik zawartości z argumencie / ścieżki
* Wszelkie kontrolera/działania
* Jeśli ciąg zapytania jest włączona na punkt końcowy CDN dowolny adres URL z ciągami zapytań

W rzeczywistości z powyższej konfiguracji, może obsługiwać usługę w chmurze całego z **http://*&lt;cdnName >*.azureedge.net/**. Jeśli I przejdź do **http://camservice.azureedge.net/**, pobrać wyniku akcji z głównej/indeksu.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Oznacza to, mimo że zawsze jest dobrym rozwiązaniem do obsługi usługi w chmurze całego za pośrednictwem usługi Azure CDN. 

CDN z optymalizacją dostarczania statycznych nie niekoniecznie przyspiesza dostarczania dynamiczne zasoby, które nie mają być buforowane lub bardzo często są aktualizowane, ponieważ sieć CDN musi ściągnięcia nową wersję elementu zawartości z serwera pochodzenia bardzo często. W tym scenariuszu można włączyć [dynamiczne przyspieszenie lokacji](cdn-dynamic-site-acceleration.md) optymalizacji (DSA) na punkt końcowy CDN, który używa różnych technik w celu przyspieszenia dostarczania-buforowalnej zasobów dynamicznych. 

Jeśli masz lokacji z różnymi statycznych i dynamicznych zawartości, można wybrać do obsługi statycznej zawartości z sieci CDN z typem optymalizacji statyczne (np. dostarczenie ogólne sieci web) i udostępniać dynamicznej zawartości bezpośrednio od serwera pochodzenia, lub za pośrednictwem punktu końcowego usługi CDN z optymalizacją DSA włączone w przypadku. W tym celu ma już przedstawiono sposób uzyskać dostęp do poszczególnych plików zawartości z punktu końcowego CDN. I opisano, jak pełniły akcji kontrolera określonej za pomocą określonego punktu końcowego CDN służą zawartości z akcji kontrolera, za pomocą usługi Azure CDN.

Alternatywą jest Określ zawartość, która do obsługi z usługi Azure CDN na podstawie przypadku — w usłudze w chmurze. W tym celu ma już przedstawiono sposób uzyskać dostęp do poszczególnych plików zawartości z punktu końcowego CDN. I opisano, jak do obsługi akcji kontrolera określonej za pośrednictwem punktu końcowego sieci CDN w [udostępniać zawartość z akcji kontrolera, za pomocą usługi Azure CDN](#controller).

<a name="caching"></a>

## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>Skonfiguruj opcje buforowania plików statycznych w usłudze w chmurze
Dzięki integracji usługi Azure CDN w usłudze w chmurze można określić sposób zawartości statycznej pamięci podręcznej w punktu końcowego CDN. Aby to zrobić, otwórz *Web.config* z roli sieci Web projektu (np. WebRole1) i Dodaj `<staticContent>` elementu `<system.webServer>`. Poniżej XML konfiguruje pamięci podręcznej wygaśnie w ciągu 3 dni.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

Po wykonaniu tej czynności wszystkie pliki statyczne z usługi w chmurze będzie obserwować tę samą zasadę w pamięci podręcznej usługi CDN. Bardziej szczegółowego kontrolowania ustawień pamięci podręcznej, należy dodać *Web.config* plików do folderu, a następnie dodaj ustawienia istnieje. Na przykład dodać *Web.config* pliku *\Content* folderu i Zastąp zawartość za pomocą następujących XML:

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

To ustawienie powoduje, że wszystkie pliki statyczne z *\Content* folderu pamięci podręcznej przez 15 dni.

Aby uzyskać więcej informacji na temat konfigurowania `<clientCache>` elementu, zobacz [pamięci podręcznej klienta &lt;clientCache >](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

W [udostępniać zawartość z akcji kontrolera, za pomocą usługi Azure CDN](#controller), można również opisano, jak można skonfigurować ustawienia pamięci podręcznej dla wyników akcji kontrolera w pamięci podręcznej usługi CDN.

<a name="controller"></a>

## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Udostępniać zawartość z akcji kontrolera, za pomocą usługi Azure CDN
Po zintegrowaniu rolę sieci Web usługi w chmurze z usługą Azure CDN jest stosunkowo łatwa do obsługi zawartości z akcji kontrolera, za pomocą usługi Azure CDN. Inne niż obsługująca chmury usługi bezpośrednio za pomocą usługi Azure CDN (zostało to pokazane powyżej), [Maarten Balliauw](https://twitter.com/maartenballiauw) przedstawiono sposób wykonania fun kontrolera MemeGenerator w [zmniejsza opóźnienie w sieci web za pomocą usługi Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). I będzie po prostu go odtworzyć w tym miejscu.

Załóżmy, że w chmurze usługi, aby wygenerować memes oparte na małych obrazu Norris Jan (photo przez [światła Alan](http://www.flickr.com/photos/alan-light/218493788/)), takich jak to:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Masz prostą `Index` akcję, która pozwala określić superlatywy w obrazie, następnie generuje meme po przesyłać do akcji. Ponieważ jest Norris Jan, można oczekiwać tę stronę, aby stać się bardzo popularny globalnie. Jest to przykład obsługę częściowo dynamicznej zawartości z usługi Azure CDN.

Wykonaj kroki opisane powyżej, aby skonfigurować tę akcję kontrolera:

1. W *\Controllers* folderu, Utwórz nowy plik .cs o nazwie *MemeGeneratorController.cs* i Zastąp zawartość następującym kodem. Koniecznie Zastąp wyróżnione części z nazwy CDN.  
   
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;
   
        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();
   
                public ActionResult Index()
                {
                    return View();
                }
   
                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }
   
                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }
   
                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }
   
                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }
   
                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
                    Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
   
                    using (Graphics graphics = Graphics.FromImage(bitmap))
                    {
                        SizeF size = new SizeF();
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                        }
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                        }
                    }
   
                    MemoryStream ms = new MemoryStream();
                    bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
                    return File(ms.ToArray(), "image/png");
                }
   
                private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
                {
                    // Compute actual size, shrink if needed
                    while (true)
                    {
                        size = g.MeasureString(text, font);
   
                        // It fits, back out
                        if (size.Height < i.Height &&
                             size.Width < i.Width) { return font; }
   
                        // Try a smaller font (90% of old size)
                        Font oldFont = font;
                        font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                        oldFont.Dispose();
                    }
                }
            }
        }
2. Kliknij prawym przyciskiem myszy w domyślnym `Index()` akcji i wybierz **Dodaj widok**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)
3. Zaakceptuj ustawienia poniżej i kliknij przycisk **Dodaj**.
   
   ![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)
4. Otwórz nową *Views\MemeGenerator\Index.cshtml* i Zastąp zawartość następujących prostych HTML składania superlatywy:
   
        <h2>Meme Generator</h2>
   
        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>
5. Ponownie opublikuj usługę w chmurze i przejdź do **http://*&lt;serviceName >*.cloudapp.net/MemeGenerator/Index** w przeglądarce.

Po przesłaniu wartości formularza do `/MemeGenerator/Index`, `Index_Post` metoda akcji zwraca łącze do `Show` metodę akcji za pomocą odpowiednich identyfikator wejściowy. Po kliknięciu łącza zostanie wyświetlona następujący kod:  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

Jeśli lokalne debuger jest dołączony, zostanie wyświetlona środowisko regularne debugowania przy użyciu przekierowania lokalnego. Jeśli działa w usłudze w chmurze, następnie go spowoduje przekierowanie do:

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Które odpowiada następujący adres URL źródła na punkt końcowy CDN:

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Następnie można użyć `OutputCacheAttribute` atrybutu `Generate` metodę, aby określić, jak wynik akcji powinno być buforowane, które podlegają Azure CDN. Poniższy kod Określ pamięci podręcznej ważności 1 godzina (3600 sekund).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Podobnie użytkownik może służyć się zawartość z dowolnego akcji kontrolera w usłudze w chmurze za pośrednictwem sieci Azure CDN z odpowiednią opcję buforowania.

W następnej sekcji I opisano sposób obsługi skryptów powiązane i zminimalizowany i CSS za pomocą usługi Azure CDN.

<a name="bundling"></a>

## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>Integracja z usługą Azure CDN ASP.NET tworzenie pakietów i minimalizowanie
Arkusze stylów CSS i skrypty Zmień rzadko i głównym nadają się do usługi Azure CDN pamięci podręcznej. Obsługująca całej roli sieci Web za pomocą programu Azure CDN jest najprostszym sposobem integracji z usługą Azure CDN tworzenie pakietów i minimalizowanie. Jednak ponieważ może chcesz to zrobić, I opisano, jak to zrobić, gdy zachowania środowisko programistyczne odpowiednie platformy ASP.NET tworzenie pakietów i minimalizowanie, takich jak:

* Środowisko trybu dużą debugowania
* Prostsze wdrożenia
* Natychmiastowej aktualizacji do klientów w celu uaktualnienia wersji skryptu/CSS
* Mechanizm rezerwowy, gdy punkt końcowy CDN nie powiodło się.
* Minimalizowanie modyfikacji kodu

W **WebRole1** projektu, który został utworzony w [integracji punktu końcowego usługi Azure CDN z witryny sieci Web platformy Azure i udostępniać zawartość statyczną na stronach sieci Web z usługi Azure CDN](#deploy), otwórz *App_Start\BundleConfig.cs* i spójrz na `bundles.Add()` wywołania metody.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

Pierwszy `bundles.Add()` instrukcja dodaje pakiet skryptu w katalogu wirtualnego `~/bundles/jquery`. Następnie otwórz *Views\Shared\_Layout.cshtml* aby zobaczyć, jak jest renderowany tag skryptu pakietu. Można znaleźć następujący wiersz kodu Razor:

    @Scripts.Render("~/bundles/jquery")

Po uruchomieniu tego kodu Razor w roli sieci Web platformy Azure będą zawierały `<script>` tagu dla pakietu skryptu podobny do następującego:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Jednak gdy jest uruchamiana w programie Visual Studio, wpisując `F5`, go spowoduje, że każdy plik skryptu w pakiecie pojedynczo (w przypadku powyższych skryptu tylko jeden plik jest w pakiecie):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Umożliwia to debugowanie kodu JavaScript w środowisku projektowania w czasie zmniejszenie połączeń współbieżnych klienta (grupowania) i poprawy pliku Pobierz wydajności (minimalizowanie) w środowisku produkcyjnym. Jest bardzo funkcji w celu zachowania w przypadku integracji usługi Azure CDN. Ponadto, ponieważ renderowanych pakietu już zawiera ciąg wersji automatycznie wygenerowaną, chcesz replikować funkcja więc po zaktualizowaniu wersji jQuery za pośrednictwem pakietu NuGet plik może być uaktualniony po stronie klienta tak szybko, jak to możliwe.

Wykonaj poniższe kroki do integracji ASP.NET tworzenie pakietów i minimalizowanie z punktu końcowego CDN.

1. W *App_Start\BundleConfig.cs*, zmodyfikuj `bundles.Add()` metod, aby użyć innego [Konstruktor pakietu](http://msdn.microsoft.com/library/jj646464.aspx), który określa adres usługi CDN. Aby to zrobić, Zamień `RegisterBundles` definicję metody z następującym kodem:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Pamiętaj zastąpić `<yourCDNName>` nazwą Twojej usługi Azure CDN.
   
    Prosty opis ustawienie `bundles.UseCdn = true` i dodać dokładnie przygotowanego adresu CDN do każdego pakietu. Na przykład pierwszy konstruktora w kodzie:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
   
    jest taka sama jak:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))
   
    Ten konstruktor informuje ASP.NET tworzenie pakietów i minimalizowanie renderowania pojedynczego skryptu plików podczas debugowania lokalnie, ale umożliwia dostęp do danego skryptu określonego adresu CDN. Zwróć jednak uwagę dwie najważniejsze czynniki z dokładnie przygotowanego CDN adres URL:
   
   * Punkt początkowy dla tego adresu URL usługi CDN jest `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, który jest rzeczywiście katalog wirtualny pakietu skryptu w usłudze w chmurze.
   * Ponieważ używasz konstruktora CDN tag skryptu CDN dla pakietu nie zawiera już ciąg wersji automatycznie generowane w renderowanym adresu URL. Musisz ręcznie wygenerować ciąg wersji unikatowy za każdym razem, gdy pakietu skryptu są modyfikowane w celu wymusić Chybienie pamięci podręcznej, w sieci Azure CDN. W tym samym czasie to ciąg wersji unikatowy musi pozostać stałej za pośrednictwem cyklu życia wdrożenia w celu zmaksymalizowania trafień w pamięci podręcznej w sieci Azure CDN, po wdrożeniu pakietu.
   * Ciąg zapytania v = < W.X.Y.Z > ściąga z *Properties\AssemblyInfo.cs* w projekcie roli sieci Web. Może mieć przepływ pracy wdrożenia, który zawiera przyrostową wersję zestawu za każdym razem, gdy publikowanie na platformie Azure. Lub, można zmodyfikować *Properties\AssemblyInfo.cs* w swoim projekcie automatycznie zwiększać ciąg wersji zawsze można utworzyć przy użyciu znaku wieloznacznego "*". Na przykład:
     
        [assembly: AssemblyVersion("1.0.0.*")]
     
     Strategii usprawnić generowania unikatowy ciąg dla cyklu życia wdrożenia będzie działać w tym miejscu.
2. Ponownie opublikować usługi w chmurze i dostęp do strony głównej.
3. Wyświetl kod HTML dla strony. Można wyświetlić adres URL CDN renderowane zawierające ciąg wersji unikatowy za każdym razem, gdy ponownie opublikować zmiany do usługi w chmurze. Na przykład:  
   
        ...
   
        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
   
        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>
   
        ...
   
        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>
   
        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>
   
        ...
4. W programie Visual Studio debugowanie usługi w chmurze w programie Visual Studio, wpisując `F5`.,
5. Wyświetl kod HTML dla strony. Będą również widzieć każdego pliku skryptu indywidualnie renderowane, dzięki czemu mają debugowania spójne środowisko w programie Visual Studio.  
   
        ...
   
            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>
   
            <script src="/Scripts/modernizr-2.6.2.js"></script>
   
        ...
   
            <script src="/Scripts/jquery-1.10.2.js"></script>
   
            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>
   
        ...   

<a name="fallback"></a>

## <a name="fallback-mechanism-for-cdn-urls"></a>Mechanizm rezerwowy umożliwiający CDN adresy URL
Kiedy punktu końcowego usługi Azure CDN nie powiedzie się z jakiegokolwiek powodu, który chcesz strony sieci Web jako inteligentne uzyskać dostęp do serwera sieci Web źródła jako opcji rezerwowej ładowania JavaScript lub ładowania początkowego. Jest to poważny utratę obrazów w witrynie sieci Web z powodu niedostępności usługi CDN, ale wiele poważniejsze utratę strony kluczowe funkcje udostępniane przez własne skrypty i arkusze stylów.

[Pakietu](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) klasa zawiera właściwość o nazwie [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) , umożliwia konfigurowanie mechanizm rezerwowy umożliwiający awarii sieci CDN. Aby używać tej właściwości, wykonaj następujące czynności:

1. W projekcie roli sieci Web otwórz *App_Start\BundleConfig.cs*, gdzie adresu CDN jest dodana w każdym [Konstruktor pakietu](http://msdn.microsoft.com/library/jj646464.aspx)i wprowadź następujące zmiany wyróżnione dodać mechanizm rezerwowy do pakietów domyślne:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                  "~/Scripts/bootstrap.js",
                                  "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Gdy `CdnFallbackExpression` jest niezerowa, skrypt wstrzykiwane do HTML, aby sprawdzić, czy pakietu została pomyślnie załadowana, a jeśli nie, uzyskać dostęp do pakietu bezpośrednio z serwera sieci Web źródła. Ta właściwość musi można ustawić wyrażenia JavaScript, który umożliwia sprawdzenie, czy odpowiedniego pakietu CDN jest poprawnie załadowany. Wyrażenie potrzebne do przetestowania każdego pakietu różni się zgodnie z zawartości. Dla powyższych pakietów domyślne:
   
   * `window.jquery` jest zdefiniowany w jquery {wersja}-js
   * `$.validator` jest zdefiniowany w jquery.validate.js
   * `window.Modernizr` jest zdefiniowany w modernizer {wersja}-js
   * `$.fn.modal` jest zdefiniowany w bootstrap.js
     
     Zwróć uwagę, że nie ustawił I CdnFallbackExpression dla `~/Cointent/css` pakietu. Ponieważ jest obecnie [usterkę w System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) który injects `<script>` tag rezerwowy CSS zamiast oczekiwanej `<link>` tagu.
     
     Istnieje, jednak duże [powrotu pakietu styl](https://github.com/EmberConsultingGroup/StyleBundleFallback) oferowane przez [Członkowskimi konsultacji grupy](https://github.com/EmberConsultingGroup).
2. Aby użyć obejście CSS, Utwórz nowy plik .cs w projekcie roli sieci Web *App_Start* folder o nazwie *StyleBundleExtensions.cs*i zastąp jego zawartość z [kod z usługi GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).
3. W *App_Start\StyleFundleExtensions.cs*, Zmień nazwę przestrzeni nazw na nazwę roli sieci Web (np. **WebRole1**).
4. Wróć do `App_Start\BundleConfig.cs` i zmodyfikuj ostatniego `bundles.Add` instrukcji z następujący wyróżniony kod:  
   
        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));
   
    Tej nowej metody rozszerzenia używa tego samego pomysł, aby uruchomić skrypt w języku HTML, aby sprawdzić w modelu DOM dla zgodną nazwę klasy, nazwy reguły i wartość reguły zdefiniowane w pakiecie CSS, a następnie powróci do serwera sieci Web źródła Jeśli go nie można odnaleźć dopasowania.
5. Ponownie opublikuj usługę w chmurze i dostęp do strony głównej.
6. Wyświetl kod HTML dla strony. Wprowadzony skrypty powinien znajdować się podobny do następującego:    
   
        ...
   
        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
   
        ...
   
            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
   
        ...

    Należy zauważyć, że wprowadzony skryptu dla pakietu CSS nadal zawiera wadliwe niewykorzystany z `CdnFallbackExpression` właściwości w wierszu:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Od czasu pierwszej części, ale || wyrażenie zawsze zwróci wartość true (w wierszu powyżej bezpośrednio), funkcja wywołania document.write() nigdy nie zostanie uruchomiony.

## <a name="more-information"></a>Więcej informacji
* [Omówienie usługi Azure Content Delivery Network (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
* [Za pomocą usługi Azure CDN](cdn-create-new-endpoint.md)
* [ASP.NET tworzenie pakietów i minimalizowanie](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)

[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png
