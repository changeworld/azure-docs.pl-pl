---
title: Tworzenie niestandardowych akcji i zasobów na platformie Azure
description: W tym samouczku pokazano, jak utworzyć niestandardowe akcje i zasoby w Azure Resource Manager. Przedstawiono w nim również sposób współpracy niestandardowych przepływów pracy z szablonami Azure Resource Manager, interfejsem wiersza polecenia platformy Azure, Azure Policy i dziennikiem aktywności platformy Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: dc1601e344c371a5f0feaadd272a2c6ff40af031
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172945"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Tworzenie niestandardowych akcji i zasobów na platformie Azure

Dostawca niestandardowy to kontrakt między platformą Azure i punktem końcowym. Dostawcy niestandardowi mogą zmieniać przepływy pracy na platformie Azure, dodając nowe interfejsy API do Azure Resource Manager. Za pomocą tych niestandardowych interfejsów API Menedżer zasobów mogą korzystać z nowych możliwości wdrażania i zarządzania.

W tym samouczku przedstawiono prosty przykład dodawania nowych akcji i zasobów do platformy Azure oraz sposobu ich integrowania.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Konfigurowanie Azure Functions dla dostawców niestandardowych platformy Azure

W pierwszej części tego samouczka opisano sposób konfigurowania aplikacji funkcji platformy Azure do pracy z dostawcami niestandardowymi:

- [Konfigurowanie Azure Functions dla dostawców niestandardowych platformy Azure](./tutorial-custom-providers-function-setup.md)

Dostawcy niestandardowi mogą współpracować z dowolnym publicznym adresem URL.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Tworzenie punktu końcowego RESTful dla dostawców niestandardowych

W drugiej części tego samouczka opisano sposób tworzenia punktu końcowego RESTful dla dostawców niestandardowych:

- [Tworzenie punktu końcowego RESTful dla dostawców niestandardowych](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Tworzenie i używanie dostawcy niestandardowego

W trzeciej części tego samouczka opisano sposób tworzenia niestandardowego dostawcy i używania jego niestandardowych akcji i zasobów:

- [Tworzenie i używanie dostawcy niestandardowego](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje o dostawcach niestandardowych i sposobach ich kompilowania. Aby przejść do następnego samouczka, zobacz [samouczek: Skonfiguruj Azure Functions dla dostawców](./tutorial-custom-providers-function-setup.md)niestandardowych platformy Azure.

Jeśli szukasz odwołań lub przewodnika Szybki Start, Oto kilka przydatnych linków:

- [Szybki start: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Instrukcje: Dodawanie niestandardowych zasobów do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
