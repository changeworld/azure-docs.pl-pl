---
title: Usługa Azure Virtual WAN — omówienie | Microsoft Docs
description: Dowiedz się więcej o automatycznej, skalowalnej łączności między oddziałami w usłudze Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/10/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 54cf6c356ec4bb51b123e48c52c5beebc990e59d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009974"
---
# <a name="what-is-azure-virtual-wan-preview"></a>Co to jest usługa Azure Virtual WAN? (Wersja zapoznawcza)

Azure Virtual WAN to usługa sieciowa zapewniająca zoptymalizowaną i zautomatyzowaną łączność między oddziałami za pośrednictwem platformy Azure. Usługa Virtual WAN umożliwia łączenie urządzeń w oddziałach z platformą Azure i konfigurowanie ich komunikacji. Można to zrobić ręcznie lub za pomocą urządzeń preferowanych dostawców — partnerów usługi Virtual WAN. Użycie urządzeń preferowanych dostawców zapewnia łatwość obsługi i upraszcza łączność oraz zarządzanie konfiguracją. Wbudowany pulpit nawigacyjny sieci WAN na platformie Azure udostępnia na bieżąco wskazówki dotyczące rozwiązywania problemów, dzięki którym oszczędzisz czas, i umożliwia łatwe monitorowanie łączności między lokacjami w dużej skali.

> [!IMPORTANT]
> Usługa Azure Virtual WAN jest obecnie dostępna w zarządzanej publicznej wersji zapoznawczej. Aby korzystać z usługi Virtual WAN, należy najpierw [zarejestrować się w wersji zapoznawczej](#enroll).
>
> Publiczna wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie należy korzystać z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten artykuł zawiera krótkie omówienie łączności sieciowej między obciążeniami na platformie Azure i poza nią. Usługa Virtual WAN oferuje następujące korzyści:

* **Zintegrowane rozwiązania w zakresie łączności w topologii piasty i szprych:** możliwość zautomatyzowania konfiguracji łączności między lokacjami lokalnymi a koncentratorem na platformie Azure za pomocą różnorodnych źródeł, w tym rozwiązań partnerów usługi Virtual WAN.
* **Automatyczna instalacja i konfiguracja szprych:** bezproblemowe łączenie sieci wirtualnych i obciążeń z koncentratorem na platformie Azure.
* **Intuicyjne rozwiązywanie problemów:** możesz monitorować cały przepływ na platformie Azure i podejmować wymagane działania na podstawie tych informacji.

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan.png)

## <a name="vendor"></a>Współpraca z partnerem usługi Virtual WAN

1. Kontroler urządzenia w oddziale (VPN/SDWAN) jest uwierzytelniany w celu wyeksportowania informacji z lokacji na platformę Azure za pomocą jednostki usługi platformy Azure.
2. Kontroler urządzenia w oddziale (VPN/SDWAN) uzyskuje konfigurację łączności z platformy Azure i aktualizuje urządzenie lokalne. To umożliwia zautomatyzowanie pobierania, edytowania i aktualizowania konfiguracji lokalnego urządzenia sieci VPN.
3. Gdy urządzenie jest prawidłowo skonfigurowane pod kątem platformy Azure, tworzone jest połączenie typu lokacja-lokacja (dwa aktywne tunele) z siecią WAN platformy Azure. Platforma Azure wymaga, aby kontroler w oddziale (VPN/SDWAN) obsługiwał protokół IKEv2. Protokół BGP jest opcjonalny.

## <a name="resources"></a>Zasoby usługi Virtual WAN

Aby skonfigurować kompleksową wirtualną sieć WAN, należy utworzyć następujące zasoby:

* **virtualWAN:** zasób virtualWAN reprezentuje wirtualną nakładkę sieci platformy Azure i stanowi zbiór kilku zasobów. Zawiera linki do wszystkich koncentratorów wirtualnych, które mają się znaleźć w wirtualnej sieci WAN. Zasoby usługi Virtual WAN są wzajemnie izolowane i nie mogą zawierać wspólnego koncentratora. Koncentratory wirtualne w usłudze Virtual WAN nie komunikują się ze sobą.

* **Lokacja:** zasób lokacji, noszący nazwę vpnsite, reprezentuje lokalne urządzenie sieci VPN i jego ustawienia. Dzięki współpracy z partnerem usługi Virtual WAN otrzymasz wbudowane rozwiązanie do automatycznego eksportowania tych informacji na platformę Azure.

* **Koncentrator:** koncentrator wirtualny to sieć wirtualna zarządzana przez firmę Microsoft. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Koncentrator to podstawowy element sieci w danym regionie. W jednym regionie świadczenia usługi Azure może znajdować się tylko jeden koncentrator. Utworzenie koncentratora w witrynie Azure Portal spowoduje automatyczne utworzenie sieci wirtualnej i bramy vpngateway dla koncentratora wirtualnego.

  Brama koncentratora różni się od bramy sieci wirtualnej używanej w usługach ExpressRoute i VPN Gateway. Na przykład w przypadku użycia usługi Virtual WAN nie tworzy się połączenia typu lokacja-lokacja z lokacji lokalnej bezpośrednio do sieci wirtualnej. Zamiast tego jest tworzone połączenie typu lokacja-lokacja z koncentratorem. Ruch zawsze jest kierowany przez bramę koncentratora. Oznacz to, że sieci wirtualne nie muszą mieć własnych bram sieci wirtualnej. Usługa Virtual WAN umożliwia łatwe skalowanie sieci wirtualnych za pośrednictwem koncentratora wirtualnego i bramy koncentratora wirtualnego. 

* **Połączenie sieci wirtualnej koncentratora:** zasób połączenia sieci wirtualnej koncentratora umożliwia bezproblemowe łączenie koncentratora z siecią wirtualną. Obecnie można tworzyć połączenia tylko z sieciami wirtualnymi znajdującymi się w tym samym regionie co koncentrator.

##<a name="enroll"></a>Rejestracja w wersji zapoznawczej

Aby móc skonfigurować usługę Virtual WAN, należy najpierw zarejestrować swoją subskrypcję w wersji zapoznawczej. Jeśli tego nie zrobisz, usługa Virtual WAN nie będzie dostępna w portalu. Aby przeprowadzić rejestrację, wyślij wiadomość e-mail na adres <azurevirtualwan@microsoft.com>, podając identyfikator subskrypcji. Gdy subskrypcja zostanie zarejestrowana, otrzymasz wiadomość e-mail.

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="feedback"></a>Opinie dotyczące wersji zapoznawczej

Będziemy wdzięczni za przesłanie opinii. Wyślij wiadomość e-mail na adres <azurevirtualwan@microsoft.com>, jeśli chcesz zgłosić problem lub wyrazić swoją opinię (pozytywną lub negatywną) dotyczącą usługi Virtual WAN. W wierszu tematu wpisz nazwę firmy w nawiasie kwadratowym („[ ]”). Jeśli zgłaszasz problem, dołącz również swój identyfikator subskrypcji.

## <a name="next-steps"></a>Następne kroki

Możesz utworzyć połączenie typu lokacja-lokacja w usłudze Virtual WAN za pośrednictwem [partnera usługi Virtual WAN](https://aka.ms/virtualwan) lub ręcznie. Aby utworzyć połączenie ręcznie, zobacz [Create a Site-to-Site connection using Virtual WAN (Tworzenie połączenia typu lokacja-lokacja w usłudze Virtual WAN)](virtual-wan-site-to-site-portal.md).