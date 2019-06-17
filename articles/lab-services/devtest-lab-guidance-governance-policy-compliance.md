---
title: Zarządzanie infrastrukturą usługi Azure DevTest Labs
description: Ten artykuł zawiera wskazówki dotyczące ładu infrastruktury usługi Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e02400ef940efdf42370fbdc1da75bdc7062a8ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127365"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Zarządzanie infrastruktury usługi Azure DevTest Labs — firmowe zasady i zgodność
Ten artykuł zawiera wskazówki dotyczące dotyczących zasad firmowych i zgodności w zakresie infrastruktury usługi Azure DevTest Labs. 

## <a name="public-vs-private-artifact-repository"></a>Publiczne, a repozytorium artefaktów prywatne

### <a name="question"></a>Pytanie
Gdy organizacja należy używać publiczne repozytorium artefaktów i repozytorium artefaktów prywatne w usłudze DevTest Labs?

### <a name="answer"></a>Odpowiedź
[Publiczne repozytorium artefaktów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) zapewnia początkowy zestaw pakietów oprogramowania, które są najczęściej używane. Pomaga przy użyciu szybkiego wdrażania bez konieczności inwestowania czasu do odtworzenia popularnych narzędzi dla deweloperów i dodatków. Można wdrożyć własne repozytorium prywatnego. Można użyć publicznego i prywatnego repozytorium równolegle. Można również wyłączyć repozytorium publicznego. Odpowiednie kryteria, aby wdrożyć repozytorium prywatnego powinien opierać się następujące pytania i uwagi:

- Czy organizacja ma wymagane są firmowych oprogramowania licencjonowanego w ramach swojej oferty usługi DevTest Labs? Jeśli odpowiedź jest twierdząca, powinny być tworzone w repozytorium prywatnym.
- Organizacji mogą tworzyć niestandardowe oprogramowanie, które udostępnia określoną operację, która jest wymagana całego procesu inicjowania obsługi administracyjnej? Jeśli odpowiedź jest twierdząca, powinny być wdrażane w repozytorium prywatnym.
- Jeśli zasady ładu organizacji wymaga izolacji i zewnętrznych repozytoriów nie znajdują się w konfiguracji usługi bezpośrednie zarządzanie przez organizację, powinny być wdrażane repozytorium prywatnego artefaktu. W ramach tego procesu tworzy początkową kopię repozytorium publicznego można skopiować i zintegrowane z prywatnym repozytorium. Następnie publicznego repozytorium można wyłączyć tak, aby nikt w organizacji będą mogli go dłużej. Ta metoda wymusza wszyscy użytkownicy w organizacji mieli tylko jednego repozytorium, które zostanie zaakceptowana przez organizację i zminimalizować nieaktualną konfigurację.

### <a name="single-repository-or-multiple-repositories"></a>Pojedyncze repozytorium lub wieloma repozytoriami 

### <a name="question"></a>Pytanie
Należy organizacji planowanie jednym repozytorium lub zezwolić na wiele repozytoriów

### <a name="answer"></a>Odpowiedź
Jako część ogólnej nadzoru Twojej organizacji i strategii zarządzania konfiguracji zaleca się, że używasz scentralizowane repozytorium. Korzystając z wieloma repozytoriami, może zostać silosów niezarządzanych oprogramowania wraz z upływem czasu. Z centralnym repozytorium wielu zespołów można skorzystać z artefaktów z tego repozytorium do swoich projektów. Wymusza normalizacji, zabezpieczenia i łatwość zarządzania i eliminuje dublowania wysiłków. W ramach centralnego wskazówki dotyczące zarządzania długoterminowego i dbałość zaleca się następujące akcje:

- Z tą samą dzierżawą usługi Azure Active Directory, używanej subskrypcji platformy Azure do uwierzytelniania i autoryzacji, należy skojarzyć repozytoriów platformy Azure.
- Utwórz grupę o nazwie **wszystkim deweloperom laboratoria DevTest** w usłudze Azure Active Directory, które jest zarządzane centralnie. Każdy deweloper, który przyczynia się do rozwoju artefaktu będzie umieszczona w tej grupie.
- Tej samej grupy usługi Azure Active Directory mogą służyć do zapewnienia dostępu do repozytorium Azure repozytoriów i laboratorium.
- W repozytoriach usługi Azure tworzenia rozgałęzień lub rozwidlenia powinny służyć do repozytorium oddzielne w rozwoju z repozytorium produkcji podstawowej. Zawartość jest dodawana tylko do gałęzi głównej, przy użyciu żądania ściągnięcia, po dokonaniu przeglądu prawidłowego kodu. Gdy recenzent kodu zatwierdza zmiany, główny programista, który jest odpowiedzialny za konserwację gałęzi głównej, scala zaktualizowany kod. 

