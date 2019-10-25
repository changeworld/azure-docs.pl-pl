---
title: Hostowanie kodu źródłowego aplikacji mobilnej w chmurze za pomocą usług GitHub i Azure DevOps
description: Dowiedz się więcej o usługach do hostowania kodu aplikacji mobilnej w chmurze za pomocą usług firmy Microsoft.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: f0af2ee5e62bbdbfb5d18cffc3e3ed62edec81f3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795629"
---
# <a name="cloud-hosted-source-code-management-services"></a>Usługi zarządzania kodem źródłowym hostowane w chmurze
Jeśli masz zespoły deweloperów, których wielu członków zespołu pracuje nad tą samą bazą kodu, musisz znaleźć odpowiednie miejsce do hostowania kodu. Przechowywanie danych w chmurze i posiadanie scentralizowanego repozytorium umożliwia wszystkim użytkownikom przekazywanie i edytowanie plików kodu oraz zarządzanie nimi, współpracujące z innymi deweloperami w projektach oraz umożliwia łatwe uzyskiwanie dostępu do kodu niezależnie od tego, gdzie się znajdują. mieć połączenie z Internetem.

Hosting w chmurze jest znacznie łatwiejszy niż w przypadku opcji lokalnych, ponieważ wymaga mniej konfiguracji sprzętowej i umożliwia organizacjom ukończenie procesu implementacji w bardziej Agile sposób.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Zalety hostingu kodu źródłowego w chmurze
- **Scentralizowany magazyn w chmurze** umożliwiający wyświetlanie danych i zarządzanie nimi z dowolnego miejsca.
- Zapewnia **lepszą współpracę i kod czyszczący** w zespołach, aby śledzić kod i zarządzać projektami w celu zapewnienia ciągłego dostarczania doskonałego oprogramowania.
- **Zyskaj udział** i ułatwiają **Współtworzenie** projektów.
- **Szybszy cykl wydawniczy** , ponieważ zespół może pracować szybciej i **łatwo współtworzyć** projekty.
- **Zmniejsz koszty** bez konieczności zajmowania się konserwacją własnego sprzętu, serwerów, sieci VPN i tak dalej.

Użyj następujących usług, aby hostować dane aplikacji w chmurze.

## <a name="github"></a>Witryna GitHub
[GitHub](https://github.com/) to usługa hostingu repozytorium typu "open source", która obsługuje projekty kodu źródłowego w różnych różnych językach programowania i śledzi różne zmiany wprowadzone w każdej iteracji.

**Najważniejsze funkcje**
- **Hosting kodu** ze wszystkimi kodem w jednym miejscu. Prywatne, publiczne lub Open Source, wszystkie repozytoria są wyposażone w narzędzia ułatwiające hostowanie, wersję i wydanie kodu.
- Bezproblemowe **Przegląd kodu** i wbudowane narzędzia do przeglądu ułatwiają przegląd kodu w ramach procesu tworzenia zespołów.
    - Ochrona gałęzi, proponowanie zmian i przeglądanie żądań.
    - Zapoznaj się z różnicą i komentarzem w kontekście i uzyskaj jasne informacje zwrotne.
- Koordynuj wczesne i niezrównane działania i Dowiedz się więcej dzięki **narzędziom do zarządzania projektami**w witrynie GitHub.
    - Zapoznaj się z dużym obrazem projektu.
    - **Tablice zadań** znajdujące się bezpośrednio obok kodu w usłudze GitHub.
    - **Przeciągnij i upuść karty** , aby umożliwić przypisywanie problemów lub żądań ściągnięcia do członków zespołu.
    - **Punkty kontrolne** do organizowania i śledzenia postępu.
    - **Uwagi** do przechwytywania pomysłów, które mogą być przydatne, ale nie należy do określonego problemu lub żądania ściągnięcia.
- Łatwo Odkrywaj i wybieraj odpowiednie narzędzia, aby zapewnić lepszą komunikację i automatyzację pracy, kupując aplikacje z **[witryny GitHub Marketplace](https://github.com/marketplace)** .
- **Zarządzanie zespołami** , aby łatwo zarządzać i rozwijać zespoły, **role użytkowników** ułatwiające organizowanie zespołów i uprawnień dostępu, tworzenie **wątków dyskusji** w celu zachowania konwersacji na temat i zespół skupiają się, a **wytyczne społeczności** umożliwiają szybkie ustawianie Nowi członkowie zespołu z kontem.
- **Przeglądaj** i **gwiazdy** popularne projekty, aby je obserwować.
- **Sieci i Dowiedz się** od innych osób w branży.

**Wołują**
- [GitHub](https://github.com/)
- [Przewodniki GitHub](https://guides.github.com/)
- [Forum społeczności GitHub](https://github.community/)
- [Portal GitHub Marketplace](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Usługa Azure DevOps](https://azure.microsoft.com/services/devops/) obsługuje [Azure Repos](https://azure.microsoft.com/services/devops/repos/) i [Kontrola wersji serwera Team Foundation (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) jako opcje kontroli źródła. Ma nieograniczone bezpłatne repozytoria prywatne z zespołowymi przeglądami kodu, zaawansowanym zarządzaniem plikami, wyszukiwaniem kodu i zasadami rozgałęzień w celu zapewnienia wysokiej jakości kodu. Azure Repos doskonale nadaje się dla małych projektów, a także dużych organizacji, które wymagają natywnej obsługi usługi AAD i zaawansowanych zasad.
    
**Najważniejsze funkcje**
- **Nieograniczone repozytorium kodu źródłowego usługi git hostowane w chmurze** dla repozytoriów publicznych i prywatnych
    - Obsługa dowolnego klienta git.
    - Elementy webhook i integracja z interfejsem API.
- Rozpocznij **następną kompilację** od żądania ściągnięcia repozytoriów
    - Współpracuj nad tworzeniem lepszego kodu przy użyciu dyskusji wielowątkowej i ciągłej integracji dla każdej zmiany.
    - Korzystając z usługi Azure Pipelines lub własnych narzędzi, skonfiguruj ciągłą integrację/ciągłe wdrażanie w taki sposób, aby kompilacje, testy i wdrożenia były automatycznie wyzwalane za pomocą każdego zakończonego żądania ściągnięcia.
    - Ochrona jakości kodu za pomocą zasad gałęzi.
- **Scentralizowany system kontroli wersji z Kontrola wersji serwera Team Foundation**, w tym przegląd kodu.
- **Nawiąż połączenie z kodem** za pomocą Xcode, zaćmienie, IntelliJ, Android Studio, Visual Studio, Visual Studio Code i innych.
- **Zaawansowane wyszukiwanie kodu semantycznego**.
- **Przedsiębiorstwo jest gotowe** , ponieważ ma natywną integrację z usługą Azure Active Directory (AD), upraszczając proces zarządzania dostępem do repozytoriów kodu.
- **Zapewnianie jakości kodu** za pomocą zasad gałęzi, takich jak minimalna liczba przeglądów kodu, wymaganie udanych kompilacji i wymuszanie strategii scalania git.
- **Połącz swoje ulubione środowisko programistyczne** , aby uzyskać dostęp do repozytoriów i zarządzać pracą.

**Wołują**
- [Wprowadzenie do Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Dokumentacja Azure Repos](/azure/devops/repos/?view=azure-devops)