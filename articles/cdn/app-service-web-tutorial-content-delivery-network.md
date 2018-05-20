---
title: Samouczek — dodawanie usługi Azure CDN do aplikacji internetowej usługi Azure App Service | Microsoft Docs
description: W tym samouczku usługa Azure Content Delivery Network (CDN) jest dodawana do aplikacji internetowej usługi Azure App Service, aby buforować i dostarczać pliki statyczne z serwerów znajdujących się blisko klientów na całym świecie.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: d782f5bf30160d85d7d9ef7a00190551f9a9843a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Samouczek: dodawanie usługi Azure CDN do aplikacji internetowej usługi Azure App Service

W tym samouczku przedstawiono sposób dodawania usługi Azure CDN do [aplikacji sieci Web w usłudze Azure App Service](../app-service/app-service-web-overview.md). Usługa [Azure Content Delivery Network (CDN)](cdn-overview.md) buforuje zawartość statyczną sieci Web w strategicznie rozmieszczonych lokalizacjach w celu zapewnienia maksymalnej przepływności dostarczania zawartości dla użytkowników. Usługa Azure CDN zmniejsza również obciążenie serwera w aplikacji internetowej. 

Oto strona główna przykładowej statycznej witryny HTML, z którą będziesz pracować:

![Strona główna przykładowej aplikacji](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

Zawartość:

> [!div class="checklist"]
> * Tworzenie punktu końcowego usługi CDN.
> * Odświeżanie elementów zawartości zapisanych w pamięci podręcznej.
> * Używanie ciągów zapytań do sterowania zbuforowanymi wersjami.
> * Używanie domeny niestandardowej na potrzeby punktu końcowego usługi CDN.

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- [Zainstaluj oprogramowanie Git](https://git-scm.com/)
- [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Tworzenie aplikacji sieci Web

Aby utworzyć aplikację internetową, z którą będziesz pracować, postępuj zgodnie z instrukcjami opisanymi w artykule [Tworzenie statycznej witryny HTML — szybki start](../app-service/app-service-web-get-started-html.md) do kroku **Przechodzenie do aplikacji**.

### <a name="have-a-custom-domain-ready"></a>Przygotowywanie domeny niestandardowej

Aby ukończyć etap tworzenia domeny niestandardowej w tym samouczku, należy posiadać domenę niestandardową oraz dostęp do rejestru systemu DNS dostawcy domeny. Aby na przykład dodać wpisy DNS dla domen contoso.com i www.contoso.com, musisz mieć możliwość skonfigurowania ustawień DNS dla domeny głównej contoso.com.

Jeśli nie masz jeszcze nazwy domeny, możesz postępować według instrukcji zawartych w [samouczku dotyczącym domeny usługi App Service](../app-service/custom-dns-web-site-buydomains-web-app.md) w celu zakupienia domeny za pośrednictwem witryny Azure Portal. 

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Otwórz przeglądarkę i przejdź do witryny [Azure Portal](https://portal.azure.com).

## <a name="create-a-cdn-profile-and-endpoint"></a>Tworzenie profilu i punktu końcowego usługi CDN

Na lewym panelu nawigacyjnym wybierz pozycję **App Services**, a następnie wybierz aplikację utworzoną w samouczku [tworzenie statycznej witryny HTML — szybki start](../app-service/app-service-web-get-started-html.md).

![Wybieranie aplikacji usługi App Service w portalu](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

Na stronie **App Service** w sekcji **Ustawienia** wybierz pozycję **Sieć > Skonfiguruj usługę Azure CDN dla aplikacji**.

![Wybieranie usługi CDN w portalu](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

Na stronie **Azure Content Delivery Network** określ ustawienia dla **nowego punktu końcowego** zgodnie z informacjami podanymi w tabeli.

![Tworzenie profilu i punktu końcowego w portalu](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| Ustawienie | Sugerowana wartość | Opis |
| ------- | --------------- | ----------- |
| **Profil CDN** | myCDNProfile | Wybierz pozycję **Utwórz nowy**, aby utworzyć profil CDN. Profil CDN jest kolekcją punktów końcowych usługi CDN znajdujących się w tej samej warstwie cenowej. |
| **Warstwa cenowa** | Standard Akamai | [Warstwa cenowa](cdn-features.md) określa dostawcę i dostępne funkcje. W tym samouczku jest używana warstwa *Standard Akamai*. |
| **Nazwa punktu końcowego usługi CDN** | Dowolna unikatowa nazwa w domenie azureedge.net | Dostęp do zbuforowanych zasobów można uzyskać w domenie *\<nazwapunktukoncowego>.azureedge.net*.

Wybierz pozycję **Utwórz**.

Na platformie Azure zostanie utworzony profil i punkt końcowy. Nowy punkt końcowy zostanie wyświetlony na liście **Punkty końcowe**, a kiedy zostanie zaprowizowany, będzie miał stan **Uruchomiono**.

![Nowy punkt końcowy na liście](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Testowanie punktu końcowego usługi CDN

W przypadku wybrania warstwy cenowej Verizon propagacja punktu końcowego trwa zwykle około 90 minut. W przypadku warstwy Akamai propagacja zajmuje kilka minut.

Przykładowa aplikacja ma plik *index.html* i foldery *css*, *img* i *js*, które zawierają inne statyczne elementy zawartości. Ścieżki zawartości dla wszystkich tych plików są takie same w punkcie końcowym usługi CDN. Na przykład oba następujące adresy URL umożliwiają dostęp do pliku *bootstrap.css* w folderze *css*:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

W przeglądarce przejdź pod następujący adres URL:

```
http://<endpointname>.azureedge.net/index.html
```

![Strona główna aplikacji przykładowej udostępnianej przez usługę CDN](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

 Zobaczysz tę samą stronę, którą uruchomiono wcześniej w aplikacji internetowej platformy Azure. Usługa Azure CDN pobrała elementy zawartości oryginalnej aplikacji internetowej i udostępnia je za pośrednictwem punktu końcowego usługi CDN.

Odśwież tę stronę, aby się upewnić, że jest ona buforowana w usłudze CDN. Aby usługa CDN umieściła żądaną zawartość w pamięci podręcznej, czasami wymagane są dwa żądania tego samego elementu zawartości.

Aby uzyskać więcej informacji o sposobie tworzenia profilów i punktów końcowych usługi Azure CDN, zobacz [Wprowadzenie do usługi Azure CDN](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Przeczyszczanie usługi CDN

Usługa CDN okresowo odświeża swoje zasoby z oryginalnej aplikacji sieci Web zgodnie z konfiguracją czasu wygaśnięcia (TTL). Domyślny czas wygaśnięcia wynosi siedem dni.

Czasami może zajść konieczność odświeżenia usługi CDN przed upływem czasu wygaśnięcia — na przykład podczas wdrażania zaktualizowanej zawartości w aplikacji internetowej. Aby wyzwolić odświeżanie, ręcznie przeczyść zasoby usługi CDN. 

W tej części samouczka zostanie wdrożona zmiana w aplikacji sieci Web i nastąpi przeczyszczenie usługi CDN w celu wyzwolenia odświeżenia pamięci podręcznej tej usługi.

### <a name="deploy-a-change-to-the-web-app"></a>Wdrażanie zmiany w aplikacji sieci Web

Otwórz plik *index.html* i dodaj ciąg *- V2* do nagłówka H1, jak pokazano w poniższym przykładzie: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Zatwierdź zmianę i wdróż ją w aplikacji sieci Web.

```bash
git commit -am "version 2"
git push azure master
```

Po zakończeniu wdrażania przejdź do adresu URL aplikacji internetowej, aby zobaczyć zmianę.

```
http://<appname>.azurewebsites.net/index.html
```

![Ciąg V2 w tytule aplikacji sieci Web](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

Jeśli przejdziesz do adresu URL punktu końcowego usługi CDN dla strony głównej, nie zobaczysz zmiany, ponieważ wersja buforowana w usłudze CDN jeszcze nie wygasła. 

```
http://<endpointname>.azureedge.net/index.html
```

![Brak ciągu V2 w tytule w usłudze CDN](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Przeczyszczanie usługi CDN w portalu

Aby w usłudze CDN wyzwolić zaktualizowanie zbuforowanej wersji, przeczyść usługę CDN.

Na lewym panelu nawigacyjnym portalu wybierz pozycję **Grupy zasobów**, a następnie wybierz grupę zasobów utworzoną na potrzeby aplikacji sieci Web (myResourceGroup).

![Wybieranie grupy zasobów](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

Na liście zasobów wybierz punkt końcowy usługi CDN.

![Wybieranie punktu końcowego](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

W górnej części strony **Punkt końcowy** wybierz pozycję **Przeczyść**.

![Wybieranie pozycji Przeczyść](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

Wprowadź ścieżki zawartości, które chcesz przeczyścić. Możesz podać pełną ścieżkę, aby przeczyścić pojedynczy plik, lub segment ścieżki, aby przeczyścić i odświeżyć całą zawartość folderu. Ponieważ zmieniono plik *index.html*, upewnij się, że znajduje się na jednej ze ścieżek.

W dolnej części strony wybierz pozycję **Przeczyść**.

![Strona przeczyszczania](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Sprawdzanie zaktualizowania usługi CDN

Poczekaj na zakończenie przetwarzania żądania przeczyszczenia, co zwykle trwa kilka minut. Aby wyświetlić bieżący stan, wybierz ikonę dzwonka w górnej części strony. 

![Powiadomienie o przeczyszczaniu](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

Po przejściu do adresu URL punktu końcowego usługi CDN dla pliku *index.html* zobaczysz element *V2* dodany do tytułu na stronie głównej, co oznacza, że pamięć podręczna usługi CDN została odświeżona.

```
http://<endpointname>.azureedge.net/index.html
```

![Ciąg V2 w tytule w usłudze CDN](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

Aby uzyskać więcej informacji, zobacz [Przeczyszczanie punktu końcowego usługi Azure CDN](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Używanie ciągów zapytań do sterowania wersjami zawartości

Usługa Azure CDN oferuje następujące opcje zachowania podczas buforowania:

* Ignoruj ciągi zapytań
* Pomiń buforowanie dla ciągów zapytań
* Buforuj każdy unikatowy adres URL 

Pierwsza opcja to opcja domyślna. W tym przypadku istnieje tylko jedna zbuforowana wersja elementu zawartości, niezależnie od tego, jakiego ciągu zapytania użyto w adresie URL. 

W tej części samouczka zostanie zmienione zachowanie buforowania w taki sposób, że w pamięci podręcznej będzie umieszczany każdy unikatowy adres URL.

### <a name="change-the-cache-behavior"></a>Zmienianie zachowania buforowania

W witrynie Azure Portal na stronie **Punkt końcowy usługi CDN** wybierz pozycję **Pamięć podręczna**.

Z listy rozwijanej **Zachowanie buforowania ciągu kwerendy** wybierz pozycję **Buforuj każdy unikatowy adres URL**.

Wybierz pozycję **Zapisz**.

![Wybieranie zachowania buforowania ciągów zapytań](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Sprawdzanie, czy unikatowe adresy URL są buforowane osobno

W przeglądarce przejdź do strony głównej w punkcie końcowym usługi CDN i dołącz ciąg zapytania: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Usługa Azure CDN zwraca bieżącą zawartość aplikacji internetowej, w tym ciąg *V2* w nagłówku. 

Odśwież tę stronę, aby się upewnić, że jest ona buforowana w usłudze CDN. 

Otwórz plik *index.html*, zmień ciąg *V2* na *V3*, a następnie wdróż zmianę. 

```bash
git commit -am "version 3"
git push azure master
```

W przeglądarce przejdź do adresu URL punktu końcowego usługi CDN, korzystając z nowego ciągu zapytania, na przykład `q=2`. Usługa Azure CDN pobiera bieżący plik *index.html* i wyświetla ciąg *V3*. Jednak jeśli przejdziesz do punktu końcowego usługi CDN, korzystając z ciągu zapytania `q=1`, zobaczysz ciąg *V2*.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 w tytule w usłudze CDN, ciąg zapytania 2](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 w tytule w usłudze CDN, ciąg zapytania 1](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

W tych danych wyjściowych widać, że każdy ciąg zapytania jest traktowany inaczej:

* Wcześniej użyto ciągu q=1, dlatego zwrócona została zbuforowana zawartość (V2).
* Ciąg q=2 jest nowy, więc pobierana i zwracana jest najnowsza zawartość aplikacji internetowej (V3).

Aby uzyskać więcej informacji, zobacz [Control Azure CDN caching behavior with query strings](../cdn/cdn-query-string.md) (Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań).

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>Mapowanie domeny niestandardowej na punkt końcowy usługi CDN

Mapowanie domeny niestandardowej na punkt końcowy usługi CDN odbywa się przez utworzenie rekordu CNAME. Rekord CNAME jest funkcją systemu DNS, która mapuje domenę źródłową na domenę docelową. Na przykład domenę cdn.contoso.com lub static.contoso.com można mapować na domenę contoso.azureedge.net.

Jeśli nie masz domeny niestandardowej, możesz postępować według instrukcji zawartych w [samouczku dotyczącym domeny usługi App Service](../app-service/custom-dns-web-site-buydomains-web-app.md) w celu zakupienia domeny za pośrednictwem witryny Azure Portal. 

### <a name="find-the-hostname-to-use-with-the-cname"></a>Znajdowanie nazwy hosta do użycia z rekordem CNAME

W witrynie Azure Portal na stronie **Punkt końcowy** upewnij się, że na lewym panelu nawigacyjnym wybrano pozycję **Przegląd**, a następnie w górnej części strony wybierz przycisk **+ Domena niestandardowa**.

![Wybieranie opcji dodawania domeny niestandardowej](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

Na stronie **Dodawanie domeny niestandardowej** zobaczysz nazwę hosta punktu końcowego do użycia w celu utworzenia rekordu CNAME. Nazwa hosta jest tworzona na podstawie adresu URL punktu końcowego usługi CDN:  **&lt;NazwaPunktuKoncowego>.azureedge.net**. 

![Strona dodawania domeny](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>Konfigurowanie rekordu CNAME u rejestratora domen

Przejdź do witryny sieci Web swojego rejestratora domen i znajdź sekcję tworzenia rekordów DNS. Na przykład ta sekcja może mieć nazwę **Nazwa domeny**, **DNS** lub **Zarządzanie serwerem nazw**.

Znajdź sekcję zarządzania rekordami CNAME. W tym celu może być konieczne przejście do strony ustawień zaawansowanych i poszukanie słów CNAME, Alias lub Subdomains (Poddomeny).

Utwórz rekord CNAME mapujący wybraną poddomenę (na przykład **static** lub **cdn**) na **nazwę hosta punktu końcowego** wyświetlaną wcześniej w portalu. 

### <a name="enter-the-custom-domain-in-azure"></a>Wprowadzanie domeny niestandardowej na platformie Azure

Powróć do strony **Dodaj domenę niestandardową** i wprowadź w oknie dialogowym domenę niestandardową, w tym poddomenę. Na przykład wprowadź domenę *cdn.contoso.com*.   
   
Platforma Azure sprawdzi, czy dla wprowadzonej nazwy domeny istnieje rekord CNAME. Jeśli rekord CNAME jest poprawny, domena niestandardowa jest weryfikowana.

Może upłynąć trochę czasu, zanim rekord CNAME zostanie rozpropagowany do serwerów nazw w Internecie. Jeśli domena nie zostanie zweryfikowana natychmiast, zaczekaj kilka minut i spróbuj ponownie.

### <a name="test-the-custom-domain"></a>Testowanie domeny niestandardowej

W przeglądarce przejdź do pliku *index.html*, korzystając z domeny niestandardowej (na przykład cdn.contoso.com/index.html), aby sprawdzić, czy wynik jest taki sam jak podczas bezpośredniego przechodzenia do adresu &lt;nazwa_punktu_końcowego&gt;azureedge.net/index.html.

![Strona główna przykładowej aplikacji wyświetlona przy użyciu adresu URL domeny niestandardowej](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

Aby uzyskać więcej informacji, zobacz [Map Azure CDN content to a custom domain](../cdn/cdn-map-content-to-custom-domain.md) (Mapowanie zawartości usługi Azure CDN na domenę niestandardową).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Tworzenie punktu końcowego usługi CDN.
> * Odświeżanie elementów zawartości zapisanych w pamięci podręcznej.
> * Używanie ciągów zapytań do sterowania zbuforowanymi wersjami.
> * Używanie domeny niestandardowej na potrzeby punktu końcowego usługi CDN.

Zapoznaj się z następującymi artykułami, aby dowiedzieć się, jak zoptymalizować wydajność usługi CDN:

> [!div class="nextstepaction"]
> [Samouczek: dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN](cdn-map-content-to-custom-domain.md)