## <a name="corporate-security-policies"></a>Zasady zabezpieczeń firmy

### <a name="question"></a>Pytanie
Jak organizacja upewnij się, że zasady zabezpieczeń firmy znajdują się w miejscu?

### <a name="answer"></a>Odpowiedź
Organizacja może osiągnąć go, wykonując następujące czynności:

1. Tworzenie i publikowanie zasad kompleksowych zabezpieczeń. Zasady articulates zasady dopuszczalnych działań związanych z korzystaniem z oprogramowania, zasobów w chmurze. Definiuje również, jakie wyraźnie narusza zasady. 
2. Tworzenie niestandardowego obrazu, niestandardowe artefakty i procesem wdrażania, która służy do aranżacji w obrębie obszaru zabezpieczeń, która jest zdefiniowana za pomocą usługi active directory. Ta metoda wymusza granic firmowych i ustawia wspólny zbiór kontroli środowiska. Te kontrolki w środowisku deweloper może wziąć pod uwagę ich opracowanie i przestrzeganie Security development lifecycle jako część ich całego procesu. Celem jest również zapewnić środowisko, które nie jest zbyt restrykcyjne ten może utrudniać rozwoju, ale uzasadnione zbiór kontrolek. Zasady grupy w jednostce organizacyjnej (OU), która zawiera maszyny wirtualne laboratorium może być podzestawem zasad grupy całkowitej, które znajdują się w środowisku produkcyjnym. Alternatywnie można je dodatkowego zestawu poprawnie eliminowania wszelkich zidentyfikowanych.

## <a name="data-integrity"></a>Integralność danych

### <a name="question"></a>Pytanie
Jak organizacja zapewnienia integralności danych, aby upewnić się, że deweloperzy usług zdalnych nie można usunąć kodu ani wprowadzenia złośliwego oprogramowania lub oprogramowania niezatwierdzone

### <a name="answer"></a>Odpowiedź
Istnieje kilka warstw kontroli w celu ograniczenia zagrożenia od konsultantów zewnętrznych, Zleceniobiorcami ani pracownicy, którzy są remoting w do współpracy w usłudze DevTest Labs. 

Jak wspomniano wcześniej, pierwszym krokiem musi mieć zasady dopuszczalnego użytkowania przygotowanego i zdefiniowane, która wyraźnie wskazuje konsekwencje, gdy ktoś narusza zasady. 

Pierwszą warstwę kontroli dostępu zdalnego polega na zastosowaniu zasad dostępu zdalnego przez połączenie sieci VPN, który nie jest podłączony bezpośrednio do laboratorium. 

Druga warstwa formantów polega na zastosowaniu zestaw obiektów zasad grupy, które zapobiec kopiowania i wklejania za pośrednictwem pulpitu zdalnego. Zasady sieci implementacji nie zezwalać na połączenia wychodzące usługi ze środowiska, takie jak FTP i usługi RDP poza środowiskiem. Routingu zdefiniowanego przez użytkownika może wymusić na cały ruch sieciowy platformy Azure do środowiska lokalnego, ale routingu mogą nie uwzględniać wszystkich adresów URL, które mogą zezwalać na przekazywanie danych, o ile nie jest kontrolowany za pośrednictwem serwera proxy w celu skanowania zawartości i sesji. Publiczne adresy IP może być ograniczona w sieci wirtualnej, obsługa DevTest Labs nie zezwalać na łączenie zasobu sieci zewnętrznej.

Ostatecznie ten sam typ ograniczenia musi zostać zastosowana w całej organizacji, który musi również uwzględnić wszystkie możliwe metody nośnika wymiennego lub zewnętrzne adresy URL, które mogłyby zaakceptować wpis zawartości. Zapoznaj się z bezpieczeństwo professional, aby przejrzeć i zaimplementować zasady zabezpieczeń. Aby uzyskać więcej zaleceń, zobacz [Microsoft Security Cybernetycznymi](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Kolejne kroki
Zobacz [migrację aplikacji i integracja](devtest-lab-guidance-governance-application-migration-integration.md).
