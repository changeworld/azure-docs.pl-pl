---
title: Automatyzuj cykl życia aplikacji dzięki Visual Studio App Center i usługom platformy Azure
description: Dowiedz się więcej na temat usług, takich jak App Center, które pomagają skonfigurować ciągłą kompilację i integrację dla aplikacji mobilnych.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 07e8e0edc4638c6c0bf9acc205175aa731c0f8bc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795551"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatyzuj cykl życia aplikacji dzięki ciągłej kompilacji i integracji

Deweloperzy mogą napisać kod i zaewidencjonować go do repozytorium kodu, ale zatwierdzenia, które zaewidencjonowano w repozytorium, nie zawsze są spójne. Dzięki wielu deweloperom pracującym nad tym samym projektem, problemy są rozwiązane z integracją, a zespół mógłby działać w sytuacjach, gdy nie działają, błędy piling się, a rozwój projektu jest opóźniony. Deweloperzy muszą czekać, aż cały kod oprogramowania zostanie skompilowany i przetestowany w celu sprawdzenia pod kątem błędów, co sprawia, że proces jest powolny i mniej iteracyjny. 

Dzięki **ciągłej kompilacji i integracji**deweloperzy mogą uprościć Kompilowanie i testowanie kodu przez nie tylko zatwierdzanie zmian w repozytorium kodu źródłowego, ale także umieszczanie testów i weryfikacji w środowisku kompilacji, aby były zawsze uruchamiane testuje kod. Wszystkie zmiany wprowadzone w kodzie źródłowym są ciągle kompilowane zawsze wtedy, gdy istnieje zatwierdzenie dokonane w repozytorium. Po każdym zaewidencjonowaniu serwer CI sprawdza poprawność i wykonuje każdy test utworzony przez dewelopera. Jeśli testy nie zachodzą, kod jest wysyłany ponownie w celu wprowadzenia dalszych zmian. Dzięki temu deweloper nie może rozbić utworzonych kompilacji, a także nie uruchamiać wszystkich testów lokalnie na komputerze, co z kolei zwiększa produktywność deweloperów. 

## <a name="key-benefits"></a>Najważniejsze korzyści
- **Zautomatyzowany** potok kompilowania, testowania i wdrażania.
- Szybko **wykrywaj usterki i naprawiaj problemy,** aby zapewnić szybsze kursy wydania.
- **Zatwierdź kod** częściej i szybko Twórz aplikacje.
- **Elastyczność** umożliwiająca szybkie zmienianie kodu bez jakichkolwiek problemów.
- Skrócenie **czasu wprowadzenia na rynek** gwarantuje, że tylko kod dobrej jakości będzie cały czas.
- **Niewielkie zmiany kodu** , ponieważ umożliwiają one integrację małych fragmentów kodu w tym samym czasie.
- **Zwiększenie przejrzystości zespołu i odpowiedzialności** pozwala uzyskać **ciągłą opinię** nie tylko od klientów, ale również Twojego zespołu.

Użyj następujących usług, aby włączyć potok ciągłej integracji w aplikacjach mobilnych.

## <a name="visual-studio-app-center"></a>Centrum aplikacji programu Visual Studio
Usługa [App Center Build](/appcenter/build/) pomaga tworzyć aplikacje natywne i Międzyplatformowe, nad którymi pracuje zespół, przy użyciu bezpiecznej infrastruktury chmurowej. Możesz łatwo połączyć swoje repozytorium w App Center i rozpocząć Kompilowanie aplikacji w chmurze na każdym zatwierdzeniu, bez konieczności zajmowania się konfiguracją serwerów kompilacji lokalnie, skomplikowane konfiguracje i kod, który kompiluje się na komputerze współpracownika, ale nie.

Dzięki dodaniu mocy usług App Center można dodatkowo zautomatyzować przepływ pracy. Automatycznie wydawanie kompilacji dla testerów i publicznych sklepów z aplikacjami za pomocą App Center dystrybucji lub uruchamiania zautomatyzowanych testów interfejsu użytkownika na tysiącach rzeczywistych konfiguracji urządzeń i systemów operacyjnych w chmurze z testowaniem App Center.

