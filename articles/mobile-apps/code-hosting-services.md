---
title: Hostuj kod źródłowy aplikacji mobilnej w chmurze za pomocą gitHub i usługi Azure DevOps
description: Dowiedz się więcej o usługach hostowanych kodu aplikacji mobilnej w chmurze za pomocą usług firmy Microsoft.
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 66d8980ab53010af0703d789fbe791c60a32052d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240958"
---
# <a name="cloud-hosted-source-code-management-services"></a>Usługi zarządzania kodami źródłowymi hostowanymi w chmurze
Jeśli masz zespoły programistyczne z wieloma członkami zespołu, którzy pracują na tej samej podstawie kodu, musisz znaleźć odpowiednie miejsce do hosta kodu. Przechowywanie danych w chmurze i posiadanie scentralizowanego repozytorium umożliwia wszystkim przekazywanie, edytowanie i zarządzanie plikami kodu. Mogą również wchodzić w interakcje z innymi deweloperami w projektach. Kod może być łatwo dostępny bez względu na to, gdzie jesteś, o ile masz połączenie z Internetem.

Hosting w chmurze jest znacznie łatwiejszy niż opcje lokalne. Wymaga mniej konfiguracji sprzętu i umożliwia organizacjom ukończenie procesu implementacji w bardziej elastyczny sposób.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Zalety hostingu kodu źródłowego w chmurze
- **Scentralizowane przechowywanie w chmurze:** Wyświetlaj dane i zarządzaj nimi z dowolnego miejsca.
- **Lepsza współpraca i czystszy kod:** Śledź kod w zespołach i zarządzaj projektami, aby zapewnić ciągłą dostawę doskonałego oprogramowania.
- **Łatwiej się zaangażować:** Łatwo wnosić wkład w swoje projekty.
- **Szybszy cykl uwalniania:** Pracuj szybciej w swoich zespołach i łatwo wnosisz wkład w swoje projekty.
- **Obniż koszty:** Nie martw się o utrzymanie własnego sprzętu, serwerów, VPN i tak dalej.

Skorzystaj z następujących usług do hosta danych aplikacji w chmurze.

## <a name="github"></a>GitHub
[GitHub](https://github.com/) to usługa hostingowa repozytorium typu open source, która obsługuje projekty kodu źródłowego w różnych językach programowania. GitHub śledzi różne zmiany wprowadzone w każdej iteracji.

**Najważniejsze funkcje**
- Użyj hostingu kodu, aby zachować cały kod w jednym miejscu. Repozytoria prywatne, publiczne i typu open source są wyposażone w narzędzia ułatwiające hostowanie, przechowywanie wersji i kodu wydania.
- Przejrzyj kod i użyj wbudowanych narzędzi do przeglądania, aby przegląd kodu był istotną częścią procesu dowolnego zespołu:
    - Chroń gałęzie, proponuj zmiany i żądaj opinii.
    - Wykryć różnice, komentować w kontekście i uzyskać jasne opinie.
- Koordynuj wcześnie, bądź wyrównany i rzuć więcej pracy dzięki narzędziom do zarządzania projektami w usłudze GitHub:
    - Zobacz ogólny obraz projektu.
    - Użyj tablic zadań, które znajdują się tuż obok kodu w usłudze GitHub.
    - Przeciągnij karty, aby przypisać problemy lub wycofać żądania do członków zespołu.
    - Ustaw punkty kontrolne, aby organizować i śledzić postęp.
    - Napisz notatki, aby przechwycić pomysły, które mogą być przydatne, ale nie należą do określonego problemu lub żądania ściągnięcia.
- Łatwo odkrywaj i wybieraj odpowiednie narzędzia do lepszej komunikacji i automatyzacji pracy, kupując aplikacje z [GitHub Marketplace.](https://github.com/marketplace)
- Zarządzanie zespołami i rozwijanie ich za pomocą: 
    - Role użytkowników ułatwiające organizowanie zespołów i uprawnienia dostępu.
    - Narzędzia wątku dyskusji, aby utrzymać konwersacje na temat i zespół koncentruje.
    - Wytyczne dla społeczności, aby szybko skonfigurować nowych członków zespołu z kontem.
- Przeglądaj i gwiazdy popularnych projektów do naśladowania.
- Sieci i uczyć się od innych w branży.

**Odwołania**
- [GitHub](https://github.com/)
- [Przewodniki gitHub](https://guides.github.com/)
- [Forum społeczności GitHub](https://github.community/)
- [Rynek GitHub](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Usługa Azure DevOps](https://azure.microsoft.com/services/devops/) obsługuje [usługi Azure Repos](https://azure.microsoft.com/services/devops/repos/) i [Team Foundation Version Control (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) jako opcje kontroli źródła. Ma nieograniczoną liczbę bezpłatnych prywatnych repozytoriów z przeglądami kodu współpracy, zaawansowanym zarządzaniem plikami, wyszukiwaniem kodu i zasadami oddziału, aby zapewnić wysokiej jakości kod. Repozytorium platformy Azure doskonale sprawdza się w przypadku małych projektów i dużych organizacji, które wymagają natywnej obsługi usługi Azure Active Directory i zaawansowanych zasad.
    
**Najważniejsze funkcje**
- Użyj nieograniczonego repozytorium kodu źródłowego Git hostowanego w chmurze dla publicznych i prywatnych repo:
    - Uzyskaj wsparcie dla dowolnego klienta Git.
    - Użyj haków internetowych i integracji interfejsu API.
- Rozpocznij następną kompilację od żądania ściągnięcia repozytorium:
    - Współpracuj, aby utworzyć lepszy kod przy użyciu wątkowej dyskusji i ciągłej integracji dla każdej zmiany.
    - Skonfiguruj ciągłą integrację/ciągłe dostarczanie (CI/CD), aby automatycznie wyzwalać kompilacje, testy i wdrożenia przy każdym zakończonym żądaniu ściągania. Można użyć usługi Azure Potoki lub narzędzia.
    - Chroń jakość kodu za pomocą zasad gałęzi.
- Obsługa scentralizowanej kontroli wersji za pomocą kontroli wersji programu Team Foundation, która zawiera przegląd kodu.
- Połącz się z kodem przy użyciu xcode, Eclipse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code i innych.
- Użyj zaawansowanego wyszukiwania kodu semantycznego.
- Korzystaj z funkcji gotowych do pracy w przedsiębiorstwie. Usługa Azure DevOps ma natywną integrację z usługą Azure Active Directory, co upraszcza proces zarządzania dostępem do repozytoriów kodu.
- Zapewnij jakość kodu za pomocą zasad gałęzi, takich jak minimalna liczba przeglądów kodu, wymagania dotyczące udanych kompilacji i wymuszanie strategii scalania Git.
- Połącz swoje ulubione środowisko programistyczne, aby uzyskać dostęp do repo i zarządzać pracą.

**Odwołania**
- [Wprowadzenie do usługi Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Dokumentacja usługi Azure Repos](/azure/devops/repos/?view=azure-devops)