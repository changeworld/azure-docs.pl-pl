---
title: Instalowanie i uruchamianie kontenerów
titlesuffix: Face - Azure Cognitive Services
description: Jak pobrać, zainstalować i uruchamiaj kontenery twarzy, w tym samouczku wskazówki.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: 310311c22be25960b15f20d573624f50b0f618b1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474816"
---
# <a name="install-and-run-containers"></a>Instalowanie i uruchamianie kontenerów

Konteneryzacji to podejście do dystrybucji oprogramowania, w której aplikacja lub usługa jest dostarczana w postaci obrazu kontenera. Konfiguracja i zależności dla aplikacji lub usługi są objęte obrazu kontenera. Następnie można wdrożyć obraz kontenera na hoście kontenera, z niewielkich modyfikacji. Kontenery są odizolowane od siebie nawzajem i zasadniczego systemu operacyjnego za pomocą mniejszy wyświetlacz niż maszyny wirtualnej. Kontenery mogą utworzonych na podstawie obrazów kontenera do krótkoterminowych zadań i usuwane, gdy nie będą już potrzebne.

Twarzy udostępnia standardowy kontener systemu Linux dla platformy Docker, o nazwie rozpoznawania twarzy wykrywa twarze osób na obrazach, która identyfikuje atrybutów, w tym to punktów charakterystycznych (takie jak elementy ruchome i oczy), płeć, wiek i inne funkcje twarzy przewidzieć maszyny. Oprócz wykrywania twarzy można sprawdzić, czy dwie twarze w ten sam obraz lub różne obrazy są takie same, za pomocą współczynnik ufności lub porównywania twarzy względem bazy danych, aby sprawdzić, czy podobnie wyglądających twarzy identyczne już istnieje. Można również zorganizować podobnych twarzy w grupach, przy użyciu udostępnionego visual traits.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="preparation"></a>Przygotowanie

Przed rozpoczęciem korzystania z kontenerów rozpoznawania twarzy, musi spełniać następujące wymagania wstępne:

