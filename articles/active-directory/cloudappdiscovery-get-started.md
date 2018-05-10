---
title: Konfigurowanie usługi Cloud App Discovery w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Znajdź i Zarządzaj aplikacjami w usłudze Cloud App Discovery o podanie informacji przydatnych wyników na użycie chmury i tle IT.
services: active-directory
keywords: Usługa cloud app discovery, zarządzanie aplikacjami
documentationcenter: ''
author: curtand
manager: mtillman
tags: ignite
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 04/25/2018
ms.author: curtand
ms.reviewer: nigu
ms.openlocfilehash: 3e4907cbeaf06a519761d76a41708eacd3843ed4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>Konfigurowanie Cloud App Discovery w usłudze Azure AD

Usługa cloud App Discovery w usłudze Azure AD zależy teraz na integracji z danymi dostępnej w sklepie Microsoft Cloud App Security. Aby podać informacje trwającą na użycie chmury i tle IT, Cloud App Discovery porównuje dzienników ruchu sieciowego do katalogu Cloud App Security z ponad 15 000 aplikacji w chmurze. W tym artykule opisano proces instalacji i zawiera linki do szczegółowych informacji dotyczących każdego etapu. Opisano również informacje zapory i serwera proxy i dziennika Obsługa plików.

## <a name="prerequisites"></a>Wymagania wstępne

* Organizacja musi mieć licencję programu Azure AD Premium P1 na korzystanie z produktu. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
* Aby skonfigurować Cloud App Discovery, musi być administratorem globalnym lub czytnik zabezpieczeń w usłudze Azure Active Directory.

## <a name="setup-steps"></a>Kroki konfiguracji

1. [Konfigurowanie raportów migawki](cloudappdiscovery-set-up-snapshots.md) do sprawdzenia format dziennika upewnij się, dzienniki świadczą użyteczne informacje Cloud App Discovery. Można też podać ad hoc widoczność dzienników ruchu, który możesz ręcznie przekazać z zapory i serwery proxy.

