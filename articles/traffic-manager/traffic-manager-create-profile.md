---
title: Utwórz profil Menedżera ruchu na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia profilu Menedżera ruchu
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
ms.date: 04/21/2017
ms.author: kumud
ms.openlocfilehash: 1994c8374244b62e65b1a54234775d9a39f72bb3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29397574"
---
# <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

W tym artykule opisano sposób profil z **priorytet** trasy użytkownikom dwa punkty końcowe aplikacji sieci Web Azure można utworzyć typu routingu. Za pomocą **priorytet** routingu typu, cały ruch jest kierowany do pierwszego punktu końcowego, podczas gdy druga jest przechowywana jako kopia zapasowa. W związku z tym użytkownicy mogą być kierowane do drugiego punktu końcowego, jeśli pierwszym punktem końcowym staje się nieprawidłowy.

W tym artykule dwa punkty końcowe z wcześniej utworzonej aplikacji sieci Web platformy Azure są skojarzone z nowo utworzoną profilu usługi Traffic Manager. Aby dowiedzieć się więcej o sposobie tworzenia punktów końcowych aplikacji sieci Web platformy Azure, odwiedź stronę [stronę dokumentacji Azure Web Apps](https://docs.microsoft.com/azure/app-service/). Umożliwia dodanie dowolnego punktu końcowego, który zawiera nazwę DNS i jest dostępny za pośrednictwem publicznej sieci internet i jako przykład korzystamy punkty końcowe aplikacji sieci Web Azure.

### <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager
1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](http://portal.azure.com). Jeśli nie masz jeszcze konta, możesz przystąpić do [bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/free/). 
2. Kliknij przycisk **Utwórz zasób** > **sieci** > **profilu usługi Traffic Manager** > **Utwórz**.
4. W **profilu usługi Traffic Manager utwórz**pełnego w następujący sposób:
    1. W polu **Nazwa** podaj nazwę profilu. Ta nazwa musi być unikatowa w ramach strefy trafficmanager.net i powoduje nazwę DNS <name>, trafficmanager.net, która umożliwia dostęp do Twojego profilu usługi Traffic Manager.
    2. W obszarze **Metoda routingu** wybierz metodę routingu **Priorytet**.
    3. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której ma zostać utworzony ten profil.
    4. W obszarze **Grupy zasobów** utwórz nową grupę zasobów, w której zostanie umieszczony ten profil.
    5. W obszarze **Lokalizacja grupy zasobów** wybierz lokalizację grupy zasobów. To ustawienie dotyczy lokalizacji grupy zasobów i nie ma wpływu na profil usługi Traffic Manager, który będzie wdrażany globalnie.
    6. Kliknij przycisk **Utwórz**.
    7. Po zakończeniu globalnego wdrażania profilu usługi Traffic Manager zostanie on wyświetlony w odpowiedniej grupie zasobów jako jeden z zasobów.

    ![Tworzenie profilu usługi Traffic Manager](./media/traffic-manager-create-profile/Create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

1. Na pasku wyszukiwania portalu, wyszukaj **profilu usługi Traffic Manager** nazwy utworzonego w poprzednim i kliknij pozycję profilu Menedżera ruchu w wynikach który wyświetlone.
2. W **profilu usługi Traffic Manager**w **ustawienia** kliknij **punkty końcowe**.
3. Kliknij pozycję **Add** (Dodaj).
4. Wykonaj w następujący sposób:
    1. Dla opcji **Typ** kliknij pozycję **Punkt końcowy platformy Azure**.
    2. W polu **Nazwa** podaj nazwę dla tego punktu końcowego.
    3. Dla **typu zasobu docelowego**, kliknij przycisk **usługi aplikacji**.
    4. Aby uzyskać **zasób docelowy**, kliknij przycisk **wybierz usługę aplikacji** można wyświetlić listę aplikacji sieci Web w ramach tej samej subskrypcji. W **zasobów**, wybierz usługę aplikacji, która ma zostać dodany jako pierwszy punkt końcowy.
    5. Dla opcji **Priorytet** wybierz wartość **1**. Spowoduje to kierowanie całego ruchu do tego punktu końcowego, jeśli jest on w dobrej kondycji.
    6. Pozycję **Dodaj jako wyłączone** pozostaw niezaznaczoną.
    7. Kliknij przycisk **OK**.
5.  Powtórz kroki 3 i 4 dla następnego punktu końcowego aplikacji sieci Web Azure. Dla tego punktu końcowego ustaw opcję **Priorytet** na wartość **2**.
6.  Po zakończeniu dodawania oba punkty końcowe są wyświetlane w **profilu usługi Traffic Manager** wraz z ich monitorowania statusu **Online**.

    ![Dodawanie punktu końcowego Menedżera ruchu](./media/traffic-manager-create-profile/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Użyj profilu Menedżera ruchu
1.  Na pasku wyszukiwania portalu, wyszukaj **profilu usługi Traffic Manager** nazwy, który został utworzony w poprzedniej sekcji. W wynikach, które są wyświetlane kliknij profil Menedżera ruchu.
2. Kliknij przycisk **omówienie**.
3. **Profilu usługi Traffic Manager** Wyświetla nazwę DNS nowo utworzony profil Menedżera ruchu. Może to służyć przez wszystkich klientów (na przykład, przechodząc do niej przy użyciu przeglądarki sieci web) do pobrania kierowane do prawej punktu końcowego jako określana przez typ routingu. W takim przypadku wszystkie żądania są kierowane do pierwszego punktu końcowego, a w przypadku wykrycia przez Menedżera ruchu jest zła, ruch automatycznie przełącza do następnego punktu końcowego.

## <a name="delete-the-traffic-manager-profile"></a>Usuwanie profilu Menedżera ruchu
Gdy nie są już potrzebne, Usuń grupę zasobów i profilu Menedżera ruchu, który został utworzony. Aby to zrobić, wybierz grupę zasobów, z **profilu usługi Traffic Manager** i kliknij przycisk **usunąć**.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [routingu typy](traffic-manager-routing-methods.md).
- Dowiedz się więcej o typy punktów końcowych [typy punktów końcowych](traffic-manager-endpoint-types.md).
- Dowiedz się więcej o [monitorowania punktów końcowych](traffic-manager-monitoring.md).



