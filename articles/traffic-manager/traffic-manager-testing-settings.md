---
title: Sprawdź ustawienia usługi Azure Traffic Manager
description: Ten artykuł pomoże Ci zweryfikować ustawień usługi Traffic Manager.
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: allensu
ms.openlocfilehash: 19ef08a40d0a84aecb070e71bbb8c9b6a88ae059
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070936"
---
# <a name="verify-traffic-manager-settings"></a>Weryfikowanie ustawień usługi Traffic Manager

Aby przetestować ustawień usługi Traffic Manager, musisz mieć wielu klientów w różnych miejscach, w których można uruchomić testy. Następnie przenieś punktów końcowych w profilu usługi Traffic Manager w dół po kolei.

* Wartość czasu wygaśnięcia DNS niski, aby zmiany Propagacja szybko (na przykład 30 sekund).
* Znane adresy IP usługi Azure cloud services i witryny sieci Web w profilu, które testujesz.
* Użyj narzędzi, które pozwalają rozwiązać nazwę DNS na adres IP i wyświetlać ten adres.

Sprawdzasz, aby zobaczyć, że nazwy DNS rozpoznać adresami IP punktów końcowych w Twoim profilu. Nazwy powinna być rozpoznawana w sposób zgodny ze zdefiniowaną w profilu usługi Traffic Manager metody routingu ruchu. Możesz użyć narzędzi, takich jak **nslookup** lub **dig** do rozpoznawania nazw DNS.

Poniższe przykłady ułatwiają Testowanie profilu usługi Traffic Manager.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Sprawdź profil usługi Traffic Manager za pomocą nslookup i ipconfig w Windows

1. Uruchom polecenia lub w wierszu polecenia programu Windows PowerShell jako administrator.
2. Typ `ipconfig /flushdns` opróżnienia pamięci podręcznej programu rozpoznawania nazw DNS.
3. Wpisz polecenie `nslookup <your Traffic Manager domain name>`. Na przykład poniższe polecenie sprawdza nazwę domeny z prefiksem *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Typowy wynik zawiera następujące informacje:

    + Nazwa DNS i adres IP serwera DNS, do którego uzyskiwany jest dostęp do rozwiązania tej nazwy domeny usługi Traffic Manager.
    + Nazwa domeny usługi Traffic Manager można wpisać w wierszu polecenia po "nslookup" i adresu IP, do którego jest rozpoznawana jako domenę usługi Traffic Manager. Drugi adres IP jest najważniejsza, aby sprawdzić. Powinien on odpowiadać publiczny wirtualny adres IP (VIP) dla jednego z usług w chmurze lub witryn internetowych w profilu usługi Traffic Manager, które testujesz.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Testowanie trybu failover metodę routingu ruchu

1. Pozostaw wszystkie punkty końcowe w górę.
2. Za pomocą jednego klienta, żądanie rozpoznawania nazw DNS dla nazwy domeny firmy przy użyciu podpolecenia lub podobnej użyteczności.
3. Upewnij się, że rozpoznać adres IP odpowiada podstawowego punktu końcowego.
4. Przenieś w dół do podstawowego punktu końcowego lub usuń plik monitorowania, więc w tym usługi Traffic Manager sądzą, że aplikacja nie działa.
5. Poczekaj, aż DNS Time-to-Live (TTL) profil usługi Traffic Manager oraz kolejnych dwóch minut. Na przykład jeśli Twoja czasu wygaśnięcia DNS to 300 sekund (5 minut), należy poczekać siedem minut.
6. Usuń z usługi DNS klienta pamięci podręcznej i żądania rozpoznawanie nazw DNS za pomocą polecenia nslookup. W Windows możesz opróżnić pamięć podręczną DNS za pomocą polecenia ipconfig/flushdns.
7. Upewnij się, że rozpoznać adres IP jest zgodny z pomocniczego punktu końcowego.
8. Powtórz ten proces trwa wyłączanie każdy punkt końcowy z osobna. Upewnij się, że na liście DNS zwracany jest adres IP następnego punktu końcowego. W przypadku wszystkich punktów końcowych w dół, należy ponownie uzyskać adres IP podstawowego punktu końcowego.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Jak przetestować metody routingu ważonego ruchu

1. Pozostaw wszystkie punkty końcowe w górę.
2. Za pomocą jednego klienta, żądanie rozpoznawania nazw DNS dla nazwy domeny firmy przy użyciu podpolecenia lub podobnej użyteczności.
3. Upewnij się, że rozpoznać adres IP odpowiada jednej z punktami końcowymi usługi.
4. Opróżnienia pamięci podręcznej klienta DNS i powtórz kroki 2 i 3 dla każdego punktu końcowego. Powinieneś zobaczyć różne adresy IP zwrócona dla każdego z punktów końcowych.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Testowanie wydajności metodę routingu ruchu

Aby skutecznie przetestować wydajność metody routingu ruchu, konieczne jest posiadanie klienci znajdujący się w różnych częściach świata. Klientów można tworzyć w różnych regionach platformy Azure, które mogą służyć do testowania usługi. W przypadku globalnej sieci można zdalnie Zaloguj się do klientów w innych częściach świata i uruchomić testy z tego miejsca.

Alternatywnie istnieją wolne opartego na sieci web wyszukiwania DNS i szczegółowej dostępne usługi. Niektóre z tych narzędzi zapewniają możliwość sprawdzenia, rozpoznawanie nazw DNS z różnych lokalizacji na całym świecie. Czy na "Wyszukiwania DNS" Wyszukaj przykłady. Usługi innych firm, takich jak Gomez lub prezentacji, którą można potwierdzić profilów dystrybuujesz ruchu zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Kolejne kroki

* [Metody routingu ruchu w usłudze Traffic Manager](traffic-manager-routing-methods.md)
* [Zagadnienia dotyczące wydajności usługi Traffic Manager](traffic-manager-performance-considerations.md)
* [Rozwiązywanie problemów ze stanem obniżonej wydajności usługi Traffic Manager](traffic-manager-troubleshooting-degraded.md)
