---
title: Usługa Azure przepisu wystąpienia kontenera
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrożyć Cognitive kontenerów usług w wystąpieniu kontenera platformy Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 288894705e1108d6dd511b60cd2bc3bcee4c6d41
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704340"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Wdrażanie i uruchamianie kontenera w wystąpieniu kontenera platformy Azure

Następujące kroki, skalowanie aplikacji usług Azure Cognitive Services w chmurze dzięki platformie Azure [wystąpienia kontenera](https://docs.microsoft.com/azure/container-instances/). To pozwala skoncentrować się na tworzeniu aplikacji, zamiast na zarządzaniu infrastrukturą.

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

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Użyj wystąpienia kontenera

1. Wybierz **Przegląd** i skopiuj adres IP. Będzie liczbowych adres IP takich jak `55.55.55.55`.
1. Otwórz nową kartę przeglądarki i użyj adresu IP, na przykład `http://<IP-address>:5000 (http://55.55.55.55:5000`). Zostanie wyświetlona strona główna kontenera, informacją o tym, że kontener został uruchomiony.

1. Wybierz **opis interfejsu API usługi** Aby wyświetlić stronę struktury swagger dla kontenera.

1. Wybierz dowolny z **WPIS** interfejsów API, a następnie wybierz pozycję **wypróbuj działanie rozwiązania**.  Parametry są wyświetlane w tym dane wejściowe. Podaj parametry.

1. Wybierz **Execute** wysyłać żądania do wystąpienia kontenera.

    Pomyślnie tworzone i używane kontenerów usług Cognitive Services w wystąpieniu kontenera platformy Azure.
