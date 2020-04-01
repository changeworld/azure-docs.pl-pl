---
title: Bastion platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o usłudze Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 01/31/2020
ms.author: cherylmc
ms.openlocfilehash: 299a69675eed1ba958c6d13cf447407450df2abb
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411129"
---
# <a name="what-is-azure-bastion"></a>Co to jest usługa Azure Bastion?

Usługa Azure Bastion to nowa w pełni zarządzana przez platformę usługa PaaS, którą aprowizujesz w sieci wirtualnej. Zapewnia bezpieczną i bezproblemową łączność RDP/SSH z maszynami wirtualnymi bezpośrednio w witrynie Azure portal za pomocą protokołu TLS. Gdy łączysz się za pomocą usługi Azure Bastion, maszyny wirtualne nie muszą mieć publicznego adresu IP.

Bastion zapewnia bezpieczną łączność RDP i SSH do wszystkich maszyn wirtualnych w sieci wirtualnej, w której jest aprowizowana. Korzystanie z usługi Azure Bastion chroni maszyny wirtualne przed udostępnianiem portów RDP/SSH na zewnątrz, zapewniając jednocześnie bezpieczny dostęp przy użyciu protokołu RDP/SSH. Za pomocą usługi Azure Bastion możesz połączyć się z maszyną wirtualną bezpośrednio z witryny Azure portal. Nie potrzebujesz dodatkowego klienta, agenta ani oprogramowania.

## <a name="architecture"></a>Architektura

Wdrożenie usługi Azure Bastion jest na sieć wirtualną, a nie na subskrypcję/konto lub maszynę wirtualną. Po zainicjowaniu obsługi administracyjnej usługi Bastion platformy Azure w sieci wirtualnej środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej.

RDP i SSH to tylko niektóre z podstawowych środków, za pomocą których można połączyć się z obciążeniami uruchomionymi na platformie Azure. Udostępnianie portów RDP/SSH przez Internet nie jest pożądane i jest postrzegane jako znacząca powierzchnia zagrożenia. Jest to często spowodowane lukami w zabezpieczeniach protokołu. Aby ograniczyć tę powierzchnię zagrożenia, można wdrożyć hosty bastionu (znane również jako serwery szybkiego dostępu) po stronie publicznej sieci obwodowej. Serwery hostów bastionu są zaprojektowane i skonfigurowane tak, aby wytrzymywały ataki. Serwery Bastion zapewniają również łączność RDP i SSH z obciążeniami znajdującymi się za bastionem, a także dalej wewnątrz sieci.

![architektura](./media/bastion-overview/architecture.png)

Na rysunku przedstawiono architekturę wdrożenia usługi Azure Bastion. Na tym diagramie:

* Host bastionu jest wdrażany w sieci wirtualnej.
* Użytkownik łączy się z portalem Azure przy użyciu dowolnej przeglądarki HTML5.
* Użytkownik wybiera maszynę wirtualną, z aby się połączyć.
* Za pomocą jednego kliknięcia sesja RDP/SSH zostanie otwarta w przeglądarce.
* Nie jest wymagany publiczny adres IP na maszynie Wirtualnej platformy Azure.

## <a name="key-features"></a>Najważniejsze funkcje

Dostępne są następujące funkcje:

* **RDP i SSH bezpośrednio w witrynie Azure portal:** Możesz bezpośrednio uzyskać dostęp do sesji RDP i SSH bezpośrednio w witrynie Azure portal przy użyciu bezproblemowego środowiska jednym kliknięciem.
* **Sesja zdalna przez TLS i przechodzenie przez zaporę dla protokołu RDP/SSH:** Usługa Azure Bastion używa klienta sieci web opartego na języku HTML5, który jest automatycznie przesyłany strumieniowo do urządzenia lokalnego, dzięki czemu sesja RDP/SSH jest dostępna za pośrednictwem protokołu TLS na porcie 443, co umożliwia bezpieczne przechodzenie przez zapory firmowe.
* **Nie jest wymagany publiczny adres IP na maszynie Wirtualnej platformy Azure:** Usługa Azure Bastion otwiera połączenie RDP/SSH z maszyną wirtualną platformy Azure przy użyciu prywatnego adresu IP na maszynie wirtualnej. Nie potrzebujesz publicznego adresu IP na maszynie wirtualnej.
* **Nie ma kłopotów z zarządzaniem nsgs:** Azure Bastion to w pełni zarządzana usługa PaaS platformy z platformy Azure, która jest wewnętrznie wzmocniona, aby zapewnić bezpieczną łączność RDP/SSH. Nie trzeba stosować żadnych sieciowychg w podsieci Bastion platformy Azure. Ponieważ usługa Azure Bastion łączy się z maszynami wirtualnymi za pomocą prywatnego adresu IP, można skonfigurować sieciowe sieciowe, aby zezwalały na protokół RDP/SSH tylko z usługi Azure Bastion. Eliminuje to kłopotów z zarządzaniem sieciami sieciowymi za każdym razem, gdy trzeba bezpiecznie połączyć się z maszynami wirtualnymi.
* **Ochrona przed skanowaniem portów:** Ponieważ nie trzeba udostępniać maszyn wirtualnych na publiczny Internet, maszyny wirtualne są chronione przed skanowaniem portów przez nieuczciwych i złośliwych użytkowników znajdujących się poza siecią wirtualną.
* **Ochrona przed exploitami zero-day. Wzmocnienie tylko w jednym miejscu:** Usługa Azure Bastion to w pełni zarządzana przez platformę usługa PaaS. Ponieważ znajduje się na obwodzie sieci wirtualnej, nie musisz się martwić o wzmocnienie każdej maszyny wirtualnej w sieci wirtualnej. Platforma Azure chroni przed exploitami zero-day, utrzymując platformę Azure Bastion wzmocnioną i zawsze aktualną.

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Następne kroki

* [Utwórz zasób hosta Bastion platformy Azure](bastion-create-host-portal.md).
* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
