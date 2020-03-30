---
title: Łączniki serwera proxy aplikacji debugowania — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Problemy z debugowaniem łączników serwera proxy aplikacji usługi Azure Active Directory (Azure AD).
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
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72311850"
---
# <a name="debug-application-proxy-connector-issues"></a>Problemy z debugowaniem łącznika serwera proxy aplikacji 

Ten artykuł ułatwia rozwiązywanie problemów z łącznikami serwera proxy aplikacji usługi Azure Active Directory (Azure AD). Jeśli używasz usługi Proxy aplikacji do zdalnego dostępu do lokalnej aplikacji sieci web, ale masz problemy z połączeniem się z aplikacją, użyj tego schematu blokowego do debugowania problemów ze łącznikiem. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że zainstalowano łącznik serwera proxy aplikacji i występuje problem. Podczas rozwiązywania problemów z serwerem proxy aplikacji zaleca się rozpoczęcie od tego przepływu rozwiązywania problemów, aby ustalić, czy łączniki serwera proxy aplikacji są poprawnie skonfigurowane. Jeśli nadal występują problemy z połączeniem się z aplikacją, postępuj zgodnie z przepływem rozwiązywania problemów z [aplikacjami serwera proxy aplikacji debugowania.](application-proxy-debug-apps.md)  


Aby uzyskać więcej informacji na temat serwera proxy aplikacji i korzystania z jego łączników, zobacz:

- [Zdalny dostęp do aplikacji lokalnych za pośrednictwem serwera proxy aplikacji](application-proxy.md)
- [Złącza serwera proxy aplikacji](application-proxy-connectors.md)
- [Instalowanie i rejestrowanie łącznika](application-proxy-add-on-premises-application.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji i komunikatów o błędach](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Schemat blokowy dla problemów z łącznikiem

Ten schemat blokowy przeprowadzi Cię przez kroki debugowania niektórych bardziej typowych problemów ze łącznikiem. Aby uzyskać szczegółowe informacje na temat każdego kroku, zobacz tabelę po schematze blokowym.

![Schemat blokowy przedstawiający kroki debugowania łącznika](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Akcja | Opis | 
|---------|---------|---------|
|1 | Znajdowanie grupy łączników przypisanej do aplikacji | Prawdopodobnie masz zainstalowane złącze na wielu serwerach, w którym to przypadku łączniki powinny być [przypisane do grup łączników](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Aby dowiedzieć się więcej o grupach łączników, zobacz [Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach przy użyciu grup łączników](application-proxy-connector-groups.md). |
|2 | Zainstaluj złącze i przypisz grupę | Jeśli nie masz zainstalowanego łącznika, zobacz [Instalowanie i rejestrowanie łącznika](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Jeśli występują problemy z instalacją łącznika, zobacz [Problem z instalacją łącznika](application-proxy-connector-installation-problem.md).<br></br> Jeśli łącznik nie jest przypisany do grupy, zobacz [Przypisywanie łącznika do grupy](application-proxy-connector-groups.md#create-connector-groups).<br></br>Jeśli aplikacja nie jest przypisana do grupy łączników, zobacz [Przypisywanie aplikacji do grupy łączników](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Uruchamianie testu portu na serwerze łączników | Na serwerze łączników uruchom test portu za pomocą [usługi telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) lub innego narzędzia do testowania portów, aby sprawdzić, czy porty 443 i 80 są otwarte.|
|4 | Konfigurowanie domen i portów | [Upewnij się, że domeny i porty są poprawnie skonfigurowane](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Aby łącznik działał poprawnie, istnieją pewne porty, które muszą być otwarte, i adresy URL, do których serwer musi mieć dostęp. |
|5 | Sprawdzanie, czy używany jest serwer proxy zaplecza | Sprawdź, czy łączniki używają serwerów proxy zaplecza lub ich pomijają. Aby uzyskać szczegółowe informacje, zobacz [Rozwiązywanie problemów z serwerem proxy łącznika i problemów z łącznością usługi](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Aktualizowanie łącznika i aktualatora w celu użycia serwera proxy zaplecza | Jeśli używany jest serwer proxy zaplecza, należy upewnić się, że łącznik używa tego samego serwera proxy. Aby uzyskać szczegółowe informacje na temat rozwiązywania problemów i konfigurowania łączników do pracy z serwerami proxy, zobacz [Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Załaduj wewnętrzny adres URL aplikacji na serwer łącznika | Na serwerze łączników załaduj wewnętrzny adres URL aplikacji. |
|8 | Sprawdzanie wewnętrznej łączności sieciowej | Istnieje problem z łącznością w sieci wewnętrznej, że ten przepływ debugowania nie jest w stanie zdiagnozować. Aplikacja musi być dostępna wewnętrznie, aby łączniki działały. Dzienniki zdarzeń łącznika można włączać i wyświetlać zgodnie z opisem w [łącznikach serwera proxy aplikacji](application-proxy-connectors.md#under-the-hood). |
|9 | Wydłużyć wartość przesuwu na zapleczu | W **ustawieniach dodatkowych** dla aplikacji zmień ustawienie **Limit czasu aplikacji wewnętrznej bazy** danych na **Długi**. Zobacz [Dodawanie aplikacji lokalnej do usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Jeśli problemy będą się powtarzać, docelowych określonych problemów z przepływem, przejrzyj przepływ debugowania aplikacji i usługi SSO | Użyj problemu z rozwiązywaniem [problemów z aplikacjami serwera proxy aplikacji debugowania.](application-proxy-debug-apps.md) |

## <a name="next-steps"></a>Następne kroki


* [Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach przy użyciu grup łączników](application-proxy-connector-groups.md)
* [Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md)
* [Rozwiązywanie problemów z błędami serwera proxy i łączników aplikacji](application-proxy-troubleshoot.md)
* [Jak dyskretnie zainstalować łącznik serwera proxy aplikacji usługi Azure AD](application-proxy-register-connector-powershell.md)
