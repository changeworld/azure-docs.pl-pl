---
title: Wymagania wstępne Avere vFXT — Azure
description: Wymagania wstępne dla usługi Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: a183989cc666f00da4be077c719c40d2524fd6e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252547"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Przygotowanie do utworzenia systemu Avere vFXT

W tym artykule opisano zadania wstępne do tworzenia klastra VFXT Avere.

## <a name="create-a-new-subscription"></a>Tworzenie nowej subskrypcji

Zacznij od utworzenia nowej subskrypcji platformy Azure. Użyj oddzielnej subskrypcji dla każdego projektu Avere vFXT, aby uprościć śledzenie wydatków i oczyszczanie oraz upewnić się, że przydziały lub ograniczanie zasobów nie mają wpływu na inne projekty.

Aby utworzyć nową subskrypcję platformy Azure w witrynie Azure portal:

1. Przejdź do [bloku Subskrypcje](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Kliknij przycisk **+ Dodaj** u góry
1. Zaloguj się, jeśli zostanie wyświetlony monit
1. Wybierz ofertę i przejdź przez kroki, aby utworzyć nową subskrypcję

## <a name="configure-subscription-owner-permissions"></a>Konfigurowanie uprawnień właściciela subskrypcji

Użytkownik z uprawnieniami właściciela dla subskrypcji powinien utworzyć klaster vFXT. Tworzenie klastra wymaga od właściciela zaakceptowania warunków korzystania z oprogramowania i autoryzacji zmian w zasobach sieciowych i magazynowych.

Istnieją pewne obejścia, aby umożliwić nie-właściciel do tworzenia Avere vFXT dla klastra platformy Azure. Te scenariusze obejmują ograniczenie zasobów i przypisanie do twórcy dodatkowych ról kontroli dostępu opartej na rolach (RBAC). We wszystkich tych przypadkach właściciel subskrypcji musi również [zaakceptować warunki oprogramowania Avere vFXT](#accept-software-terms) z wyprzedzeniem.

| Scenariusz | Ograniczenia | Role dostępu wymagane do utworzenia klastra VFXT Avere |
|----------|--------|-------|
| Administrator grupy zasobów tworzy vFXT | Sieć wirtualna, kontroler klastra i węzły klastra muszą być tworzone w ramach grupy zasobów. | [Administrator dostępu użytkownika](../role-based-access-control/built-in-roles.md#user-access-administrator) i [współautor](../role-based-access-control/built-in-roles.md#contributor) ról, oba ograniczone do docelowej grupy zasobów. |
| Korzystanie z istniejącej, zewnętrznej sieci wirtualnej | Kontroler klastra i węzły klastra są tworzone w ramach grupy zasobów vFXT, ale używają istniejącej sieci wirtualnej w innej grupie zasobów. | (1) [Administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) i rola [współautora](../role-based-access-control/built-in-roles.md#contributor) są ograniczone do grupy zasobów vFXT; oraz (2) [Współtwórca maszyny wirtualnej,](../role-based-access-control/built-in-roles.md#virtual-machine-contributor) [administrator dostępu użytkownika](../role-based-access-control/built-in-roles.md#user-access-administrator)i [avere współautor](../role-based-access-control/built-in-roles.md#avere-contributor) role ograniczone do grupy zasobów sieci wirtualnej. |
| Rola niestandardowa dla twórców klastrów | Brak ograniczeń umieszczania zasobów. Ta metoda daje nie-właścicieli znaczące uprawnienia. | Właściciel subskrypcji tworzy niestandardową rolę RBAC, jak wyjaśniono w [tym artykule](avere-vfxt-non-owner.md). |

## <a name="quota-for-the-vfxt-cluster"></a>Przydział dla klastra vFXT

Sprawdź, czy masz wystarczający przydział dla następujących składników platformy Azure. W razie potrzeby [poproś o zwiększenie kwoty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Maszyny wirtualne i składniki SSD wymienione w tym miejscu są dla samego klastra vFXT. Należy pamiętać, że potrzebujesz również przydziału dla maszyn wirtualnych i ssd, które będą używane dla farmy obliczeniowej.
>
> Upewnij się, że przydział jest włączony dla regionu, w którym zamierzasz uruchomić przepływ pracy.

|Składnik platformy Azure|Limit przydziału|
|----------|-----------|
|Maszyny wirtualne|3 lub więcej E32s_v3 (po jednym na węzeł klastra) |
|Magazyn SSD w warstwie Premium|200 GB na system operacyjny i od 1 TB do 4 TB miejsca obszaru pamięci podręcznej na węzeł |
|Konto magazynu (opcjonalnie) |v2|
|Magazyn zaplecza danych (opcjonalnie) |Jeden nowy kontener obiektów blob LRS |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Akceptowanie warunków oprogramowania

> [!TIP]
> Pomiń ten krok, jeśli właściciel subskrypcji utworzy klaster Avere vFXT.

Podczas tworzenia klastra należy zaakceptować warunki korzystania z usługi oprogramowania Avere vFXT. Jeśli nie jesteś właścicielem subskrypcji, niech właściciel subskrypcji zaakceptuje warunki z wyprzedzeniem.

Ten krok musi być wykonany tylko raz na subskrypcję.

Aby zaakceptować warunki oprogramowania z wyprzedzeniem:

1. Otwórz powłokę chmury w witrynie Azure <https://shell.azure.com>lub przejdź do . Zaloguj się przy po swojemu identyfikatorowi subskrypcji.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Wystosuj to polecenie, aby zaakceptować warunki usługi i włączyć dostęp programowy dla obrazu oprogramowania Avere vFXT for Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Tworzenie punktu końcowego usługi magazynowania w sieci wirtualnej (w razie potrzeby)

[Punkt końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) przechowuje ruch obiektów blob platformy Azure lokalnie zamiast routingu go poza siecią wirtualną. Jest zalecane dla dowolnego Avere vFXT dla klastra platformy Azure, który używa usługi Azure Blob do magazynu danych zaplecza.

Jeśli podczas tworzenia klastra utworzysz nową sieć wirtualną, punkt końcowy jest tworzony automatycznie. Jeśli podasz istniejącą sieć wirtualną, musi mieć punkt końcowy usługi magazynu firmy Microsoft, jeśli chcesz utworzyć nowy kontener magazynu obiektów Blob podczas tworzenia klastra.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Pomiń ten krok, jeśli tworzysz nową sieć wirtualną w ramach tworzenia klastra.
>* Punkt końcowy jest opcjonalny, jeśli nie tworzysz magazynu obiektów Blob podczas tworzenia klastra. W takim przypadku można utworzyć punkt końcowy usługi później, jeśli zdecydujesz się użyć obiektu Blob platformy Azure.

Utwórz punkt końcowy usługi magazynu z witryny Azure portal.

1. W portalu otwórz listę sieci wirtualnych.
1. Wybierz sieć wirtualną dla klastra.
1. Kliknij **polecenie Punkty końcowe usługi** z lewego menu.
1. Kliknij **pozycję Dodaj** u góry.
1. Wybierz usługę ``Microsoft.Storage``.
1. Wybierz podsieć klastra.
1. U dołu kliknij pozycję **Dodaj**.

   ![Zrzut ekranu portalu Platformy Azure z adnotacjami na temat kroków tworzenia punktu końcowego usługi](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Następne kroki

Po wykonaniu tych wymagań wstępnych można utworzyć klaster. Przeczytaj [artykuł Wdrażanie klastra vFXT,](avere-vfxt-deploy.md) aby uzyskać instrukcje.
