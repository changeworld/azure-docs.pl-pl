---
title: Usługa Azure bastionu | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o usłudze Azure bastionu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 06/17/2019
ms.author: cherylmc
ms.openlocfilehash: d153c876366c8c747e06bf50a0ea26bcdcea10eb
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303591"
---
# <a name="what-is-azure-bastion-preview"></a>Co to jest Azure bastionu? (Wersja zapoznawcza)

Usługa Azure bastionu jest nowe w pełni zarządzana platforma usługa PaaS aprowizować w Twojej sieci wirtualnej. Zapewnia bezpieczny i bezproblemowy łączność RDP/SSH do maszyn wirtualnych bezpośrednio w witrynie Azure portal za pośrednictwem protokołu SSL. Gdy łączysz się za pomocą usługi Azure Bastion, maszyny wirtualne nie muszą mieć publicznego adresu IP.

 Bastionu zapewnia bezpieczną łączność RDP i SSH do wszystkich maszyn wirtualnych w sieci wirtualnej, w którym jest zainicjowany. Za pomocą usługi Azure bastionu chroni maszyny wirtualne z ujawniania portów protokołu RDP/SSH światem, jednocześnie zapewniając bezpieczny dostęp przy użyciu protokołu RDP/SSH. Dzięki bastionu platformy Azure możesz łączyć się z maszyną wirtualną bezpośrednio w witrynie Azure portal. Nie potrzebujesz dodatkowych klienta, agent lub fragment oprogramowania.

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="architecture"></a>Architektura

Bastionu Azure wdrożone w sieci wirtualnej, a po jej wdrożeniu, umożliwia bezpieczne środowisko RDP/SSH dla maszyn wirtualnych w sieci wirtualnej. Po zainicjowaniu usługi Azure bastionu w Twojej sieci wirtualnej, środowisko RDP/SSH jest dostępny dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Wdrożenie jest na sieć wirtualną, a nie na konto/subskrypcji lub maszyny wirtualnej.

RDP i SSH są niektóre z podstawowych oznacza, że za pomocą którego można połączyć do Twoich obciążeń działających na platformie Azure. Udostępnianie portów protokołu RDP/SSH za pośrednictwem Internetu nie jest konieczne i jest widoczny jako powierzchnia poważne zagrożenia. Jest to często spowodowane luki w zabezpieczeniach protokołów. Zawiera tę powierzchnię przed zagrożeniami, można wdrożyć hosty bastionu (znany także jako serwery przeskoku) z boku publicznych sieci obwodowej. Serwery hosta bastionu są zaprojektowane i skonfigurowane radzić sobie ze ataków. Serwery bastionu również zapewnić łączność RDP i SSH do obciążeń, znajdują się za zaporą bastionu, a także bardziej szczegółowo w sieci.

![architektura](./media/bastion-overview/architecture.png)

Poniższy rysunek przedstawia architekturę wdrożenia usługi Azure bastionu. Na poniższym diagramie:

* Host bastionu jest wdrażana w sieci wirtualnej.
* Użytkownik łączy się do witryny Azure portal przy użyciu dowolnej przeglądarki HTML5.
* Gdy użytkownik wybierze maszyny wirtualnej, aby nawiązać połączenie.
* Za pomocą jednego kliknięcia sesję RDP/SSH zostanie otwarta w przeglądarce.
* Nie publicznego adresu IP jest wymagany na maszynie Wirtualnej platformy Azure.

## <a name="key-features"></a>Najważniejsze funkcje

Następujące funkcje są dostępne do wypróbowania w publicznej wersji zapoznawczej:

* **RDP i SSH bezpośrednio w witrynie Azure portal:** Możesz bezpośrednio uzyskać sesji protokołu RDP i SSH bezpośrednio w witrynie Azure portal, za pomocą jednego kliknięcia bezproblemowe.
* **Sesja zdalna za pośrednictwem protokołu SSL i zapory przechodzenia do protokołu RDP/SSH:** Azure bastionu używa klienta sieci web HTML5, na podstawie, który jest automatycznie przesyłany strumieniowo na urządzeniu lokalnym, aby pobrać sesję RDP/SSH za pośrednictwem protokołu SSL na porcie 443, dzięki któremu można bezpiecznie przechodzić przez zapory firmowej.
* **Nie publicznego adresu IP na maszynie Wirtualnej platformy Azure wymagane:** Bastionu platformy Azure zostanie otwarte połączenia RDP/SSH z maszyną wirtualną platformy Azure przy użyciu prywatnego adresu IP na maszynie Wirtualnej. Nie potrzebujesz publicznego adresu IP na maszynie wirtualnej.
* **Bez problemów z zarządzaniem sieciowych grup zabezpieczeń:** Bastionu platformy Azure jest w pełni zarządzanej platformy PaaS w ramach usługi platformy Azure, która jest wewnętrznie wzmocnione w celu zapewnienia bezpiecznego połączenia RDP/SSH. Nie trzeba zastosować sieciowe grupy zabezpieczeń w podsieci platformy Azure bastionu. Ponieważ bastionu Azure nawiązuje połączenie z maszynami wirtualnymi za pośrednictwem prywatnego adresu IP, możesz skonfigurować usługi sieciowe grupy zabezpieczeń, aby zezwolić na użycie protokołu RDP/SSH z bastionu Azure tylko. Spowoduje to usunięcie wysiłek związany z zarządzaniem sieciowych grup zabezpieczeń z każdym razem, gdy potrzebujesz do bezpiecznego łączenia maszyn wirtualnych.
* **Ochrona przed skanowanie portów:** Ponieważ nie ma potrzeby udostępnianie maszyn wirtualnych do publicznej sieci Internet, maszyny wirtualne są chronione przed skanowania przez nieautoryzowanych i złośliwych użytkowników znajdujących się poza siecią wirtualną.
* **Zabezpiecz się przed lukami w zabezpieczeniach zero day. Zaostrzanie poziomu zabezpieczeń w jednym miejscu:** Bastionu platformy Azure to usługa PaaS w pełni zarządzana platforma. Ponieważ znajduje się w sieci obwodowej Twojej sieci wirtualnej, nie trzeba martwić się o wzmacniania ochrony wszystkich maszyn wirtualnych w sieci wirtualnej. Platforma Azure chroni przed luki w zabezpieczeniach zero day utrzymując bastionu Azure ze wzmocnionymi zabezpieczeniami stale aktualizowane za Ciebie.

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Utwórz zasób usługi host bastionu Azure](bastion-create-host-portal.md).
* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
