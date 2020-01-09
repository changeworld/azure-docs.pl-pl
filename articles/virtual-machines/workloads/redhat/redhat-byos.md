---
title: Red Hat Enterprise Linux obrazy subskrypcję z własnych potrzeb | Microsoft Docs
description: Dowiedz się więcej na temat przenoszenia własnych obrazów subskrypcji dla Red Hat Enterprise Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: alsin
ms.openlocfilehash: afd59c44bea238b7dd6f116e491054f8b4a52d48
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486510"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-images-in-azure"></a>Red Hat Enterprise Linux dołączenia obrazów własnych subskrypcji na platformie Azure
Obrazy Red Hat Enterprise Linux (RHEL) są dostępne na platformie Azure za pośrednictwem modelu "płatność zgodnie z rzeczywistym użyciem" lub "Przenieś własną subskrypcję" (BYOS). Ten dokument zawiera omówienie obrazów BYOS na platformie Azure.

## <a name="important-points-to-consider"></a>Ważne kwestie, które należy wziąć pod uwagę

- Obrazy RHEL BYOS dostępne w tym programie to gotowe do produkcji obrazy RHEL, podobne do obrazów RHEL PAYG w galerii platformy Azure/Marketplace. Proces rejestracji w celu uzyskania obrazów jest w wersji zapoznawczej.

- Obrazy są zgodne z naszymi bieżącymi zasadami opisanymi w [Red Hat Enterprise Linux obrazów na platformie Azure](./redhat-images.md)

- Standardowe zasady pomocy technicznej mają zastosowanie do maszyn wirtualnych utworzonych na podstawie tych obrazów

- Maszyny wirtualne udostępnione z obrazów RHEL BYOS nie zawierają opłat RHEL związanych z obrazami RHEL PAYG

- Obrazy są nieuprawnione, dlatego należy użyć Menedżera subskrypcji do zarejestrowania i subskrybowania maszyn wirtualnych, aby uzyskać aktualizacje z rozwiązania Red Hat bezpośrednio

- Nie jest obecnie możliwe przełączenie dynamicznie między modelami rozliczeń BYOS i PAYG dla obrazów systemu Linux. Ponowne wdrożenie maszyny wirtualnej z odpowiedniego obrazu jest wymagane do przełączenia modelu rozliczania

- Azure Disk Encryption (ADE) jest obsługiwana w tych obrazach BYOS RHEL. Obsługa ADE jest obecnie dostępna w wersji zapoznawczej. Przed skonfigurowaniem ADE należy zarejestrować się w usłudze Red Hat przy użyciu Menedżera subskrypcji. Po zarejestrowaniu w celu skonfigurowania usługi ADE zapoznaj się z: [włączanie Azure Disk Encryption dla maszyn wirtualnych z systemem Linux IaaS](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-overview)

- Obrazy nie ulegną zmianie (poza aktualizacjami standardowymi i poprawkami), ale proces rejestracji jest w wersji zapoznawczej, a przepływ zostanie ulepszony w celu usprawnienia procesu

- Masz pełną kontrolę nad maszynami wirtualnymi, które są już obsługiwane z tych obrazów lub ich migawek niezależnie od implementacji końcowej

## <a name="requirements-and-conditions-to-access-the-rhel-byos-images"></a>Wymagania i warunki dostępu do obrazów BYOS RHEL

