---
title: Aprowizuj pulę w sieci wirtualnej — usługa Azure Batch | Dokumenty firmy Microsoft
description: Jak utworzyć pulę usługi Batch w sieci wirtualnej platformy Azure, aby węzły obliczeniowe mogły bezpiecznie komunikować się z innymi maszynami wirtualnymi w sieci, takimi jak serwer plików.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 04/10/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: bbe38a9dc7be749b8e138ff3ca9ec4f06255b389
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247750"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Tworzenie puli wsadowej platformy Azure w sieci wirtualnej

Podczas tworzenia puli usługi Azure Batch, można aprowizować puli w podsieci [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) (VNet), które określisz. W tym artykule wyjaśniono, jak skonfigurować pulę usługi Batch w sieci wirtualnej. 

## <a name="why-use-a-vnet"></a>Dlaczego warto korzystać z sieci wirtualnej?

Pula usługi Azure Batch ma ustawienia umożliwiające węzłom obliczeniowym komunikowanie się ze sobą — na przykład w celu uruchamiania zadań z wieloma wystąpieniami. Te ustawienia nie wymagają oddzielnej sieci wirtualnej. Jednak domyślnie węzły nie mogą komunikować się z maszynami wirtualnymi, które nie są częścią puli batch, takimi jak serwer licencji lub serwer plików. Aby umożliwić węzłom obliczeniowym puli bezpieczną komunikację z innymi maszynami wirtualnymi lub z siecią lokalną, można aprowizować pulę w podsieci sieci wirtualnej platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

* **Uwierzytelnianie**. Aby użyć sieci wirtualnej platformy Azure, interfejs API klienta usługi Batch musi korzystać z uwierzytelniania usługi Azure Active Directory (AD). Obsługa usługi Azure Batch dla usługi Azure AD jest udokumentowana w temacie [Authenticate Batch service solutions with Active Directory (Uwierzytelnianie rozwiązań usługi Batch za pomocą usługi Active Directory)](batch-aad-auth.md). 

* **Sieci wirtualnej platformy Azure**. Zobacz poniższą sekcję wymagania sieci wirtualnej i konfiguracji. Aby przygotować witrynę wirtualną z co najmniej jedną podsiecią z wyprzedzeniem, można użyć witryny Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure (CLI) lub innych metod.  
  * Aby utworzyć sieć wirtualną opartą na usłudze Azure Resource Manager, zobacz [Tworzenie sieci wirtualnej](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Sieć wirtualna oparta na Menedżerze zasobów jest zalecana dla nowych wdrożeń i jest obsługiwana tylko w pulach w konfiguracji maszyny wirtualnej.
  * Aby utworzyć klasyczną sieć [wirtualną, zobacz Tworzenie sieci wirtualnej (klasycznej) z wieloma podsieciami](../virtual-network/create-virtual-network-classic.md). Klasyczna sieci wirtualnej jest obsługiwana tylko w pulach w konfiguracji usług w chmurze.

## <a name="vnet-requirements"></a>Wymagania dotyczące sieci wirtualnej

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Tworzenie puli z siecią wirtualną w portalu

Po utworzeniu sieci wirtualnej i przypisaniu do niej podsieci można utworzyć pulę usługi Batch z tą siecią wirtualną. Wykonaj następujące kroki, aby utworzyć pulę z witryny Azure portal: 

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch. To konto musi znajdować się w tej samej subskrypcji i regionie co grupa zasobów zawierająca sieci wirtualnej, której zamierzasz użyć. 
2. W oknie **Ustawienia** po lewej stronie wybierz pozycję menu **Baseny.**
3. W oknie **Pule** wybierz polecenie **Dodaj.**
4. W oknie **Dodawanie puli** wybierz opcję, której chcesz użyć z listy rozwijanej **Typ** obrazu. 
5. Wybierz odpowiedni **wydawca/ofertę/sku** dla niestandardowego obrazu.
6. Określ pozostałe wymagane ustawienia, w tym **rozmiar węzła,** **dedykowane węzły docelowe**i **węzły o niskim priorytecie,** a także wszelkie żądane ustawienia opcjonalne.
7. W **obszarze Sieć wirtualna**wybierz sieć wirtualną i podsieć, której chcesz użyć.
  
   ![Dodawanie puli z siecią wirtualną](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Trasy zdefiniowane przez użytkownika dla tunelowania wymuszonego

W organizacji mogą być wymagane przekierowanie (wymuszenia) ruchu związanego z Internetem z podsieci z powrotem do lokalizacji lokalnej w celu inspekcji i rejestrowania. Być może włączono wymuszone tunelowanie podsieci w sieci wirtualnej. 

Aby upewnić się, że węzły obliczeniowe puli usługi Azure Batch działają w sieci wirtualnej, która ma włączone tunelowanie wymuszone, należy dodać następujące [trasy zdefiniowane](../virtual-network/virtual-networks-udr-overview.md) przez użytkownika dla tej podsieci:

* Usługa Batch musi komunikować się z węzłami obliczeniowymi puli do planowania zadań. Aby włączyć tę komunikację, należy dodać trasę zdefiniowaną przez użytkownika dla każdego adresu IP używanego przez usługę Batch w regionie, w którym istnieje konto usługi Batch. Aby dowiedzieć się, jak uzyskać listę adresów IP usługi Batch, zobacz [Tagi usługi w środowisku lokalnym](../virtual-network/service-tags-overview.md)

* Upewnij się, że ruch wychodzący do usługi Azure `<account>.table.core.windows.net` `<account>.queue.core.windows.net`Storage `<account>.blob.core.windows.net`(w szczególności adresy URL formularza i ) nie jest blokowany za pośrednictwem lokalnego urządzenia sieciowego.

Podczas dodawania trasy zdefiniowanej przez użytkownika należy zdefiniować marszrutę dla każdego powiązanego prefiksu adresu IP partii i ustawić **następny typ przeskoku** na **Internet**. Zobacz poniższy przykład:

![Trasa zdefiniowana przez użytkownika](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowy przegląd usługi Batch, zobacz [Tworzenie dużych równoległych rozwiązań obliczeniowych za pomocą usługi Batch](batch-api-basics.md).
- Aby uzyskać więcej informacji na temat tworzenia trasy zdefiniowanej przez użytkownika, zobacz [Tworzenie trasy zdefiniowanej przez użytkownika — Azure portal](../virtual-network/tutorial-create-route-table-portal.md).
