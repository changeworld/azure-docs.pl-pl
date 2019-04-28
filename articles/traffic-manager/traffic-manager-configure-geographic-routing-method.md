---
title: Skonfiguruj metody geograficznego routingu ruchu przy użyciu usługi Azure Traffic Manager
description: W tym artykule opisano sposób konfigurowania metody geograficznego routingu ruchu przy użyciu usługi Azure Traffic Manager
services: traffic-manager
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 47cc56aac7d3e0147ef8577aac19776c6cacf7a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884159"
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Konfigurowanie metody routingu geograficznego ruchu przy użyciu usługi Traffic Manager

Metody geograficznego routingu ruchu pozwala kierować ruch do określonych punktów końcowych na podstawie lokalizacji geograficznej, z którego pochodzą żądania. W tym samouczku dowiesz się, jak utworzyć profil usługi Traffic Manager przy użyciu tej metody routingu i konfigurowanie punktów końcowych do odbierania ruchu z określonych lokalizacji geograficznych.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/).
2. Kliknij pozycję **Utwórz zasób** > **Sieć** > **Profil usługi Traffic Manager** > **Utwórz**.
4. W **profilu usługi Traffic Manager utworzyć**:
    1. Podaj nazwę dla swojego profilu. Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Aby uzyskać dostęp do profilu usługi Traffic Manager, należy użyć nazwy DNS `<profilename>.trafficmanager.net`.
    2. Wybierz **Geographic** metody routingu.
    3. Wybierz subskrypcję, która ma zostać utworzony ten profil w obszarze.
    4. Użyj istniejącej grupy zasobów lub Utwórz nową grupę zasobów, można umieścić tego profilu. Jeśli zdecydujesz się utworzyć nową grupę zasobów, użyj **lokalizacja grupy zasobów** listę rozwijaną, aby określić lokalizację grupy zasobów. To ustawienie dotyczy lokalizacji grupy zasobów i nie ma wpływu na profil usługi Traffic Manager, który jest wdrażany globalnie.
    5. Po kliknięciu **Utwórz**, profilu usługi Traffic Manager jest tworzona i wdrażana globalnie.

![Tworzenie profilu usługi Traffic Manager](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Dodaj punkty końcowe

1. Wyszukaj nazwę profilu usługi Traffic Manager, utworzonej za pomocą paska wyszukiwania portalu, a następnie kliknij wynik, gdy jest on wyświetlany.
2. Przejdź do **ustawienia** -> **punktów końcowych** w usłudze Traffic Manager.
3. Kliknij przycisk **Dodaj** pokazanie **Dodaj punkt końcowy**.
3. Kliknij przycisk **Dodaj** i **Dodaj punkt końcowy** wyświetlonej, wykonaj następujące:
4. Wybierz **typu** zależności od typu w przypadku dodawania punktu końcowego. Geograficznego routingu profile używane w środowisku produkcyjnym zaleca się używanie typów zagnieżdżony punkt końcowy zawierający profil podrzędne o więcej niż jeden punkt końcowy. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania o metodach routingu ruchu geograficzne](traffic-manager-FAQs.md).
5. W polu **Nazwa** podaj nazwę dla tego punktu końcowego.
6. Niektóre pola na tej stronie zależą od typu punktu końcowego, który dodajesz:
    1. W przypadku dodawania punktu końcowego platformy Azure wybierz **typ zasobu docelowego** i **docelowej** oparte na zasób, aby kierować ruch do
    2. W przypadku dodawania **zewnętrznych** punktu końcowego, podaj **w pełni kwalifikowana nazwa domeny (FQDN)** dla punktu końcowego usługi.
    3. W przypadku dodawania **punktu końcowego zagnieżdżone**, wybierz opcję **zasób docelowy** odnosi się do profilu podrzędnych chcesz użyć, a następnie określ **minimalna liczba punktów końcowych podrzędnych**.
7. W sekcji map geograficznych można użyć listy rozwijanej, dodawanie regionów, z którym chcesz, aby ruch był przesyłany do tego punktu końcowego. Należy dodać co najmniej jednego regionu i może mieć wiele regionów mapowane.
8. Powtórz tę czynność dla wszystkich punktów końcowych, które chcesz dodać w ramach tego profilu

![Dodawanie punktu końcowego usługi Traffic Manager](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Użyj profilu usługi Traffic Manager
1.  Na pasku wyszukiwania portalu, wyszukaj **profilu usługi Traffic Manager** nazwy, który został utworzony w poprzedniej sekcji i profilu usługi traffic manager w wynikach, kliknij przycisk, wyświetlana.
2. Kliknij pozycję **Przegląd**.
3. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. Może to służyć przez dowolnego klienta (na przykład, przechodząc do niego w przeglądarce sieci web) Pobierz kierowane do prawego punktu końcowego jako ustalany na podstawie typu routingu.  W przypadku geograficznego routingu usługi Traffic Manager sprawdza źródłowy adres IP żądania przychodzącego i określa region, z którego jest pochodzących. Jeśli ten region jest mapowany do punktu końcowego, ruch jest kierowany do miejsca. Jeśli ten region nie została zamapowana na punkt końcowy, usługa Traffic Manager zwraca odpowiedzi na zapytanie NODATA.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Geographic metodę routingu ruchu](traffic-manager-routing-methods.md#geographic).
- Dowiedz się, jak [testowanie ustawień usługi Traffic Manager](traffic-manager-testing-settings.md).
