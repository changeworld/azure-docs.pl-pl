---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 03/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57a764b62fcda333f042794e176c24c8e6cc5526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80374016"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Które regiony są dostępne?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>Czy na mojej maszynie wirtualnej potrzebny jest publiczny adres IP?

Po nawiązaniu połączenia z maszyną wirtualną przy użyciu usługi Azure Bastion nie potrzebujesz publicznego adresu IP na maszynie wirtualnej platformy Azure, z którą się łączysz. Usługa Bastion otworzy sesję/połączenie RDP/SSH z maszyną wirtualną za pośrednictwem prywatnego adresu IP maszyny wirtualnej w sieci wirtualnej.

### <a name="is-ipv6-supported"></a>Czy obsługa IPv6 jest obsługiwana?

W tej chwili IPv6 nie jest obsługiwany. Usługa Azure Bastion obsługuje tylko protokół IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Czy potrzebuję klienta RDP lub SSH?

Nie potrzebujesz klienta RDP lub SSH, aby uzyskać dostęp do protokołu RDP/SSH do maszyny wirtualnej platformy Azure w witrynie Azure portal. Użyj [witryny Azure portal,](https://portal.azure.com) aby uzyskać dostęp RDP/SSH do maszyny wirtualnej bezpośrednio w przeglądarce.

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Czy usługa Azure Bastion wymaga certyfikatu CAL usług pulpitu zdalnego do celów administracyjnych na maszynach wirtualnych hostowanych na platformie Azure?
Nie, dostęp do maszyn wirtualnych z systemem Windows Server przez usługę Azure Bastion nie wymaga [licencji CAL usług pulpitu](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) zdalnego, gdy jest używany wyłącznie do celów administracyjnych.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Ile równoczesnych sesji RDP i SSH obsługuje każdą usługę Azure Bastion?
Zarówno RDP, jak i SSH są protokołem opartym na użyciu. Wysokie użycie sesji spowoduje, że host bastionu będzie obsługiwał mniejszą całkowitą liczbę sesji. Poniższe liczby zakładają normalne codzienne przepływy pracy.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Czy potrzebuję agenta działającego na maszynie wirtualnej platformy Azure?

Nie musisz instalować agenta ani żadnego oprogramowania w przeglądarce lub na maszynie wirtualnej platformy Azure. Usługa Bastion jest bezagentowa i nie wymaga dodatkowego oprogramowania dla protokołu RDP/SSH.

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Które przeglądarki są obsługiwane?

Użyj przeglądarki Microsoft Edge lub Google Chrome w systemie Windows. W przypadku komputerów Apple Mac użyj przeglądarki Google Chrome. Microsoft Edge Chromium jest również obsługiwany zarówno w systemie Windows, jak i mac, odpowiednio.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Czy wszystkie role są wymagane do uzyskania dostępu do maszyny wirtualnej?

Aby nawiązać połączenie, wymagane są następujące role:

* Rola czytnika na maszynie wirtualnej
* Rola czytnika karty sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytnika w zasobie Bastion platformy Azure

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Co to jest cennik?

Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://aka.ms/BastionHostPricing).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Dlaczego przed rozpoczęciem sesji bastionu pojawia się komunikat o błędzie "Twoja sesja wygasła"?

Sesja powinna być inicjowana tylko z witryny Azure portal. Zaloguj się do witryny Azure portal i ponownie rozpocznij sesję. Jeśli przejdziesz do adresu URL bezpośrednio z innej sesji przeglądarki lub karty, ten błąd jest oczekiwany. Pomaga upewnić się, że sesja jest bardziej bezpieczne i że sesja jest dostępna tylko za pośrednictwem witryny Azure portal.

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Jakie układy klawiatury są obsługiwane podczas sesji zdalnej Bastion?

Usługa Azure Bastion obsługuje obecnie układ klawiatury en-us-qwerty wewnątrz maszyny Wirtualnej.  Obsługa innych ustawień regionalnych dla układu klawiatury jest w toku.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Czy routing zdefiniowany przez użytkownika (UDR) jest obsługiwany w podsieci Bastion platformy Azure?

Nie. UDR nie jest obsługiwany w podsieci Bastion platformy Azure.
W scenariuszach, które obejmują zarówno Azure Bastion i Azure Firewall/Network Virtual Appliance (NVA) w tej samej sieci wirtualnej, nie trzeba wymuszać ruchu z podsieci Bastion platformy Azure do Zapory platformy Azure, ponieważ komunikacja między Bastion platformy Azure i maszyn wirtualnych jest prywatna. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do maszyn wirtualnych za zaporą platformy Azure za pomocą bastionu](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="is-file-transfer-supported-with-azure-bastion-rdp-session"></a><a name="filetransfer"></a>Czy transfer plików jest obsługiwany w sesji RDP bastionu platformy Azure?

Ciężko pracujemy nad dodaniem nowych funkcji. Obecnie przesyłanie plików nie jest obsługiwane, ale jest częścią naszego planu działania. Zachęcamy do dzielenia się opiniami na temat nowych funkcji na [stronie Opinie bastionu platformy Azure.](https://feedback.azure.com/forums/217313-networking?category_id=367303)

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Jak radzić sobie z błędami wdrażania?

Przejrzyj wszystkie komunikaty o błędach i w razie potrzeby [zgłosić żądanie pomocy technicznej w witrynie Azure Portal.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Błędy wdrażania mogą wynikać z [limitów subskrypcji platformy Azure, przydziałów i ograniczeń.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) W szczególności klienci mogą napotkać limit liczby publicznych adresów IP dozwolonych na subskrypcję, co powoduje niepowodzenie wdrożenia usługi Azure Bastion.
