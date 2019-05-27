---
title: Debugowanie łączników serwera Proxy aplikacji — Azure Active Directory | Dokumentacja firmy Microsoft
description: Przy debugowaniu problemów z łącznikami serwera Proxy aplikacji usługi Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: c3088ae777fe1a64be218105d36fdb9e01d7b798
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172238"
---
# <a name="debug-application-proxy-connector-issues"></a>Debugowanie problemów z łącznika serwera Proxy aplikacji 

Ten artykuł ułatwia rozwiązywanie problemów z łącznikami serwera Proxy aplikacji usługi Azure Active Directory (Azure AD). Jeśli używasz usługi serwera Proxy aplikacji dostępu zdalnego do aplikacji sieci web w środowisku lokalnym, ale występują problemy z nawiązaniem połączenia z aplikacją, użyj tego schematu blokowego do debugowania problemów łącznika. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, zainstalowano łącznik serwera Proxy aplikacji usługi i są wystąpił problem. Podczas rozwiązywania problemów z serwera Proxy aplikacji, zalecamy uruchomić ten przepływ rozwiązywania problemów, aby określić, jeśli łączników serwera Proxy aplikacji są poprawnie skonfigurowane. Jeśli nadal występują problemy z nawiązaniem połączenia z aplikacją, postępuj zgodnie z rozwiązywania problemów z przepływem w [problemy z aplikacją serwer Proxy debugowania aplikacji](application-proxy-debug-apps.md).  


Aby uzyskać więcej informacji dotyczących serwera Proxy aplikacji i używania jej łączników zobacz:

- [Dostęp zdalny do aplikacji lokalnych za pośrednictwem serwera Proxy aplikacji](application-proxy.md)
- [Łączników serwera Proxy aplikacji](application-proxy-connectors.md)
- [Instalowanie i rejestrowanie łącznika](application-proxy-add-on-premises-application.md)
- [Rozwiązywanie problemów z serwera Proxy aplikacji i komunikaty o błędach](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Schemat blokowy problemy dotyczące łącznika

Ten schemat blokowy przeprowadzi Cię przez kroki do debugowania, niektóre najbardziej typowe problemy, łącznik. Aby uzyskać szczegółowe informacje o każdym kroku Zobacz tabeli schematu blokowego.

![Schemat blokowy pokazujący etapy dotyczące debugowania łącznika](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | action | Opis | 
|---------|---------|---------|
|1 | Znajdź grupy łączników, przypisany do aplikacji | Prawdopodobnie już łącznik zainstalowane na wielu serwerach, w którym to przypadku należy łączniki [przypisane do grupy łączników](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Aby dowiedzieć się więcej na temat grupy łączników, zobacz [Publikuj aplikacje w oddzielnych sieciach i miejsc za pomocą grupy łączników](application-proxy-connector-groups.md). |
|2 | Zainstaluj łącznik i przypisać grupę | Jeśli nie masz zainstalowanego łącznika, zobacz [Instalowanie i rejestrowanie łącznika](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br>Jeśli łącznik nie jest przypisany do grupy, zobacz [przypisać łącznika do grupy](application-proxy-connector-groups.md#create-connector-groups).<br></br>Jeśli aplikacja nie jest przypisany do grupy łączników, zobacz [przypisania aplikacji do grupy łączników](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Uruchom test portu na serwerze łącznika | Na serwerze łącznika, uruchom test portów przy użyciu [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) lub innych portów, testowanie narzędzia do sprawdzania, czy Otwórz porty 443 i 80.|
|4 | Konfigurowanie domen i portów | [Upewnij się, że Twoich domen i porty są skonfigurowane prawidłowo](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) dla łącznika zapewnić prawidłowe działanie, istnieją Niektóre porty, które muszą być otwarte i adresów URL, które serwer musi mieć możliwość uzyskania dostępu. |
|5 | Sprawdź, czy serwer proxy zaplecza jest w użyciu | Sprawdź, czy łączniki są używane serwery proxy zaplecza lub je z pominięciem. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z łącznika serwera proxy i problemy z połączeniem usługi](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Aktualizowanie łącznika i aktualizacji, aby użyć serwera proxy zaplecza | Proxy zaplecza jest używany, należy się upewnić, że łącznik używa tego samego serwera proxy. Aby uzyskać szczegółowe informacje dotyczące rozwiązywania problemów i konfigurowania łączników do pracy z serwerami proxy, zobacz [pracy przy użyciu istniejących serwerów proxy lokalnych](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Ładowanie wewnętrzny adres URL aplikacji na serwerze łącznika | Na serwerze łącznika załadować wewnętrzny adres URL aplikacji. |
|8 | Sprawdź łączność z siecią wewnętrzną | Istnieje problem z łącznością w sieci wewnętrznej, ten przepływ debugowania nie jest w stanie do diagnozowania. Aplikacja musi być dostępny wewnętrznie dla łączników do pracy. Można włączyć i wyświetlić dzienniki zdarzeń łącznika, zgodnie z opisem w [łączników serwera Proxy aplikacji](application-proxy-connectors.md#under-the-hood). |
|9 | Wartość limitu czasu na zapleczu wydłużyć | W **dodatkowe ustawienia** dla aplikacji, należy zmienić **limit czasu aplikacji zaplecza** ustawienie **długie**. Zobacz [Dodawanie aplikacji lokalnych do usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Jeśli problemy będą się powtarzać, twórz problemów określonego przepływu, przeglądu aplikacji i logowanie Jednokrotne debugowanie przepływów | Użyj [problemy z aplikacją serwer Proxy debugowania aplikacji](application-proxy-debug-apps.md) Rozwiązywanie problemów z przepływem. |

## <a name="next-steps"></a>Kolejne kroki


* [Publikuj aplikacje w oddzielnych sieciach i miejsc za pomocą grupy łączników](application-proxy-connector-groups.md)
* [Praca z istniejących serwerów proxy w środowisku lokalnym](application-proxy-configure-connectors-with-proxy-servers.md)
* [Rozwiązywanie problemów z błędami serwera Proxy aplikacji i łączników](application-proxy-troubleshoot.md)
* [Jak dyskretnie zainstalować łącznik serwera Proxy aplikacji usługi Azure AD](application-proxy-register-connector-powershell.md)
