---
title: Jak zainstalować i uruchamiać kontenery
titlesuffix: Computer Vision - Cognitive Services - Azure
description: Jak pobrać, zainstalować i uruchamiaj kontenery dla przetwarzania obrazów w tym samouczku wskazówki.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 2ba7039fe42e3b5638b99161e12e9888bc852f87
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51635016"
---
# <a name="install-and-run-containers"></a>Instalowanie i uruchamianie kontenerów

Konteneryzacji to podejście do dystrybucji oprogramowania, w której aplikacja lub usługa jest dostarczana w postaci obrazu kontenera. Konfiguracja i zależności dla aplikacji lub usługi są objęte obrazu kontenera. Następnie można wdrożyć obraz kontenera na hoście kontenera, z niewielkich modyfikacji. Kontenery są odizolowane od siebie nawzajem i zasadniczego systemu operacyjnego za pomocą mniejszy wyświetlacz niż maszyny wirtualnej. Kontenery mogą utworzonych na podstawie obrazów kontenera do krótkoterminowych zadań i usuwane, gdy nie będą już potrzebne.

Rozpoznaj tekst część przetwarzania obrazów jest również dostępny jako kontener platformy Docker. Umożliwia ona wykrywanie i wyodrębnianie tekstu drukowanego z obrazów różne obiekty na różnych powierzchniach i tłach, takich jak przychody oraz plakaty i wizytówki.  
> [!IMPORTANT]
> Kontener Rozpoznaj tekst obecnie działa tylko w języku angielskim.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="preparation"></a>Przygotowanie

Przed rozpoczęciem korzystania z kontenerów rozpoznawanie tekstu, musi spełniać następujące wymagania wstępne:

**Aparat platformy docker**: konieczne jest posiadanie aparat platformy Docker zainstalowany lokalnie. Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), i [Windows](https://docs.docker.com/docker-for-windows/). W Windows platformy Docker musi być skonfigurowany do obsługi kontenerów systemu Linux. Kontenery platformy docker — można również wdrożyć bezpośrednio do [usługi Azure Kubernetes Service](/azure/aks/), [usługi Azure Container Instances](/azure/container-instances/), lub [Kubernetes](https://kubernetes.io/) klastra wdrożone [Usługi azure Stack](/azure/azure-stack/). Aby uzyskać więcej informacji na temat wdrażania rozwiązania Kubernetes do usługi Azure Stack, zobacz [wdrażanie platformy Kubernetes w usłudze Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure.

**Znajomość Microsoft Container Registry i Docker**: powinien mieć podstawową wiedzę na temat koncepcji Microsoft Container Registry i Docker, takich jak rejestrów, repozytoriów, kontenerów, a obrazy kontenerów, a także wiedzę na temat podstawowe `docker` poleceń.  

Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).

### <a name="server-requirements-and-recommendations"></a>Wymagania dotyczące serwera i zalecenia

Kontener rozpoznawanie tekstu wymaga co najmniej 1 rdzenia Procesora, co najmniej 2,6 gigaherc (GHz) lub szybszy i 8 gigabajtów (GB) pamięci ilość przydzielonej pamięci, ale firma Microsoft zaleca, aby co najmniej 2 rdzeni Procesora i 8 GB pamięci przydzielonych.

## <a name="request-access-to-the-private-container-registry"></a>Poproś o dostęp do prywatnego rejestru kontenerów

