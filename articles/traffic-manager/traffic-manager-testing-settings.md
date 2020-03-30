---
title: Weryfikowanie ustawień usługi Azure Traffic Manager
description: W tym artykule dowiesz się, jak zweryfikować ustawienia usługi Traffic Manager i przetestować metodę routingu ruchu.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 94ab5e550f0053fa19b9b93f1d67690211543325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938382"
---
# <a name="verify-traffic-manager-settings"></a>Weryfikowanie ustawień usługi Traffic Manager

Aby przetestować ustawienia menedżera ruchu, musisz mieć wielu klientów w różnych lokalizacjach, z których można uruchomić testy. Następnie przenieś punkty końcowe w profilu usługi Traffic Manager w dół po kolei.

* Ustaw niską wartość czasu ttl DNS, tak aby zmiany były szybko propagowane (na przykład 30 sekund).
* Poznaj adresy IP usług w chmurze platformy Azure i witryn sieci Web w testowanym profilu.
* Użyj narzędzi, które umożliwiają rozpoznanie nazwy DNS na adres IP i wyświetlenie tego adresu.

Sprawdzasz, czy nazwy DNS są rozpoznawane na adresach IP punktów końcowych w profilu. Nazwy powinny być rozpoznawane w sposób zgodny z metodą routingu ruchu zdefiniowaną w profilu usługi Traffic Manager. Można użyć narzędzi, takich jak **nslookup** lub **kopać,** aby rozpoznać nazwy DNS.

Poniższe przykłady ułatwią przetestowanie profilu usługi Traffic Manager.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Sprawdź profil usługi Traffic Manager za pomocą nslookup i ipconfig w systemie Windows

1. Otwórz polecenie lub wiersz programu Windows PowerShell jako administrator.
2. Wpisz, `ipconfig /flushdns` aby opróżnić pamięć podręczną programu rozpoznawania nazw DNS.
3. Wpisz polecenie `nslookup <your Traffic Manager domain name>`. Na przykład następujące polecenie sprawdza nazwę domeny z prefiksem *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Typowy wynik pokazuje następujące informacje:

    + Nazwa DNS i adres IP serwera DNS, do który uzyskuje się dostęp, aby rozpoznać tę nazwę domeny Usługi Traffic Manager.
    + Nazwa domeny usługi Traffic Manager wpisana w wierszu polecenia po "nslookup" i adres IP, do którego adres usługi Traffic Manager jest rozpoznawany. Drugi adres IP jest ważny do sprawdzenia. Powinien być zgodny z publicznym adresem wirtualnego adresu IP (VIP) dla jednej z usług w chmurze lub witryn sieci Web w testowanym profilu usługi Traffic Manager.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Jak przetestować metodę routingu ruchu trybu failover

1. Pozostaw wszystkie punkty końcowe w górę.
2. Korzystając z jednego klienta, zażądaj rozwiązania DNS dla nazwy domeny firmy przy użyciu funkcji nslookup lub podobnego narzędzia.
3. Upewnij się, że rozwiązany adres IP jest zgodny z podstawowym punktem końcowym.
4. Obniżyć podstawowy punkt końcowy lub usunąć plik monitorowania, tak aby usługa Traffic Manager uważa, że aplikacja jest wyłączona.
5. Poczekaj na czas wygaśnięcia dns (TTL) profilu usługi Traffic Manager plus dodatkowe dwie minuty. Na przykład jeśli czas wygaśnięcia DNS wynosi 300 sekund (5 minut), należy odczekać siedem minut.
6. Opróżnij pamięć podręczną klienta DNS i zażądaj rozpoznawania DNS przy użyciu funkcji nslookup. W systemie Windows można opróżnić pamięć podręczną DNS za pomocą polecenia ipconfig /flushdns.
7. Upewnij się, że rozwiązany adres IP jest zgodny z pomocniczym punktem końcowym.
8. Powtórz proces, obniżając po kolei każdy punkt końcowy. Sprawdź, czy system DNS zwraca adres IP następnego punktu końcowego na liście. Gdy wszystkie punkty końcowe są w dół, należy ponownie uzyskać adres IP podstawowego punktu końcowego.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Jak przetestować metodę ważonego routingu ruchu

1. Pozostaw wszystkie punkty końcowe w górę.
2. Korzystając z jednego klienta, zażądaj rozwiązania DNS dla nazwy domeny firmy przy użyciu funkcji nslookup lub podobnego narzędzia.
3. Upewnij się, że rozwiązany adres IP jest zgodny z jednym z punktów końcowych.
4. Opróżnij pamięć podręczną klienta DNS i powtórz kroki 2 i 3 dla każdego punktu końcowego. Powinny być widoczne różne adresy IP zwrócone dla każdego punktu końcowego.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Jak przetestować metodę routingu ruchu wydajności

Aby skutecznie przetestować metodę routingu ruchu wydajności, musisz mieć klientów znajdujących się w różnych częściach świata. Można tworzyć klientów w różnych regionach platformy Azure, które mogą służyć do testowania usług. Jeśli masz sieć globalną, możesz zdalnie logować się do klientów w innych częściach świata i uruchamiać testy.

Alternatywnie dostępne są bezpłatne internetowe usługi wyszukiwania i kopania DNS. Niektóre z tych narzędzi umożliwiają sprawdzanie rozpoznawania nazw DNS z różnych lokalizacji na całym świecie. Wykonaj wyszukiwanie na przykładach "Wyszukiwanie DNS". Usługi innych firm, takie jak Gomez lub Keynote, mogą być używane do potwierdzania, że Twoje profile rozpowszechniają ruch zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki

* [Informacje o metodach routingu ruchu usługi Traffic Manager](traffic-manager-routing-methods.md)
* [Zagadnienia dotyczące wydajności usługi Traffic Manager](traffic-manager-performance-considerations.md)
* [Rozwiązywanie problemów ze stanem obniżonej wydajności usługi Traffic Manager](traffic-manager-troubleshooting-degraded.md)
