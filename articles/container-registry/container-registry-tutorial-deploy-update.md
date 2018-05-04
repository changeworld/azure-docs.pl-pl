---
title: Samouczek dotyczący usługi Azure Container Registry — wypychanie zaktualizowanego obrazu do wdrożeń regionalnych
description: Wypchnij zmodyfikowany obraz Docker do usługi Azure Container Registry z replikacją geograficzną, a następnie zobacz zmiany automatycznie wdrażane w aplikacjach internetowych działających w wielu regionach. Trzecia część trzyczęściowej serii.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 2e9a46f2a99bc9b530ac5859068bde58bf5b5098
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-push-an-updated-image-to-regional-deployments"></a>Samouczek: wypychanie zaktualizowanego obrazu do wdrożeń regionalnych

Niniejszy samouczek jest trzecią częścią trzyczęściowej serii. W [poprzednim samouczku](container-registry-tutorial-deploy-app.md) skonfigurowano replikację geograficzną dla dwóch różnych regionalnych wdrożeń aplikacji internetowej. W tym samouczku najpierw zmodyfikujesz aplikację, a następnie utworzysz nowy obraz kontenera i wypchniesz go do rejestru replikowanego geograficznie. Na koniec wyświetlisz zmianę, wdrożoną automatycznie przez elementy webhook usługi Azure Container Registry w obu wystąpieniach aplikacji internetowej.

Ten samouczek, będący ostatnią częścią serii, obejmuje:

> [!div class="checklist"]
> * Modyfikowanie kodu HTML aplikacji internetowej
> * Budowanie i tagowanie obrazu platformy Docker
> * Wypychanie zmiany do usługi Azure Container Registry
> * Wyświetlanie zaktualizowanej aplikacji w dwóch różnych regionach

Jeśli jeszcze nie skonfigurowano dwóch regionalnych wdrożeń usługi *Web App for Containers*, wróć do poprzedniego samouczka z tej serii — [Wdrażanie aplikacji internetowej z usługi Azure Container Registry](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>Modyfikowanie aplikacji sieci Web

W tym kroku należy wprowadzić w aplikacji zmianę, która będzie dobrze widoczna po wypchnięciu zaktualizowanego obrazu kontenera do usługi Azure Container Registry.

Znajdź plik `AcrHelloworld/Views/Home/Index.cshtml` w źródle aplikacji [sklonowanym z usługi GitHub](container-registry-tutorial-prepare-registry.md#get-application-code) w poprzednim samouczku i otwórz go w edytorze tekstu. Dodaj następujący wiersz poniżej istniejącego wiersza `<h1>`:

```html
<h1>MODIFIED</h1>
```

Zmodyfikowany plik `Index.cshtml` powinien wyglądać podobnie do poniższego:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>Ponowne tworzenie obrazu

Teraz, po zaktualizowaniu aplikacji internetowej, należy ponownie utworzyć obraz jej kontenera. Tak jak wcześniej, użyj w pełni kwalifikowanej nazwy obrazu zawierającej adres URL serwera logowania dla tagu:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Wypychanie obrazu do usługi Azure Container Registry

Teraz wypchnij zaktualizowany kontener obrazu *acr-helloworld* do rejestru replikowanego geograficznie. W tym miejscu wykonujesz jedno polecenie `docker push`, aby wdrożyć zaktualizowany obraz w replikach rejestru w regionach *Zachodnie stany USA* i *Wschodnie stany USA*.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Dane wyjściowe powinny wyglądać podobnie do następujących:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>Wyświetlanie dzienników elementów webhook

Gdy obraz jest replikowany, możesz zobaczyć wyzwalane elementy webhook usługi Azure Container Registry.

Aby wyświetlić regionalne elementy webhook utworzone podczas wdrażania kontenera w usłudze *Web App for Containers* w poprzednim samouczku, przejdź do usługi Container Registry w witrynie Azure Portal, a następnie wybierz pozycję **Elementy webhook** w obszarze **USŁUGI**.

![Elementy webhook usługi Container Registry w witrynie Azure Portal][tutorial-portal-01]

Wybierz każdy element webhook, aby wyświetlić historię jego wywołań i odpowiedzi. W dziennikach obu elementów webhook powinien być wyświetlony wiersz akcji **push** (wypychanie). Tutaj dziennik dla elementu webhook w regionie *Zachodnie stany USA* wyświetla akcję **push** wyzwoloną przez polecenie `docker push` w poprzednim kroku:

![Dziennik elementów webhook usługi Container Registry w witrynie Azure Portal (Zachodnie stany USA)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Wyświetlanie zaktualizowanej aplikacji internetowej

Elementy webhook powiadamiają usługę Web Apps o wypchnięciu nowego obrazu do rejestru, co automatycznie powoduje wdrożenie zaktualizowanego kontenera w dwóch regionalnych aplikacjach internetowych.

Sprawdź, czy aplikacja została zaktualizowana w obu wdrożeniach, przechodząc do obu regionalnych wdrożeń aplikacji internetowej w przeglądarce internetowej. Przypominamy, że adres URL wdrożonej aplikacji internetowej można znaleźć w prawym górnym rogu każdej karty Przegląd usługi App Service.

![Przegląd usługi App Service w witrynie Azure Portal][tutorial-portal-03]

Aby wyświetlić zaktualizowaną aplikację, wybierz link w obszarze Przegląd usługi App Service. Oto przykładowy widok aplikacji uruchomionej w regionie *Zachodnie stany USA*:

![Wyświetlany w przeglądarce widok zmodyfikowanej aplikacji internetowej uruchomionej w regionie Zachodnie stany USA][deployed-app-westus-modified]

Sprawdź, czy zaktualizowany obraz kontenera został również wdrożony we wdrożeniu dla regionu *Wschodnie stany USA*, wyświetlając go w przeglądarce.

![Wyświetlany w przeglądarce widok zmodyfikowanej aplikacji internetowej uruchomionej w regionie Wschodnie stany USA][deployed-app-eastus-modified]

Za pomocą jednego polecenia `docker push` zaktualizowane zostały oba regionalne wdrożenia aplikacji internetowej, a usługa Azure Container Registry obsłużyła obrazy kontenera z repozytorium w pobliżu sieci.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z samouczkiem utworzono i wypchnięto nową wersję kontenera aplikacji internetowej do rejestru replikowanego geograficznie. Elementy webhook w usłudze Azure Container Registry powiadomiły o aktualizacji usługę Web Apps for Containers, która wyzwoliła lokalne ściągnięcie z replik rejestru.

W tym samouczku, ostatnim z serii, wykonano następujące czynności:

> [!div class="checklist"]
> * Zaktualizowano kod HTML aplikacji internetowej
> * Zbudowano i otagowano obraz platformy Docker
> * Wypchnięto zmiany do usługi Azure Container Registry
> * Wyświetlono zaktualizowaną aplikację w dwóch różnych regionach

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png