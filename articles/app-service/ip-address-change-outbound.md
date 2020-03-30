---
title: Przygotowanie do zmiany wychodzącego adresu IP
description: Jeśli twój wychodzący adres IP zostanie zmieniony, dowiedz się, co zrobić, aby aplikacja nadal działała po zmianie.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671663"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Jak przygotować się do zmiany wychodzącego adresu IP

Jeśli otrzymałeś powiadomienie, że wychodzące adresy IP aplikacji usługi Azure App Service zmieniają się, postępuj zgodnie z instrukcjami w tym artykule.

## <a name="determine-if-you-have-to-do-anything"></a>Określ, czy musisz coś zrobić

* Opcja 1: Jeśli aplikacja usługi App Service nie używa filtrowania IP, listy jawnego dołączania lub specjalnej obsługi ruchu wychodzącego, takiego jak routing lub zapora, nie jest wymagana żadna akcja.

* Opcja 2: Jeśli aplikacja ma specjalną obsługę wychodzących adresów IP (zobacz przykłady poniżej), dodaj nowe wychodzące adresy IP wszędzie tam, gdzie pojawiają się istniejące. Nie zastępuj istniejących adresów IP. Nowe wychodzące adresy IP można znaleźć, postępując zgodnie z instrukcjami w następnej sekcji.

  Na przykład wychodzący adres IP może być jawnie uwzględniony w zaporze poza aplikacją lub zewnętrzna usługa płatności może mieć dozwoloną listę zawierającą wychodzący adres IP aplikacji. Jeśli twój adres wychodzący jest skonfigurowany na liście w dowolnym miejscu poza aplikacją, musi to ulec zmianie.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Znajdowanie wychodzących adresów IP w witrynie Azure portal

Nowe wychodzące adresy IP są wyświetlane w portalu, zanim zajdą w życie. Gdy platforma Azure zacznie używać nowych, stare nie będą już używane. Używany jest tylko jeden zestaw naraz, więc wpisy na listach dołączania muszą mieć zarówno stare, jak i nowe adresy IP, aby zapobiec awarii, gdy przełącznik się dzieje. 

1.  Otwórz witrynę [Azure Portal](https://portal.azure.com).

2.  W menu nawigacji po lewej stronie wybierz pozycję **Usługi aplikacji**.

3.  Wybierz aplikację usługi App Service z listy.

1.  Jeśli aplikacja jest aplikacją funkcyjną, zobacz [Funkcja wychodzących adresów IP aplikacji](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  W nagłówku **Ustawienia** kliknij pozycję **Właściwości** w lewej nawigacji i znajdź sekcję z etykietą **Wychodzące adresy IP**.

5. Skopiuj adresy IP i dodaj je do specjalnej obsługi ruchu wychodzącego, takiej jak filtr lub lista dozwolonych. Nie usuwaj istniejących adresów IP z listy.

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak przygotować się do zmiany adresu IP, który został zainicjowany przez platformę Azure. Aby uzyskać więcej informacji na temat adresów IP w usłudze Azure App Service, zobacz [Przychodzące i wychodzące adresy IP w usłudze Azure App Service](overview-inbound-outbound-ips.md).
