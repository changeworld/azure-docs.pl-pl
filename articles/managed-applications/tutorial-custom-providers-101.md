---
title: Tworzenie akcji niestandardowych i zasobów na platformie Azure
description: W tym samouczku zostanie przechodzi przez sposób tworzenia akcji niestandardowych i zasobów w usłudze Azure Resource Manager oraz zintegrować je niestandardowych przepływów pracy dla szablonów usługi Azure Resource Manager, wiersza polecenia platformy Azure, usługi Azure Policy i dziennika aktywności.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 4bbfcf070611e3df5c0fe47070f2ab6961111e07
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800043"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Tworzenie akcji niestandardowych i zasobów na platformie Azure

Dostawcy niestandardowi umożliwiają dostosowywanie przepływów pracy na platformie Azure. Niestandardowy dostawca jest kontrakt między platformą Azure i `endpoint`. Umożliwia dodawanie nowych, niestandardowych interfejsów API do usługi Azure Resource Manager Włącz nowego wdrożenia i możliwości zarządzania. W tym samouczku będzie przejście przez prosty przykład, jak dodać nowe akcje i zasobów na platformie Azure i sposobu ich integracji.

W tym samouczku jest dzielony na następujące czynności:

- Konfigurowanie usługi Azure Functions dla dostawców niestandardowych usługi Azure
- Tworzenie elementów punkt końcowy usługi RESTful dla dostawcy niestandardowi
- Tworzenie i korzystanie z niestandardowego dostawcy

## <a name="setup-azure-functions-for-azure-custom-providers"></a>Konfigurowanie usługi Azure Functions dla dostawców niestandardowych usługi Azure

Tej części samouczka będzie przejść do szczegółów na temat sposobu konfigurowania funkcji platformy Azure do pracy z niestandardowych dostawców. Niestandardowi dostawcy mogą pracować z dowolnego publicznego adresu URL.

- [Konfigurowanie usługi Azure Functions dla dostawców niestandardowych usługi Azure](./tutorial-custom-providers-function-setup.md)

## <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Tworzenie elementów punkt końcowy usługi RESTful dla dostawcy niestandardowi

Tej części samouczka będzie przejść do szczegółów na temat tworzenia punkt końcowy usługi RESTful dla niestandardowych dostawców.

- [Tworzenie elementów punkt końcowy usługi RESTful dla dostawcy niestandardowi](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-and-utilizing-the-custom-provider"></a>Tworzenie i korzystanie z niestandardowego dostawcy

Ta część samouczka przejdzie do szczegółów dotyczących sposobu tworzenia niestandardowego dostawcy i korzystać z jego akcje niestandardowe i zasobów.

- [Tworzenie i korzystanie z niestandardowego dostawcę usługi Azure](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Następne kroki

W tym artykule poznaliśmy niestandardowych dostawców oraz jak utworzyć jeden. Aby kontynuować do następnego kroku samouczka:

- [Samouczek: Konfigurowanie usługi Azure Functions dla dostawców niestandardowych usługi Azure](./tutorial-custom-providers-function-setup.md)

Jeśli szukasz odwołania lub Szybki Start, poniżej przedstawiono kilka przydatnych linków:

- [Szybki start: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Instrukcje: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
