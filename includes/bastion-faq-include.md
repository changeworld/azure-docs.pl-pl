---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 02/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922e8a71a22db975685cb82bbd51a125c619ccf2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989500"
---
### <a name="regions"></a>Które regiony są dostępne?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Czy na mojej maszynie wirtualnej jest potrzebny publiczny adres IP?

Po nawiązaniu połączenia z maszyną wirtualną za pomocą usługi Azure bastionu nie potrzebujesz publicznego adresu IP na maszynie wirtualnej platformy Azure, z którą nawiązujesz połączenie. Usługa bastionu otworzy sesję/połączenie RDP/SSH z maszyną wirtualną za pośrednictwem prywatnego adresu IP maszyny wirtualnej w sieci wirtualnej.

### <a name="is-ipv6-supported"></a>Czy jest obsługiwany protokół IPv6?

W tej chwili protokół IPv6 nie jest obsługiwany. Usługa Azure bastionu obsługuje tylko protokół IPv4.

### <a name="rdpssh"></a>Czy potrzebuję klienta RDP lub SSH?

Nie jest potrzebny klient RDP lub SSH do uzyskiwania dostępu do maszyny wirtualnej platformy Azure w Azure Portal. Użyj [Azure Portal](https://portal.azure.com) , aby zezwolić na dostęp do maszyny wirtualnej za pośrednictwem protokołu RDP/SSH bezpośrednio w przeglądarce.

### <a name="agent"></a>Czy potrzebuję agenta uruchomionego na maszynie wirtualnej platformy Azure?

Nie musisz instalować agenta ani żadnego oprogramowania w przeglądarce ani na maszynie wirtualnej platformy Azure. Usługa bastionu jest bez agentów i nie wymaga dodatkowego oprogramowania dla protokołu RDP/SSH.

### <a name="browsers"></a>Które przeglądarki są obsługiwane?

Użyj przeglądarki Microsoft Edge lub Google Chrome w systemie Windows. W przypadku komputerów Mac firmy Apple należy użyć przeglądarki Google Chrome. Program Microsoft Edge chrom jest również obsługiwany odpowiednio w systemach Windows i Mac.

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

### <a name="filetransfer"></a>Czy transfer plików jest obsługiwany przez sesję RDP usługi Azure bastionu?

Pracujemy nad dodaniem nowych funkcji. Obecnie transfer plików nie jest obsługiwany, ale jest częścią naszego planu. Możesz bezpłatnie podzielić się swoją opinią na temat nowych funkcji na [stronie opinii o usłudze Azure bastionu](https://feedback.azure.com/forums/217313-networking?category_id=367303).
