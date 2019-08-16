---
title: Zalecenia dotyczące maszyny wirtualnej w Azure Security Center | Microsoft Docs
description: W tym dokumencie opisano Azure Security Center zalecenia dotyczące ochrony maszyn wirtualnych i komputerów oraz aplikacji sieci Web i środowisk App Service.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: rkarlin
ms.openlocfilehash: 798e027ca611905766b1fb8bcdb89cba4aeaf9b2
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69531337"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Opis zaleceń dotyczących zasobów Azure Security Center


## <a name="recommendations"></a>Zalecenia
Poniższe tabele zawierają informacje ułatwiające zrozumienie dostępnych zaleceń dotyczących usług obliczeniowych i aplikacji oraz to, co każda z nich ma zastosowanie.

### <a name="computers"></a>Komputery
| Zalecenie | Opis |
| --- | --- |
| Włącz zbieranie danych dla subskrypcji|Zaleca włączenie zbierania danych w zasadach zabezpieczeń dla każdej subskrypcji i wszystkich maszyn wirtualnych w subskrypcjach. |
| Włącz szyfrowanie dla konta usługi Azure Storage| Zalecane jest włączenie usługi Azure szyfrowanie usługi Storage dla danych przechowywanych w spoczynku. Szyfrowanie usługi Storage (SSE) działa przez szyfrowanie danych podczas zapisywania ich w usłudze Azure Storage i odszyfrowywanie przed pobraniem. Funkcja SSE jest obecnie dostępna tylko dla Blob service platformy Azure i może być używana dla blokowych obiektów blob, stronicowych obiektów blob i dołączania obiektów BLOB. Aby dowiedzieć się więcej, zobacz [szyfrowanie usługi Storage dla danych przechowywanych w spoczynku](../storage/common/storage-service-encryption.md).</br>Funkcja SSE jest obsługiwana tylko na kontach magazynu Menedżer zasobów. Klasyczne konta magazynu nie są obecnie obsługiwane. Aby poznać modele wdrażania klasycznego i Menedżer zasobów, zobacz modele wdrażania platformy Azure. |
| Skoryguj konfiguracje zabezpieczeń|Zaleca dostosowanie konfiguracji systemu operacyjnego przy użyciu zalecanych reguł konfiguracji zabezpieczeń, np. nie pozwala na zapisywanie haseł. |
| Zastosuj aktualizacje systemu |Zaleca wdrożenie na maszynach wirtualnych brakujących zabezpieczeń systemu i aktualizacji krytycznych. |
| Stosowanie kontroli dostępu just in time do sieci| Zaleca zastosowanie just in Time do maszyny wirtualnej. Funkcja just in time jest w wersji zapoznawczej i jest dostępna w warstwie Standardowa Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center. |
| Uruchom ponownie po zaktualizowaniu systemu|Zaleca ponowne uruchomienie maszyny wirtualnej w celu ukończenia procesu stosowania aktualizacji systemu. |
| Zainstaluj program Endpoint Protection|Zaleca aprowizację programów chroniących przed złośliwym oprogramowaniem na maszynach wirtualnych (dotyczy tylko maszyn wirtualnych z systemem Windows). |
| Włącz agenta maszyny wirtualnej |Umożliwia zobaczenie, które maszyny wirtualne wymagają agenta maszyny wirtualnej. Agent maszyny wirtualnej musi być zainstalowany na maszynach wirtualnych, aby zapewnić aprowizację skanowania w poszukiwaniu poprawek, skanowania linii bazowej i programów chroniących przed złośliwym oprogramowaniem. Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu portalu Azure Marketplace. Informacje na temat instalowania agenta maszyny wirtualnej można znaleźć w artykule [Agent maszyny wirtualnej i rozszerzenia — część 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/). |
| Zastosuj szyfrowanie dysków|Zaleca szyfrowanie dysków maszyny wirtualnej przy użyciu usługi Azure Disk Encryption (maszyny wirtualne z systemami Windows i Linux). Szyfrowanie jest zalecane zarówno w przypadku woluminów z systemem operacyjnym, jak i z danymi maszyny wirtualnej. |
| Aktualizacja wersji systemu operacyjnego|Zalecane jest zaktualizowanie wersji systemu operacyjnego dla usługi w chmurze do najnowszej wersji dostępnej dla rodziny systemów operacyjnych.  Aby dowiedzieć się więcej na temat Cloud Services, zobacz Omówienie Cloud Services. |
| Funkcja oceny luk w zabezpieczeniach nie jest zainstalowana|Zaleca się zainstalowanie na maszynie wirtualnej rozwiązania do oceny luk w zabezpieczeniach. |
| Koryguj luki w zabezpieczeniach) |Umożliwia wyświetlenie luk w zabezpieczeniach systemu i aplikacji wykrytych przez rozwiązanie do oceny luk w zabezpieczeniach zainstalowane na maszynie wirtualnej. |

