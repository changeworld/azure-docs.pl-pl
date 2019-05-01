---
title: Tworzenie i kojarzenie zasad punktów końcowych usługi — witryna Azure portal
titlesuffix: Azure Virtual Network
description: W tym artykule Dowiedz się, jak skonfigurować i skojarzona usługa zasad punktów końcowych przy użyciu witryny Azure portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 09/18/2018
ms.author: kumud
ms.openlocfilehash: b1d2d04e74828323166810d93c52a60671bf71e8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64710921"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Tworzenie, zmienianie lub usuwanie zasad punktów końcowych usługi za pomocą witryny Azure portal

Zasad punktów końcowych usługi umożliwiają filtrowanie ruchu w sieci wirtualnej do określonych zasobów platformy Azure za pośrednictwem punktów końcowych usługi. Jeśli nie jesteś zaznajomiony z zasad punktów końcowych usługi, zobacz [Przegląd zasad punktów końcowych usługi](virtual-network-service-endpoint-policies-overview.md) Aby dowiedzieć się więcej.

 Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasad punktów końcowych usługi
> * Utwórz definicję zasad punktu końcowego usługi
> * Utwórz sieć wirtualną z podsiecią
> * Kojarzenie zasad punktów końcowych usługi do podsieci

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Tworzenie zasad punktów końcowych usługi

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. W okienku wyszukiwania wpisz "Usługa zasad punktów końcowych" i wybierz **usługi zasad punktów końcowych (wersja zapoznawcza)** , a następnie wybierz **Utwórz**.
3. Wprowadź lub wybierz następujące informacje w **podstawy** 

   - Subskrypcja: Wybierz swoją subskrypcję dla zasad.    
   - Grupa zasobów: Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę *myResourceGroup*.     
   - Name           : myEndpointPolicy
   - Lokalizacja: Środkowo-zachodnie stany USA     
 
   ![Tworzenie punktu końcowego usługi podstawowe informacje dotyczące zasad](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-startpane.PNG)
   
4. Wprowadź lub wybierz następujące informacje w **definicje zasad**

   - Kliknij przycisk **+ Dodaj zasób**, wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień i kliknij przycisk **Dodaj**.  
   - Zakres: Wybierz **jednego konta** lub **wszystkich kont w subskrypcji** lub **wszystkich kont w grupie zasobów**.    
   - Subskrypcja: Wybierz swoją subskrypcję dla konta magazynu. Konta zasad i magazynu mogą być w różnych subskrypcjach.   
   - Grupa zasobów: Wybierz grupę zasobów. Wymagane, jeśli zakres jest ustawiony jako "Wszystkie konta w grupie zasobów" lub "Jedno konto".  
   - Zasób: mystorageaccountportal    
   - Kliknij przycisk **+ Dodaj zasób** aby kontynuować dodawanie większej ilości zasobów.
   
   ![Tworzenie punktu końcowego usługi definicje zasad](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-policydefinitionspane.PNG)
   
5. Opcjonalnie: Wprowadź lub wybierz następujące informacje w **tagi**:
   
   - Klucz: Wybierz klucz dla zasad. Na przykład: Dział     
   - Wartość: Wprowadź pary wartości klucza. Na przykład: Finanse

6. Wybierz pozycję **Przegląd + utwórz**. Zweryfikuj informacje i kliknięcie **Utwórz**. Może wprowadzać dalszych zmian, kliknij przycisk **Wstecz**. 

   ![Tworzenie punktu końcowego usługi końcowego walidacji zasad](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-finalcreatereview.PNG)
  
 
## <a name="view-endpoint-policies"></a>Wyświetl zasady punktu końcowego 

1. W *wszystkich usług* pole w portalu, zacznij pisać *zasad punktów końcowych usługi*. Wybierz **Policies(Preview) punktu końcowego usługi**.
2. W obszarze **subskrypcje**, wybierz subskrypcję i grupę zasobów, jak pokazano na poniższej ilustracji

   ![Pokaż zasad](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicies.PNG)
       
3. Wybierz zasady, a następnie kliknij pozycję **definicje zasad** Aby wyświetlić lub dodać więcej definicji zasad.

   ![Pokaż definicje zasad](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicy-adddefinitions.PNG)

4. Wybierz **skojarzone podsieci** do wyświetlania podsieci, zasady są skojarzone. Aby skojarzyć zasady z podsiecią, kliknij "Przejdź do sieci wirtualnej, w tym samym regionie".

   ![Pokaż skojarzone podsieci](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-view-associatedsubnets.PNG)
 
## <a name="associate-a-policy-to-a-subnet"></a>Skojarz zasady z podsiecią

>[!WARNING] 
> Upewnij się, że wszystkie zasoby, które są dostępne z podsieci, do wybranej usługi są dodawane do zasad przed skojarzeniem zasad. Gdy zasady są skojarzone, tylko dostęp do zasobów wymienionych w zasad może być dla punktu końcowego regionów dla usługi. 

Zanim będzie można skojarzyć zasady z podsiecią, należy utworzyć sieć wirtualną i podsieć, a następnie można skojarzyć z podsiecią:

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:
   - Nazwa: myVirtualNetwork      
   - Przestrzeń adresowa: 10.0.0.0/16      
   - Subskrypcja: Wybierz subskrypcję. Zasady powinny znajdować się w tej samej subskrypcji co sieć wirtualna     
   - Grupa zasobów: Wybierz **Użyj istniejącej** , a następnie wybierz *myResourceGroup*     
   - Lokalizacja: Środkowo-zachodnie stany USA     
   - Nazwa podsieci: prywatne     
   - Zakres adresów: 10.0.0.0/24
     
4. W polu **Szukaj zasobów, usług i dokumentów** w górnej części portalu rozpocznij wpisywanie ciągu *myVirtualNetwork*. Gdy pozycja **myVirtualNetwork** pojawi się w wynikach wyszukiwania, wybierz ją.
5. W obszarze **ustawienia**, wybierz opcję **podsieci** , a następnie wybierz **prywatnej**.
6. Jak pokazano na poniższej ilustracji, wybierz **punkty końcowe usługi**, wybierz opcję **Microsoft.Storage**, wybierz opcję **zasad punktów końcowych usługi**, wybierz opcję  **myEndpointPolicy**, a następnie wybierz pozycję **Zapisz**:

   ![Skojarz zasady](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-associatepolicies.PNG)

>[!WARNING] 
>Z tej podsieci, w oparciu o grupy zabezpieczeń sieci (NSG) będą miały dostępu do zasobów usługi w innych regionach. Aby ograniczyć dostęp tylko punkt końcowy regionach, Ogranicz sieciowych grup zabezpieczeń z tylko ruchu w ramach usługi w regionach punktu końcowego. Aby uzyskać więcej informacji na temat sposobu tworzenia sieciowych grup zabezpieczeń z tagami usługi na region, zobacz [tagi usługi sieciowej grupy zabezpieczeń platformy Azure.](manage-network-security-group.md?toc=%2fcreate-a-security-rule%2f.json)

W poniższym przykładzie sieciowa grupa zabezpieczeń jest ograniczony do dostępu tylko zasobami usługi Azure Storage w WestCentralUS i WestUS2, z regułą "Deny wszystkie", zgodnie z zasadą o niższym priorytecie.

![Odmów wszystkich sieciowych grup zabezpieczeń](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-nsg-rules.PNG)


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku utworzono zasad punktów końcowych usługi i skojarzono ją z podsiecią. Aby dowiedzieć się więcej na temat zasad punktów końcowych usługi, zobacz [Przegląd zasad punktów końcowych usługi.](virtual-network-service-endpoint-policies-overview.md)