2. [Konfigurowanie ciągłego raportowania](https://docs.microsoft.com/cloud-app-security/discovery-docker) analizować wszystkie dzienniki, które są przekazywane z sieci przy użyciu modułu zbierającego dzienniki Cloud App Security. Można używać ich do identyfikacji nowych aplikacji i trendów użycia.

3. Jeśli dzienniki nie są obecnie obsługiwane, [skonfigurować dziennik niestandardowy analizator składni](https://docs.microsoft.com/cloud-app-security/custom-log-parser) tak, aby je analizować Cloud App Discovery.
  
## <a name="log-processing-flow"></a>Przepływ przetwarzania dziennika

Go może potrwać od kilku minut do kilku godzin do generowania raportów w zależności od ilości danych. Oto, co to są analizowane:

* **Przekaż** dzienników ruchu w sieci Web z sieci są przekazywane do portalu.
* **Przeanalizować** Cloud App Security analizuje i wyodrębnia dane o ruchu z dzienników ruchu za pomocą dedykowanego parsera dla każdego źródła danych.
* **Analizowanie** dane o ruchu są analizowane względem katalogu Cloud App Security, aby zidentyfikować więcej niż 15 000 aplikacji w chmurze. Aktywni użytkownicy i adresy IP są również identyfikowane jako część analizy.
* **Generowanie raportu** Cloud App Security generuje raport danych wyodrębniony z plików dziennika i udostępnia Cloud App Discovery.

> [!NOTE]
> * Raport ciągłego dane są analizowane dwa razy dziennie.
> * Moduł zbierający dzienniki kompresuje dane przed przesłaniem. Ruch wychodzący z modułu zbierającego dzienniki wynosi około 10% rozmiaru dzienników odebrany ruch.

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>Przy użyciu dzienników ruchu w usłudze Cloud App Discovery

Usługa cloud App Discovery używa danych zawartych w dzienników ruchu sieciowego. Więcej szczegółów można dodać w dzienniku, lepszą widoczność Ci. Usługa cloud App Discovery wymaga danych ruchu w sieci web z następującymi atrybutami:

* Data transakcji
* Źródłowy adres IP
* Użytkownik źródłowy **zalecane**
* Docelowy adres IP
* Docelowy adres URL **zalecane** (adresy URL zawierają więcej dokładność wykrywania aplikacji w chmurze niż adresów IP)
* Łączna ilość danych
* Ilość przekazanych lub pobrać dane, aby uzyskać szczegółowe informacje o sposobie użycie aplikacji w chmurze
* Działania podjęte (dozwolone/blokowane)

Cloud App Discovery nie można pokazać lub analizowania atrybuty, które nie znajdują się w dziennikach. Na przykład **Zapora Cisco ASA** format dziennika standard nie zawiera **ilości bajtów przekazane na transakcję**, **Username**, lub **docelowy adres URL**  , ale tylko docelowy adres IP. W związku z tym konieczne może być mniej wgląd w aplikacje w chmurze z tego źródła danych. Dla zapór Cisco ASA Ustaw poziom informacji do 6.1.

Aby można było pomyślnie wygenerować raport Cloud App Discovery, dzienników ruchu sieciowego musi spełniać następujące warunki:

1.  Źródło danych jest [obsługiwany serwer zapory lub serwera proxy](#supported-firewalls-and-proxies).
2.  Format dziennika jest zgodny z oczekiwanym formatem. To jest sprawdzana podczas przekazywania. Aby zoptymalizować format dziennika uour, zobacz [tworzenia migawki usługa Cloud App Discovery raportów](cloudappdiscovery-set-up-snapshots.md).
3.  Zdarzenia nie zostaną więcej niż 90 dni.
4.  Plik dziennika jest nieprawidłowy i zawiera informacje na ruch wychodzący.

## <a name="supported-firewalls-and-proxy-servers"></a>Obsługiwanych zapór i serwerów proxy

* Barracuda - zapory aplikacji sieci Web (W3C)
* Blue Coat Proxy SG — dziennik dostępu (W3C)
* Check Point
* Siłę Cisco ASA rażenia
* Zapora Cisco ASA (Cisco ASA zapory, Ustaw poziom informacji 6)
* Cisco IronPort WSA
* Cisco ScanSafe
* Cisco Meraki — dziennik adresów URL
* NGFW Clavister (Syslog)
* Dell Sonicwall
* Filtr i Arts cyfrowych
* Fortinet Fortigate
* Juniper SRX
* Juniper SSG
* McAfee bezpieczną internetową bramy
* Microsoft Forefront Threat Management Gateway (W3C)
* Zapora serii Palo Alto
* Sophos ogólnych
* Sophos Cyberoam
* Squid (typowy)
* Squid (natywny)
* Websense — Web Security Solutions — szczegółowy raport dochodzeniowy (CSV)
* Websense — Web Security Solutions — dziennik działań w Internecie (CEF)
* Zscaler

> [!NOTE]
> Usługa cloud App Discovery obsługuje adresy IPv4 i IPv6.

Jeśli dziennik nie jest obsługiwany, wybierz **innych** jako **źródła danych** i określ urządzenie i próbujesz przekazać dziennika. Dziennik jest rozpatrywana przez zespół analityka chmury Cloud App Security. Podczas obsługi dla danego typu dziennika zostanie dodany, powiadomimy Cię, ale zamiast tego możesz zdefiniować niestandardowy analizator składni zgodny format dziennika. Aby uzyskać więcej informacji, zobacz [użyć analizatora dziennik niestandardowy](https://docs.microsoft.com/cloud-app-security/custom-log-parser).

## <a name="data-attributes-according-to-vendor-documentation"></a>Atrybuty danych (zgodnie z dokumentacją dostawcy)

| Źródło danych         | Adres URL aplikacji docelowej | Adres IP aplikacji docelowej | Nazwa użytkownika | Punkt początkowy adres IP | Całkowity ruch | Przekazane bajtów |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **Tak**        | **Tak**       | **Tak**  | **Tak**   | Nie            | Nie             |
| Blue Coat                               | **Tak**        | Nie            | **Tak**  | **Tak**   | **Tak**       | **Tak**        |
| Punkt kontrolny                              | Nie             | **Tak**       | Nie       | **Tak**   | Nie            | Nie             |
| Cisco ASA                               | Nie             | **Tak**       | Nie       | **Tak**   | **Tak**       | Nie             |
| Cisco FWSM                              | Nie             | **Tak**       | Nie       | **Tak**   | **Tak**       | Nie             |
| Cisco Ironport WSA                      | **Tak**        | **Tak**       | **Tak**  | **Tak**   | **Tak**       | **Tak**        |
| Cisco Meraki                            | **Tak**        | **Tak**       | Nie       | **Tak**   | Nie            | Nie             |
| NGFW Clavister (Syslog)                 | **Tak**        | **Tak**       | **Tak**  | **Tak**   | **Tak**       | **Tak**        |
| Dell SonicWall                          | **Tak**        | **Tak**       | Nie       | **Tak**   | **Tak**       | **Tak**        |
| FortiGate                               | Nie             | **Tak**       | Nie       | **Tak**   | **Tak**       | **Tak**        |
| Juniper SRX                             | Nie             | **Tak**       | Nie       | **Tak**   | **Tak**       | **Tak**        |
| Juniper SSG                             | Nie             | **Tak**       | Nie       | **Tak**   | **Tak**       | **Tak**        |
| McAfee SWG                              | **Tak**        | Nie            | Nie       | **Tak**   | **Tak**       | **Tak**        |
| MS TMG                                  | **Tak**        | Nie            | **Tak**  | **Tak**   | **Tak**       | **Tak**        |
| Palo Alto Networks                      | Nie             | **Tak**       | **Tak**  | **Tak**   | **Tak**       | **Tak**        |
| Sophos                                  | **Tak**        | **Tak**       | **Tak**  | **Tak**   | **Tak**       | Nie             |
| Squid (typowy)                          | **Tak**        | Nie            | **Tak**  | **Tak**   | Nie            | **Tak**        |
| Squid (natywny)                          | **Tak**        | Nie            | **Tak**  | **Tak**   | Nie            | **Tak**        |
| WebSense — dochodzeniowych raportu (CSV)   | **Tak**        | **Tak**       | **Tak**  | **Tak**   | **Tak**       | **Tak**        |
| WebSense — dziennik działań w Internecie (CEF)  | **Tak**        | **Tak**       | **Tak**  | **Tak**   | **Tak**       | **Tak**        |
| Zscaler                                 | **Tak**        | **Tak**       | **Tak**  | **Tak**   | **Tak**       | **Tak**        |


## <a name="next-steps"></a>Kolejne kroki
Użyj następujących linków, aby kontynuować konfigurowanie Cloud App Discovery w usłudze Azure AD.

* [Tworzenie raportów migawek](cloudappdiscovery-set-up-snapshots.md)
* [Konfigurowanie ciągłego raportowania](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Korzystanie z niestandardowego analizatora dziennika](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
