---
title: Udostępnić puli partii zadań Azure w sieci wirtualnej | Dokumentacja firmy Microsoft
description: Tak, aby węzły obliczeniowe można bezpiecznego komunikowania się z innych maszyn wirtualnych w sieci, takich jak serwer plików, można utworzyć puli partii w sieci wirtualnej.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/05/2018
ms.author: danlep
ms.openlocfilehash: 5a06ad5086a42bb00147e085227f3c71c357544e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29846812"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Tworzenie puli partii zadań Azure w sieci wirtualnej


Podczas tworzenia puli partii zadań Azure można udostępnić puli w podsieci [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) (VNet) określonym przez użytkownika. W tym artykule wyjaśniono, jak skonfigurować pulę partii w sieci wirtualnej. 



## <a name="why-use-a-vnet"></a>Dlaczego warto używać sieci wirtualnej?


Puli partii zadań Azure zawiera ustawienia umożliwiające programowi węzłów obliczeniowych do komunikowania się ze sobą — na przykład, aby uruchamiać wiele wystąpień zadania. Te ustawienia nie wymagają oddzielnego sieci wirtualnej. Jednak domyślnie węzły nie mogą komunikować się z maszynami wirtualnymi, które nie są częścią puli partii, takich jak serwer licencji lub na serwerze plików. Aby zezwolić na węzłach obliczeń puli do bezpiecznego komunikowania się z innych maszyn wirtualnych lub z sieci lokalnej, można udostępnić puli w podsieci sieci wirtualnej platformy Azure. 



## <a name="prerequisites"></a>Wymagania wstępne

* **Uwierzytelnianie**. Aby użyć sieci wirtualnej platformy Azure, interfejs API klienta usługi Batch musi korzystać z uwierzytelniania usługi Azure Active Directory (AD). Obsługa usługi Azure Batch dla usługi Azure AD jest udokumentowana w temacie [Authenticate Batch service solutions with Active Directory (Uwierzytelnianie rozwiązań usługi Batch za pomocą usługi Active Directory)](batch-aad-auth.md). 

* **Sieci wirtualnej platformy Azure**. Aby przygotować sieć wirtualną z jednej lub kilku podsieci z wyprzedzeniem, można użyć portalu Azure, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure (CLI) lub innych metod. Aby utworzyć sieć wirtualną platformy Azure Resource Manager, zobacz [utworzyć sieć wirtualną](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Tworzenie klasycznej sieci wirtualnej, zobacz [tworzenie sieci wirtualnej (wdrożenia klasyczne) z wieloma podsieciami](../virtual-network/create-virtual-network-classic.md).

### <a name="vnet-requirements"></a>Wymagania dotyczące sieci wirtualnej
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]
    
## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Tworzenie puli za pomocą sieci wirtualnej w portalu

Po utworzeniu sieci wirtualnej i przypisane do podsieci, można utworzyć puli partii z tej sieci wirtualnej. Wykonaj następujące kroki, aby utworzyć pulę z portalu Azure: 



1. W witrynie Azure Portal przejdź do swojego konta usługi Batch. To konto musi być w tej samej subskrypcji i regionu jako grupę zasobów, zawierającą sieci wirtualnej mają być używane. 
2. W **ustawienia** oknie po lewej stronie, wybierz opcję **pule** elementu menu.
3. W **pule** wybierz **Dodaj** polecenia.
4. Na **Dodaj pulę,** okna, wybierz opcję mają być używane z **typ obrazu** listy rozwijanej. 
5. Wybierz poprawny **wydawcy lub oferta/jednostki Sku** dla niestandardowego obrazu.
6. Określ pozostałe wymagane ustawienia, w tym **rozmiaru węzła**, **Target dedykowanych węzłów**, i **niski priorytet węzłów**, a także wszelkie potrzebne ustawienia opcjonalne.
7. W **sieci wirtualnej**, wybierz sieć wirtualną i podsieci, które chcesz użyć.
  
  ![Dodaj pulę z sieci wirtualnej](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Zdefiniowane przez użytkownika trasy tunelowania wymuszonego

Może być konieczne wymagania w organizacji przekierowania (wymuszanie) ruch do Internetu z podsieci do Twojej lokalizacji lokalnej inspekcji i rejestrowania. Może włączono wymuszanie tunelowania podsieci w sieci wirtualnej. 

Aby sprawdzić, czy węzły obliczeń w puli partii zadań Azure działają w sieci wirtualnej, która wymusił tunelowania włączone, należy dodać następujące [trasy zdefiniowane przez użytkownika](../virtual-network/virtual-networks-udr-overview.md) dla tej podsieci:

* Usługa partia zadań musi łączyć się z węzłami obliczeniowymi puli do planowania zadań. Aby włączyć tę komunikację, Dodaj trasy zdefiniowane przez użytkownika dla każdego adresu IP używane przez usługi partia zadań w regionie, w którym istnieje konta partii zadań. Aby uzyskać listę adresów IP usługi partia zadań, skontaktuj się z pomocą techniczną platformy Azure.

* Upewnij się, że ruch wychodzący do magazynu Azure (w szczególności adresów URL w postaci `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, i `<account>.blob.core.windows.net`) nie jest zablokowany przez urządzenia sieci lokalnej.

Po dodaniu trasy zdefiniowane przez użytkownika, należy zdefiniować trasy dla każdego pokrewne prefiksu adresu IP partii oraz **następnego przeskoku typu** do **Internet**. Zobacz poniższy przykład:

![Trasa zdefiniowana przez użytkownika](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe informacje o partii, zobacz [Programowanie równoległe na dużą skalę obliczeniowe rozwiązań w partii](batch-api-basics.md).
- Aby uzyskać więcej informacji o tworzeniu trasy zdefiniowane przez użytkownika, zobacz [utworzenie trasy zdefiniowanej przez użytkownika - portalu Azure](../virtual-network/tutorial-create-route-table-portal.md).
