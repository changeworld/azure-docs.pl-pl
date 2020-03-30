---
title: Zasady i zgodność firmy w laboratoriach DevTest w usłudze Azure
description: Ten artykuł zawiera wskazówki dotyczące zasad firmy i zgodności infrastruktury platformy Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 81ca2a90b1940d70e170cab3f8d18144a5d5e5a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74560502"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Zarządzanie infrastrukturą laboratoriów azure devtest — zasady i zgodność z przepisami firmy
Ten artykuł zawiera wskazówki dotyczące zasad firmy i zgodności infrastruktury platformy Azure DevTest Labs. 

## <a name="public-vs-private-artifact-repository"></a>Publiczne i prywatne repozytorium artefaktów

### <a name="question"></a>Pytanie
Kiedy organizacja powinna używać publicznego repozytorium artefaktów w porównaniu z prywatnym repozytorium artefaktów w laboratorium DevTest?

### <a name="answer"></a>Odpowiedź
[Publiczne repozytorium artefaktów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) zawiera początkowy zestaw pakietów oprogramowania, które są najczęściej używane. Pomaga w szybkim wdrażaniu bez konieczności inwestowania czasu na odtworzenie wspólnych narzędzi deweloperskich i dodatków. Można wdrożyć własne repozytorium prywatne. Można korzystać z repozytorium publicznego i prywatnego równolegle. Można również wyłączyć repozytorium publiczne. Kryteria wdrażania repozytorium prywatnego powinny być oparte na następujących pytaniach i uwagach:

- Czy organizacja ma wymóg posiadania licencjonowanego oprogramowania firmowego w ramach oferty DevTest Labs? Jeśli odpowiedź jest twierdząca, należy utworzyć repozytorium prywatne.
- Czy organizacja opracowuje niestandardowe oprogramowanie, które zapewnia określoną operację, która jest wymagana w ramach ogólnego procesu inicjowania obsługi administracyjnej? Jeśli odpowiedź jest twierdząca, należy wdrożyć repozytorium prywatne.
- Jeśli zasady ładu organizacji wymagają izolacji, a repozytoria zewnętrzne nie są zarządzane przez organizację bezpośrednio, należy wdrożyć prywatne repozytorium artefaktów. W ramach tego procesu początkowa kopia publicznego repozytorium może być kopiowana i integrowana z repozytorium prywatnym. Następnie publiczne repozytorium można wyłączyć, aby nikt w organizacji nie miał już do niego dostępu. Takie podejście wymusza wszystkich użytkowników w organizacji, aby mieć tylko jedno repozytorium, który jest zatwierdzony przez organizację i zminimalizować dryf konfiguracji.

### <a name="single-repository-or-multiple-repositories"></a>Pojedyncze repozytorium lub wiele repozytoriów 

### <a name="question"></a>Pytanie
Czy plan organizacyjny dla pojedynczego repozytorium lub zezwolić na wiele repozytoriów?

### <a name="answer"></a>Odpowiedź
W ramach ogólnej strategii zarządzania i zarządzania konfiguracją w organizacji zaleca się użycie scentralizowanego repozytorium. Gdy używasz wielu repozytoriów, mogą one stać się silosami niezarządzanego oprogramowania w czasie. Za pomocą centralnego repozytorium wiele zespołów może korzystać z artefaktów z tego repozytorium dla swoich projektów. Wymusza standaryzacji, bezpieczeństwa, łatwość zarządzania i eliminuje powielanie wysiłków. W ramach centralizacji zalecane są następujące działania w zakresie długoterminowego zarządzania i zrównoważonego rozwoju:

- Skojarz repozytorium platformy Azure z tą samą dzierżawą usługi Azure Active Directory, z którą korzysta subskrypcja platformy Azure do uwierzytelniania i autoryzacji.
- Utwórz grupę o nazwie **Wszystkie deweloperów laboratoriów DevTest** w usłudze Azure Active Directory, która jest zarządzana centralnie. Każdy deweloper, który przyczynia się do tworzenia artefaktów powinny być umieszczone w tej grupie.
- Tej samej grupy usługi Azure Active Directory można użyć do zapewnienia dostępu do repozytorium repozytorium repozytorium usługi Azure i do laboratorium.
- W repozytorium platformy Azure rozgałęzianie lub rozwidanie powinny być używane do oddzielnego repozytorium w rozwoju od podstawowego repozytorium produkcji. Zawartość jest dodawana tylko do gałęzi głównej z żądaniem ściągnięcia po dokonaniu prawidłowego przeglądu kodu. Gdy recenzent kodu zatwierdzi zmianę, główny deweloper, który jest odpowiedzialny za konserwację gałęzi głównej, scala zaktualizowany kod. 