**Aparat platformy docker**: Konieczne jest posiadanie aparat platformy Docker zainstalowany lokalnie. Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), i [Windows](https://docs.docker.com/docker-for-windows/). W Windows platformy Docker musi być skonfigurowany do obsługi kontenerów systemu Linux. Kontenery platformy docker — można również wdrożyć bezpośrednio do [usługi Azure Kubernetes Service](../../aks/index.yml), [usługi Azure Container Instances](../../container-instances/index.yml), lub [Kubernetes](https://kubernetes.io/) klastra wdrożone [Usługi azure Stack](../../azure-stack/index.yml). Aby uzyskać więcej informacji na temat wdrażania rozwiązania Kubernetes do usługi Azure Stack, zobacz [wdrażanie platformy Kubernetes w usłudze Azure Stack](../../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).

Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure.

**Znajomość Microsoft Container Registry i Docker**: Należy mieć podstawową wiedzę na temat koncepcji Microsoft Container Registry i Docker, takich jak rejestry, repozytoria, kontenery i obrazów kontenerów, a także wiedzę na temat basic `docker` poleceń.  

Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

Kontener twarzy wymaga co najmniej 1 rdzenia Procesora, co najmniej 2,6 gigaherc (GHz) lub szybszy i 4 gigabajty (GB) pamięci przydzielone, ale firma Microsoft zaleca się co najmniej 2 rdzeni procesora CPU i 6 GB ilość przydzielonej pamięci.

## <a name="request-access-to-the-private-container-registry"></a>Poproś o dostęp do prywatnego rejestru kontenerów

Najpierw należy wypełnić oraz przesłać [formularz żądania kontenerów przetwarzania w usłudze Cognitive Services](https://aka.ms/VisionContainersPreview) Aby zażądać dostępu do kontenera twarzy. Formularz żądania informacji o Tobie, firmy i Scenariusz użytkownika, dla którego będzie używać kontenera. Po przesłaniu, zespół usług Azure Cognitive Services przegląda formularza, aby upewnić się, że spełniają kryteria, aby uzyskać dostęp do prywatnego rejestru kontenerów.

> [!IMPORTANT]
> Należy użyć adresu e-mail skojarzonego z kontem Microsoft (MSA) lub Azure Active Directory kontem (Azure AD) w formularzu.

Jeśli Twoje żądanie zostanie zatwierdzone, następnie otrzymasz wiadomość e-mail z instrukcjami opisujących sposób uzyskać poświadczenia i dostęp do prywatnego rejestru kontenerów.

## <a name="create-a-face-resource-on-azure"></a>Utwórz zasób twarzy na platformie Azure

Jeśli chcesz użyć kontenera rozpoznawania twarzy, należy utworzyć zasób twarzy na platformie Azure. Po utworzeniu zasobu, następnie należy użyć subskrypcji klucz i punkt końcowy adres URL z zasobu do utworzenia wystąpienia kontenera. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera, zobacz [wystąpienia kontenera za pomocą obrazu kontenera pobrany](#instantiate-a-container-from-a-downloaded-container-image).

Wykonaj poniższe kroki, aby utworzyć i pobieranie informacji z zasobu twarzy:

1. Utwórz zasób twarzy w witrynie Azure portal.  
   Jeśli chcesz użyć kontenera rozpoznawania twarzy, należy najpierw utworzyć odpowiadający jej zasób twarzy w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Utwórz konto usług Cognitive Services w witrynie Azure portal](../cognitive-services-apis-create-account.md).

1. Pobieranie klucza punktu końcowego adresu URL i subskrypcji dla zasobów platformy Azure.  
   Po utworzeniu zasobu platformy Azure musi być wystąpienia odpowiedniego kontenera twarzy klucza punktu końcowego adresu URL i subskrypcji z tego zasobu. Można skopiować klucza punktu końcowego adresu URL i subskrypcji z odpowiednio strony Szybki Start i klucze zasobu twarzy w witrynie Azure portal.

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

Obraz kontenera dla kontenera twarzy jest dostępna z prywatnego rejestru kontenerów platformy Docker, o nazwie `containerpreview.azurecr.io`, w usłudze Azure Container Registry. Obraz kontenera dla kontenera twarzy musi zostać pobrany z repozytorium w celu uruchomienia kontenera lokalnie.

Użyj [polecenie docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera z repozytorium. Na przykład aby pobrać najnowsze twarzy obraz kontenera z repozytorium, użyj następującego polecenia:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

Pełny opis tagów dostępnych w kontenerze rozpoznawania twarzy, zobacz [Rozpoznaj tekst](https://go.microsoft.com/fwlink/?linkid=2018655) w usłudze Docker Hub.

> [!TIP]
> Możesz użyć [obrazów platformy docker](https://docs.docker.com/engine/reference/commandline/images/) polecenie, aby wyświetlić listę obrazów kontenerów pobrany. Na przykład następujące polecenie wyświetla identyfikator, repozytorium i tag każdy obraz kontenera pobrany, sformatowane jako tabela:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Utwórz wystąpienie kontenera z obrazu kontenera pobrany

Użyj [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie, aby utworzyć wystąpienie kontenera z obrazu kontenera pobrany. Na przykład następujące polecenie:

* Tworzy wystąpienie kontenera z obrazu kontenera twarzy
* Przydziela dwa rdzenie Procesora i 6 gigabajtów (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Po wychodzi są automatycznie usuwane kontenera

```Docker
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
```

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` do wystąpienia kontenera należy określić opcje; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

Po utworzeniu można wywołać operacji z kontenera za pomocą kontenera hosta identyfikatora URI. Na przykład następujący host identyfikatora URI reprezentuje kontener rozpoznawania twarzy, który został utworzony w poprzednim przykładzie:

```http
http://localhost:5000/
```

> [!TIP]
> Możesz uzyskać dostęp [specyfikacji interfejsu OpenAPI](https://swagger.io/docs/specification/about/) (dawniej specyfikacją struktury Swagger), opisujący operacje obsługiwane przez kontener wystąpień z `/swagger` względny identyfikator URI dla tego kontenera. Na przykład następujący identyfikator URI zapewnia dostęp do specyfikacji interfejsu OpenAPI dla kontenera rozpoznawania twarzy, do którego został utworzony w poprzednim przykładzie:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Możesz albo [wywoływanie operacji interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/howtodetectfacesinimage) dostępne z kontenera lub użyj [Cognitive Services Face biblioteki klienta usługi Azure](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/) biblioteki klienta do wywołania tych operacji.  
> [!IMPORTANT]
> Jeśli chcesz używać biblioteki klienckiej z kontenera, musisz mieć Cognitive Services Face biblioteki klienta usługi Azure w wersji 2.0 lub nowszej.

Jedyną różnicą między wywoływanie operacji z kontenera, a wywołanie tej samej operacji z odpowiednich usług na platformie Azure to czy użyjesz host identyfikatora URI kontenera, a nie host identyfikatora URI z regionu platformy Azure, aby wywołać operację. Na przykład jeśli chcesz użyć wystąpienia twarzy działa w regionie zachodnie stany USA Azure wykrywać twarze wywołać następującej operacji interfejsu API REST:

```http
POST https://westus.api.cognitive.microsoft.com/face/v1.0/detect
```

Jeśli chce się kontener twarzy na komputerze lokalnym w ramach konfiguracji domyślnej umożliwia wykrywanie twarzy, możesz wywołać następującej operacji interfejsu API REST:

```http
POST http://localhost:5000/face/v1.0/detect
```

### <a name="billing"></a>Rozliczenia

Kontener twarzy wysyła informacje rozliczeniowe na platformę Azure przy użyciu odpowiedniego zasobu twarzy na Twoim koncie platformy Azure. Następujące opcje wiersza polecenia są używane przez kontener twarzy na potrzeby rozliczeń:

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API zasobu rozpoznawania twarzy, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji, musi być równa klucza interfejsu API dla aprowizowanego zasobu Azure twarzy określone w `Billing`. |
| `Billing` | Punkt końcowy zasobu rozpoznawania twarzy, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość ta opcja musi być równa punkt końcowy identyfikatora URI zasobu Azure twarzy elastycznie.|
| `Eula` | Wskazuje, zaakceptowane licencji dla kontenera.<br/>Wartość ta opcja musi być równa `accept`. |

> [!IMPORTANT]
> Prawidłowe wartości należy określić wszystkie trzy opcje, lub uruchomić kontenera.

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](face-resource-container-config.md).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania kontenerów twarzy. Podsumowanie:

* Twarzy zawiera jeden kontener systemu Linux dla platformy Docker, o nazwie twarzy, wykrywanie twarzy lub identyfikowanie twarzy przy użyciu bazy danych osoby.
* Obrazy kontenerów są pobierane z prywatnego rejestru kontenerów na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach rozpoznawania twarzy, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klientów (np. obraz lub tekst, który jest analizowana) do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [skonfigurować kontenery](face-resource-container-config.md) ustawień konfiguracji
* Przegląd [Przegląd twarzy](Overview.md) Aby dowiedzieć się więcej na temat wykrywania i identyfikacji twarzy  
* Zapoznaj się [interfejsu API rozpoznawania twarzy](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) szczegółowe informacje na temat metod obsługiwanych przez kontener.
* Zapoznaj się [— często zadawane pytania (FAQ)](FAQ.md) Aby rozwiązać problemy związane z funkcji rozpoznawania twarzy.
* Użycie [kontenerów usługi Cognitive Services](../cognitive-services-container-support.md)