---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 3ee9aca1ee86b04998e357875b98b14782b3e56d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963375"
---
### <a name="launch-the-publish-wizard"></a>Uruchamianie kreatora publikacji

W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt **SharingService** i wybierz polecenie **Opublikuj**.

Kreator publikowania jest uruchamiany automatycznie. Wybierz pozycje **App Service** > **Publikuj**, aby otworzyć okno dialogowe **Utwórz usługę App Service**.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

W oknie dialogowym **Tworzenie usługi App Service** kliknij pozycję **Dodaj konto**, a następnie zaloguj się do swojej subskrypcji platformy Azure. Jeśli logowanie zostało już wykonane, wybierz żądane konto z listy rozwijanej.

> [!NOTE]
> Jeśli przeprowadzono już logowanie, nie wybieraj jeszcze pozycji **Utwórz**.
>

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [resource group intro text](resource-group.md)]

Obok pozycji **Grupa zasobów** wybierz pozycję **Nowa**.

Nadaj grupie zasobów nazwę **myResourceGroup**, a następnie kliknij przycisk **OK**.

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [app-service-plan](app-service-plan.md)]

Obok pozycji **Plan hostingu** wybierz pozycję **Nowy**.

W oknie dialogowym **Konfiguruj plan hostingu** użyj ustawień znajdujących się w tabeli.

| Ustawienie | Sugerowana wartość | Opis |
|-|-|-|
|Plan usługi App Service| MySharingServicePlan | Nazwa planu usługi App Service. |
| Lokalizacja | Zachodnie stany USA | Centrum danych, w którym hostowana jest aplikacja internetowa. |
| Rozmiar | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) określa funkcje hostowania. |

Kliknij przycisk **OK**.

### <a name="create-and-publish-the-web-app"></a>Tworzenie i publikowanie aplikacji internetowej

W polu **Nazwa aplikacji** wpisz unikatową nazwę aplikacji (dozwolone znaki to `a-z`, `0-9` i `-`) lub zaakceptuj automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `https://<app_name>.azurewebsites.net`, gdzie `<app_name>` to nazwa aplikacji.

Wybierz pozycję **Utwórz**, aby rozpocząć tworzenie zasobów platformy Azure.

Po zakończeniu działania kreatora aplikacja internetowa ASP.NET Core zostanie opublikowana na platformie Azure, a następnie uruchomiona w domyślnej przeglądarce.

![Opublikowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Nazwa aplikacji określona w [kroku tworzenia i publikowania](#create-and-publish-the-web-app) jest używana jako prefiks adresu URL w formacie `https://<app_name>.azurewebsites.net`. Zanotuj ten adres URL, ponieważ będzie on używany później.
