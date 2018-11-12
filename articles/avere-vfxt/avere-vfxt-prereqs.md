---
title: Wymagania wstępne z vFXT Avere - Azure
description: Wymagania wstępne dotyczące vFXT Avere dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d32c664049b7e7c1231e78c552e7c61d016fbe84
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51286762"
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

Użytkownik z uprawnieniami właściciela subskrypcji należy utworzyć klaster vFXT. Uprawnienia właściciela subskrypcji są wymagane do wykonywania tych akcji, między innymi:

* Zaakceptuj postanowienia oprogramowania vFXT Avere
* Tworzenie roli dostęp do węzła klastra
* Zezwalaj na węzeł klastra Kontroler zarządzania grupami zasobów i sieciami wirtualnymi 
* Zezwalaj na kontroler klastra do tworzenia i modyfikowania węzłów klastra 

Istnieją dwa obejścia problemu, jeśli nie chcesz nadać dostęp właściciela do użytkowników, którzy tworzą vFXT:

* Właścicielem grupy zasobów można utworzyć klaster, jeśli te warunki są spełnione:

  * Właściciel subskrypcji musi [zaakceptuj postanowienia dotyczące oprogramowania dla vFXT Avere](#accept-software-terms-in-advance) i [tworzenie roli dostęp do węzła klastra](avere-vfxt-deploy.md#create-the-cluster-node-access-role).
  * Wszystkie zasoby vFXT Avere musi zostać wdrożony w grupie zasobów, w tym:
    * Kontroler klastra
    * Węzły klastra
    * Blob Storage
    * Elementy sieci
 
* Użytkownik z uprawnieniami właściciela, nie można utworzyć klastry vFXT, jeśli dodatkowy dostęp roli zostanie utworzone i przypisane do użytkownika, które wcześniej. Jednak ta rola zapewnia znaczące uprawnienia dla tych użytkowników. [W tym artykule](avere-vfxt-non-owner.md) wyjaśnia, jak autoryzować innych właścicieli do tworzenia klastrów.

## <a name="quota-for-the-vfxt-cluster"></a>Limit przydziału dla klastra vFXT

Musi mieć wystarczającego limitu przydziału dla następujących składników platformy Azure. Jeśli to konieczne, [zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Maszyny wirtualne i dyski SSD składniki wymienione w tym miejscu są vFXT klastrowi. Konieczne będzie dodatkowego przydziału dla maszyn wirtualnych i dysków SSD, których zamierzasz używać w kolektywie obliczeń.  Upewnij się, że włączono limit przydziału dla regionu, w którym zamierzasz uruchomić przepływ pracy.

|Składnik platformy Azure|Przydział|
|----------|-----------|
|Maszyny wirtualne|3 lub więcej maszyny wirtualne D16s_v3 lub E32s_v3|
|Magazyn SSD w warstwie Premium|200 GB na system operacyjny i od 1 TB do 4 TB miejsca obszaru pamięci podręcznej na węzeł |
|Konto magazynu (opcjonalnie) |v2|
|Magazyn zaplecza danych (opcjonalnie) |Jeden nowy kontener obiektów Blob LRS |

## <a name="accept-software-terms-in-advance"></a>Zaakceptuj warunki wcześniej oprogramowania

> [!NOTE] 
> Ten krok nie jest wymagane, jeśli właściciel subskrypcji tworzy klaster vFXT Avere.

Przed utworzeniem klastra, musisz zaakceptować warunki użytkowania oprogramowania vFXT Avere. Jeśli nie jesteś właścicielem subskrypcji, należy mieć właścicielem subskrypcji, zaakceptuj warunki wcześniej. Ten krok tylko musi odbywać się jeden raz na subskrypcję.

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

## <a name="next-step-create-the-vfxt-cluster"></a>Następny krok: Tworzenie klastra vFXT

Po zakończeniu tych wymagań wstępnych, możesz przejść do tworzenia samego klastra. Odczyt [wdrożenie klastra vFXT](avere-vfxt-deploy.md) instrukcje.