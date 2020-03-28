---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f63510771e4bd71a3ab6cf048bc5fb5296042a4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75564675"
---
## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

W tym samouczku użyj rozszerzenia Narzędzia IoT platformy Azure, aby utworzyć moduł i utworzyć **obraz kontenera** z plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.

Do przechowywania obrazów kontenerów możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry.

Jeśli nie masz jeszcze rejestru kontenerów, wykonaj następujące kroki, aby utworzyć nowy rejestr na platformie Azure:

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz kolejno pozycje **Utwórz zasób** > **Kontenery** > **Container Registry**.

2. Podaj następujące wartości, aby utworzyć rejestr kontenerów:

   | Pole | Wartość |
   | ----- | ----- |
   | Nazwa rejestru | Podaj unikatową nazwę. |
   | Subskrypcja | Wybierz subskrypcję z listy rozwijanej. |
   | Grupa zasobów | Zalecamy używanie tej samej grupy zasobów dla wszystkich zasobów testowych tworzonych podczas pracy z przewodnikami Szybki Start i samouczkami usługi IoT Edge. Na przykład **IoTEdgeResources**. |
   | Lokalizacja | Wybierz bliską lokalizację. |
   | Administrator | Ustaw na wartość **Włącz**. |
   | SKU | Wybierz **opcję Podstawowe**. |

3. Wybierz **pozycję Utwórz**.

4. Po utworzeniu rejestru kontenerów przejdź do niego i z lewego okienka wybierz **klawisze programu Access** z menu znajdującego się w obszarze **Ustawienia**.

5. Skopiuj wartości **serwera logowania,** **nazwy użytkownika**i **hasła** i zapisz je w wygodnym miejscu. Te wartości są używane w tym samouczku, aby zapewnić dostęp do rejestru kontenerów.

   ![Kopiowanie serwera logowania, nazwy użytkownika i hasła do rejestru kontenerów](./media/iot-edge-create-container-registry/registry-access-key.png)