---
title: Konfigurowanie metody routingu ruchu geograficzny przy użyciu usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak skonfigurować metody routingu ruchu geograficzny przy użyciu usługi Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 7b49e2a4eef5a966f1ef2aa283a3089bb5b73734
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29397807"
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Konfigurowanie metody routingu ruchu geograficzny przy użyciu Menedżera ruchu

Metody routingu ruchu geograficzny umożliwia kierować ruch do określonych punktów końcowych na podstawie lokalizacji geograficznej, których pochodzą żądania. Ten samouczek przedstawia sposób tworzenia profilu usługi Traffic Manager przy użyciu tej metody routingu i skonfiguruj punkty końcowe odbierać ruch z określonych lokalizacji geograficznych.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu Menedżera ruchu

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](http://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/).
2. Kliknij przycisk **Utwórz zasób** > **sieci** > **profilu usługi Traffic Manager** > **Utwórz**.
4. W **profilu usługi Traffic Manager utwórz**:
    1. Podaj nazwę profilu. Ta nazwa musi być unikatowa w ramach strefy trafficmanager.net. Aby uzyskać dostęp do Twojego profilu usługi Traffic Manager, należy użyć nazwy DNS <profilename>. trafficmanager.net.
    2. Wybierz **Geographic** metody routingu.
    3. Wybierz subskrypcję, którą chcesz utworzyć tego profilu.
    4. Użyj istniejącej grupy zasobów lub Utwórz nową grupę zasobów, który można umieścić tego profilu. Jeśli wybierzesz opcję utworzenia nowej grupy zasobów, użyj **lokalizacja grupy zasobów** listy rozwijanej, aby określić lokalizację grupy zasobów. To ustawienie odwołuje się do lokalizacji grupy zasobów i nie ma wpływu na wdrożonej globalnie profil Menedżera ruchu.
    5. Po kliknięciu **Utwórz**, profilu usługi Traffic Manager jest tworzona i wdrażana globalnie.

![Tworzenie profilu usługi Traffic Manager](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Dodawanie punktów końcowych

1. Wyszukaj nazwę profilu Menedżera ruchu, utworzony w pasku wyszukiwania portalu i kliknij wynik podczas jego wyświetlania.
2. Przejdź do **ustawienia** -> **punktów końcowych** w usłudze Traffic Manager.
3. Kliknij przycisk **Dodaj** pokazanie **Dodawanie punktu końcowego**.
3. Kliknij przycisk **Dodaj** i w **dodać punkt końcowy** która jest wyświetlana, wykonania w następujący sposób:
4. Wybierz **typu** w zależności od typu w przypadku dodawania punktu końcowego. Geograficzne profile routingu używane w środowisku produkcyjnym zaleca się używanie typów zagnieżdżonych punkt końcowy zawierający profil podrzędnego z więcej niż jeden punkt końcowy. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania o metodach routingu ruchu geograficzne](traffic-manager-FAQs.md).
5. W polu **Nazwa** podaj nazwę dla tego punktu końcowego.
6. Niektóre pola na tej stronie zależą od typu punktu końcowego, który dodajesz:
    1. W przypadku dodawania punktu końcowego platformy Azure, wybierz **typu zasobu docelowego** i **docelowej** oparte na zasób, należy skierować ruch do
    2. Jeśli dodajesz **zewnętrznych** punktu końcowego, podaj **pełni kwalifikowaną nazwę domeny (FQDN)** dla punktu końcowego.
    3. Jeśli dodajesz **punktu końcowego zagnieżdżone**, wybierz pozycję **zasób docelowy** odpowiadający profil podrzędne chcesz używać i określ **minimalna liczba punktów końcowych podrzędnych**.
7. W sekcji map geograficznych przy użyciu listy rozwijanej Dodaj regionów, z którym chcesz ruchu do wysłania do tego punktu końcowego. Należy dodać co najmniej jeden region, a może mieć wielu regionach zamapowane.
8. Powtórz tę czynność dla wszystkich punktów końcowych, które mają zostać dodane w tym profilu

![Dodawanie punktu końcowego Menedżera ruchu](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Użyj profilu Menedżera ruchu
1.  Na pasku wyszukiwania portalu, wyszukaj **profilu usługi Traffic Manager** nazwy, który został utworzony w poprzedniej sekcji i profilu Menedżera ruchu w wynikach, kliknij pozycję który wyświetlone.
2. Kliknij przycisk **omówienie**.
3. **Profilu usługi Traffic Manager** Wyświetla nazwę DNS nowo utworzony profil Menedżera ruchu. Może to służyć przez wszystkich klientów (na przykład, przechodząc do niej przy użyciu przeglądarki sieci web) do pobrania kierowane do prawej punktu końcowego jako określana przez typ routingu.  W przypadku routingu geograficzne Menedżera ruchu sprawdza źródłowy adres IP żądania przychodzącego i określa region, z którego jest pochodzących. Jeśli region jest zamapowana na punkt końcowy, ruch jest kierowany do niego. Jeśli ten region nie jest zamapowany na punkt końcowy, Traffic Manager zwraca NODATA odpowiedzi na kwerendę.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Metoda routingu ruchu Geographic](traffic-manager-routing-methods.md#geographic).
- Dowiedz się, jak [Menedżera ruchu ustawienia testu](traffic-manager-testing-settings.md).
