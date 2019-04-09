---
title: Wymagania wstępne z vFXT Avere - Azure
description: Wymagania wstępne dotyczące vFXT Avere dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 352833b12c00abbefcf7016d27dfb580ee25e450
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275882"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Przygotowanie do utworzenia systemu Avere vFXT

W tym artykule opisano zadania wstępne do tworzenia klastra vFXT Avere.

## <a name="create-a-new-subscription"></a>Utwórz nową subskrypcję

Rozpocznij od utworzenia nowej subskrypcji platformy Azure. Użyj oddzielnej subskrypcji dla każdego projektu vFXT Avere pozwala łatwo śledzić wszystkie zasoby projektu i wydatki, ochrony innych projektów z możliwości ograniczania podczas aprowizowania zasobów i uprościć czyszczenie.  

Aby utworzyć nową subskrypcję platformy Azure w witrynie Azure portal:

* Przejdź do [bloku subskrypcje](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Kliknij przycisk **+ Dodaj** znajdujący się u góry
* Zaloguj się, jeśli zostanie wyświetlony monit
* Wybierz ofertę i opisano kroki, aby utworzyć nową subskrypcję

## <a name="configure-subscription-owner-permissions"></a>Skonfiguruj uprawnienia właściciela do subskrypcji

Użytkownik z uprawnieniami właściciela subskrypcji należy utworzyć klaster vFXT. Zaakceptuj postanowienia dotyczące oprogramowania, usług i wykonywać inne czynności są potrzebne uprawnienia właściciela subskrypcji. 

Istnieją pewne scenariusze obejście, umożliwiające niebędących właścicielami utworzyć vFTX Avere klastra platformy Azure. Te scenariusze obejmują ograniczanie zasobów i przypisywania dodatkowych ról do twórcy. W obu przypadkach należy również właściciela subskrypcji [zaakceptuj postanowienia dotyczące oprogramowania dla vFXT Avere](#accept-software-terms) wcześniej. 

| Scenariusz | Ograniczenia | Role dostępu wymagane do utworzenia klastra vFXT Avere | 
|----------|--------|-------|
| Administrator grupy zasobów | Sieć wirtualną, kontrolera klastra i węzłów klastra musi zostać utworzona w grupie zasobów | [Administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) i [Współautor](../role-based-access-control/built-in-roles.md#contributor) ról, zarówno ograniczone do docelowej grupy zasobów | 
| Zewnętrznej sieci wirtualnej | Kontroler klastra i węzły klastra są tworzone w obrębie grupy zasobów, ale jest używany przez istniejącą sieć wirtualną w innej grupie zasobów | (1) [Administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) i [Współautor](../role-based-access-control/built-in-roles.md#contributor) role ograniczone do grupy zasobów vFXT; oraz (2) [Współautor maszyny wirtualnej](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [dostępu użytkownika Administrator](../role-based-access-control/built-in-roles.md#user-access-administrator), i [Współautor Avere](../role-based-access-control/built-in-roles.md#avere-contributor) ról do zakresu grupy zasobów sieci Wirtualnej. |
 
Alternatywą jest utworzyć rolę kontroli dostępu opartej na rolach niestandardowych wcześniejsze i przypisywanie uprawnień użytkownikowi, jak wyjaśniono w [w tym artykule](avere-vfxt-non-owner.md). Ta metoda zapewnia znaczące uprawnienia dla tych użytkowników. 

## <a name="quota-for-the-vfxt-cluster"></a>Limit przydziału dla klastra vFXT

Musi mieć wystarczającego limitu przydziału dla następujących składników platformy Azure. Jeśli to konieczne, [zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Maszyny wirtualne i dyski SSD składniki wymienione w tym miejscu są vFXT klastrowi. Konieczne będzie dodatkowego przydziału dla maszyn wirtualnych i dysków SSD, których zamierzasz używać w kolektywie obliczeń.  Upewnij się, że włączono limit przydziału dla regionu, w którym zamierzasz uruchomić przepływ pracy.

|Składnik platformy Azure|Przydział|
|----------|-----------|
|Maszyny wirtualne|co najmniej 3 E32s_v3|
|Magazyn SSD w warstwie Premium|200 GB na system operacyjny i od 1 TB do 4 TB miejsca obszaru pamięci podręcznej na węzeł |
|Konto magazynu (opcjonalnie) |v2|
|Magazyn zaplecza danych (opcjonalnie) |Jeden nowy kontener obiektów Blob LRS |

## <a name="accept-software-terms"></a>Zaakceptuj postanowienia dotyczące oprogramowania

> [!NOTE] 
> Ten krok nie jest wymagane, jeśli właściciel subskrypcji tworzy klaster vFXT Avere.

Podczas tworzenia klastra musisz zaakceptować warunki użytkowania oprogramowania vFXT Avere. Jeśli nie jesteś właścicielem subskrypcji, należy mieć właścicielem subskrypcji, zaakceptuj warunki wcześniej. Ten krok tylko musi odbywać się jeden raz na subskrypcję.

Aby zaakceptować oprogramowania wcześniej warunków: 

1. Otwórz usługę cloud shell, w witrynie Azure portal lub przechodząc do <https://shell.azure.com>. Zaloguj się przy użyciu identyfikatora subskrypcji.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Wykonaj to polecenie, aby zaakceptować warunki usługi i włączyć dostęp programowy do vFXT Avere obrazu oprogramowania platformy Azure: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Tworzenie punktu końcowego usługi magazynu w sieci wirtualnej (w razie potrzeby)

A [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) utrzymuje lokalnego zamiast przesyłać go poza siecią wirtualną ruchu obiektu Blob platformy Azure. Zalecane dla dowolnej vFXT Avere dla klastra platformy Azure, który używa obiektu Blob platformy Azure do przechowywania danych zaplecza. 

Jeśli udostępniasz istniejącej sieci wirtualnej i utworzenia nowego kontenera obiektów Blob platformy Azure do obsługi magazynu zaplecza w ramach tworzenia klastra, musisz mieć punktu końcowego usługi w sieci wirtualnej dla usługi Microsoft storage. Ten punkt końcowy musi istnieć przed utworzeniem klastra lub tworzenia zakończy się niepowodzeniem. 

Punkt końcowy usługi magazynu jest zalecana dla dowolnego vFXT Avere dla klastra platformy Azure, która używa usługi Azure Blob storage, nawet, jeśli magazyn jest dodawany do dalszej części. 

> [!TIP] 
> * Pomiń ten krok, jeśli tworzysz nową sieć wirtualną jako część tworzenia klastra. 
> * Ten krok jest opcjonalny, jeśli nie utworzysz magazyn obiektów Blob podczas tworzenia klastra. W takim przypadku można utworzyć punktu końcowego usługi później Jeśli zdecydujesz się używać usługi Azure blob Storage.

Tworzenie punktu końcowego usługi storage w witrynie Azure portal. 

1. Z poziomu portalu, kliknij przycisk **sieci wirtualne** po lewej stronie.
1. Wybierz sieć wirtualną dla klastra. 
1. Kliknij przycisk **punkty końcowe usługi** po lewej stronie.
1. Kliknij przycisk **Dodaj** u góry.
1. Pozostaw usługę jako ``Microsoft.Storage`` i wybierz podsieć klastra.
1. Kliknij u dołu, **Dodaj**.

   ![Azure portal zrzut ekranu z adnotacjami, aby uzyskać instrukcje tworzenia punktu końcowego usługi](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Następny krok: Tworzenie klastra vFXT

Po zakończeniu tych wymagań wstępnych, możesz przejść do tworzenia samego klastra. Odczyt [wdrożenie klastra vFXT](avere-vfxt-deploy.md) instrukcje.