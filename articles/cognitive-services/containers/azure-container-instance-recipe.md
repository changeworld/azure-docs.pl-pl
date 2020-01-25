---
title: Przepis wystąpienia kontenera platformy Azure
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrażać kontenery Cognitive Services w usłudze Azure Container instance
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 78f35042678aa7c30cebf73796df3e5d564b4502
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717002"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Wdrażanie i uruchamianie kontenera w wystąpieniu kontenera platformy Azure

Poniższe kroki umożliwiają łatwe skalowanie aplikacji Cognitive Services platformy Azure w chmurze za pomocą usługi Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Kontenerach ułatwia skoncentrowanie się na tworzeniu aplikacji, a nie zarządzaniu infrastrukturą. Aby uzyskać więcej informacji na temat korzystania z kontenerów, zobacz [funkcje i korzyści](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Wymagania wstępne

Przepis działa z jakimkolwiek kontenerem Cognitive Services. Przed użyciem przepisu należy utworzyć zasób usługi poznawczej w Azure Portal. Każda usługa poznawcze, która obsługuje kontenery, ma dokument "jak zainstalować" przeznaczony do instalowania i konfigurowania usługi dla kontenera. Niektóre usługi wymagają pliku lub zestawu plików jako dane wejściowe dla kontenera, dlatego ważne jest, aby zrozumieć i używało kontenera pomyślnie przed użyciem tego rozwiązania.

* Zasób usługi poznawczej utworzony w Azure Portal.
* **Adres URL punktu końcowego** usługi poznawczej — zapoznaj się z określoną usługą "jak zainstalować" dla kontenera, aby sprawdzić, gdzie znajduje się adres URL punktu końcowego w Azure Portal i jak wygląda prawidłowy przykład adresu URL. Dokładny format może ulec zmianie od usługi do usługi.
* **Klucz** usługi poznawczej — klucze znajdują się na stronie **klucze** zasobu platformy Azure. Wystarczy tylko jeden z dwóch kluczy. Klucz jest ciągiem 32 znaków alfanumerycznych.
* Pojedynczy kontener Cognitive Services na hoście lokalnym (Twoim komputerze). Upewnij się, że możesz:
  * Pobierz obraz za pomocą polecenia `docker pull`.
  * Pomyślnie uruchomiono kontener lokalny ze wszystkimi wymaganymi ustawieniami konfiguracji za pomocą polecenia `docker run`.
  * Wywołaj punkt końcowy kontenera, pobierając odpowiedź HTTP 2xx i odpowiedź JSON z powrotem.

Wszystkie zmienne w nawiasach ostrych, `<>`muszą zostać zastąpione własnymi wartościami. To zastąpienie obejmuje nawiasy ostre.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Korzystanie z wystąpienia kontenera

1. Wybierz **Przegląd** i skopiuj adres IP. Będzie to liczbowy adres IP, taki jak `55.55.55.55`.
1. Otwórz nową kartę przeglądarki i użyj adresu IP, na przykład `http://<IP-address>:5000 (http://55.55.55.55:5000`). Zostanie wyświetlona strona główna kontenera z informacją o tym, że kontener jest uruchomiony.

1. Wybierz pozycję **opis interfejsu API usługi** , aby wyświetlić stronę struktury Swagger dla kontenera.

1. Wybierz dowolny z **wpisów** interfejsów API i wybierz opcję **Wypróbuj**.  Parametry są wyświetlane wraz z danymi wejściowymi. Wypełnij parametry.

1. Wybierz polecenie **Wykonaj** , aby wysłać żądanie do wystąpienia kontenera.

    Tworzenie i używanie kontenerów Cognitive Services w usłudze Azure Container instance zostało pomyślnie zakończone.
