---
title: Zalecenia dotyczące maszyny wirtualnej w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie wyjaśniono zaleceń Centrum zabezpieczeń Azure, jak pomóc w ochronie maszyn wirtualnych i komputerów i aplikacji sieci web i środowisk usługi App Service.
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
ms.openlocfilehash: a4aaf440856746895a31914aeee2bddec2ce23f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544996"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Omówienie zaleceń dotyczących zasobów usługi Azure Security Center


## <a name="recommendations"></a>Zalecenia
Użyj poniższe tabele jako źródła, aby lepiej zrozumieć dostępne zasoby obliczeniowe i usługi App services zalecenia i każdej z nich działanie w przypadku zastosowania.

### <a name="computers"></a>Komputery
| Zalecenie | Opis |
| --- | --- |
| [Włącz zbieranie danych dla subskrypcji](security-center-enable-data-collection.md) |Zaleca włączenie zbierania danych w zasadach zabezpieczeń dla każdej subskrypcji i wszystkich maszyn wirtualnych w subskrypcjach. |
| [Włączanie szyfrowania dla konta usługi Azure Storage](security-center-enable-encryption-for-storage-account.md) | Zaleca się, zostanie włączone szyfrowanie usługi Azure Storage dla danych magazynowanych. Szyfrowanie usługi Storage (SSE) działa przez szyfrowanie danych podczas są zapisywane do magazynu platformy Azure i odszyfrowuje przed pobierania. SSE jest obecnie dostępna tylko w przypadku usługi Azure Blob service i może służyć do blokowych obiektów blob, stronicowe obiekty BLOB i uzupełnialnych obiektów blob. Aby dowiedzieć się więcej, zobacz [szyfrowanie usługi Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md).</br>Funkcja SSE jest obsługiwana tylko na kontach magazynu usługi Resource Manager. Klasyczne konta magazynu nie są obecnie obsługiwane. Aby poznać klasyczny, jak i modelem wdrażania usługi Resource Manager, zobacz [modele wdrażania Azure](../azure-classic-rm.md). |
| [Korygowanie konfiguracji zabezpieczeń](security-center-remediate-os-vulnerabilities.md) |Zaleca dostosowanie konfiguracji systemu operacyjnego za pomocą reguł konfiguracji zabezpieczeń, np. nie zezwalaj na zapisywanie haseł. |
| [Zastosuj aktualizacje systemu](security-center-apply-system-updates.md) |Zaleca wdrożenie na maszynach wirtualnych brakujących zabezpieczeń systemu i aktualizacji krytycznych. |
| [Zastosuj Just-In-Time kontrolę dostępu do sieci](security-center-just-in-time.md) | Zaleca się zastosowanie dostęp dokładnie na czas maszyny Wirtualnej. Po prostu w czasie funkcja jest dostępna w wersji zapoznawczej i dostępne w warstwie standardowa usługi Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center. |
| [Uruchom ponownie po zaktualizowaniu systemu](security-center-apply-system-updates.md#reboot-after-system-updates) |Zaleca ponowne uruchomienie maszyny wirtualnej w celu ukończenia procesu stosowania aktualizacji systemu. |
| [Zainstaluj punkt końcowy](security-center-install-endpoint-protection.md) |Zaleca aprowizację programów chroniących przed złośliwym oprogramowaniem na maszynach wirtualnych (dotyczy tylko maszyn wirtualnych z systemem Windows). |
| [Włącz agenta maszyny wirtualnej](security-center-enable-vm-agent.md) |Umożliwia zobaczenie, które maszyny wirtualne wymagają agenta maszyny wirtualnej. Agent maszyny wirtualnej musi być zainstalowany na maszynach wirtualnych, aby zapewnić aprowizację skanowania w poszukiwaniu poprawek, skanowania linii bazowej i programów chroniących przed złośliwym oprogramowaniem. Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu portalu Azure Marketplace. Informacje na temat instalowania agenta maszyny wirtualnej można znaleźć w artykule [Agent maszyny wirtualnej i rozszerzenia — część 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/). |
| [Zastosuj szyfrowanie dysków](security-center-apply-disk-encryption.md) |Zaleca szyfrowanie dysków maszyny wirtualnej przy użyciu usługi Azure Disk Encryption (maszyny wirtualne z systemami Windows i Linux). Szyfrowanie jest zalecane zarówno w przypadku woluminów z systemem operacyjnym, jak i z danymi maszyny wirtualnej. |
| [Aktualizacja wersji systemu operacyjnego](security-center-update-os-version.md) |Zaleca się, zaktualizować wersję systemu operacyjnego (OS) dla usługi w chmurze do najnowszej wersji dostępnych dla rodziny systemów operacyjnych.  Aby dowiedzieć się więcej o usługach Cloud Services, zobacz [Omówienie usług w chmurze](../cloud-services/cloud-services-choose-me.md). |
| [Funkcja oceny luk w zabezpieczeniach nie jest zainstalowana](security-center-vulnerability-assessment-recommendations.md) |Zaleca się zainstalowanie na maszynie wirtualnej rozwiązania do oceny luk w zabezpieczeniach. |
| [Korygowanie luk w zabezpieczeniach](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Umożliwia wyświetlenie luk w zabezpieczeniach systemu i aplikacji wykrytych przez rozwiązanie do oceny luk w zabezpieczeniach zainstalowane na maszynie wirtualnej. |

### Usługi aplikacji <a name="app-services"></a>
| Zalecenie | Opis |
| --- | --- |
| Usługa App Service powinny być dostępne tylko za pośrednictwem protokołu HTTPS | Zaleca się ograniczenie dostępu do usługi App Service przy użyciu protokołu HTTPS tylko. |
| Gniazda sieci Web powinny być wyłączone dla aplikacji sieci Web| Zaleca się dokładnie przejrzyj użycie gniazda sieci Web w aplikacjach sieci web.  Protokół Websocket jest narażony na różnego rodzaju zagrożenia bezpieczeństwa. |
| Użyj domen niestandardowych dla aplikacji sieci Web | Zaleca się, użyj domen niestandardowych do ochrony aplikacji sieci web przed typowymi atakami, takimi jak wyłudzanie informacji i inne ataki związane z usługą DNS. |
| Konfiguruj ograniczenia adresów IP dla aplikacji sieci Web | Zaleca się, że definiowanie listy adresów IP, które mogą uzyskać dostęp do Twojej aplikacji.  Użyj ograniczeń adresów IP chroni aplikację internetową przed typowymi atakami. |
| Nie zezwalaj na wszystkie ("*") zasobów uzyskiwanie dostępu do aplikacji | Zaleca się, że nie należy ustawiać parametru WEBSITE_LOAD_CERTIFICATES na "*". Ustawienie dla parametru "*" oznacza, że wszystkie certyfikaty zostaną załadowane do magazynu certyfikatów osobiste aplikacje sieci web.  Może to prowadzić do naruszenia zasady najmniejszych uprawnień, ponieważ jest mało prawdopodobne, że witryna musi mieć dostęp do wszystkich certyfikatów w czasie wykonywania. |
| Mechanizm CORS nie powinien zezwalać na każdy zasób, uzyskiwanie dostępu do aplikacji | Zaleca się, aby zezwolić tylko wymagane domen do interakcji z aplikacją sieci web. Krzyżowe pochodzenia zasobów między źródłami (cors) nie powinien zezwalać na wszystkie domeny uzyskiwanie dostępu do sieci web aplikacji. |
| Użyj najnowszej obsługiwanej wersji systemu .NET Framework dla aplikacji sieci Web | Zaleca się, że używasz najnowszej wersji .NET Framework dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację. |
| Użyj najnowszej obsługiwanej wersji środowiska Java dla aplikacji sieci Web | Zaleca się, że używasz najnowszej wersji języka Java dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację. |
| Użyj najnowszej obsługiwanej wersji środowiska PHP dla aplikacji sieci Web | Zaleca się, że używasz najnowszej wersji języka PHP dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację. |
| [Dodawanie zapory aplikacji internetowej](security-center-add-web-application-firewall.md) |Zaleca wdrożenie zapory aplikacji sieci web (WAF) dla punktów końcowych sieci web. Dla wszelkich publicznych umożliwiający dostęp do Internetu adresu IP (adres IP na poziomie wystąpienia lub adres IP ze zrównoważonym obciążeniem) zawierającej sieciową grupę zabezpieczeń skojarzoną z portami Otwórz przychodzącego ruchu internetowego (80,443) jest wyświetlane zalecenie zapory aplikacji sieci Web.</br></br>Usługa Security Center zaleca obsługi administracyjnej zapory aplikacji sieci Web, aby pomóc Ci chronić przed atakami przeznaczone dla aplikacji sieci web na maszynach wirtualnych i w środowisku App Service Environment. App Service Environment (ASE) jest [Premium](https://azure.microsoft.com/pricing/details/app-service/) usługi opcja planu usługi Azure App Service udostępniająca w pełni izolowane, dedykowane środowisko do bezpiecznego uruchamiania aplikacji w usłudze Azure App Service. Aby dowiedzieć się więcej na temat środowiska ASE, zobacz [dokumentacja środowiska usługi App Service](../app-service/environment/intro.md).</br></br>Możesz chronić wiele aplikacji sieci web w usłudze Security Center, dodając te aplikacje do istniejących wdrożeń zapory aplikacji sieci Web. |
| [Finalizowanie ochrony aplikacji](security-center-add-web-application-firewall.md#finalize-application-protection) |Aby ukończyć konfigurację zapory aplikacji sieci Web, ruch musi zostać skierowany do urządzenia zapory aplikacji sieci Web. Tym zaleceniem kończy zmiany wymagane ustawienia. |
| Użyj najnowszej obsługiwanej wersji środowiska Node.js dla aplikacji sieci Web | Zaleca się, że używasz najnowszej wersji środowiska Node.js dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację. |
| Mechanizm CORS nie powinien zezwalać na każdy zasób, dostęp do aplikacji funkcji | Zaleca się, aby zezwolić tylko wymagane domen do interakcji z aplikacją sieci web. Krzyżowe origin resource sharing (CORS) nie powinien zezwalać na wszystkie domeny uzyskiwanie dostępu do funkcji aplikacji. |
| Użyj domen niestandardowych dla aplikacji funkcji | Zaleca się, użyj domen niestandardowych do ochrony aplikacji funkcji przed typowymi atakami, takimi jak wyłudzanie informacji i inne ataki związane z usługą DNS. |
| Skonfiguruj ograniczenia adresów IP dla aplikacji funkcji | Zaleca się, że definiowanie listy adresów IP, które mogą uzyskać dostęp do Twojej aplikacji. Użyj ograniczeń adresów IP chroni aplikację funkcji przed typowymi atakami. |
| Funkcja aplikacji powinny być dostępne tylko za pośrednictwem protokołu HTTPS | Zaleca się ograniczenie dostępu do aplikacji funkcji przy użyciu protokołu HTTPS tylko. |
| Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji | Zaleca się wyłączyć debugowania dla aplikacji funkcji, jeśli nie potrzebujesz już go używać. Zdalne debugowanie wymaga porty wejściowe były otwarte w aplikacji funkcji. |
| Gniazda sieci Web powinny być wyłączone dla aplikacji funkcji | Zaleca się dokładnie przejrzyj użycie gniazda sieci Web w obrębie aplikacji funkcji. Protokół Websocket jest narażony na różnego rodzaju zagrożenia bezpieczeństwa. |


## <a name="next-steps"></a>Kolejne kroki
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