Najpierw należy wypełnić oraz przesłać [formularz żądania kontenerów przetwarzania w usłudze Cognitive Services](https://aka.ms/VisionContainersPreview) Aby zażądać dostępu do kontenera rozpoznawanie tekstu. Formularz żądania informacji o Tobie, firmy i Scenariusz użytkownika, dla którego będzie używać kontenera. Po przesłaniu, zespół usług Azure Cognitive Services przegląda formularza, aby upewnić się, że spełniają kryteria, aby uzyskać dostęp do prywatnego rejestru kontenerów.

> [!IMPORTANT]
> Należy użyć adresu e-mail skojarzonego z kontem Microsoft (MSA) lub Azure Active Directory kontem (Azure AD) w formularzu.

Jeśli Twoje żądanie zostanie zatwierdzone, następnie otrzymasz wiadomość e-mail z instrukcjami opisujących sposób uzyskać poświadczenia i dostęp do prywatnego rejestru kontenerów.

## <a name="create-a-computer-vision-resource-on-azure"></a>Tworzenie zasobów przetwarzania obrazów na platformie Azure

Jeśli chcesz użyć kontenera rozpoznawanie tekstu, należy utworzyć zasób przetwarzania obrazów na platformie Azure. Po utworzeniu zasobu, następnie należy użyć subskrypcji klucz i punkt końcowy adres URL z zasobu do utworzenia wystąpienia kontenera. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera, zobacz [wystąpienia kontenera za pomocą obrazu kontenera pobrany](#instantiate-a-container-from-a-downloaded-container-image).

Wykonaj poniższe kroki, aby utworzyć i pobierania informacji z zasobów platformy Azure:

1. Tworzenie zasobu platformy Azure w witrynie Azure portal.  
   Jeśli chcesz użyć kontenera rozpoznawanie tekstu, należy najpierw utworzyć odpowiadający jej zasób przetwarzania obrazów w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie konta usług Cognitive Services w witrynie Azure portal](../cognitive-services-apis-create-account.md).

   > [!IMPORTANT]
   > Zasobów przetwarzania obrazów, musisz używać warstwy cenowej F0.

1. Pobieranie klucza punktu końcowego adresu URL i subskrypcji dla zasobów platformy Azure.  
   Po utworzeniu zasobu platformy Azure musi być wystąpienia odpowiedniego kontenera Rozpoznaj tekst klucza punktu końcowego adresu URL i subskrypcji z tego zasobu. Można skopiować klucza punktu końcowego adresu URL i subskrypcji z odpowiednio strony Szybki Start i klucze zasobu przetwarzania obrazów w witrynie Azure portal.

## <a name="log-in-to-the-private-container-registry"></a>Zaloguj się do prywatnego rejestru kontenerów

Istnieje kilka sposobów uwierzytelniania za pomocą prywatnego rejestru kontenera dla kontenerów usługi Cognitive Services, ale zalecana metoda w wierszu polecenia jest za pomocą [interfejsu wiersza polecenia Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Użyj [docker login](https://docs.docker.com/engine/reference/commandline/login/) polecenia, jak pokazano w poniższym przykładzie, aby zalogować się do `containerpreview.azurecr.io`, prywatnego rejestru kontenera dla kontenerów usługi Cognitive Services. Zastąp *\<username\>* z nazwą użytkownika i *\<hasło\>* przy użyciu hasła podanego poświadczenia otrzymane od platformy Azure Zespół usługi cognitive Services.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Jeśli objęta swoje poświadczenia w pliku tekstowym, można łączyć ze sobą zawartość ten tekst pliku, przy użyciu `cat` polecenia do `docker login` polecenia, jak pokazano w poniższym przykładzie. Zastąp *\<wartości passwordFile\>* ze ścieżką i nazwą pliku tekstowego zawierającego hasło i *\<username\>* z nazwą użytkownika podać swoje poświadczenia.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

## <a name="download-container-images-from-the-private-container-registry"></a>Pobieranie obrazów kontenerów z prywatnego rejestru kontenerów

Obraz kontenera dla kontenera rozpoznawanie tekstu jest dostępna z prywatnego rejestru kontenerów platformy Docker, o nazwie `containerpreview.azurecr.io`, w usłudze Azure Container Registry. Obraz kontenera dla kontenera rozpoznawanie tekstu musi zostać pobrany z repozytorium w celu uruchomienia kontenera lokalnie.

Użyj [polecenie docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera z repozytorium. Na przykład można pobrać najnowsze rozpoznawanie tekstu obrazu kontenera z repozytorium, użyj następującego polecenia:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

Pełny opis tagów dostępnych w kontenerze rozpoznawanie tekstu, zobacz [Rozpoznaj tekst](https://go.microsoft.com/fwlink/?linkid=2018655) w usłudze Docker Hub.

> [!TIP]
> Możesz użyć [obrazów platformy docker](https://docs.docker.com/engine/reference/commandline/images/) polecenie, aby wyświetlić listę obrazów kontenerów pobrany. Na przykład następujące polecenie wyświetla identyfikator, repozytorium i tag każdy obraz kontenera pobrany, sformatowane jako tabela:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Utwórz wystąpienie kontenera z obrazu kontenera pobrany

Użyj [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie, aby utworzyć wystąpienie kontenera z obrazu kontenera pobrany. Na przykład następujące polecenie:

* Tworzy wystąpienie kontenera z obrazu kontenera rozpoznawanie tekstu
* Przydziela dwa rdzenie Procesora i 8 gigabajtów (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Po wychodzi są automatycznie usuwane kontenera

```docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westus.api.cognitive.microsoft.com/vision/v2.0 ApiKey=0123456789
```

Po utworzeniu można wywołać operacji z kontenera za pomocą kontenera hosta identyfikatora URI. Na przykład następujący host identyfikatora URI reprezentuje kontener Rozpoznaj tekst, który został utworzony w poprzednim przykładzie:

```http
http://localhost:5000/
```

> [!IMPORTANT]
> Możesz uzyskać dostęp [specyfikacji interfejsu OpenAPI](https://swagger.io/docs/specification/about/) (dawniej specyfikacją struktury Swagger), opisujący operacje obsługiwane przez kontener wystąpień z `/swagger` względny identyfikator URI dla tego kontenera. Na przykład następujący identyfikator URI zapewnia dostęp do specyfikacji interfejsu OpenAPI dla kontenera Rozpoznaj tekst, który został uruchomiony w poprzednim przykładzie:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Możesz albo [wywoływanie operacji interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) dostępne z kontenera dla asynchronicznego lub synchronicznego rozpoznawanie tekstu, lub użyj [Cognitive Services komputera przetwarzania zestawu SDK usługi Azure](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) klienta Biblioteka do wywołania tych operacji.  
> [!IMPORTANT]
> Konieczne jest posiadanie Azure Cognitive Services komputera przetwarzania zestawu SDK w wersji 3.2.0 lub nowszej, aby za pomocą kontenera za pomocą biblioteki klienta.

### <a name="asynchronous-text-recognition"></a>Rozpoznawanie tekstu asynchroniczne

Możesz użyć `POST /vision/v2.0/recognizeText` i `GET /vision/v2.0/textOperations/*{id}*` operacje z optymalizacją asynchronicznie, rozpoznawał drukowanego tekstu w obrazie, podobnie jak Usługa przetwarzania obrazów używa tych odpowiednich operacji REST. Kontener Rozpoznaj tekst rozpoznaje tylko drukowanego tekstu, tekst nie pisma odręcznego w tej chwili więc `mode` parametr zwykle określony dla operacji przetwarzania obrazów usługi jest ignorowana przez kontener rozpoznawanie tekstu.

### <a name="synchronous-text-recognition"></a>Rozpoznawanie tekstu synchroniczne

Możesz użyć `POST /vision/v2.0/recognizeTextDirect` operacji synchronicznie, rozpoznawał drukowanego tekstu w obrazie. Ponieważ ta operacja jest synchroniczne, treść żądania dla tej operacji jest taka sama, jak dla `POST /vision/v2.0/recognizeText` operacja, ale odpowiedź treści dla tej operacji jest taka sama jak zwracany przez `GET /vision/v2.0/textOperations/*{id}*` operacji.

### <a name="billing"></a>Rozliczenia

Kontener Rozpoznaj tekst wysyła informacje rozliczeniowe na platformie Azure, przy użyciu odpowiedniego zasobu przetwarzania obrazów na Twoim koncie platformy Azure. Poniższe opcje są używane przez kontener Rozpoznaj tekst na potrzeby rozliczeń:

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API zasobów przetwarzania obrazów, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji, musi być równa klucza interfejsu API dla aprowizowanego zasobu komputera przetwarzania w usłudze Azure określone w `Billing`. |
| `Billing` | Punkt końcowy zasobu przetwarzania obrazów, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość ta opcja musi być równa punkt końcowy identyfikatora URI zasobu Azure przetwarzania komputera elastycznie.|
| `Eula` | Wskazuje, zaakceptowane licencji dla kontenera.<br/>Wartość ta opcja musi być równa `accept`. |

> [!IMPORTANT]
> Prawidłowe wartości należy określić wszystkie trzy opcje, lub uruchomić kontenera.

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](computer-vision-resource-container-config.md).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania przetwarzania obrazów kontenerów. Podsumowanie:

* Przetwarzania obrazów udostępnia kontenerów systemu Linux dla platformy Docker, aby wykrywanie i wyodrębnianie drukowanych tekstu.
* Obrazy kontenerów są pobierane z prywatnego rejestru kontenerów na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach przetwarzania obrazów, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.
* ** Cognitive Services kontenery nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klientów (np. obraz lub tekst, który jest analizowana) do firmy Microsoft.  

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [skonfigurować kontenery](computer-vision-resource-container-config.md) ustawień konfiguracji
* Przegląd [omówienie wizualizacji komputerowej](Home.md) dowiedzieć się więcej o rozpoznawaniu tekstu drukowanego i pisma odręcznego  
* Zapoznaj się [interfejs API przetwarzania obrazów](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) szczegółowe informacje na temat metod obsługiwanych przez kontener.
* Zapoznaj się [— często zadawane pytania (FAQ)](FAQ.md) Aby rozwiązać problemy związane z funkcji przetwarzania obrazów.
