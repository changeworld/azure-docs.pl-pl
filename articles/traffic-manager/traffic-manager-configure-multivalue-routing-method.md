---
title: Konfigurowanie routingu ruchu wielowartościowego — usługa Azure Traffic Manager
description: W tym artykule wyjaśniono, jak skonfigurować menedżera ruchu do kierowania ruchu do punktów końcowych A/AAAA.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: rohink
ms.openlocfilehash: daf7d09916d276130e337f7acea738228ee23707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938776"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Konfigurowanie metody routingu MultiValue w usłudze Traffic Manager

W tym artykule opisano sposób konfigurowania metody routingu ruchu MultiValue. Metoda routingu ruchu **wielowartościowego** umożliwia zwracanie wielu zdrowych punktów końcowych i pomaga zwiększyć niezawodność aplikacji, ponieważ klienci mają więcej opcji ponowienia prób bez konieczności wykonywania innego wyszukiwania DNS. Routing wielowartościowy jest włączony tylko dla profilów, które mają wszystkie swoje punkty końcowe określone przy użyciu adresów IPv4 lub IPv6. Po odebraniu kwerendy dla tego profilu wszystkie punkty końcowe w dobrej kondycji są zwracane na podstawie określonej konfigurowalnej maksymalnej liczby zwracanych. 

>[!NOTE]
> W tej chwili dodawanie punktów końcowych przy użyciu adresów IPv4 lub IPv6 jest obsługiwane tylko dla punktów końcowych typu **Zewnętrzne** i dlatego routing MultiValue jest również obsługiwany tylko dla takich punktów końcowych.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów dla profilu Usługi Traffic Manager.
1. W lewym okienku witryny Azure portal wybierz pozycję **Grupy zasobów**.
2. W **grupach zasobów**u góry strony wybierz pozycję **Dodaj**.
3. W **pliku Nazwa grupy zasobów**wpisz nazwę *myResourceGroupTM1*. W obszarze **Lokalizacja grupy zasobów**wybierz pozycję **Wschodnie**stany USA , a następnie wybierz przycisk **OK**.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager
Utwórz profil usługi Traffic Manager, który kieruje ruch użytkowników, wysyłając go do punktu końcowego o najniższym opóźnieniu.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz** > **profil** > menedżera ruchu**sieciowego** > **Utwórz**.
2. W **obszarze Tworzenie profilu usługi Traffic Manager**wprowadź lub wybierz następujące informacje, zaakceptuj ustawienia domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz:**
    
    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Nazwa                   | Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Na jej podstawie zostanie utworzona nazwa DNS trafficmanager.net służąca do uzyskiwania dostępu do profilu usługi Traffic Manager.                                   |
    | Metoda routingu          | Wybierz metodę **routingu wielowartościowego.**                                       |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz pozycję *myResourceGroupTM1*. |
    | Lokalizacja                | To ustawienie dotyczy lokalizacji grupy zasobów i nie ma wpływu na profil usługi Traffic Manager, który będzie wdrażany globalnie.                              |
   |        |           | 
  
   ![Tworzenie profilu usługi Traffic Manager](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj dwa adresy IP jako zewnętrzne punkty końcowe do profilu MultiValue Traffic Manager utworzonego w poprzednim kroku.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji, a następnie wybierz ten profil w wyświetlonych wynikach.
2. W obszarze **Profil usługi Traffic Manager** w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**, a następnie kliknij pozycję **Dodaj**.
3. Wprowadź lub wybierz następujące informacje, zaakceptuj ustawienia domyślne dla pozostałych ustawień, a następnie wybierz **przycisk OK:**

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Typ                    | Zewnętrzny punkt końcowy                                   |
    | Nazwa           | myEndpoint1                                        |
    | W pełni kwalifikowana nazwa domeny (FQDN) lub IP           | Wpisz publiczny adres IP punktu końcowego, który chcesz dodać do tego profilu usługi Traffic Manager                         |
    |        |           |

4. Powtórz kroki 2 i 3, aby dodać inny punkt końcowy o nazwie *myEndpoint2*, dla **w pełni kwalifikowanej nazwy domeny (FQDN) lub IP**, wprowadź publiczny adres IP drugiego punktu końcowego.
5. Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w obszarze **Profil usługi Traffic Manager** ze stanem monitorowania **Online**.

   ![Dodawanie punktu końcowego usługi Traffic Manager](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [ważonej metodzie routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [priorytetowej metodzie routingu](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [metodzie routingu wydajności](traffic-manager-configure-performance-routing-method.md)
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).


