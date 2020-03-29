---
title: Przepis wystąpienia kontenera platformy Azure
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrożyć kontenery usług Cognitive Services w wystąpieniu kontenera platformy Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 78f35042678aa7c30cebf73796df3e5d564b4502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717002"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Wdrażanie i uruchamianie kontenera w wystąpieniu kontenera platformy Azure

W poniższych krokach łatwo skaluj aplikacje usługi Azure Cognitive Services w chmurze za pomocą [wystąpień kontenerów](https://docs.microsoft.com/azure/container-instances/)platformy Azure. Konteneryzacja pomaga skupić się na tworzeniu aplikacji zamiast zarządzania infrastrukturą. Aby uzyskać więcej informacji na temat korzystania z kontenerów, zobacz [funkcje i korzyści](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Wymagania wstępne

Przepis działa z dowolnego kontenera usług Cognitive Services. Zasób usługi Cognitive Service musi zostać utworzony w witrynie Azure portal przed użyciem receptury. Każda usługa Cognitive Service obsługująca kontenery ma dokument "Jak zainstalować" specjalnie do instalowania i konfigurowania usługi dla kontenera. Niektóre usługi wymagają pliku lub zestawu plików jako dane wejściowe dla kontenera, ważne jest, aby zrozumieć i pomyślnie użyto kontenera przed użyciem tego rozwiązania.

* Zasób usługi Cognitive Service, utworzony w witrynie Azure portal.
* Adres **URL punktu końcowego** usługi Cognitive Service — przejrzyj "Jak zainstalować" dla kontenera określoną usługę, aby znaleźć, gdzie znajduje się adres URL punktu końcowego z witryny Azure portal i jak wygląda poprawny przykład adresu URL. Dokładny format można zmienić z usługi na usługę.
* **Klucz** usługi Cognitive Service — klucze znajdują się na stronie **Klucze** dla zasobu platformy Azure. Potrzebujesz tylko jednego z dwóch kluczy. Klucz jest ciągiem 32 znaków alfanumerycznym.
* Pojedynczy kontener usług Cognitive Services na lokalnym hoście (komputerze). Upewnij się, że możesz:
  * Pociągnij w dół `docker pull` obraz za pomocą polecenia.
  * Pomyślnie uruchom kontener lokalny, przy czym `docker run` wszystkie wymagane ustawienia konfiguracji za pomocą polecenia.
  * Wywołanie punktu końcowego kontenera, uzyskanie odpowiedzi HTTP 2xx i odpowiedzi JSON z powrotem.

Wszystkie zmienne w nawiasach `<>`kątowych, , muszą być zastąpione własnymi wartościami. Wymiana ta obejmuje wsporniki kątowe.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Użyj wystąpienia kontenera

1. Wybierz **przegląd** i skopiuj adres IP. Będzie to numeryczny adres IP, taki jak `55.55.55.55`.
1. Otwórz nową kartę przeglądarki i użyj na `http://<IP-address>:5000 (http://55.55.55.55:5000`przykład adresu IP). Zostanie wyświetlona strona główna kontenera, informując o tym, że kontener jest uruchomiony.

1. Wybierz **opis interfejsu API usługi,** aby wyświetlić stronę swagger dla kontenera.

1. Wybierz dowolny z **interfejsów** API POST i wybierz pozycję **Wypróbuj**.  Wyświetlane są parametry, w tym dane wejściowe. Wypełnij parametry.

1. Wybierz **polecenie Wykonaj,** aby wysłać żądanie do wystąpienia kontenera.

    Pomyślnie utworzono i użyto kontenerów usług Cognitive Services w wystąpieniu kontenera platformy Azure.
