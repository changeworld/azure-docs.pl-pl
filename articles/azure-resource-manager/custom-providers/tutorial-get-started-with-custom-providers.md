---
title: Tworzenie niestandardowych akcji i zasobów
description: W tym samouczku opisano sposób tworzenia niestandardowych akcji i zasobów w usłudze Azure Resource Manager. Pokazano również, jak niestandardowe przepływy pracy współdziałają z szablonami usługi Azure Resource Manager, interfejsu wiersza polecenia platformy Azure, zasad platformy Azure i dziennika aktywności platformy Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649897"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Tworzenie niestandardowych akcji i zasobów na platformie Azure

Dostawca niestandardowy to umowa między platformą Azure a punktem końcowym. Za pomocą dostawców niestandardowych można zmieniać przepływy pracy na platformie Azure, dodając nowe interfejsy API do usługi Azure Resource Manager. Dzięki tym niestandardowym interfejsom API Menedżer zasobów może korzystać z nowych funkcji wdrażania i zarządzania.

W tym samouczku opisano prosty przykład dodawania nowych akcji i zasobów do platformy Azure i sposobu ich integracji.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Konfigurowanie usług Azure Functions dla dostawców niestandardowych platformy Azure

W części jeden z tego samouczka opisano sposób konfigurowania aplikacji funkcji platformy Azure do pracy z dostawcami niestandardowymi:

- [Konfigurowanie usług Azure Functions dla dostawców niestandardowych platformy Azure](./tutorial-custom-providers-function-setup.md)

Dostawcy niestandardowi mogą pracować z dowolnym publicznym adresem URL.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Tworzenie punktu końcowego RESTful dla dostawców niestandardowych

W drugiej części tego samouczka opisano sposób tworzenia punktu końcowego RESTful dla dostawców niestandardowych:

- [Tworzenie punktu końcowego RESTful dla dostawców niestandardowych](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Tworzenie i używanie dostawcy niestandardowego

W części trzeciej tego samouczka opisano sposób tworzenia dostawcy niestandardowego i używania jego niestandardowych akcji i zasobów:

- [Tworzenie i używanie dostawcy niestandardowego](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się o dostawców niestandardowych i jak go zbudować. Aby przejść do następnego samouczka, zobacz [Samouczek: Konfigurowanie funkcji platformy Azure dla dostawców niestandardowych platformy Azure](./tutorial-custom-providers-function-setup.md).

Jeśli szukasz referencji lub przewodnika Szybki start, oto kilka przydatnych linków:

- [Szybki start: tworzenie dostawcy zasobów niestandardowych platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Jak: Dodawanie akcji niestandardowych do interfejsu API rest platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Jak: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
