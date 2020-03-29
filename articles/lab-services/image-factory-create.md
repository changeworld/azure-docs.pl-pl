---
title: Tworzenie fabryki obrazów w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak skonfigurować fabrykę obrazów niestandardowych przy użyciu przykładowych skryptów dostępnych w repozytorium Git (Laboratorium DevTest platformy Azure).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 2c5a44a9505d4a312be521cdc3219c5e4ce95a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759452"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Tworzenie niestandardowej fabryki obrazów w laboratoriach devtest platformy Azure
W tym artykule pokazano, jak skonfigurować niestandardową fabrykę obrazów przy użyciu przykładowych skryptów dostępnych w [repozytorium Git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Co to jest fabryka obrazów?
Fabryka obrazów to rozwiązanie konfiguracji jako kodu, które regularnie tworzy i dystrybuuje obrazy ze wszystkimi żądanymi konfiguracjami. Obrazy w fabryce obrazów są zawsze aktualne, a bieżąca konserwacja jest prawie zerowa, gdy cały proces jest zautomatyzowany. A ponieważ wszystkie wymagane konfiguracje są już na obrazie, oszczędza czas od ręcznego konfigurowania systemu po utworzeniu maszyny Wirtualnej z podstawowym systemem operacyjnym.

Znaczący akcelerator, aby uzyskać pulpit dewelopera do stanu gotowości w DevTest Labs używa obrazów niestandardowych. Wadą obrazów niestandardowych jest to, że jest coś ekstra do utrzymania w laboratorium. Na przykład wersje próbne produktów wygasają z upływem czasu (lub) nowo wydane aktualizacje zabezpieczeń nie są stosowane, co zmusza nas do okresowego odświeżania obrazu niestandardowego. W fabryce obrazów masz definicję obrazu zaewidencjonowanego do kontroli kodu źródłowego i masz zautomatyzowany proces tworzenia obrazów niestandardowych na podstawie definicji.

Rozwiązanie umożliwia szybkie tworzenie maszyn wirtualnych z niestandardowych obrazów, eliminując jednocześnie dodatkowe bieżące koszty konserwacji. Dzięki temu rozwiązaniu można automatycznie tworzyć obrazy niestandardowe, dystrybuować je do innych laboratoriów DevTest i wycofywać stare obrazy. W poniższym klipie wideo dowiesz się o fabryce obrazów i o tym, jak jest ona implementowana w devtest labs.  Wszystkie skrypty programu Azure Powershell są swobodnie [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)dostępne i znajdują się tutaj: .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Widok wysokiego poziomu rozwiązania
Rozwiązanie umożliwia szybkie tworzenie maszyn wirtualnych z niestandardowych obrazów, eliminując jednocześnie dodatkowe bieżące koszty konserwacji. Dzięki temu rozwiązaniu można automatycznie tworzyć niestandardowe obrazy i dystrybuować je do innych laboratoriów DevTest. Używasz usługi Azure DevOps (dawniej Visual Studio Team Services) jako aparat aranżacji do automatyzacji wszystkich operacji w Laboratoriach DevTest.

![Widok wysokiego poziomu rozwiązania](./media/create-image-factory/high-level-view-of-solution.png)

Istnieje rozszerzenie [VSTS dla DevTest Labs,](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) które umożliwia wykonanie tych poszczególnych kroków:

- Tworzenie obrazu niestandardowego
- Tworzenie maszyny wirtualnej
- Usuwanie maszyny wirtualnej
- Tworzenie środowiska
- Usuń środowisko
- Wypełnianie środowiska

Korzystanie z rozszerzenia DevTest Labs to prosty sposób na rozpoczęcie pracy z automatycznym tworzeniem niestandardowych obrazów w devtest labs.

Istnieje alternatywna implementacja przy użyciu skryptu programu PowerShell dla bardziej złożonego scenariusza. Za pomocą programu PowerShell można w pełni zautomatyzować fabrykę obrazów opartą na laboratoriach DevTest, która może być używana w pęku narzędzi ciągłej integracji i ciągłego dostarczania (CI/CD). Zasady stosowane w tym alternatywnym rozwiązaniu są następujące:

- Typowe aktualizacje nie powinny wymagać żadnych zmian w fabryce obrazu. (na przykład dodawanie nowego typu obrazu niestandardowego, automatyczne wycofywanie starych obrazów, dodawanie nowego "punktu końcowego" DevTest Labs do odbierania obrazów niestandardowych itd.)
- Typowe zmiany są wspierane przez kontrolę kodu źródłowego (infrastruktura jako kod)
- Laboratoria DevTest odbierające obrazy niestandardowe mogą nie być w tej samej subskrypcji platformy Azure (laboratoria obejmują subskrypcje)
- Skrypty programu PowerShell muszą być wielokrotnego pożytu, abyśmy mogli w razie potrzeby rozkręcić dodatkowe fabryki

## <a name="next-steps"></a>Następne kroki
Przejdź do następnego artykułu w tej sekcji: [Uruchamianie fabryki obrazów z usługi Azure DevOps](image-factory-set-up-devops-lab.md)