**Najważniejsze funkcje**
- **Skonfiguruj ciągłą integrację** w kilka minut i szybciej Twórz aplikacje.
- Integruj się z usługami **GitHub, BitBucket, Azure DevOps i GitLab**.
- **Szybkie i bezpieczne kompilacje** na zarządzanych maszynach hostowanych w chmurze.
- **Zezwól na kompilacje w celu "uruchomienia testu"** i sprawdź, czy aplikacja jest oparta na rzeczywistych urządzeniach z systemami iOS i Android.
- **Obsługa natywnych i międzyplatformowych** — iOS, Android, MacOS, Windows, Xamarin, reagowanie na natywne.
- **Dostosuj kompilacje** , dodając skrypty po kompilacji, które są wykonywane przed kompilacją i po kompilacji.

**Wołują**
- [Zarejestruj się w usłudze App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do kompilacji App Center](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), usługa na platformie Azure DevOps, to w pełni funkcjonalne usługi ciągłej integracji (ci) i ciągłe dostarczanie (CD), które współdziałają z preferowanym dostawcą usługi git, i mogą wdrożyć w większości najważniejszych usług w chmurze, w tym na platformie Azure. Możesz zacząć od kodu w usłudze GitHub, w witrynie GitHub Enterprise Server, GitLab, BitBucket Cloud lub Azure Repos. Następnie można zautomatyzować kompilowanie, testowanie i wdrażanie kodu w celu Microsoft Azure, Google Cloud Platform lub Amazon Web Services.

**Najważniejsze funkcje**
- Uproszczone środowisko oparte na zadaniach służące do konfigurowania serwera CI dla aplikacji mobilnych zarówno natywnych (Android, iOS i Windows), jak i dla wielu platform (platformy Xamarin, Cordova i reakcji natywnych), a także do serwera z systemem Microsoft i innym niż Microsoft (Node. js, Java) informacyjn.
- **Wszystkie aplikacje języka, platformy i chmury, które są używane** do kompilowania, testowania i wdrażania aplikacji node. js, Python, Java, php, RubyC++, C#go, C/,, Android i iOS. Uruchamiaj zadania równolegle w systemach Linux, macOS i Windows. Wdrażaj do dostawców chmury, takich jak Azure, AWS i GCP. Dystrybuuj aplikacje mobilne za poorednictwem kanałów beta i sklepów z aplikacjami.
- **Obsługa kontenerów natywnych** — tworzenie nowych kontenerów z łatwością i wypychanie ich do dowolnego rejestru. Wdróż kontenery na niezależnych hostach lub Kubernetes.
- **Zaawansowane przepływy pracy** — proste tworzenie łańcucha i wieloetapowe kompilacje. Obsługa YAML, integracja z testowaniem, bramy wydań, raportowanie i inne informacje.
- **Rozszerzalne** — korzystaj z wielu zadań kompilowania, testowania i wdrażania utworzonych przez społeczność — setki rozszerzeń od zapasu do SonarCloud. Możesz nawet wdrażać z innych systemów CI, takich jak Jenkins, i korzystać z elementów webhook i interfejsów API REST, aby ułatwić integrację
- **Bezpłatne kompilacje hostowane w chmurze** dla repozytoriów publicznych i prywatnych.
- **Obsługuje wdrażanie do innych dostawców chmury** , takich jak Amazon Web Services, Google Cloud Platform itp.

**Wołują**
- [Wprowadzenie do przewodnika Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Rozpoczynanie pracy z usługą Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Przewodniki Szybki start](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

W celu wybrania odpowiedniej usługi dla kompilacji aplikacji postępuj zgodnie z artykułem, który porównuje [App Center kompilacja a Azure Pipelines](/appcenter/build/choose-between-services).
