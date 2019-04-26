---
title: Płaszczyzna kontroli usługi Office 365 w sieci platformy Azure z wirtualnych WAN
description: Więcej informacji na temat warstwy kontroli usługi Office 365 w wirtualnej sieci WAN.
author: cherylmc
ms.service: virtual-wan
services: virtual-wan
ms.topic: article
ms.date: 9/24/2018
ms.author: cherylmc
ms.openlocfilehash: cb91c1364a91c101ecf8362acd7aab01440143fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60458608"
---
# <a name="office-365-control-plane-in-virtual-wan"></a>Płaszczyzna kontroli usługi Office 365 w wirtualnej sieci WAN

Wirtualne sieci WAN klientom wybierz urządzenia SDWAN zasady można skonfigurować Internetowe usługi Office 365 mogą dla zaufanego ruchu w witrynie Azure portal. Dzięki temu:
- Ruch usługi O365 do wprowadzania sieci firmy Microsoft w pobliżu użytkowników, zapewniając poprawić komfort.
- Pozwala uniknąć ciągnięcia wstecz ruchu i włosów pining, dzięki czemu można oszczędzić koszty sieci WAN.
- Dostarczanie na zasadach łączności usługi Office 365.

## <a name="faqs"></a>Często zadawane pytania
### <a name="what-is-the-customer-benefit"></a>Co to jest korzyść klienta?
Dzięki tej funkcji w wirtualnej sieci WAN klientów teraz określić kategorie ruchu usługi Office 365, które są zaufane dla bezpośredniego podgrupach internet. To zaufana O365 ruch będzie pomijania proxy i wyznaczać trasy bezpośrednio z lokalizacji użytkownika do najbliższej POP firmy Microsoft. Umożliwia to uniknięcie ciągnięcia wstecz ruchu i włosów pining, dlatego zapewnienie poprawić komfort oraz minimalizując koszty sieci WAN. 

### <a name="what-are-the-office-365-traffic-categories"></a>Co to są kategorie ruchu usługi Office 365?
Punkty końcowe usługi Office 365 reprezentują adresy sieciowe i podsieci. Punkty końcowe mogą być adresami URL, zakresów adresów IP lub adres IP. Adresy URL mogą być nazwy FQDN, takich jak *account.office.net*, lub adres URL symboli wieloznacznych, takich jak **. office365.com*. Punkty końcowe są można podzielić na trzy kategorie — **Optymalizacja**, **Zezwalaj**, i **domyślne**zgodnie z ich poziomu krytyczności. Więcej informacji o kategoriach punktu końcowego [tutaj](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles#BKMK_Categories).

### <a name="which-office-365-traffic-category-is-recommended-by-microsoft-for-direct-internet-breakout"></a>Jakiej kategorii ruchu usługi Office 365 jest zalecane przez firmę Microsoft w przypadku bezpośredniego podgrupach Internetu?
**Optymalizacja** kategoria jest najważniejsze punkty końcowe sieci i jest wymagana do obejścia SSL podziału i kontroli i innych urządzeń zabezpieczeń sieciowych. Powinien mieć bezpośrednie wyjście internetowe blisko użytkowników. Te punkty końcowe reprezentują scenariuszy usługi Office 365, które są najbardziej poufnych wydajności, opóźnienia i dostępności sieci. Ta kategoria obejmuje (rzędu kilku ~ 10) niewielka część klucza adresy URL i zdefiniowany zestaw podsieci IP w wersji dedykowanej do obciążeń podstawowe usługi Office 365, takich jak Exchange Online, SharePoint Online, Skype dla firm Online i Microsoft Teams. 

**Zezwalaj** kategorii jest zalecane w przypadku bezpośredniego wyjście internetowe również. Zezwalaj na ruch sieciowy może tolerować jednak pewne opóźnienie sieci. Punkty końcowe w kategoriach optymalizacji i Zezwalaj są wszystkie hostowane w centrach danych firmy Microsoft i zarządzane w ramach usługi Office 365. Kategoria domyślna może zostać skierowany do domyślnej lokalizacji wyjście internetowe i nie wymaga bezpośredniego ruch wychodzący z Internetu lub pominąć podziału protokołu SSL i sprawdzanie urządzeń.

### <a name="how-do-i-set-my-o365-policies-via-virtual-wan"></a>Jak ustawić Moje zasady usługi Office 365 za pośrednictwem wirtualnej sieci WAN?
Można włączyć zasady dostępu za pomocą **wirtualne sieci WAN** -> **ustawienia** -> **konfiguracji** kartę. W tym miejscu można określić preferowaną kategorie ruchu usługi Office 365 dla bezpośredniego podgrupach internet.

![Konfigurowanie warstwy kontroli usługi Office 365 w wirtualnej sieci WAN](media/virtual-wan-office365-overview/configure-office365-control-plane.png)

### <a name="how-does-this-work"></a>Jak to działa?

1.  Ruch usługi Office 365 wchodzi sieci firmy Microsoft w pobliżu użytkowników, zapewniając zapewnienia optymalnego działania.
2.  Zasady trasy są używane przez SDWAN. Następnie pomija proxy zabezpieczeń dla zaufanego kategorie i przeprowadza lokalne mogą bezpośrednie dla tych kategorii.
3.  Unikać ciągnięcia Wstecz i pining włosów ruchu minimalizując koszty sieci WAN.

### <a name="which-partner-devices-support-this-via-virtual-wan"></a>Partner urządzeń, które obsługują to za pośrednictwem wirtualnej sieci WAN?
Obecnie usługa Citrix obsługuje te zasady, za pośrednictwem wirtualnej sieci WAN.

### <a name="what-happens-to-the-remaining-categories-of-untrusted-o365-traffic"></a>Co się dzieje z pozostałych kategorii (niezaufanych) ruchu usługi Office 365?
Pozostały ruch usługi Office 365 będą zgodne ścieżki ruchu internetowego domyślnej klientów.

### <a name="what-if-i-have-already-specified-my-o365-policies-via-my-sdwan-provider"></a>Co zrobić, jeśli już podanymi Moje zasady usługi Office 365 za pośrednictwem dostawcy Moje SDWAN?
Jeśli określisz zasad za pomocą zarówno SDWAN środowiska użytkownika, jak i WAN wirtualnych platformy Azure, są nadrzędne zasadami ustawionymi w wirtualnej sieci WAN.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [wirtualne sieci WAN](virtual-wan-about.md).