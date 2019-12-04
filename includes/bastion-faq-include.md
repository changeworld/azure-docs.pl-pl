---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ada64d6bbce4fe7401bbfc078ba0903a28f06971
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795992"
---
### <a name="regions"></a>Które regiony są dostępne?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Czy na mojej maszynie wirtualnej jest potrzebny publiczny adres IP?

NIE potrzebujesz publicznego adresu IP na maszynie wirtualnej platformy Azure, z którą nawiązujesz połączenie z usługą Azure bastionu. Usługa bastionu otworzy sesję/połączenie RDP/SSH z maszyną wirtualną za pośrednictwem prywatnego adresu IP maszyny wirtualnej w sieci wirtualnej.

### <a name="rdpssh"></a>Czy potrzebuję klienta RDP lub SSH?

Nie musisz mieć klienta RDP lub SSH, aby uzyskiwać dostęp RDP/SSH do maszyny wirtualnej platformy Azure w witrynie Azure Portal. Użyj [Azure Portal](https://portal.azure.com) , aby zezwolić na dostęp do maszyny wirtualnej za pośrednictwem protokołu RDP/SSH bezpośrednio w przeglądarce.

### <a name="agent"></a>Czy potrzebuję agenta uruchomionego na maszynie wirtualnej platformy Azure?

Nie musisz instalować agenta ani innego oprogramowania w przeglądarce ani na maszynie wirtualnej platformy Azure. Usługa Bastion nie wymaga agenta ani dodatkowego oprogramowania na potrzeby połączeń RDP/SSH.

### <a name="browsers"></a>Które przeglądarki są obsługiwane?

Użyj przeglądarki Microsoft Edge lub Google Chrome w systemie Windows. W komputerze Apple Mac korzystaj z przeglądarki Google Chrome. Przeglądarka Microsoft Edge Chromium jest też obsługiwana odpowiednio w systemach Windows i komputerach Mac.

### <a name="roles"></a>Czy wszystkie role są wymagane do uzyskania dostępu do maszyny wirtualnej?

Aby można było nawiązać połączenie, wymagane są następujące role:

* Rola czytelnika na maszynie wirtualnej
* Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytelnika w zasobie Azure bastionu

### <a name="pricingpage"></a>Jakie są ceny?

Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://aka.ms/BastionHostPricing).

### <a name="session"></a>Dlaczego otrzymuję komunikat o błędzie "sesja wygasła" przed rozpoczęciem sesji bastionu?

Sesja powinna zostać zainicjowana tylko z Azure Portal. Zaloguj się do Azure Portal i ponownie Rozpocznij sesję. Jeśli przejdziesz do adresu URL bezpośrednio z innej sesji lub karty przeglądarki, ten błąd jest oczekiwany. Gwarantuje to, że sesja jest bezpieczniejsza i dostęp do sesji można uzyskać tylko za pomocą Azure Portal.

### <a name="keyboard"></a>Jakie układy klawiatury są obsługiwane podczas sesji zdalnej bastionu?

Usługa Azure bastionu obecnie obsługuje układ klawiatury en-us-QWERTY wewnątrz maszyny wirtualnej.  Obsługa innych ustawień regionalnych układu klawiatury jest w toku.

### <a name="udr"></a>Czy zdefiniowane przez użytkownika Routing (UDR) jest obsługiwany w podsieci usługi Azure bastionu?

Nie. UDR nie jest obsługiwana w podsieci bastionu platformy Azure.
W przypadku scenariuszy obejmujących zarówno usługę Azure bastionu, jak i zaporę sieci wirtualnej (urządzenie WUS) w tej samej sieci wirtualnej, nie trzeba wymuszać ruchu z podsieci bastionu platformy Azure do zapory platformy Azure, ponieważ komunikacja między usługą Azure bastionu i maszynami wirtualnymi jest prywatna. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do maszyn wirtualnych za zaporą platformy Azure za pomocą bastionu](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).
