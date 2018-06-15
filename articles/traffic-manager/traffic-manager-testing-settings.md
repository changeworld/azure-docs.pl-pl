---
title: Sprawdź ustawienia usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci Sprawdź ustawienia Menedżera ruchu
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 2180b640-596e-4fb2-be59-23a38d606d12
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: aadff1806a7cb22347283143563467366e857569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876355"
---
# <a name="verify-traffic-manager-settings"></a>Sprawdź ustawienia Menedżera ruchu

Aby przetestować ustawienia Menedżera ruchu, należy mieć wielu klientów w różnych lokalizacjach, z których można uruchomić testy. Następnie Przełącz punkty końcowe w Twoim profilu Menedżera ruchu w dół pojedynczo.

* Wartość DNS TTL niski, aby zmiany propagację szybko (na przykład 30 sekund).
* Znane adresy IP usług w chmurze Azure i witryn sieci Web w wybranym profilu podczas testowania.
* Użyj narzędzi, które pozwalają rozwiązać nazwę DNS na adres IP i wyświetlenie tego adresu.

Czy sprawdzanie, czy nazwy DNS rozpoznawać adresy IP punktów końcowych w Twoim profilu. Nazwy powinna być rozpoznawana w sposób zgodny z metody routingu ruchu zdefiniowaną w profilu usługi Traffic Manager. Można użyć narzędzia, takie jak **nslookup** lub **dig** do rozpoznawania nazw DNS.

Poniższe przykłady ułatwić testowanie profilu Menedżera ruchu.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Sprawdź profil usługi Traffic Manager przy użyciu polecenia nslookup i ipconfig w systemie Windows

1. Uruchom polecenia lub w wierszu polecenia Windows PowerShell jako administrator.
2. Typ `ipconfig /flushdns` opróżnić pamięć podręczną programu rozpoznawania nazw DNS.
3. Wpisz polecenie `nslookup <your Traffic Manager domain name>`. Na przykład następujące polecenie sprawdza nazwy domeny z prefiksem *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Typowy wynik zawiera następujące informacje:

    + Nazwa DNS i adres IP serwera DNS, do której uzyskuje dostęp do rozpoznania tej nazwy domeny usługi Traffic Manager.
    + Nazwa domeny usługi Traffic Manager został wpisany w wierszu polecenia po "nslookup" i adresu IP, do którego jest rozpoznawany jako domeny usługi Traffic Manager. Jako drugiego adresu IP jest ważne, do sprawdzenia. Publiczny wirtualny adres IP (VIP) dla usługi w chmurze lub witryny sieci Web w profilu Menedżera ruchu, testowany powinien być zgodny.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Jak przetestować metody routingu ruchu sieciowego trybu failover

1. Pozostaw wszystkie punkty końcowe w górę.
2. Za pomocą jednego klienta, żądanie rozpoznawania nazw DNS dla nazwy domeny firmy przy użyciu polecenia nslookup lub podobne narzędzia.
3. Upewnij się, że rozpoznany adres IP odpowiada podstawowy punkt końcowy.
4. Przenieś w dół podstawowego punktu końcowego lub usuń monitorowania pliku, tak aby Menedżer ruchu sądzi, że aplikacja działa.
5. Poczekaj, aż DNS Time-to-Live (TTL) profilu usługi Traffic Manager oraz kolejnych dwóch minut. Na przykład jeśli Twoje czas wygaśnięcia DNS to 300 sekund (5 minut), należy poczekać na siedem minut.
6. Flush że klienta pamięci podręcznej i żądania DNS rozpoznawanie nazw DNS za pomocą polecenia nslookup. W systemie Windows możesz opróżnić pamięć podręczną DNS przy użyciu polecenia ipconfig/flushdns.
7. Upewnij się, że rozpoznany adres IP odpowiada dodatkowej punktu końcowego.
8. Powtórz ten proces z kolei wyłączania każdego punktu końcowego. Sprawdź, czy DNS zwraca adres IP następnego punktu końcowego na liście. W przypadku wszystkich punktów końcowych w dół, należy ponownie uzyskać adres IP podstawowego punktu końcowego.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Jak przetestować metody routingu ruchu ważoną

1. Pozostaw wszystkie punkty końcowe w górę.
2. Za pomocą jednego klienta, żądanie rozpoznawania nazw DNS dla nazwy domeny firmy przy użyciu polecenia nslookup lub podobne narzędzia.
3. Upewnij się, że rozpoznany adres IP odpowiada jednej z punktami końcowymi.
4. Opróżnienia pamięci podręcznej klienta DNS i powtórz kroki 2 i 3 dla każdego punktu końcowego. Powinny pojawić różne adresy IP zwrócona dla każdego z punktów końcowych.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Jak przetestować metody routingu ruchu wydajności

Aby skutecznie przetestować wydajność metody routingu ruchu, musi mieć klienci znajdujący się w różnych części świata. Klientów można tworzyć w różnych regionach platformy Azure, które mogą służyć do testowania usług. Jeśli masz globalnej sieci można zdalnie zalogować się do klientów w innych częściach świata i uruchamiania testów z tego miejsca.

Alternatywnie istnieją Zwolnij opartych na sieci web wyszukiwania DNS i szczegółowej dostępność usług. Niektóre z tych narzędzi zapewniają możliwość sprawdź rozpoznawanie nazwy DNS z różnych lokalizacji na całym świecie. Czy na "Wyszukiwania DNS" Wyszukiwanie przykłady. Usług innych firm, takich jak Gomez lub prelekcji można potwierdzić, czy profile są Dystrybucja ruchu zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki

* [O metodach routingu ruchu Menedżera ruchu](traffic-manager-routing-methods.md)
* [Zagadnienia dotyczące wydajności usługi Traffic Manager](traffic-manager-performance-considerations.md)
* [Rozwiązywanie problemów ze stanem obniżonej wydajności usługi Traffic Manager](traffic-manager-troubleshooting-degraded.md)
