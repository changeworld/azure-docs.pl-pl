---
title: Aprowizacja puli w sieci wirtualnej — Azure Batch | Dokumentacja firmy Microsoft
description: Jak utworzyć pulę usługi Batch w sieci wirtualnej platformy Azure, dzięki czemu węzły obliczeniowe mogą bezpiecznego komunikowania się z innymi maszynami wirtualnymi w sieci, takich jak serwer plików.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 2583e7e218e765e0d7745978582e19a5a4fe17ce
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550205"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Tworzenie puli usługi Azure Batch w sieci wirtualnej

Podczas tworzenia puli usługi Azure Batch możesz aprowizować puli w podsieci [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) (VNet), który określisz. W tym artykule opisano sposób konfigurowania puli usługi Batch w sieci wirtualnej. 

## <a name="why-use-a-vnet"></a>Dlaczego warto używać sieci wirtualnej?

Puli usługi Azure Batch ma ustawienia, aby umożliwić węzłów obliczeniowych do komunikowania się ze sobą — na przykład, aby uruchamiać zadania obejmujące wiele wystąpień. Te ustawienia nie wymagają oddzielną sieć wirtualną. Jednak domyślnie węzłów nie może komunikować się z maszynami wirtualnymi, które nie są częścią puli usługi Batch, np. serwera licencji lub na serwerze plików. Aby zezwolić na pulę węzłów obliczeniowych do bezpiecznego komunikowania się z maszynami wirtualnymi lub między siecią lokalną, można udostępnić puli w podsieci sieci wirtualnej platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

* **Uwierzytelnianie**. Aby użyć sieci wirtualnej platformy Azure, interfejs API klienta usługi Batch musi korzystać z uwierzytelniania usługi Azure Active Directory (AD). Obsługa usługi Azure Batch dla usługi Azure AD jest udokumentowana w temacie [Authenticate Batch service solutions with Active Directory (Uwierzytelnianie rozwiązań usługi Batch za pomocą usługi Active Directory)](batch-aad-auth.md). 

* **Sieci wirtualnej platformy Azure**. Zobacz w poniższej sekcji wymagania dotyczące sieci wirtualnej i konfigurację. Aby przygotować sieć wirtualną przy użyciu co najmniej jednej podsieci z wyprzedzeniem, można użyć witryny Azure portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure (CLI) lub innych metod.  
  * Aby utworzyć sieć wirtualną na podstawie usługi Azure Resource Manager, zobacz [tworzenie sieci wirtualnej](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Sieć wirtualna opartych na usłudze Resource Manager jest zalecane w przypadku nowych wdrożeń i jest obsługiwana tylko w pulach w konfiguracji maszyny wirtualnej.
  * Aby utworzyć klasyczną sieć wirtualną, zobacz [tworzenie sieci wirtualnej (klasycznej) z wieloma podsieciami](../virtual-network/create-virtual-network-classic.md). Klasyczna sieć wirtualna jest obsługiwana tylko w pulach w konfiguracji usługi w chmurze.

## <a name="vnet-requirements"></a>Wymagania dotyczące sieci wirtualnej

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Utwórz pulę z siecią wirtualną w portalu

Po utworzeniu sieci wirtualnej i do niej przypisany podsieci, można utworzyć pulę usługi Batch za pomocą tej sieci wirtualnej. Wykonaj następujące kroki, aby utworzyć pulę w witrynie Azure portal: 

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch. To konto musi być w tej samej subskrypcji i regionie co grupa zasobów zawierająca sieć wirtualną, których zamierzasz używać. 
2. W **ustawienia** oknie po lewej stronie, wybierz opcję **pule** elementu menu.
3. W **pule** wybierz **Dodaj** polecenia.
4. Na **Dodaj pulę** okna, wybierz opcję, których zamierzasz używać z **typ obrazu** listy rozwijanej. 
5. Wybierz poprawnego **wydawcę/ofertę/jednostkę Sku** dla obrazu niestandardowego.
6. Określ pozostałe wymagane ustawienia, w tym **rozmiar węzła**, **docelowe węzły dedykowane**, i **niskim priorytecie węzłów**, jak również dowolne żądane ustawienia opcjonalne.
7. W **sieci wirtualnej**, wybierz sieć wirtualną i podsieć, o których chcesz użyć.
  
   ![Dodawanie puli przy użyciu sieci wirtualnej](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Trasy zdefiniowane przez użytkownika dla wymuszonego tunelowania

Trzeba wymagania w organizacji przekierowania (wymuszone) ruchu skierowanego do Internetu z podsieci do Twojej lokalizacji lokalnej inspekcji i rejestrowania. Może być włączono wymuszone tunelowanie dla podsieci w sieci wirtualnej. 

Aby upewnić się, czy węzły obliczeniowe puli Azure Batch działają w sieci wirtualnej, która ma wymuszonego tunelowania, włączone, należy dodać następujące [trasy zdefiniowane przez użytkownika](../virtual-network/virtual-networks-udr-overview.md) dla tej podsieci:

* Usługa Batch musi komunikować się z puli węzłów obliczeniowych do planowania zadań. Aby włączyć tę komunikację, należy dodać zdefiniowana przez użytkownika trasa dla każdego adresu IP używanego przez usługę Batch w regionie, w której istnieje konta usługi Batch. Aby uzyskać listę adresów IP usługi Batch, skontaktuj się z pomocą techniczną platformy Azure.

* Upewnij się, że ruch wychodzący do usługi Azure Storage (w szczególności adresy URL formularzy `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, i `<account>.blob.core.windows.net`) nie jest zablokowany za pomocą swojego urządzenia w sieci lokalnej.

Po dodaniu trasy zdefiniowane przez użytkownika, należy zdefiniować trasę dla każdego powiązane prefiksu adresu IP usługi Batch oraz **typu następnego przeskoku** do **Internet**. Zobacz poniższy przykład:

![Trasa zdefiniowana przez użytkownika](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe omówienie usługi Batch, zobacz [tworzenie rozbudowanych rozwiązań przetwarzania równoległego przy użyciu usługi Batch](batch-api-basics.md).
- Aby uzyskać więcej informacji o tworzeniu trasy zdefiniowane przez użytkownika, zobacz [tworzona jest trasa zdefiniowana przez użytkownika — witryna Azure portal](../virtual-network/tutorial-create-route-table-portal.md).
