---
title: Usługa Azure przepisu wystąpienia kontenera
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrożyć Cognitive kontenerów usług w wystąpieniu kontenera platformy Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 06/12/2019
ms.author: dapine
ms.openlocfilehash: 5de11b62bb2a2302c247907627e27339400fcf25
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207492"
---
# <a name="deploy-and-run-container-on-azure-container-instance-aci"></a>Wdrażanie i uruchamianie kontenera w wystąpieniu kontenera platformy Azure (ACI)

Następujące kroki, skalowanie aplikacji usług Azure Cognitive Services w chmurze dzięki platformie Azure [wystąpienia kontenera](https://docs.microsoft.com/azure/container-instances/) (ACI). To pozwala skoncentrować się na tworzeniu aplikacji, zamiast na zarządzaniu infrastrukturą.

## <a name="prerequisites"></a>Wymagania wstępne

To rozwiązanie działa z dowolnym kontenerze usług Cognitive Services. Zasób usługi cognitive Services, należy utworzyć w witrynie Azure portal, przed rozpoczęciem korzystania z tego przepisu. Każda usługa Cognitive, który obsługuje kontenery zawiera dokument "Jak zainstalować" specjalnie do instalowania i konfigurowania usługi kontenera. Ponieważ niektóre usługi wymagają pliku lub zestawu plików jako dane wejściowe dla kontenera, ważne jest zrozumienie oraz użyto kontenera pomyślnie przed rozpoczęciem korzystania z tego rozwiązania.

* Zasób usługi cognitive Services, utworzone w witrynie Azure portal.
* Usługi cognitive Services **adresu URL punktu końcowego** — zapoznaj się z określonej usługi "jak zainstalować" z kontenera, aby dowiedzieć się, gdy adres URL punktu końcowego jest zrobienie tego z portalu Azure i co przykład poprawnego adresu URL wygląda następująco. Dokładny format można zmienić z usług.
* Usługi cognitive Services **klucz** — klucze znajdują się na **klucze** strony dla zasobów platformy Azure. Wystarczy tylko jeden z dwóch kluczy. Klucz jest ciągiem 32 znaków alfanumerycznych.
* Pojedynczy Cognitive Services kontenerze na hosta lokalnego (komputer). Upewnij się, że możesz wykonywać następujące czynności:
  * Ściągnąć obraz z `docker pull` polecenia.
  * Pomyślnie uruchomić lokalny kontener przy użyciu wszystkich wymaganych ustawień konfiguracji z `docker run` polecenia.
  * Wywołanie punktu końcowego kontenera, powrót odpowiedzi 2xx i odpowiedź w formacie JSON.

Wszystkie zmienne w nawiasy kątowe `<>`, trzeba je zastąpić własnymi wartościami. Ta zastępowania zawiera nawiasy kątowe.

## <a name="step-2-launch-your-container-on-azure-container-instances-aci"></a>Krok 2: Uruchamianie kontenera w usłudze Azure Container Instances (ACI)

**Tworzenie zasobów wystąpienia kontenera platformy Azure (ACI).**

1. Przejdź do [Utwórz](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) strona Container Instances.

1. Na **podstawy** wprowadź następujące informacje:

    |Strona|Ustawienie|Wartość|
    |--|--|--|
    |Podstawy|Subskrypcja|Wybierz subskrypcję.|
    |Podstawy|Grupa zasobów|Wybierz grupę dostępnych zasobów lub Utwórz nową, takich jak `cognitive-services`.|
    |Podstawy|Nazwa kontenera|Wprowadź nazwę, taką jak `cognitive-container-instance`. Ta nazwa musi mieć niższe limity.|
    |Podstawy|Lokalizacja|Wybierz region dla wdrożenia.|
    |Podstawy|Typ obrazu|`Public`|
    |Podstawy|Nazwa obrazu|Wprowadź lokalizację kontenera usługi Cognitive Services. Może to być tej samej lokalizacji, które są używane w `docker pull` polecenia _na przykład_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Podstawy|Typ systemu operacyjnego|`Linux`|
    |Podstawy|Rozmiar|Zmień rozmiar na sugerowane zalecenia dotyczące określonego kontenera usługi cognitive Services.:<br>2 rdzenie<br>4 GB
    ||||
  
1. Na **sieć** wprowadź następujące informacje:

    |Strona|Ustawienie|Wartość|
    |--|--|--|
    |Networking|Porty|Edytowanie istniejącego portu dla protokołu TCP z `80` do `5000`. Oznacza to, że wyświetlasz kontenera na porcie 5000.|
    ||||

1. Na **zaawansowane** wprowadź następujące szczegóły, aby przechodzić przez kontener wymagane ustawienia zasobów wystąpienia kontenera rozliczeń:

    |Strona Zaawansowane klucza|Advanced page value|
    |--|--|
    |`apikey`|Skopiowane ze **klucze** strony zasobu. Konieczne jest tylko jeden z dwóch kluczy. Jest to 32 ciąg znaków alfanumerycznych, bez spacji lub kreski, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Skopiowane ze **Przegląd** strony zasobu. |
    |`eula`|`accept`|

    Jeśli kontener ma inne ustawienia konfiguracji, takie jak instaluje danych wejściowych, danych wyjściowych instaluje lub rejestrowania, te ustawienia również należy dodać.

1. Wybierz **Przejrzyj i Utwórz**.
1. Po pomyślnej weryfikacji, wybierz **Utwórz** na zakończenie procesu tworzenia.
1. Wybierz ikonę dzwonka w górnym menu nawigacyjnym. To okno powiadomienia. Zostanie wyświetlone niebieski **przejdź do zasobu** przycisk po utworzeniu zasobu. Wybierz ten przycisk, aby przejść do nowego zasobu.

## <a name="use-the-container-instance"></a>Użyj wystąpienia kontenera

1. Wybierz **Przegląd** i skopiuj adres IP. Będzie liczbowych adres IP takich jak `55.55.55.55`.
1. Otwórz nową kartę przeglądarki i użyj adresu IP, na przykład `http://<IP-address>:5000 (http://55.55.55.55:5000`). Zostanie wyświetlona strona główna kontenera, informacją o tym, że kontener został uruchomiony.

1. Wybierz **opis interfejsu API usługi** Aby wyświetlić stronę struktury swagger dla kontenera.

1. Wybierz dowolny z **WPIS** interfejsów API, a następnie wybierz pozycję **wypróbuj działanie rozwiązania**.  Parametry są wyświetlane w tym dane wejściowe. Podaj parametry.

1. Wybierz **Execute** wysyłać żądania do wystąpienia kontenera.

    Pomyślnie tworzone i używane kontenerów usług Cognitive Services w wystąpieniu kontenera platformy Azure.