1. Zapoznaj się z postanowieniami [programu Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) i zarejestruj się na [stronie rejestracji dostępu do chmury w systemie Red Hat](https://access.redhat.com/cloude/manager/image_imports/new).

1. Ukończ [formularz żądania dostępu RHEL BYOS](https://aka.ms/rhel-byos). Musisz mieć dostęp do numeru konta Red Hat, a także Twoich subskrypcji platformy Azure, za pomocą których będziesz mieć możliwość dostępu do obrazów RHEL BYOS.

1. Po przejrzeniu i zatwierdzeniu przez firmę Red Hat i Microsoft subskrypcje platformy Azure będą włączane dla dostępu do obrazu.

### <a name="expected-time-for-image-access"></a>Oczekiwany czas dostępu do obrazu

Po ukończeniu formularza RHEL BYOS i przyjęcia warunków Red Hat sprawdzi, czy Twoje uprawnienia do obrazów BYOS w ciągu trzech dni roboczych i, jeśli będzie obowiązywać, otrzymają później dostęp do obrazów BYOS w ciągu jednego dnia roboczego.

## <a name="use-the-rhel-byos-images-from-the-azure-portal"></a>Korzystanie z obrazów RHEL BYOS w witrynie Azure Portal

1. Po włączeniu subskrypcji dla obrazów RHEL BYOS można zlokalizować ją w [Azure Portal](https://portal.azure.com) , przechodząc do sekcji **Tworzenie zasobu** , a następnie klikając pozycję **wszystkie**.

1. W górnej części strony zobaczysz, że masz prywatne oferty.

    ![Oferty prywatne w witrynie Marketplace](./media/rhel-byos-privateoffers.png)

1. Możesz kliknąć link purpurowy lub przewinąć w dół do dolnej części strony, aby wyświetlić oferty prywatne.

1. Dalsze Inicjowanie obsługi w interfejsie użytkownika nie będzie inne niż w żadnym innym istniejącym obrazie Red Hat. Wybierz wersję RHEL i postępuj zgodnie z monitami, aby zainicjować obsługę administracyjną maszyny wirtualnej. Ten proces umożliwia również zaakceptowanie warunków obrazu w ostatnim kroku.

>[!NOTE]
>Te kroki do tej pory nie spowodują włączenia obrazu programu RHEL BYOS na potrzeby wdrażania programistycznego — należy wykonać dodatkowy krok zgodnie z opisem w sekcji "dodatkowe informacje" poniżej.

Pozostała część tego dokumentu koncentruje się na metodzie interfejsu wiersza polecenia w celu aprowizacji i akceptowania warunków na obrazie. Interfejs użytkownika i interfejsu wiersza polecenia są w pełni zamiennie zmieniane w miarę jak końcowy wynik (administracyjna RHEL BYOS VM).

## <a name="use-the-rhel-byos-images-from-the-azure-cli"></a>Korzystanie z obrazów RHEL BYOS w interfejsie wiersza polecenia platformy Azure
Poniższy zestaw instrukcji przeprowadzi Cię przez proces wdrażania wstępnego dla maszyny wirtualnej RHEL przy użyciu interfejsu wiersza polecenia platformy Azure. W tych instrukcjach przyjęto założenie, że masz [zainstalowany interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Upewnij się, że używasz wszystkich małych liter w odwołaniach do wydawcy, oferty, planu i obrazu dla wszystkich następujących poleceń

1. Sprawdź, czy jesteś w żądanej subskrypcji:
    ```azurecli
    az account show -o=json
    ```

1. Utwórz grupę zasobów dla maszyny wirtualnej RHEL BYOS:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Zaakceptuj warunki obrazu:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Te postanowienia muszą zostać zaakceptowane *raz dla każdej subskrypcji platformy Azure, dla jednostki SKU obrazu*.

1. Obowiązkowe Sprawdź poprawność wdrożenia maszyny wirtualnej za pomocą następującego polecenia:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Inicjowanie obsługi administracyjnej maszyny wirtualnej przez uruchomienie tego samego polecenia jak powyżej bez argumentu `--validate`:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. Użyj protokołu SSH do maszyny wirtualnej i sprawdź, czy masz nieuprawniony obraz. W tym celu uruchom `sudo yum repolist`. W danych wyjściowych zostanie wyświetlony zapytanie o użycie Menedżera subskrypcji w celu zarejestrowania maszyny wirtualnej przy użyciu rozwiązania Red Hat.

## <a name="additional-information"></a>Dodatkowe informacje
- Jeśli spróbujesz zainicjować obsługę administracyjną maszyny wirtualnej w subskrypcji, która nie jest włączona dla tej oferty, zostanie wyświetlony następujący błąd i należy skontaktować się z firmą Microsoft lub Red Hat w celu włączenia subskrypcji.
    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

- W przypadku tworzenia migawki z obrazu RHEL BYOS i publikowania obrazu w [galerii obrazów udostępnionych](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)należy podać informacje o planie zgodne z oryginalnym źródłem migawki. Na przykład polecenie może wyglądać jak (zanotuj parametry planu w ostatnim wierszu):
    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

- Jeśli używasz automatyzacji do inicjowania obsługi maszyn wirtualnych z obrazów RHEL BYOS, musisz dostarczyć parametry planu podobne do tego, co zostało pokazane powyżej. Na przykład jeśli używasz Terraform, podaj informacje o planie w [bloku planu](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [infrastrukturze aktualizacji usługi Azure Red Hat](./redhat-rhui.md).
* Aby dowiedzieć się więcej na temat obrazów Red Hat na platformie Azure, przejdź do [strony dokumentacji](./redhat-images.md).
* Informacje o zasadach obsługi systemu Red Hat dla wszystkich wersji programu RHEL można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .