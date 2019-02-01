---
title: Azure weryfikacji stosu jako usługa z kluczowych pojęć | Dokumentacja firmy Microsoft
description: W tym artykule opisano podstawowe pojęcia dotyczące usługi Azure Stack weryfikacji jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/07/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1f5c47dd3453c0c8f02f1b0a87e5f2fff123f8be
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242811"
---
# <a name="validation-as-a-service-key-concepts"></a>Sprawdzanie poprawności jako kluczowe pojęcia dotyczące usługi

W tym artykule opisano podstawowe pojęcia dotyczące weryfikacji jako usługa (VaaS).

## <a name="solutions"></a>Rozwiązania

Rozwiązanie VaaS reprezentuje rozwiązanie usługi Azure Stack z konkretnego sprzętu zestawienie komponentów (BoM). Rozwiązanie VaaS działa jako kontener dla przepływów pracy, które uruchamiać rozwiązania usługi Azure Stack.

### <a name="create-a-solution-in-the-vaas-portal"></a>Tworzenie rozwiązania w portalu VaaS

1. Zaloguj się do [VaaS portal](https://azurestackvalidation.com).
2. Na pulpicie nawigacyjnym rozwiązania wybierz **nowe rozwiązanie**.
3. Wprowadź nazwę dla rozwiązania. Nazewnictwa sugestii, zobacz [konwencje nazewnictwa dla rozwiązania VaaS](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Wybierz **Zapisz** do utworzenia rozwiązania.

## <a name="workflows"></a>Przepływy

Przepływ pracy VaaS działa w kontekście rozwiązania VaaS. Reprezentuje zbiór zestawów testów, które korzystają z funkcji do wdrożenia usługi Azure Stack. Przepływ pracy, należy utworzyć dla każdego wdrożenia lub aktualizacji oprogramowania rozwiązania usługi Azure Stack.

Przepływy pracy są pogrupowane według typu scenariusz testowania. Podczas testowania nieoficjalny **przebiegu testu** przepływu pracy pozwala wybrać testy z wszystkie dostępne zabezpieczenia VaaS. Podczas testowania oficjalne **weryfikacji** przepływy pracy docelowych określonych scenariuszy testowania wybranych przez firmę Microsoft.

![Kafelki przepływu pracy VaaS](media/tile_all-workflows.png)

> [!NOTE]
> **Sprawdzania poprawności rozwiązań** przepływu pracy aktualnie obsługuje dwa scenariusze: [Sprawdzanie poprawności pakietów OEM](azure-stack-vaas-validate-oem-package.md) i [Zweryfikuj aktualizacje oprogramowania firmy Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

Aby uzyskać więcej informacji na temat typów przepływu pracy, zobacz [co to jest weryfikacja jako usługa dla usługi Azure Stack?](azure-stack-vaas-overview.md).

### <a name="getting-started-with-vaas-workflows"></a>Wprowadzenie do przepływów pracy VaaS

1. Na pulpicie nawigacyjnym rozwiązania Utwórz nowe rozwiązanie lub wybierz istniejącą. Odświeża i umożliwia Kafelki przepływu pracy.
2. Aby utworzyć nowy przepływ pracy, wybierz **Start** w dowolnym kafelku. Aby uzyskać informacje specyficzne dla każdego przepływu pracy zobacz następujące artykuły:
    - Przebieg testu: [Szybki start: Użyj sprawdzania poprawności jako portal usługi, do zaplanowania Twojego pierwszego testu](azure-stack-vaas-schedule-test-pass.md)
    - Sprawdzanie poprawności rozwiązania: [Weryfikowanie nowych rozwiązań usługi Azure Stack](azure-stack-vaas-validate-solution-new.md)
    - Sprawdzanie poprawności rozwiązania: [Weryfikowanie aktualizacje oprogramowania firmy Microsoft](azure-stack-vaas-validate-microsoft-updates.md)
    - Sprawdzanie poprawności rozwiązania: [Sprawdzanie poprawności pakietów producenta OEM](azure-stack-vaas-validate-oem-package.md)

3. Do zarządzania i monitorowania istniejącego przepływu pracy, wybierz **Zarządzaj** na kafelku przepływu pracy. Wybierz nazwę przepływu pracy i użyć **Edytuj** przycisk, aby wyświetlić właściwości lub zmień wspólnych parametrów testu.

Aby uzyskać więcej informacji na temat właściwości przepływu pracy i parametrów, zobacz [wspólnych parametrów przepływów pracy dla usługi Azure Stack weryfikacji jako usługa](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Testy

Test w VaaS składa się z zestawu działań, które uruchamiać rozwiązanie usługi Azure Stack. Testy różnym przeznaczeniu zamierzony według kategorii, takich jak funkcjonalności lub niezawodności, a celem co najmniej jednej usługi Azure Stack. Każdy test definiuje swój własny zestaw parametrów, z których niektóre są określone przez wspólne parametry zawierającego przepływu pracy.

Aby uzyskać więcej informacji na temat zarządzania i monitorowania testów, zobacz [monitorowanie i zarządzanie testami w portalu VaaS](azure-stack-vaas-monitor-test.md).

Aby uzyskać więcej informacji na temat parametrów testu zobacz [wspólnych parametrów przepływów pracy dla usługi Azure Stack weryfikacji jako usługa](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Agenci

VaaS agent zależy od wykonania testu. Dwa rodzaje agentów VaaS testy:

- **Agenta chmurowego**. Jest to dostępne w VaaS domyślnego agenta. Konfiguracja nie jest wymagana, ale wymaga połączenia przychodzącego do środowiska i punktów końcowych usługi Azure Stack jest rozpoznawana z Internetu. Niektóre testy nie są zgodne z agentem chmury.
- A **agent lokalny**. Umożliwia uruchomienie sprawdzania poprawności w scenariuszach, w którym połączenia przychodzącego do środowiska nie jest możliwe. Niektóre testy wymagają wykonywania za pośrednictwem lokalnego agenta.

Lokalne agenty nie są powiązane żadne rozwiązanie do określonej usługi Azure Stack lub VaaS. Najlepszym rozwiązaniem powinno być ono uruchomione poza środowiskiem usługi Azure Stack.

Aby uzyskać instrukcje na temat dodawania lokalnego agenta, zobacz [wdrożenia lokalnego agenta](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Kolejne kroki

- [Najlepsze rozwiązania dotyczące weryfikacji jako usługa](azure-stack-vaas-best-practice.md)