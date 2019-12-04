---
title: Azure Security Center zalecenia dotyczące maszyn & aplikacji
description: Zalecenia dotyczące zabezpieczeń Azure Security Center, które pomagają chronić maszyny wirtualne, komputery, aplikacje sieci Web i środowiska App Service.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2019
ms.author: memildin
ms.openlocfilehash: c0cf5951daf004a0c805b688f08d1ccfa4679615
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782166"
---
# <a name="compute-and-app-recommendations---reference-guide"></a>Zalecenia dotyczące obliczeń i aplikacji — Przewodnik referencyjny

Ten artykuł zawiera pełną listę zaleceń, które mogą być widoczne w Azure Security Center dotyczących następujących typów zasobów:

* Maszyny wirtualne i komputery
* VM Scale Sets
* Cloud Services
* Usługi aplikacji
* Containers
* Zasoby obliczeniowe

Aby dowiedzieć się, jak je znaleźć i jak rozwiązać, zobacz [tutaj](security-center-virtual-machine-protection.md).

## Zalecenia dotyczące obliczeń i aplikacji<a name="compute-and-app-recs"></a>
|Typ zasobu|Wskaźnik bezpieczeństwa|Zalecenie|Opis|
|----|----|----|----|
|Usługa App Service|20|Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Ogranicz dostęp do aplikacji sieci Web tylko za pośrednictwem protokołu HTTPS.|
|Usługa App Service|20|aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Ogranicz dostęp do aplikacji funkcji tylko za pośrednictwem protokołu HTTPS.|
|Usługa App Service|5|Dzienniki diagnostyczne w App Services powinny być włączone|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Usługa App Service|10|Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web|Wyłącz debugowanie aplikacji sieci Web, jeśli nie potrzebujesz już jej używać. Zdalne debugowanie wymaga otwarcia portów przychodzących na aplikacja funkcji.|
|Usługa App Service|10|Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji|Wyłącz debugowanie dla aplikacja funkcji, jeśli nie potrzebujesz już go używać. Zdalne debugowanie wymaga otwarcia portów przychodzących na aplikacja funkcji.|
|Usługa App Service|10|Nie Zezwalaj wszystkim zasobom ("*") na dostęp do aplikacji| Nie Zezwalaj na zestaw WEBSITE_LOAD_CERTIFICATES parametru "". Ustawienie parametru na "" oznacza, że wszystkie certyfikaty są ładowane do osobistego magazynu certyfikatów aplikacji sieci Web. Może to prowadzić do nadużycia zasady najniższych uprawnień, ponieważ jest mało prawdopodobne, że lokacja musi mieć dostęp do wszystkich certyfikatów w czasie wykonywania.|
|Usługa App Service|20|Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web|Zezwalaj na współpracującie z aplikacją sieci Web tylko wymaganych domen. Udostępnianie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji sieci Web.|
|Usługa App Service|20|Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacja funkcji| Zezwalaj na współdziałanie z aplikacją funkcji tylko dla wymaganych domen. Współużytkowanie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji funkcji.|
|Zasoby obliczeniowe (Batch)|1|Reguły alertów metryk powinny być skonfigurowane na kontach wsadowych|Skonfiguruj reguły alertów metryki na koncie wsadowym i Włącz opcję Usuń wszystkie zdarzenia i Usuń zdarzenia uruchamiania puli metryk|
|Zasoby obliczeniowe (Usługa Service Fabric)|10|W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów|Uwierzytelnianie klienta jest wykonywane tylko za pośrednictwem Azure Active Directory w Service Fabric.|
|Zasoby obliczeniowe (konto usługi Automation)|5|Zmienne konta usługi Automation powinny być szyfrowane|Włącz szyfrowanie zasobów zmiennych konta usługi Automation podczas przechowywania poufnych danych.|
|Zasoby obliczeniowe (wyszukiwanie)|5|Inspekcja włączenia dzienników diagnostycznych dla usług wyszukiwania|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Zasoby obliczeniowe (Magistrala usług)|5|Dzienniki diagnostyczne w Service Bus powinny być włączone|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Zasoby obliczeniowe (Stream Analytics)|5|Dzienniki diagnostyczne w Azure Stream Analytics powinny być włączone|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Zasoby obliczeniowe (Batch)|5|Włączanie dzienników diagnostycznych na kontach wsadowych|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Zasoby obliczeniowe (centrum zdarzeń)|5|Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Zasoby obliczeniowe (Aplikacje logiki)|5|Włączanie dzienników diagnostycznych w Logic Apps|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Zasoby obliczeniowe (Usługa Service Fabric)|15|Ustaw właściwość ClusterProtectionLevel na EncryptAndSign w Service Fabric|Service Fabric zapewnia trzy poziomy ochrony (brak, podpisywanie i EncryptAndSign) w przypadku komunikacji między węzłami przy użyciu podstawowego certyfikatu klastra. Ustaw poziom ochrony, aby upewnić się, że wszystkie komunikaty między węzłami są szyfrowane i podpisane cyfrowo. |
|Zasoby obliczeniowe (Magistrala usług)|1|Usuń wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey z przestrzeni nazw Service Bus |Klienci Service Bus nie powinni używać zasad dostępu na poziomie przestrzeni nazw, które zapewniają dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby dostosować model zabezpieczeń najniższych uprawnień, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp tylko do określonej jednostki.|
|Zasoby obliczeniowe (centrum zdarzeń)|1|Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw centrum zdarzeń|Klienci centrum zdarzeń nie powinni używać zasad dostępu na poziomie przestrzeni nazw, które zapewniają dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby dostosować model zabezpieczeń najniższych uprawnień, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp tylko do określonej jednostki.|
|Zasoby obliczeniowe (centrum zdarzeń)|5|Należy zdefiniować reguły autoryzacji w jednostce centrum zdarzeń|Przeprowadź inspekcję reguł autoryzacji w jednostce centrum zdarzeń, aby udzielić dostępu z najwyższymi uprawnieniami.|
|Maszyna|50|Zainstaluj agenta monitorowania na swoich maszynach|Zainstaluj agenta monitorowania, aby włączyć zbieranie danych, skanowanie aktualizacji, skanowanie linii bazowej i program Endpoint Protection na poszczególnych komputerach.|
|Maszyna|50|Włącz automatyczną obsługę administracyjną i zbieranie danych dla subskrypcji |Włącz automatyczne Inicjowanie obsługi i zbieranie danych dla maszyn w Twoich subskrypcjach, aby umożliwić zbieranie danych, skanowanie aktualizacji, skanowanie linii bazowej i program Endpoint Protection na każdej maszynie, która została dodana do subskrypcji.|
|Maszyna|40|Rozwiązywanie problemów z kondycją agenta monitorowania na maszynach|Aby uzyskać pełną ochronę Security Center, należy rozwiązać problemy z agentem monitorowania na swoich maszynach, postępując zgodnie z instrukcjami w przewodniku rozwiązywania problemów.| 
|Maszyna|40|Rozwiązywanie problemów z kondycją programu Endpoint Protection na maszynach|Aby uzyskać pełną ochronę Security Center, należy rozwiązać problemy z agentem monitorowania na swoich maszynach, postępując zgodnie z instrukcjami w przewodniku rozwiązywania problemów.|
|Maszyna|40|Rozwiązywanie problemów z brakującymi danymi skanowania na maszynach|Rozwiązywanie problemów z brakującymi danymi skanowania na maszynach wirtualnych i komputerach. Brak danych skanowania na swoich maszynach powoduje brak oceny zabezpieczeń, takich jak skanowanie aktualizacji, skanowanie linii bazowej i brak skanowania rozwiązań Endpoint Protection.|
|Maszyna|40|Aktualizacje systemu powinny być zainstalowane na maszynach|Instalowanie brakujących zabezpieczeń systemu i aktualizacji krytycznych w celu zabezpieczania maszyn wirtualnych i komputerów z systemami Windows i Linux
|Maszyna|15|Dodawanie zapory aplikacji internetowej| Wdróż rozwiązanie zapory aplikacji sieci Web (WAF), aby zabezpieczyć aplikacje sieci Web. |
|Maszyna|40|Aktualizowanie wersji systemu operacyjnego dla ról usługi w chmurze|Zaktualizuj wersję systemu operacyjnego dla ról usługi w chmurze do najnowszej wersji dostępnej dla rodziny systemów operacyjnych.|
|Maszyna|35|Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować|Koryguj luki w zabezpieczeniach na maszynach w celu ochrony przed atakami.|
|Maszyna|35|Koryguj luki w zabezpieczeniach w konfiguracjach w kontenerach|Koryguj luki w zabezpieczeniach w konfiguracji zabezpieczeń na maszynach z zainstalowanym systemem Docker, aby chronić je przed atakami.|
|Maszyna|25|Włącz adaptacyjne kontrolki aplikacji|Włącz sterowanie aplikacjami, aby kontrolować, które aplikacje mogą być uruchamiane na maszynach wirtualnych znajdujących się na platformie Azure. Ułatwi to ochronę maszyn wirtualnych przed złośliwym oprogramowaniem. Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na poszczególnych maszynach wirtualnych i ułatwia stosowanie reguł zezwalania przy użyciu tej analizy. Ta funkcja upraszcza proces konfigurowania i konserwowania reguł zezwalania aplikacji.|
|Maszyna|20|Zainstaluj rozwiązanie Endpoint Protection na swoich maszynach|Zainstaluj rozwiązanie Endpoint Protection na maszynach wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach.|
|Maszyna|20|Uruchom ponownie maszyny, aby zastosować aktualizacje systemu|Uruchom ponownie maszyny, aby zastosować aktualizacje systemu i zabezpieczyć maszynę przed lukami w zabezpieczeniach.|
|Maszyna|15|Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych|Szyfruj dyski maszyn wirtualnych przy użyciu Azure Disk Encryption obu dla maszyn wirtualnych z systemami Windows i Linux. Azure Disk Encryption (ADE) korzysta z standardowej funkcji funkcji BitLocker systemu Windows i funkcji DM-Crypt w systemie Linux, aby zapewnić szyfrowanie dysków systemu operacyjnego i danych w celu zapewnienia lepszej ochrony i ochrony danych oraz zapewnienia bezpieczeństwa i zgodności w organizacji. zobowiązania w magazynie kluczy platformy Azure. Jeśli wymagania dotyczące zgodności i zabezpieczeń wymagają szyfrowania danych końcowych przy użyciu kluczy szyfrowania, w tym szyfrowania tymczasowego dysku z załączonym lokalnie, Użyj usługi Azure Disk Encryption. Alternatywnie domyślnie Managed Disks są szyfrowane domyślnie przy użyciu usługi Azure szyfrowanie usługi Storage, gdzie klucze szyfrowania są kluczami zarządzanymi przez firmę Microsoft na platformie Azure. Jeśli spełnia wymagania związane ze zgodnością i zabezpieczeniami, można użyć domyślnego szyfrowania dysków zarządzanych do spełnienia wymagań.|
|Maszyna|30|Instalowanie rozwiązania do oceny luk w zabezpieczeniach na maszynach wirtualnych|Instalowanie rozwiązania do oceny luk w zabezpieczeniach na maszynach wirtualnych|
|Maszyna|15|Dodawanie zapory aplikacji internetowej| Wdróż rozwiązanie zapory aplikacji sieci Web (WAF), aby zabezpieczyć aplikacje sieci Web. |
|Maszyna|30|Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach|Maszyny wirtualne, dla których wdrożono rozwiązanie innej firmy do oceny luk w zabezpieczeniach, są stale oceniane pod kątem luk w zabezpieczeniach aplikacji i systemu operacyjnego. Po każdym znalezieniu tych luk w zabezpieczeniach można uzyskać więcej informacji w ramach zalecenia.|
|Maszyna|30|Instalowanie rozwiązania do oceny luk w zabezpieczeniach na maszynach wirtualnych|Instalowanie rozwiązania do oceny luk w zabezpieczeniach na maszynach wirtualnych|
|Maszyna|1|Maszyny wirtualne powinny być migrowane do nowych zasobów AzureRM|Użyj Azure Resource Manager dla maszyn wirtualnych, aby zapewnić lepsze zabezpieczenia, takie jak: silniejsza kontrola dostępu (RBAC), lepsza Inspekcja, wdrażanie i zarządzanie oparte na Menedżer zasobówach, dostęp do zarządzanych tożsamości, dostęp do magazynu kluczy dla wpisów tajnych, Uwierzytelnianie oparte na usłudze Azure AD i obsługa tagów i grup zasobów w celu łatwiejszego zarządzania zabezpieczeniami. |
|Maszyna|30|Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach|Maszyny wirtualne, dla których wdrożono rozwiązanie innej firmy do oceny luk w zabezpieczeniach, są stale oceniane pod kątem luk w zabezpieczeniach aplikacji i systemu operacyjnego. Po każdym znalezieniu tych luk w zabezpieczeniach można uzyskać więcej informacji w ramach zalecenia.|
|Zestaw skalowania maszyn wirtualnych |4|Dzienniki diagnostyczne w Virtual Machine Scale Sets powinny być włączone|Włącz dzienniki i zachowaj je przez maksymalnie rok. Dzięki temu można odtworzyć ślady aktywności do celów dochodzeniowych. Jest to przydatne w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Zestaw skalowania maszyn wirtualnych|35|Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować|Koryguj luki w zabezpieczeniach w konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |
|Zestaw skalowania maszyn wirtualnych|5|Koryguj błędy kondycji programu Endpoint Protection w zestawach skalowania maszyn wirtualnych|Skoryguj błędy kondycji programu Endpoint Protection w zestawach skalowania maszyn wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach. |
|Zestaw skalowania maszyn wirtualnych|10|Program Endpoint Protection powinien być zainstalowany na maszynach wirtualnych|Zainstaluj rozwiązanie Endpoint Protection w zestawach skalowania maszyn wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach. |
|Zestaw skalowania maszyn wirtualnych|40|Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych|Zainstaluj brakujące zabezpieczenia systemu i aktualizacje krytyczne, aby zabezpieczyć zestawy skalowania maszyn wirtualnych z systemami Windows i Linux. |
|


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w Azure Security Center](security-center-sql-service-recommendations.md)
