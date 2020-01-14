---
title: Avere vFXT — wymagania wstępne — Azure
description: Wymagania wstępne dotyczące programu avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 0dafef7cf262153ccdb3b490aa0c7bd039b4a89b
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889185"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Przygotowanie do utworzenia systemu Avere vFXT

W tym artykule opisano wstępnie wymagane zadania tworzenia klastra avere vFXT.

## <a name="create-a-new-subscription"></a>Utwórz nową subskrypcję

Zacznij od utworzenia nowej subskrypcji platformy Azure. Użyj oddzielnej subskrypcji dla każdego projektu avere vFXT, aby można było łatwo śledzić wszystkie zasoby projektu i wydatki, chronić inne projekty przed możliwymi ograniczeniami zasobów podczas aprowizacji i uprościć czyszczenie.

Aby utworzyć nową subskrypcję platformy Azure w Azure Portal:

* Przejdź do [bloku subskrypcje](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Kliknij przycisk **+ Dodaj** w górnej części strony
* Zaloguj się, jeśli zostanie wyświetlony monit
* Wybierz ofertę i zapoznaj się z instrukcjami, aby utworzyć nową subskrypcję

## <a name="configure-subscription-owner-permissions"></a>Konfigurowanie uprawnień właściciela subskrypcji

Użytkownik z uprawnieniami właściciela dla subskrypcji powinien utworzyć klaster vFXT. Uprawnienia właściciela subskrypcji są konieczne, aby zaakceptować warunki użytkowania oprogramowania i wykonać inne czynności.

Istnieją pewne scenariusze obejścia, które umożliwiają innym firmom tworzenie avere vFTX dla klastra platformy Azure. Te scenariusze obejmują ograniczanie zasobów i przypisywanie dodatkowych ról do twórcy. W obu tych przypadkach właściciel subskrypcji również musi [zaakceptować warunki oprogramowania avere vFXT](#accept-software-terms) przed czasem.

| Scenariusz | Ograniczenia | Role dostępu wymagane do utworzenia klastra avere vFXT |
|----------|--------|-------|
| Administrator grupy zasobów | Sieć wirtualna, kontroler klastra i węzły klastra muszą zostać utworzone w obrębie grupy zasobów. | Rola [administratora dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) i [współautorów](../role-based-access-control/built-in-roles.md#contributor) , należących do zakresu docelowej grupy zasobów |
| Zewnętrzna Sieć wirtualna | Kontroler klastra i węzły klastra są tworzone w grupie zasobów, ale używana jest istniejąca sieć wirtualna w innej grupie zasobów | (1) [administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) i role [współautor](../role-based-access-control/built-in-roles.md#contributor) zakresu do grupy zasobów vFXT; i (2) [współautor maszyny wirtualnej](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [administrator dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator)i role [współautor avere](../role-based-access-control/built-in-roles.md#avere-contributor) należące do grupy zasobów sieci wirtualnej. |

Alternatywą jest utworzenie niestandardowej roli kontroli dostępu opartej na rolach (RBAC) przed czasem i przypisanie uprawnień użytkownikowi, zgodnie z opisem w [tym artykule](avere-vfxt-non-owner.md). Ta metoda zapewnia znaczące uprawnienia dla tych użytkowników.

## <a name="quota-for-the-vfxt-cluster"></a>Przydział dla klastra vFXT

Musisz mieć wystarczający przydział dla następujących składników platformy Azure. W razie konieczności [Poproś o zwiększenie limitu przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Maszyny wirtualne i składniki SSD wymienione w tym miejscu są przeznaczone dla samego klastra vFXT. Wymagany jest dodatkowy przydział dla maszyn wirtualnych i dysku SSD, które mają być używane w farmie obliczeniowej.  Upewnij się, że przydział jest włączony dla regionu, w którym zamierzasz uruchomić przepływ pracy.

|Składnik platformy Azure|Przydział|
|----------|-----------|
|Maszyny wirtualne|3 lub więcej E32s_v3|
|Magazyn SSD w warstwie Premium|200 GB na system operacyjny i od 1 TB do 4 TB miejsca obszaru pamięci podręcznej na węzeł |
|Konto magazynu (opcjonalnie) |v2|
|Magazyn zaplecza danych (opcjonalnie) |Jeden nowy kontener obiektów BLOB LRS |

## <a name="accept-software-terms"></a>Zaakceptuj warunki użytkowania oprogramowania

> [!NOTE]
> Ten krok nie jest wymagany, jeśli właściciel subskrypcji tworzy klaster avere vFXT.

Podczas tworzenia klastra należy zaakceptować warunki użytkowania oprogramowania avere vFXT. Jeśli nie jesteś właścicielem subskrypcji, mieć właściciela subskrypcji, który wcześniej akceptuje warunki. Ten krok należy wykonać tylko raz dla każdej subskrypcji.

Aby zaakceptować z góry warunki oprogramowania:

1. Otwórz powłokę chmury w Azure Portal lub przechodząc do <https://shell.azure.com>. Zaloguj się przy użyciu identyfikatora subskrypcji.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Wydaj to polecenie, aby akceptować warunki korzystania z usługi i włączyć dostęp programistyczny dla obrazu oprogramowania avere vFXT for Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Utwórz punkt końcowy usługi magazynu w sieci wirtualnej (w razie konieczności)

[Punkt końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) utrzymuje ruch obiektów blob platformy Azure jako lokalny zamiast routingu poza siecią wirtualną. Jest to zalecane w przypadku wszystkich avere vFXT dla klastra platformy Azure, które używają obiektów blob platformy Azure do przechowywania danych zaplecza.

Jeśli udostępniasz istniejącą sieć wirtualną i utworzysz nowy kontener obiektów blob platformy Azure dla magazynu zaplecza w ramach tworzenia klastra, musisz mieć punkt końcowy usługi w sieci dla magazynu Microsoft Storage. Ten punkt końcowy musi istnieć przed utworzeniem klastra lub utworzenie nie powiedzie się.

Punkt końcowy usługi magazynu jest zalecany dla dowolnego avere vFXT dla klastra platformy Azure korzystającego z usługi Azure Blob Storage, nawet jeśli później zostanie dodany magazyn.

> [!TIP]
>
>* Pomiń ten krok, jeśli tworzysz nową sieć wirtualną w ramach tworzenia klastra.
>* Ten krok jest opcjonalny, jeśli nie tworzysz magazynu obiektów BLOB podczas tworzenia klastra. W takim przypadku można utworzyć punkt końcowy usługi później, jeśli zdecydujesz się użyć obiektu blob platformy Azure.

Utwórz punkt końcowy usługi magazynu na podstawie Azure Portal.

1. W portalu Otwórz listę sieci wirtualnych.
1. Wybierz sieć wirtualną dla klastra.
1. Kliknij pozycję **punkty końcowe usługi** w menu po lewej stronie.
1. Kliknij przycisk **Dodaj** w górnej części ekranu.
1. Wybierz ``Microsoft.Storage``usługi.
1. Wybierz podsieć klastra.
1. Kliknij przycisk **Dodaj**w dolnej części.

   ![Zrzut ekranu Azure Portal z adnotacjami dotyczącymi kroków tworzenia punktu końcowego usługi](media/avere-vfxt-service-endpoint.png)

## <a name="next-step-create-the-vfxt-cluster"></a>Następny krok: Tworzenie klastra vFXT

Po wykonaniu tych wymagań wstępnych możesz przejść do tworzenia klastra. Aby uzyskać instrukcje, zapoznaj [się z artykułem wdrażanie klastra vFXT](avere-vfxt-deploy.md) .