## <a name="corporate-security-policies"></a>Zasady bezpieczeństwa firmy

### <a name="question"></a>Pytanie
W jaki sposób organizacja może zapewnić, że zasady zabezpieczeń firmy są w miejscu?

### <a name="answer"></a>Odpowiedź
Organizacja może to osiągnąć, wykonując następujące czynności:

1. Opracowanie i opublikowanie kompleksowej polityki bezpieczeństwa. Zasady określa zasady dopuszczalnego użytkowania związane z oprogramowaniem, zasobami w chmurze. Określa również, co wyraźnie narusza zasady. 
2. Opracowanie niestandardowego obrazu, artefaktów niestandardowych i procesu wdrażania, który umożliwia aranżację w obrębie obszaru zabezpieczeń zdefiniowanego za pomocą usługi Active Directory. Takie podejście wymusza granice firmy i ustawia wspólny zestaw kontroli środowiska. Te formanty środowiska dewelopera można wziąć pod uwagę, jak opracowują i postępuj zgodnie z bezpiecznym cyklem życia rozwoju w ramach ich ogólnego procesu. Celem jest również stworzenie środowiska, które nie jest zbyt restrykcyjne, co może utrudniać rozwój, ale rozsądny zestaw kontroli. Zasady grupy w jednostce organizacyjnej (OU), która zawiera maszyny wirtualne w laboratorium może być podzbiorem zasad grupy całkowitej, które znajdują się w środowisku produkcyjnym. Alternatywnie mogą one być dodatkowym zestawem, aby właściwie ograniczyć wszelkie zidentyfikowane zagrożenia.

## <a name="data-integrity"></a>Integralność danych

### <a name="question"></a>Pytanie
W jaki sposób organizacja może zapewnić integralność danych, aby zapewnić, że deweloperzy zajmujący się zdalnym tworzeniem danych nie mogą usunąć kodu ani wprowadzić złośliwego oprogramowania lub niezatwierdzonego oprogramowania?

### <a name="answer"></a>Odpowiedź
Istnieje kilka warstw kontroli w celu złagodzenia zagrożenia ze strony zewnętrznych konsultantów, wykonawców lub pracowników, którzy są komunikacji zdalnej w celu współpracy w DevTest Labs. 

Jak wspomniano wcześniej, pierwszy krok musi mieć zasady dopuszczalnego użytkowania opracowane i zdefiniowane, które jasno określają konsekwencje, gdy ktoś narusza zasady. 

Pierwszą warstwą formantów dostępu zdalnego jest zastosowanie zasad dostępu zdalnego za pośrednictwem połączenia sieci VPN, które nie jest bezpośrednio połączone z laboratorium. 

Druga warstwa formantów polega na zastosowaniu zestawu obiektów zasad grupy, które uniemożliwiają kopiowanie i wklejanie za pośrednictwem pulpitu zdalnego. Można zaimplementować zasady sieciowe, aby nie zezwalać na korzystanie ze środowiska usług wychodzących, takich jak usługi FTP i RDP. Routing zdefiniowany przez użytkownika może wymusić cały ruch sieciowy platformy Azure z powrotem do lokalnego, ale routing nie może uwzględniać wszystkich adresów URL, które mogą zezwalać na przekazywanie danych, chyba że jest kontrolowana za pośrednictwem serwera proxy do skanowania zawartości i sesji. Publiczne adresy IP mogą być ograniczone w sieci wirtualnej obsługującej DevTest Labs, aby nie zezwalać na mostkowanie zewnętrznego zasobu sieciowego.

Ostatecznie ten sam rodzaj ograniczeń musi być stosowany w całej organizacji, która musiałaby również uwzględniać wszystkie możliwe metody nośników wymiennych lub zewnętrznych adresów URL, które mogłyby zaakceptować wpis treści. Skonsultuj się ze swoim specjalistą w dziedzinie zabezpieczeń, aby przejrzeć i wdrożyć zasady zabezpieczeń. Aby uzyskać więcej zaleceń, zobacz [Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Następne kroki
Zobacz [Migracja i integracja aplikacji](devtest-lab-guidance-governance-application-migration-integration.md).
