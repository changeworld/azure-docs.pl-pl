---
title: Zabezpieczenia fizyczne modułu HSM — dedykowany moduł HSM platformy Azure | Dokumenty firmy Microsoft
description: Informacje o fizycznym bezpieczeństwie urządzeń HSM dedykowanych platformy Azure w centrach danych
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ede1af4625d06af4e280eda86d09ae1db3dfdfd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881032"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Zabezpieczenia fizyczne dedykowanego modułu HSM platformy Azure

Dedykowany moduł HSM platformy Azure pomaga spełnić zaawansowane wymagania dotyczące zabezpieczeń magazynu kluczy. Jest zarządzany zgodnie z rygorystycznymi praktykami bezpieczeństwa w całym cyklu życia, aby zaspokoić potrzeby klientów.

## <a name="security-through-procurement"></a>Bezpieczeństwo dzięki zaopatrzeniu

Firma Microsoft śledzi proces bezpiecznego zaopatrzenia. Zarządzamy łańcuchem opieki i zapewniamy, że konkretne urządzenie zamówione i wysłane jest urządzeniem docierającym do naszych centrów danych. Urządzenia znajdują się w plastikowych plecach typu manipulacje. Są one przechowywane w bezpiecznym miejscu do momentu uruchomienia w galerii danych centrum danych.  Stojaki zawierające urządzenia HSM są uważane za duży wpływ na działalność (HBI). Urządzenia są zablokowane i pod nadzorem wideo przez cały czas z przodu i z tyłu.

## <a name="security-through-deployment"></a>Bezpieczeństwo dzięki wdrożeniu

Moduły HSM są instalowane w stojakach wraz z powiązanymi składnikami sieciowymi. Po zainstalowaniu muszą być skonfigurowane, zanim zostaną udostępnione jako część usługi Azure Dedicated HSM. To działanie konfiguracji jest wykonywane przez pracowników firmy Microsoft, którzy przeszli sprawdzanie w tle. Administracja "Just In Time" (JIT) służy do ograniczania dostępu tylko do odpowiednich pracowników i tylko przez czas, w którego dostęp jest potrzebny. Procedury i systemy również zapewnić, że wszystkie działania związane z urządzeniami HSM jest rejestrowany.

## <a name="security-in-operations"></a>Bezpieczeństwo w operacjach

Moduły HSM są urządzeniami sprzętowymi (rzeczywisty moduł HSM jest kartą PCI w urządzeniu), więc możliwe jest, że mogą wystąpić problemy z poziomem komponentu. Potencjalne problemy obejmują między innymi awarie wentylatorów i zasilaczy. Ten typ zdarzenia będzie wymagać konserwacji lub przerwy / naprawy działania, aby zastąpić wszelkie składniki wymienialne.

### <a name="component-replacement"></a>Wymiana komponentów

Po udostępnienie urządzenia i pod zarządzaniem klientem, zasilacz z możliwością wymiany podczas pracy jest jedynym składnikiem, które zostaną zastąpione. Ten składnik znajduje się poza granicą zabezpieczeń i nie powoduje zdarzenia sabotażu. System sprzedaży biletów służy do autoryzowania inżyniera firmy Microsoft w celu uzyskania dostępu do tylnej części stelaża HBI. Podczas przetwarzania biletu wystawiany jest tymczasowy klucz fizyczny. Ten klucz daje inżynierowi dostęp do urządzenia i umożliwia im wymianę składnika, którego dotyczy problem. Każdy inny dostęp (czyli spowodowanie zdarzenia sabotażu) zostanie wykonany, gdy urządzenie nie zostanie przydzielone klientowi, minimalizując w ten sposób ryzyko bezpieczeństwa i dostępności.  

### <a name="device-replacement"></a>Wymiana urządzenia

W przypadku całkowitej awarii urządzenia jest przestrzegany proces podobny do tego, który jest używany podczas awarii składnika. Jeśli klient nie jest w stanie zerować urządzenia lub urządzenie jest w nieznanym stanie, urządzenia z łożyskiem danych zostaną usunięte i umieszczone w pojemniku do niszczenia w stojaku. Urządzenia umieszczone w koszu zostaną zniszczone w sposób kontrolowany i bezpieczny. Żadne urządzenia z danymi z stelaża HBI nie pozostawią centrum danych firmy Microsoft.

### <a name="other-rack-access-activities"></a>Inne działania związane z dostępem do szafy rack

Jeśli inżynier firmy Microsoft musi uzyskać dostęp do stelaża używanego przez urządzenia HSM (na przykład konserwacja urządzeń sieciowych), w celu uzyskania dostępu do bezpiecznego stelaża HBI zostaną użyte standardowe procedury zabezpieczeń. Dostęp do nich będzie podlegał nadzorowi wideo. Urządzenia HSM są weryfikowane zgodnie [z poziomem 3 FIPS 140-2,](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) więc każdy nieautoryzowany dostęp do urządzeń HSM zostanie zasygnalizowany klientowi, a dane zostaną zerowane.

## <a name="logical-level-security-considerations"></a>Zagadnienia dotyczące zabezpieczeń na poziomie logicznym

Moduły HSM są aprowidzone do sieci wirtualnej utworzonej przez klienta. Jest to prywatna przestrzeń adresowa IUP klienta.  Ta konfiguracja zapewnia cenne izolacji poziom sieci logicznej i zapewnia dostęp tylko przez klienta. Oznacza to, że wszystkie kontrole zabezpieczeń poziomu logicznego są obowiązkiem klienta.

## <a name="next-steps"></a>Następne kroki

Zaleca się, aby wszystkie kluczowe pojęcia usługi, takie jak wysoka dostępność i zabezpieczenia i możliwości obsługi, na przykład, są dobrze rozumiane przed inicjowania obsługi administracyjnej urządzenia, projektowania aplikacji lub wdrażania.

* [Wysoka dostępność](high-availability.md)
* [Obsługa sieci](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowania](monitoring.md)
* [Architektura wdrażania](deployment-architecture.md)