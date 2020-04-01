---
title: Samouczek — konfigurowanie routingu ruchu geograficznego za pomocą usługi Azure Traffic Manager
description: W tym samouczku wyjaśniono, jak skonfigurować metodę routingu ruchu geograficznego przy użyciu usługi Azure Traffic Manager
services: traffic-manager
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: rohink
ms.openlocfilehash: 3eb3f354d51833e55f405ed35679f1a5882c057a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938788"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Samouczek: Konfigurowanie metody routingu ruchu geograficznego przy użyciu usługi Traffic Manager

Metoda routingu ruchu geograficznego umożliwia kierowanie ruchu do określonych punktów końcowych na podstawie lokalizacji geograficznej, z której pochodzą żądania. W tym samouczku pokazano, jak utworzyć profil usługi Traffic Manager za pomocą tej metody routingu i skonfigurować punkty końcowe do odbierania ruchu z określonych regionów geograficznych.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/).
2. Kliknij **pozycję Utwórz profil** > **menedżera** > ruchu**sieciowego** > **Utwórz**.
4. W **profilu Utwórz menedżera ruchu:**
    1. Podaj nazwę swojego profilu. Ta nazwa musi być unikatowa w strefie trafficmanager.net. Aby uzyskać dostęp do profilu Usługi `<profilename>.trafficmanager.net`Traffic Manager, należy użyć nazwy DNS .
    2. Wybierz metodę routingu **geograficznego.**
    3. Wybierz subskrypcję, w której chcesz utworzyć ten profil.
    4. Użyj istniejącej grupy zasobów lub utwórz nową grupę zasobów, aby umieścić ten profil. Jeśli wybierzesz utworzenie nowej grupy zasobów, użyj listy rozwijanej **Lokalizacja grupy zasobów,** aby określić lokalizację grupy zasobów. To ustawienie odnosi się do lokalizacji grupy zasobów i nie ma wpływu na profil usługi Traffic Manager, który jest wdrażany globalnie.
    5. Po **kliknięciu przycisku Utwórz**profil usługi Traffic Manager jest tworzony i wdrażany globalnie.

![Tworzenie profilu usługi Traffic Manager](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Dodawanie punktów końcowych

1. Wyszukaj nazwę profilu usługi Traffic Manager utworzonej na pasku wyszukiwania portalu i kliknij wynik, gdy zostanie wyświetlony.
2. Przejdź do**pozycji Punkty końcowe** **ustawień** -> w Usłudze Traffic Manager.
3. Kliknij **przycisk Dodaj,** aby wyświetlić **punkt końcowy Add**.
3. Kliknij **przycisk Dodaj** i w wyświetlonym punkcie końcowym **Dodaj** zakończ się w następujący sposób:
4. Wybierz **opcję Typ** w zależności od typu dodanego punktu końcowego. W przypadku profilów routingu geograficznego używanych w produkcji zdecydowanie zaleca się używanie zagnieżdżonych typów punktów końcowych zawierających profil podrzędny z więcej niż jednym punktem końcowym. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące metod routingu ruchu geograficznego](traffic-manager-FAQs.md).
5. W polu **Nazwa** podaj nazwę dla tego punktu końcowego.
6. Niektóre pola na tej stronie zależą od typu dodania punktu końcowego:
    1. W przypadku dodawania punktu końcowego platformy Azure wybierz **typ zasobu docelowego** i **obiekt docelowy** na podstawie zasobu, do którego chcesz skierować ruch
    2. Jeśli dodajesz **zewnętrzny** punkt końcowy, podaj **w pełni kwalifikowaną nazwę domeny (FQDN)** dla punktu końcowego.
    3. W przypadku dodawania **zagnieżdżonego punktu końcowego**wybierz **zasób docelowy** odpowiadający profilowi podrzędnemu, którego chcesz użyć, i określ **minimalną liczbę podrzędnych punktów końcowych**.
7. W sekcji Mapowanie geograficzne użyj listy rozwijanej, aby dodać regiony, z których chcesz wysłać ruch do tego punktu końcowego. Należy dodać co najmniej jeden region i można zamapować wiele regionów.
8. Powtórz tę czynność dla wszystkich punktów końcowych, które chcesz dodać w ramach tego profilu

![Dodawanie punktu końcowego usługi Traffic Manager](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Korzystanie z profilu Usługi Traffic Manager
1.  Na pasku wyszukiwania portalu wyszukaj nazwę **profilu Usługi Traffic Manager** utworzonej w poprzedniej sekcji i kliknij profil menedżera ruchu w wyświetlanych wynikach.
2. Kliknij pozycję **Przegląd**.
3. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. Może to być używane przez wszystkich klientów (na przykład przez przejście do niego za pomocą przeglądarki sieci web), aby uzyskać kierowane do odpowiedniego punktu końcowego, zgodnie z typem routingu.  W przypadku routingu geograficznego usługa Traffic Manager analizuje źródłowy adres IP żądania przychodzącego i określa region, z którego pochodzi. Jeśli ten region jest mapowany do punktu końcowego, ruch jest kierowany do tego miejsca. Jeśli ten region nie jest mapowany do punktu końcowego, usługa Traffic Manager zwraca odpowiedź na kwerendę NODATA.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [metodzie routingu ruchu geograficznego](traffic-manager-routing-methods.md#geographic).
- Dowiedz się, jak [przetestować ustawienia Menedżera ruchu](traffic-manager-testing-settings.md).
