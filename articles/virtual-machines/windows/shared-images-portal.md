---
title: Tworzenie udostępnionych obrazów maszyn wirtualnych platformy Azure dla systemu Windows za pomocą portalu | Microsoft Docs
description: Dowiedz się, jak tworzyć i udostępniać obrazy maszyn wirtualnych przy użyciu Azure Portal.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: e04cbd4750a97857166eb5939045bdb8ace1b426
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102379"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Tworzenie udostępnionej galerii obrazów przy użyciu Azure Portal

[Galeria obrazów udostępnionych](shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Niestandardowe obrazy mogą służyć do uruchamiania zadań wdrażania, takich jak wstępne ładowanie aplikacji, konfiguracje aplikacji i inne konfiguracje systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom w organizacji, w obrębie lub w różnych regionach, w ramach dzierżawy usługi AAD. Wybierz obrazy, które chcesz udostępnić, które regiony mają być dostępne w programie, oraz użytkowników, którym chcesz je udostępnić. Można utworzyć wiele galerii, aby można było logicznie grupować obrazy udostępnione. 

Galeria jest zasobem najwyższego poziomu, który zapewnia pełną kontrolę dostępu opartą na rolach (RBAC). Obrazy mogą być poddane wersji i można wybrać replikację poszczególnych wersji obrazu do innego zestawu regionów świadczenia usługi Azure. Galeria działa tylko z obrazami zarządzanymi.

Funkcja galerii obrazów udostępnionych ma wiele typów zasobów. Firma Microsoft będzie używać tych aplikacji lub tworzyć je w tym artykule:

| Resource | Opis|
|----------|------------|
| **Obraz zarządzany** | Jest to podstawowy obraz, którego można użyć samodzielnie lub użyć do utworzenia **wersji obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na podstawie uogólnionych maszyn wirtualnych. Obraz zarządzany jest specjalnym typem dysku VHD, który może służyć do tworzenia wielu maszyn wirtualnych i może być teraz używany do utworzenia wersji obrazu udostępnionego. |
| **Galeria obrazów** | Podobnie jak w przypadku portalu Azure Marketplace, **Galeria obrazów** jest repozytorium do zarządzania i udostępniania obrazów, ale ty kontrolujesz, kto ma dostęp. |
| **Definicja obrazu** | Obrazy są zdefiniowane w galerii i zawierają informacje o obrazie i wymaganiach dotyczących używania go wewnętrznie. Dotyczy to zarówno obrazu systemu Windows, jak i Linux, informacji o wersji oraz minimalnych i maksymalnych wymagań dotyczących pamięci. Jest to definicja typu obrazu. |
| **Wersja obrazu** | **Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii. Dla danego środowiska można mieć wiele wersji obrazu. Podobnie jak w przypadku obrazu zarządzanego, w przypadku tworzenia maszyny wirtualnej przy użyciu **wersji obrazu** wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać. |


## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten przykład w tym artykule, musisz mieć istniejący obraz zarządzany. Możesz skorzystać z [samouczka: Utwórz niestandardowy obraz maszyny wirtualnej platformy Azure z Azure PowerShell](tutorial-custom-images.md) , aby utworzyć je w razie potrzeby. Jeśli zarządzany obraz zawiera dysk z danymi, rozmiar dysku danych nie może być większy niż 1 TB.

Podczas pracy z tym artykułem należy zastąpić grupę zasobów i nazwy maszyn wirtualnych, jeśli są one odpowiednie.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms-from-an-image"></a>Tworzenie maszyn wirtualnych na podstawie obrazu

Po zakończeniu wersji obrazu można utworzyć co najmniej jedną nową maszynę wirtualną. 

> [!IMPORTANT]
> Nie można użyć portalu do wdrożenia maszyny wirtualnej na podstawie obrazu w innej dzierżawie platformy Azure. Aby utworzyć maszynę wirtualną na podstawie obrazu udostępnianego między dzierżawcami, musisz użyć [interfejsu wiersza polecenia platformy Azure](../linux/shared-images.md#create-a-vm) lub [programu PowerShell](shared-images.md#create-vms-from-an-image).

Ten przykład tworzy maszynę wirtualną o nazwie *myVMfromImage*, w liście *zasobów* w *regionie Wschodnie stany USA* .

1. Na stronie wersji obrazu wybierz pozycję **Utwórz maszynę wirtualną** z menu w górnej części strony.
1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową** , a następnie wpisz nazwę *zasobu* .
1. W polu **Nazwa maszyny wirtualnej**wpisz *myVM*.
1. W **obszarze region**wybierz pozycję *Wschodnie stany USA*.
1. W przypadku **opcji dostępności**pozostaw wartość domyślną *Brak wymaganej nadmiarowości infrastruktury*.
1. Wartość **obrazu** powinna być wypełniana automatycznie, jeśli rozpoczęto od strony wersji obrazu.
1. W polu **rozmiar**wybierz rozmiar maszyny wirtualnej z listy Dostępne rozmiary, a następnie kliknij pozycję "Wybierz".
1. W obszarze **Konto administratora** podaj nazwę użytkownika, taką jak *azureuser*, oraz hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. Jeśli chcesz zezwolić na dostęp zdalny do maszyny wirtualnej w obszarze **publiczne porty przychodzące**, wybierz opcję **Zezwalaj na wybrane porty** , a następnie wybierz pozycję **RDP (3389)** z listy rozwijanej. Jeśli nie chcesz zezwalać na dostęp zdalny do maszyny wirtualnej, pozostaw **nie** wybrane dla **publicznych portów przychodzących**.
1. Po zakończeniu wybierz przycisk **Recenzja + tworzenie** w dolnej części strony.
1. Po zakończeniu walidacji maszyny wirtualnej wybierz pozycję **Utwórz** w dolnej części strony, aby rozpocząć wdrażanie.



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

Jeśli chcesz usunąć poszczególne zasoby, musisz je usunąć w odwrotnej kolejności. Na przykład aby usunąć definicję obrazu, należy usunąć wszystkie wersje obrazu utworzone na podstawie tego obrazu.

## <a name="next-steps"></a>Następne kroki

Możesz również utworzyć zasób udostępnionej galerii obrazów przy użyciu szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny wirtualnej z wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów, zobacz [Omówienie](shared-image-galleries.md). Jeśli występują problemy, zobacz temat [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).

