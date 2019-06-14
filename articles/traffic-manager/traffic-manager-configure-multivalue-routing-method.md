---
title: Konfigurowanie metody routingu ruchu sieciowego wielu wartości w usłudze Azure Traffic Manager
description: W tym artykule opisano sposób konfigurowania usługi Traffic Manager na potrzeby kierowania ruchu do punktów końcowych A/AAAA.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: allensu
ms.openlocfilehash: 5db8e2932a43a2d6c6cb8a99c4f32b37a4a5a3f8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050899"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Konfigurowanie metody routingu dla wielu wartości w usłudze Traffic Manager

W tym artykule opisano sposób konfigurowania wielu wartości metody routingu ruchu. **Atrybut wielowartościowy elementu** ruch metody routingu opartego na można zwrócić wiele punktów końcowych w dobrej kondycji i pomaga zwiększyć niezawodność aplikacji, ponieważ klienci mają więcej opcji, aby ponowić próbę bez konieczności innego wyszukiwania DNS. Wielowartościowe routing jest włączony tylko w przypadku profilów, które mają wszystkie ich punkty końcowe określony przy użyciu adresów IPv4 lub IPv6. Po otrzymaniu kwerendy dla tego profilu, wszystkie punkty końcowe w dobrej kondycji są zwracane na podstawie konfigurowalnych maksymalna liczba zwracany określony. 

>[!NOTE]
> W tej chwili Dodawanie punktów końcowych przy użyciu adresów IPv4 lub IPv6 jest obsługiwana tylko w przypadku punktów końcowych typu **zewnętrznych** i dlatego wielowartościowe routing jest również obsługiwany tylko dla tych punktów końcowych.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów dla profilu usługi Traffic Manager.
1. W okienku po lewej stronie w witrynie Azure Portal wybierz **grup zasobów**.
2. W **grup zasobów**, górnej części strony wybierz **Dodaj**.
3. W **nazwy grupy zasobów**, wpisz nazwę *myResourceGroupTM1*. Dla **lokalizację grupy zasobów**, wybierz opcję **wschodnie stany USA**, a następnie wybierz pozycję **OK**.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager
Tworzenie profilu usługi Traffic Manager, który kieruje ruch użytkowników tak, wysyłając je do punktu końcowego z najniższym opóźnieniem.

1. W lewej górnej części ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Profil usługi Traffic Manager** > **Utwórz**.
2. W **profilu usługi Traffic Manager utworzyć**wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz **Utwórz**:
    
    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Name (Nazwa)                   | Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Na jej podstawie zostanie utworzona nazwa DNS trafficmanager.net służąca do uzyskiwania dostępu do profilu usługi Traffic Manager.                                   |
    | Metoda routingu          | Wybierz **atrybut wielowartościowy elementu** metody routingu.                                       |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz pozycję *myResourceGroupTM1*. |
    | Lokalizacja                | To ustawienie dotyczy lokalizacji grupy zasobów i nie ma wpływu na profil usługi Traffic Manager, który będzie wdrażany globalnie.                              |
   |        |           | 
  
   ![Tworzenie profilu usługi Traffic Manager](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj dwa adresy IP jako zewnętrzne punkty końcowe, do profilu usługi Traffic Manager atrybut wielowartościowy elementu, który został utworzony w poprzednim kroku.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji, a następnie wybierz ten profil w wyświetlonych wynikach.
2. W obszarze **Profil usługi Traffic Manager** w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**, a następnie kliknij pozycję **Dodaj**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Typ                    | Jest zewnętrzny punkt końcowy                                   |
    | Name (Nazwa)           | myEndpoint1                                        |
    | W pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP           | Wpisz publiczny adres IP punktu końcowego, który chcesz dodać do tego profilu usługi Traffic Manager                         |
    |        |           |

4. Powtórz kroki 2 i 3, aby dodać innego punktu końcowego o nazwie *myEndpoint2*, aby uzyskać **w pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP**, wprowadź publiczny adres IP drugiego punktu końcowego.
5. Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w obszarze **Profil usługi Traffic Manager** ze stanem monitorowania **Online**.

   ![Dodawanie punktu końcowego usługi Traffic Manager](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [ważonej metodzie routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [priorytetowej metodzie routingu](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [metody routingu opartego na wydajności](traffic-manager-configure-performance-routing-method.md)
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).


