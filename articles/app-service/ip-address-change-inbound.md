---
title: Przygotowanie do zmiany przychodzącego adresu IP
description: Jeśli twój przychodzący adres IP zostanie zmieniony, dowiedz się, co zrobić, aby aplikacja nadal działała po zmianie.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672415"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Jak przygotować się do zmiany przychodzącego adresu IP

Jeśli otrzymałeś powiadomienie, że przychodzący adres IP aplikacji usługi Azure App Service zmienia się, postępuj zgodnie z instrukcjami w tym artykule.

## <a name="determine-if-you-have-to-do-anything"></a>Określ, czy musisz coś zrobić

* Opcja 1: Jeśli aplikacja usługi App Service nie ma domeny niestandardowej, nie jest wymagana żadna akcja.

* Opcja 2: Jeśli w portalu rejestracji domeny (dostawcy DNS lub usłudze Azure DNS) skonfigurowano tylko rekord CNAME (rekord DNS wskazujący identyfikator URI), nie jest wymagana żadna akcja.

* Opcja 3: Jeśli rekord A (rekord DNS skierowany bezpośrednio do adresu IP) jest skonfigurowany w portalu rejestracji domeny (zewnętrznego dostawcy DNS lub usłudze Azure DNS), zastąp istniejący adres IP nowym. Nowy adres IP można znaleźć, postępując zgodnie z instrukcjami w następnej sekcji.

* Opcja 4: Jeśli aplikacja znajduje się za modułem równoważenia obciążenia, filtrem IP lub innym mechanizmem IP, który wymaga adresu IP aplikacji, zastąp istniejący adres IP nowym. Nowy adres IP można znaleźć, postępując zgodnie z instrukcjami w następnej sekcji.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Znajdowanie nowego przychodzącego adresu IP w witrynie Azure portal

Nowy przychodzący adres IP, który jest nadawany aplikacji znajduje się w portalu w polu **Wirtualny adres IP.** Zarówno ten nowy adres IP, jak i stary są teraz połączone z aplikacją, a później stary zostanie rozłączony.

1.  Otwórz witrynę [Azure Portal](https://portal.azure.com).

2.  W menu nawigacji po lewej stronie wybierz pozycję **Usługi aplikacji**.

3.  Wybierz aplikację usługi App Service z listy.

1.  Jeśli aplikacja jest aplikacją funkcyjną, zobacz [Przychodzący adres IP aplikacji Funkcji](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  W nagłówku **Ustawienia** kliknij pozycję **Właściwości** w lewej nawigacji i znajdź sekcję z etykietą **Wirtualny adres IP**.

5. Skopiuj adres IP i skonfiguruj ponownie rekord domeny lub mechanizm IP.

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak przygotować się do zmiany adresu IP, który został zainicjowany przez platformę Azure. Aby uzyskać więcej informacji na temat adresów IP w usłudze Azure App Service, zobacz [Przychodzące i wychodzące adresy IP w usłudze Azure App Service](overview-inbound-outbound-ips.md).