### App Services<a name="app-services"></a>
| Zalecenie | Opis |
| --- | --- |
| App Service powinna być dostępna tylko za pośrednictwem protokołu HTTPS | Zaleca się, aby ograniczyć dostęp do App Service tylko za pośrednictwem protokołu HTTPS. |
| Gniazda sieci Web powinny być wyłączone dla aplikacji sieci Web| Zalecane jest dokładne zapoznanie się z użyciem gniazd sieci Web w aplikacjach sieci Web.  Protokół Websocket jest narażony na różnego rodzaju zagrożenia bezpieczeństwa. |
| Użyj domen niestandardowych dla aplikacji sieci Web | Zalecane jest używanie domen niestandardowych do ochrony aplikacji sieci Web przed typowymi atakami, takimi jak wyłudzanie informacji, oraz innymi atakami związanymi z usługą DNS. |
| Konfiguruj ograniczenia adresów IP dla aplikacji sieci Web | Zaleca się zdefiniowanie listy adresów IP, które mogą uzyskiwać dostęp do aplikacji.  Użyj ograniczeń adresów IP chroni aplikację internetową przed typowymi atakami. |
| Nie zezwalaj na wszystkie ("*") zasobów uzyskiwanie dostępu do aplikacji | Zaleca się, aby nie ustawiać parametru WEBSITE_LOAD_CERTIFICATES " *". Ustawienie parametru na "* " oznacza, że wszystkie certyfikaty zostaną załadowane do osobistego magazynu certyfikatów aplikacji sieci Web.  Może to prowadzić do naruszenia zasady najmniejszych uprawnień, ponieważ jest mało prawdopodobne, że witryna musi mieć dostęp do wszystkich certyfikatów w czasie wykonywania. |
| Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji | Zaleca się, aby zezwalać na współpracującie z aplikacją sieci Web tylko wymaganych domen. Krzyżowe pochodzenia zasobów między źródłami (cors) nie powinien zezwalać na wszystkie domeny uzyskiwanie dostępu do sieci web aplikacji. |
| Użyj najnowszej obsługiwanej wersji systemu .NET Framework dla aplikacji sieci Web | Zaleca użycie najnowszej wersji .NET Framework dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację. |
| Użyj najnowszej obsługiwanej wersji środowiska Java dla aplikacji sieci Web | Zaleca użycie najnowszej wersji języka Java dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację. |
| Użyj najnowszej obsługiwanej wersji środowiska PHP dla aplikacji sieci Web | Zaleca użycie najnowszej wersji języka PHP dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację. |
| [Dodawanie zapory aplikacji internetowej](security-center-add-web-application-firewall.md) |Zalecane jest wdrożenie zapory aplikacji sieci Web (WAF) dla punktów końcowych sieci Web. Zalecenie WAF jest pokazane dla dowolnego publicznego adresu IP (na poziomie wystąpienia adresu IP lub IP ze zrównoważonym obciążeniem), który ma skojarzoną sieciową grupę zabezpieczeń z otwartymi portami sieci Web dla ruchu przychodzącego (80 443).</br></br>Security Center zaleca się, aby pomóc w obrony przed atakami na aplikacje sieci Web na maszynach wirtualnych i App Service Environment. App Service Environment (ASE) to opcja planu usługi [Premium](https://azure.microsoft.com/pricing/details/app-service/) systemu Azure App Service, która zapewnia w pełni izolowane i dedykowane środowisko do bezpiecznego uruchamiania aplikacji Azure App Service. Aby dowiedzieć się więcej na temat środowiska ASE, zapoznaj się z [dokumentacją App Service Environment](../app-service/environment/intro.md).</br></br>W Security Center można chronić wiele aplikacji sieci Web, dodając te aplikacje do istniejących wdrożeń WAF. |
| [Finalizowanie ochrony aplikacji](security-center-add-web-application-firewall.md#finalize-application-protection) |Aby ukończyć konfigurację WAF, ruch musi być kierowany do urządzenia WAF. Wykonanie tego zalecenia spowoduje zakończenie niezbędnych zmian konfiguracji. |
| Użyj najnowszej obsługiwanej wersji środowiska Node.js dla aplikacji sieci Web | Zaleca użycie najnowszej wersji środowiska Node. js dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację. |
| Mechanizm CORS nie powinien zezwalać na każdy zasób, dostęp do aplikacji funkcji | Zaleca się, aby zezwalać na współpracującie z aplikacją sieci Web tylko wymaganych domen. Krzyżowe origin resource sharing (CORS) nie powinien zezwalać na wszystkie domeny uzyskiwanie dostępu do funkcji aplikacji. |
| Użyj domen niestandardowych dla aplikacji funkcji | Zalecane jest używanie domen niestandardowych do ochrony aplikacji funkcji przed typowymi atakami, takimi jak wyłudzanie informacji, oraz innymi atakami związanymi z usługą DNS. |
| Konfiguruj ograniczenia adresów IP dla aplikacji funkcji | Zaleca się zdefiniowanie listy adresów IP, które mogą uzyskiwać dostęp do aplikacji. Użyj ograniczeń adresów IP chroni aplikację funkcji przed typowymi atakami. |
| Funkcja aplikacji powinny być dostępne tylko za pośrednictwem protokołu HTTPS | Zaleca się, aby ograniczyć dostęp do aplikacji funkcji tylko za pośrednictwem protokołu HTTPS. |
| Zdalne debugowanie powinno zostać wyłączone dla aplikacja funkcji | Zalecane jest wyłączenie debugowania dla aplikacja funkcji, jeśli nie trzeba już z niego korzystać. Zdalne debugowanie wymaga porty wejściowe były otwarte w aplikacji funkcji. |
| Gniazda sieci Web powinny być wyłączone dla aplikacji funkcji | Zalecane jest dokładne zapoznanie się z użyciem gniazd sieci Web w aplikacjach funkcji. Protokół Websocket jest narażony na różnego rodzaju zagrożenia bezpieczeństwa. |


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w usłudze Azure Security Center](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
