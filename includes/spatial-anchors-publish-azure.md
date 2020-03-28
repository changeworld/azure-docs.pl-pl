---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67182920"
---
### <a name="open-the-publish-wizard"></a>Otwieranie kreatora publikowania

W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **Usługi Udostępniania** i wybierz polecenie **Publikuj**.

Zostanie uruchomiony Kreator publikowania. Wybierz**pozycję Publikuj** **usługę app service,** > aby otworzyć okno dialogowe Tworzenie usługi **aplikacji.**

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

W oknie dialogowym **Tworzenie usługi app service** wybierz pozycję Dodaj **konto** i zaloguj się do subskrypcji platformy Azure. Jeśli jesteś już zalogowany, wybierz konto, które chcesz z listy rozwijanej.

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

W oknie dialogowym **Konfigurowanie planu hostingu** użyj następujących ustawień:

| Ustawienie | Sugerowana wartość | Opis |
|-|-|-|
|Plan usługi App Service| MySharingServicePlan | Nazwa planu usługi App Service. |
| Lokalizacja | Zachodnie stany USA | Centrum danych, w którym hostowana jest aplikacja internetowa. |
| Rozmiar | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) określająca funkcje hostingu. |

Kliknij przycisk **OK**.

### <a name="create-and-publish-the-web-app"></a>Tworzenie i publikowanie aplikacji internetowej

W **aplikacji Nazwa aplikacji**wprowadź unikatową `a-z`nazwę `0-9`aplikacji `-`(prawidłowe znaki to , i ) lub zaakceptuj automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `https://<app_name>.azurewebsites.net`, gdzie `<app_name>` to nazwa aplikacji.

Wybierz pozycję **Utwórz**, aby rozpocząć tworzenie zasobów platformy Azure.

Po zakończeniu pracy kreatora publikuje ASP.NET podstawową aplikację sieci web na platformie Azure, a następnie otwiera aplikację w domyślnej przeglądarce.

![Opublikowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Nazwa aplikacji użyta w tej sekcji jest używana jako `https://<app_name>.azurewebsites.net`prefiks adresu URL w formacie . Zanotuj ten adres URL, ponieważ jest potrzebny.
