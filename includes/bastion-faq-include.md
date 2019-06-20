---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e29a9265e010c3f442b742faf62b16dae02739fa
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191182"
---
### <a name="preview"></a>Jak uczestniczyć w publicznej wersji zapoznawczej

Należy dodać do udziału w publicznej wersji zapoznawczej. Użyj kroków w [w tym artykule](../articles/bastion/bastion-create-host-portal.md) do utworzenia nowego zasobu platformy Azure bastionu. Obecnie, gdy dostęp do korzystania z tej usługi, należy użyć [portal Azure — wersja zapoznawcza](https://aka.ms/BastionHost) zamiast regularnego witryny Azure portal.

### <a name="regions"></a>Które regiony są dostępne w okresie obowiązywania wersji zapoznawczej?

Możesz wdrożyć i używać bastionu zasobu w tych regionach za pomocą w wersji zapoznawczej [portal Azure — Podgląd łącza](https://aka.ms/BastionHost).

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="portal"></a>Nie można znaleźć zasobu bastionu w witrynie Azure portal. Co mam zrobić?

Upewnij się, że używasz [portal Azure — Podgląd łącza](https://aka.ms/BastionHost), nie regularne witryny Azure portal.

### <a name="publicip"></a>Czy muszę mieć publiczny adres IP na mojej maszynie wirtualnej?

Publiczny adres IP na maszynie wirtualnej platformy Azure, która łączysz się z usługą Azure bastionu nie jest konieczne. Usługa bastionu spowoduje otwarcie RDP/SSH/połączenie sesji z maszyną wirtualną za pośrednictwem prywatny adres IP swojej maszyny wirtualnej w ramach sieci wirtualnej.

### <a name="rdpssh"></a>Czy muszę mieć klienta protokołu RDP lub SSH?

Nie musisz mieć klienta RDP lub SSH, aby uzyskiwać dostęp RDP/SSH do maszyny wirtualnej platformy Azure w witrynie Azure Portal. Użyj [portal Azure — Podgląd łącza](https://aka.ms/BastionHost) dostępu lotu w wersji zapoznawczej portalu do. Dzięki temu uzyskasz dostęp RDP/SSH do maszyny wirtualnej bezpośrednio w przeglądarce.

### <a name="agent"></a>Czy potrzebuję agenta uruchomionego na maszynie wirtualnej platformy Azure?

Nie musisz instalować agenta ani innego oprogramowania w przeglądarce ani na maszynie wirtualnej platformy Azure. Usługa Bastion nie wymaga agenta ani dodatkowego oprogramowania na potrzeby połączeń RDP/SSH.

### <a name="browsers"></a>Jakie przeglądarki są obsługiwane?

W publicznej wersji zapoznawczej należy użyć przeglądarki Microsoft Edge lub Google Chrome w Windows. W komputerze Apple Mac korzystaj z przeglądarki Google Chrome. Przeglądarka Microsoft Edge Chromium jest też obsługiwana odpowiednio w systemach Windows i komputerach Mac.

### <a name="roles"></a>Czy wszystkie role wymagane do dostępu do maszyny wirtualnej?

Aby można było utworzyć połączenie, wymagane są następujące role:

* Rola czytelnika na maszynie wirtualnej
* Rola czytelnika na karcie Sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytelnika na zasobie bastionu platformy Azure

### <a name="previewbill"></a>Cennik — będą rozliczane za udział w wersji zapoznawczej?

Możesz będą naliczane tylko częściowo w publicznej wersji zapoznawczej. Jednak nie ma umowy SLA załączone wraz z wdrożenia. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://aka.ms/BastionHostPricing).