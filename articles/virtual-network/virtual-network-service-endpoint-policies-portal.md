---
title: Tworzenie i kojarzenie zasad punktu końcowego usługi — witryna Azure portal
titlesuffix: Azure Virtual Network
description: W tym artykule dowiesz się, jak skonfigurować i skojarzone zasady punktu końcowego usługi przy użyciu witryny Azure portal.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: d26fd2fec5f9d5ab8e9d82ff2c6bd83b11c72e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651276"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Tworzenie, zmienianie lub usuwanie zasad punktu końcowego usługi przy użyciu portalu Azure

Zasady punktu końcowego usługi umożliwiają filtrowanie ruchu sieciowego wirtualnego do określonych zasobów platformy Azure za pośrednictwem punktów końcowych usługi. Jeśli nie znasz zasad punktu końcowego usługi, zobacz [omówienie zasad punktu końcowego usługi,](virtual-network-service-endpoint-policies-overview.md) aby dowiedzieć się więcej.

 Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasad punktu końcowego usługi
> * Tworzenie definicji zasad punktu końcowego usługi
> * Tworzenie sieci wirtualnej za pomocą podsieci
> * Kojarzenie zasad punktu końcowego usługi z podsiecią

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Tworzenie zasad punktu końcowego usługi

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. W okienku wyszukiwania wpisz "zasady punktu końcowego usługi" i wybierz pozycję **Zasada punktu końcowego usługi,** a następnie wybierz pozycję **Utwórz**.

![Tworzenie zasad punktu końcowego usługi](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Wprowadź lub wybierz następujące informacje w **obszarze Podstawy** 

   - Subskrypcja : Wybierz subskrypcję dla zasad
   - Grupa zasobów : Wybierz **pozycję Utwórz nowe** i wprowadź *myResourceGroup*
   - Imię i nazwisko : myEndpointPolicy
   - Lokalizacja : Central US
 
   ![Tworzenie podstaw zasad punktu końcowego usługi](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Wybierz **+ Dodaj** w obszarze **Zasoby** i wprowadź lub wybierz następujące informacje w **okienku Dodawanie zasobów**

   - Usługa: Tylko **microsoft.storage** jest dostępny z zasadami punktu końcowego usługi
   - Zakres: Wybierz jedno z **jednego konta,** **Wszystkie konta w subskrypcji** i Wszystkie konta w grupie **zasobów**
   - Subskrypcja : wybierz subskrypcję dla konta magazynu. Konta zasad i magazynu mogą znajdować się w różnych subskrypcjach.
   - Grupa zasobów : Wybierz grupę zasobów. Wymagane, jeśli zakres jest ustawiony jako "Wszystkie konta w grupie zasobów" lub "Pojedyncze konto".  
   - Zasób : Wybierz zasób usługi Azure Storage w ramach wybranej subskrypcji lub grupy zasobów
   - Kliknij przycisk **Dodaj** u dołu, aby zakończyć dodawanie zasobu

   ![Definicja zasad punktu końcowego usługi — zasób](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Dodaj więcej zasobów, powtarzając powyższe kroki w razie potrzeby

5. Opcjonalnie: Wprowadź lub wybierz następujące informacje w **tagach:**
   
   - Klucz: Wybierz klucz dla zasady. Przykład: Dział     
   - Wartość : Wprowadź parę wartości dla klucza. Przykład: Finanse

6. Wybierz **pozycję Recenzja + Utwórz**. Sprawdź poprawność informacji i kliknij przycisk **Utwórz**. Aby wprowadzić dalsze zmiany, kliknij przycisk **Poprzedni**. 

   ![Tworzenie ostatecznych weryfikacji zasad punktu końcowego usługi](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Wyświetlanie zasad dotyczących punktów końcowych 

1. W polu *Wszystkie usługi* w portalu rozpocznij wpisywanie zasad *punktu końcowego usługi*. Wybierz **pozycję Zasady punktu końcowego usługi**.
2. W obszarze **Subskrypcje**wybierz swoją subskrypcję i grupę zasobów, jak pokazano na poniższej ilustracji

   ![Pokaż zasady](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Wybierz zasady i kliknij **definicje zasad,** aby wyświetlić lub dodać więcej definicji zasad.

   ![Pokaż definicje zasad](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Wybierz **skojarzone podsieci,** aby wyświetlić podsieci, z które są skojarzone zasady. Jeśli nie jest jeszcze skojarzona żadna podsieć, postępuj zgodnie z instrukcjami w następnym kroku.

   ![Skojarzone podsieci](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Kojarzenie zasad z podsiecią

>[!WARNING] 
> Upewnij się, że wszystkie zasoby dostępne z podsieci są dodawane do definicji zasad przed skojarzeniem zasad z daną podsiecią. Po skojarzeniu zasad tylko dostęp do *dozwolonych zasobów na liście* będzie dozwolony zao takie punkty końcowe usługi. 
>
> Upewnij się również, że w podsieci nie istnieją żadne zarządzane usługi platformy Azure skojarzone z zasadami punktu końcowego usługi

- Aby można było skojarzyć zasady z podsiecią, należy utworzyć sieć wirtualną i podsieć. Aby uzyskać pomoc w tym artykule, zapoznaj się z [artykułem Tworzenie sieci wirtualnej.](./quick-create-portal.md)

- Po skonfigurowaniu sieci wirtualnej i podsieci należy skonfigurować punkty końcowe usługi sieci wirtualnej dla usługi Azure Storage. W bloku Sieć wirtualna wybierz pozycję **Punkty końcowe usługi**, a w następnym okienku wybierz pozycję **Microsoft.Storage** i w **obszarze Podsieci** wybierz żądaną sieć wirtualną lub podsieć

- Teraz można wybrać opcję wybrania zasady punktu końcowego usługi z listy rozwijanej w powyższym okienku, jeśli utworzono już zasady punktu końcowego usługi przed skonfigurowaniem punktu końcowego usługi dla podsieci, jak pokazano poniżej

    ![Skojarz podsieć podczas tworzenia punktu końcowego usługi](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- Lub jeśli kojarzysz zasady punktu końcowego usługi po skonfigurowaniu punktów końcowych usługi, można skojarzyć podsieć z poziomu bloku Zasady punktu końcowego usługi, przechodząc do okienka **Skojarzone podsieci,** jak pokazano poniżej

    ![Skojarz podsieć za pośrednictwem SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>Dostęp do zasobów usługi Azure Storage we wszystkich regionach będzie ograniczony zgodnie z zasadami punktu końcowego usługi z tej podsieci.

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono zasadę punktu końcowego usługi i skojarzyno ją z podsiecią. Aby dowiedzieć się więcej na temat zasad punktu końcowego usługi, zobacz [omówienie zasad punktu końcowego usługi.](virtual-network-service-endpoint-policies-overview.md)
