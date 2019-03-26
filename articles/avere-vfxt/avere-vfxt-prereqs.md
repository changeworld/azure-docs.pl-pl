---
title: Wymagania wstępne z vFXT Avere - Azure
description: Wymagania wstępne dotyczące vFXT Avere dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 04af92f21cecaa832e857a7017b67f815f6ab685
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417976"
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

Istnieją dwa obejścia problemu, jeśli nie chcesz nadać dostęp właściciela do użytkowników, którzy tworzą vFXT:

* Właścicielem grupy zasobów można utworzyć klaster, jeśli te warunki są spełnione:

  * Właściciel subskrypcji musi [zaakceptuj postanowienia dotyczące oprogramowania dla vFXT Avere](#accept-software-terms) i [tworzenie roli dostęp do węzła klastra](#create-the-cluster-node-access-role). 
  * Wszystkie zasoby vFXT Avere musi zostać wdrożony w grupie zasobów, w tym:
    * Kontroler klastra
    * Węzły klastra
    * Blob Storage
    * Elementy sieci
 
* Użytkownik z uprawnieniami właściciela, nie można utworzyć klastry vFXT przy użyciu kontroli dostępu opartej na rolach (RBAC), wcześniej, aby przypisać uprawnienia dla użytkownika. Ta metoda zapewnia znaczące uprawnienia dla tych użytkowników. [W tym artykule](avere-vfxt-non-owner.md) wyjaśnia, jak utworzyć rolę dostępu do autoryzacji innych właścicieli do tworzenia klastrów.

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

## <a name="create-access-roles"></a>Tworzenie ról dostępu 

[Kontrola dostępu oparta na rolach](../role-based-access-control/index.yml) (RBAC) umożliwia vFXT klastra kontrolera i węzłami klastra autoryzacji do wykonania niezbędnych zadań.

* Kontroler klastra wymaga uprawnienia do tworzenia i modyfikowania maszyn wirtualnych w celu utworzenia klastra. 

* VFXT poszczególne węzły muszą wykonywać następujące czynności odczytu właściwości zasobów platformy Azure, zarządzania magazynem i kontrolować ustawienia interfejsu sieciowego innych węzłów w ramach klastra normalnych operacji.

Przed utworzeniem klastra vFXT Avere należy zdefiniować rolę niestandardową do użycia z węzłów klastra. 

Dla kontrolera klastra możesz zaakceptować domyślną rolę z szablonu. Wartość domyślna zapewnia klastra uprawnienia właściciela grupy zasobów dla kontrolera. Jeśli wolisz utworzyć rolę niestandardową dla kontrolera, zobacz [roli dostęp do kontrolera dostosowany](avere-vfxt-controller-role.md).

> [!NOTE] 
> Tylko właściciel subskrypcji lub użytkownik mający rolę właściciel lub Administrator dostępu użytkowników, można utworzyć role. Role można utworzyć wcześniej.  

### <a name="create-the-cluster-node-access-role"></a>Tworzenie roli dostęp do węzła klastra

<!-- caution - this header is linked to in the template so don't change it unless you can change that -->

Należy utworzyć rolę węzła klastra, aby można było utworzyć vFXT Avere klastra platformy Azure.

> [!TIP] 
> Użytkownicy wewnętrzni firmy Microsoft należy używać istniejącej roli o nazwie "Operator środowiska uruchomieniowego klastra Avere" zamiast próby, aby je utworzyć. 

1. Skopiuj ten plik. Dodaj swój identyfikator subskrypcji, w wierszu AssignableScopes.

   (Bieżąca wersja tego pliku są przechowywane w repozytorium github.com/Azure/Avere jako [AvereOperator.txt](https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereOperator.txt).)  

   ```json
   {
      "AssignableScopes": [
          "/subscriptions/PUT_YOUR_SUBSCRIPTION_ID_HERE"
      ],
      "Name": "Avere Operator",
      "IsCustom": "true",
      "Description": "Used by the Avere vFXT cluster to manage the cluster",
      "NotActions": [],
      "Actions": [
          "Microsoft.Compute/virtualMachines/read",
          "Microsoft.Network/networkInterfaces/read",
          "Microsoft.Network/networkInterfaces/write",
          "Microsoft.Network/virtualNetworks/read",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "DataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ]
   }
   ```

1. Zapisz plik jako ``avere-operator.json`` lub podobny do zapamiętania nazwę. 


1. Otwórz powłokę w chmurze platformy Azure i zaloguj się przy użyciu Identyfikatora subskrypcji (opisane [we wcześniejszej części tego dokumentu](#accept-software-terms)). Użyj tego polecenia, aby utworzyć rolę:

   ```bash
   az role definition create --role-definition /avere-operator.json
   ```

Nazwa roli jest używany podczas tworzenia klastra. W tym przykładzie nazwa to ``avere-operator``.

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