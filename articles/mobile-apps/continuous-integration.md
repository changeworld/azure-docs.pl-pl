---
title: Automatyzacja cyklu życia aplikacji za pomocą programu Visual Studio App Center i usług platformy Azure
description: Dowiedz się więcej o usługach, takich jak Centrum aplikacji, które pomagają skonfigurować ciągłą kompilację i integrację aplikacji mobilnych.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240920"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatyzacja cyklu życia aplikacji dzięki ciągłej kompilacji i integracji

Jako deweloperzy piszesz kod i sprawdzasz go w repozytorium kodu, ale zatwierdzenia zaewidencjonowane w repozytorium mogą nie zawsze być spójne. Gdy wielu deweloperów pracuje nad tym samym projektem, problemy mogą pochodzić z integracji. Zespoły mogą napotkać sytuacje, w których rzeczy nie działają, błędy piętrzą się, a rozwój projektu zostanie opóźniony. Deweloperzy muszą czekać, aż cały kod oprogramowania zostanie zbudowany i przetestowany, aby sprawdzić, czy nie ma błędów, co sprawia, że proces jest powolny i mniej iteracyjny. 

Dzięki ciągłej kompilacji i integracji deweloperzy mogą uprościć kompilacje i przetestować swój kod, zatwierdzając ich zmiany w repozytorium kodu źródłowego i umieszczając testy i weryfikacje w środowisku kompilacji. W ten sposób są one zawsze uruchomione testy względem ich kodu. Wszystkie zmiany wprowadzone w kodzie źródłowym są tworzone w sposób ciągły, gdy istnieje zatwierdzenie wprowadzone do repozytorium. Przy każdym zaewidencjonowaniu serwer ciągłej integracji (CI) sprawdza poprawność i wykonuje każdy test utworzony przez dewelopera. Jeśli testy nie przejdą, kod zostanie odesłany w celu uzyskania dalszych zmian. W ten sposób deweloperzy nie rozbić kompilacje, które są tworzone. Nie muszą również uruchamiać wszystkich testów lokalnie na swoich komputerach, co zwiększa produktywność deweloperów. 

## <a name="key-benefits"></a>Najważniejsze korzyści
- Automatyzuj kompilacje, testy i wdrożenia dla potoków.
- Wykrywaj błędy i naprawiaj problemy wcześnie, aby zapewnić szybsze tempo uwalniania.
- Zatwierdzanie kodu częściej i tworzenie aplikacji szybko.
- Uzyskaj elastyczność, aby szybko zmienić kod bez żadnych problemów.
- Zyskaj szybszy czas na rynku, dzięki czemu tylko dobrej jakości kod sprawia, że przez całą drogę.
- Wprowadzać małe zmiany kodu bardziej efektywnie, ponieważ małe kawałki kodu są zintegrowane w tym czasie.
- Zwiększ przejrzystość i odpowiedzialność zespołu, aby otrzymywać ciągłe informacje zwrotne od klientów i zespołu.

Skorzystaj z następujących usług, aby włączyć potok ciągłej integracji w aplikacjach mobilnych.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[Kompilacja centrum aplikacji](/appcenter/build/) ułatwia tworzenie aplikacji natywnych i międzyplatformowych, nad którymi pracuje zespół przy użyciu bezpiecznej infrastruktury chmury. Możesz łatwo połączyć repozytorium w programie Visual Studio App Center i rozpocząć tworzenie aplikacji w chmurze przy każdym zatwierdzeniu. Nie musisz się martwić o konfigurowanie serwerów kompilacji lokalnie, skomplikowane konfiguracje i kod, który opiera się na komputerze współpracownika, ale nie twoje.

Dzięki dodatkowej mocy usług Visual Studio App Center można dodatkowo zautomatyzować przepływ pracy. Kompilacje można automatycznie zwolnić dla testerów i publicznych sklepów z aplikacjami za pomocą aplikacji App Center Distribute. Można również uruchomić zautomatyzowane testy interfejsu użytkownika na tysiącach rzeczywistych konfiguracji urządzeń i systemu operacyjnego w chmurze za pomocą testu Centrum aplikacji.

**Najważniejsze funkcje**
- Skonfiguruj ciągłą integrację w ciągu kilku minut i twórz aplikacje częściej i szybciej.
- Integracja z GitHub, BitBucket, Azure DevOps i GitLab.
- Twórz szybkie i bezpieczne kompilacje na zarządzanych komputerach hostowanych w chmurze.
- Włącz kompilacje, aby uruchomić test i sprawdź, czy aplikacja tworzy na rzeczywistych urządzeniach z systemem iOS i Android.
- Uzyskaj natywną i wieloplatformową obsługę systemów iOS, Android, macOS, Windows, Xamarin i React Native.
- Dostosuj kompilacje, dodając skrypty post-clone, pre-build i post-build.

**Odwołania**
- [Zarejestruj się w programie Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do kompilacji centrum aplikacji](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Usługa Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)— usługa w usłudze Azure DevOps — jest w pełni funkcjonalną usługą ciągłej integracji i ciągłego dostarczania (CD), która współpracuje z preferowanym dostawcą git. Można go wdrożyć do większości głównych usług w chmurze, która obejmuje platformę Azure. Możesz zacząć od kodu na GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud lub Azure Repos. Następnie można zautomatyzować kompilacji, testowania i wdrażania kodu na platformie Microsoft Azure, Google Cloud Platform lub Amazon Web Services (AWS).

**Najważniejsze funkcje**
- **Uproszczone środowisko oparte na zadaniach konfigurowania serwera ciągłej integracji:** Skonfiguruj serwer ciągłej integracji dla aplikacji mobilnych natywnych (Android, iOS i Windows) i międzyplatformowych (Xamarin, Cordova i React Native), oprócz technologii serwerowych firmy Microsoft i innych firm (Node.js, Java).
- **Dowolny język, platforma i chmura:** Twórz, testuj i wdrażaj aplikacje Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android i iOS. Działaj równolegle w systemach Linux, macOS i Windows. Wdrażanie u dostawców usług w chmurze, takich jak Azure, AWS i Google Cloud Platform. Rozpowszechniaj aplikacje mobilne za pośrednictwem kanałów beta i sklepów z aplikacjami.
- **Obsługa kontenerów natywnych:** Łatwo twórz nowe kontenery i wypychaj je do dowolnego rejestru. Wdrażanie kontenerów do niezależnych hostów lub kubernetes.
- **Zaawansowane przepływy pracy:** Łatwe tworzenie łańcuchów kompilacji i wielofazowych kompilacji. Uzyskaj obsługę YAML, integracji testów, bramek wydania, raportowania i innych.
- **Rozszerzalny:** Korzystaj z zakresu zadań kompilacji, testowania i wdrażania utworzonych przez społeczność, które obejmują setki rozszerzeń od Slack do SonarCloud. Można nawet wdrożyć z innych systemów ciągłej integracji, takich jak Jenkins. Haki internetowe i interfejsy API REST mogą pomóc w integracji.
- **Bezpłatne kompilacje hostowane w chmurze:** Kompilacje te są dostępne dla publicznych i prywatnych repozytoriów.
- **Obsługa wdrażania u innych dostawców chmury:** Dostawcy obejmują AWS i Google Cloud Platform.

**Odwołania**
- [Przewodnik Wprowadzenie do usługi Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Rozpoczynanie pracy z usługą Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Szybki start](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Aby ułatwić wybór odpowiedniej usługi dla kompilacji aplikacji, zobacz artykuł, który porównuje [kompilację centrum aplikacji a potoki platformy Azure.](/appcenter/build/choose-between-services)
