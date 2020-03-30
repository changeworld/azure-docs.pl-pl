---
title: Wprowadzenie do testu porównawczego zabezpieczeń platformy Azure
description: Wprowadzenie do wzoru zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 8f65eb008d5df6ea3f2f85778d538023e6ce98eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945331"
---
# <a name="azure-security-benchmark-introduction"></a>Wprowadzenie do testów porównawczych zabezpieczeń platformy Azure

Możesz mieć kilka lat, a nawet dziesięciolecia doświadczenia w zakresie komputerów lokalnych. Wiesz, jak zabezpieczyć te wdrożenia; ale chmura jest inna. Skąd wiesz, czy wdrożenia w chmurze są bezpieczne? Jakie są różnice między rozwiązaniami w zakresie zabezpieczeń dla systemów lokalnych i wdrożeń w chmurze?

Istnieje duży zbiór oficjalnych dokumentów, najlepszych praktyk, architektur referencyjnych, wskazówek internetowych, narzędzi open source, rozwiązań komercyjnych, źródeł danych wywiadowczych i innych, które mogą być wykorzystane do zabezpieczenia chmury. Której opcji należy użyć? Co można zrobić, aby uzyskać akceptowalny poziom bezpieczeństwa w chmurze? 

Jednym z najlepszych sposobów zabezpieczenia wdrożeń w chmurze jest skupienie się na zaleceniach dotyczących testów porównawczych zabezpieczeń w chmurze. Zalecenia dotyczące wskaźników porównawczych, dotyczące zabezpieczenia dowolnej usługi, zaczynają się od podstawowego zrozumienia ryzyka związanego z cyberbezpieczeństwem i sposobu zarządzania nimi. Następnie można użyć tego zrozumienia, przyjmując zalecenia dotyczące zabezpieczeń porównawcze od dostawcy usług w chmurze, aby ułatwić wybór określonych ustawień konfiguracji zabezpieczeń w danym środowisku. 

Usługa Azure Security Benchmark zawiera kolekcję zaleceń dotyczących zabezpieczeń o dużym wpływie, których można użyć, aby zabezpieczyć większość usług używanych na platformie Azure. Te zalecenia można potraktować jako "ogólne" lub "organizacyjne", ponieważ mają one zastosowanie do większości usług platformy Azure. Zalecenia dotyczące usługi Azure Security Benchmark są następnie dostosowywane dla każdej usługi platformy Azure, a te dostosowane wskazówki są zawarte w artykułach dotyczących rekomendacji usług. 

Dokumentacja usługi Azure Security Benchmark określa formanty zabezpieczeń i zalecenia dotyczące usług.

- **Kontrole zabezpieczeń:** zalecenia dotyczące testów porównawczych zabezpieczeń platformy Azure są klasyfikowane według kontroli zabezpieczeń. Mechanizmy kontroli zabezpieczeń reprezentują wymagania zabezpieczeń niezależne od dostawców wysokiego poziomu, takie jak zabezpieczenia sieci i ochrona danych. Każda kontrola zabezpieczeń ma zestaw zaleceń dotyczących zabezpieczeń i instrukcji, które ułatwiają wdrażanie tych zaleceń. 
- **Zalecenia dotyczące usług:** Jeśli są dostępne, zalecenia dotyczące testów porównawczych dla usług platformy Azure będą zawierać zalecenia dotyczące usługi Azure Security Benchmark, które są dostosowane specjalnie do tej usługi. 

Terminy "Kontrola", "Benchmark" i "Linia bazowa" są często używane w dokumentacji usługi Azure Security Benchmark i ważne jest, aby zrozumieć, jak platforma Azure używa tych terminów. 

| Termin | Opis | Przykład |
|--|--|--|
| Kontrola | **Formant** jest opis wysokiego poziomu funkcji lub działania, które muszą być kierowane i nie jest specyficzne dla technologii lub implementacji. | Ochrona danych jest jednym z mechanizmów kontroli bezpieczeństwa. Ten formant zawiera określone akcje, które należy rozwiązać, aby zapewnić ochronę danych. |
| Punkt odniesienia | **Benchmark** zawiera zalecenia dotyczące zabezpieczeń dla określonej technologii, takiej jak Azure. Zalecenia są podzielone na kategorie według formantu, do którego należą. | Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące zabezpieczeń specyficzne dla platformy Azure  |
| Punkt odniesienia | **Punkt odniesienia** to wymagania dotyczące zabezpieczeń dla organizacji. Wymogi dotyczące zabezpieczeń są oparte na zaleceniach dotyczących wskaźników referencyjnych. Każda organizacja decyduje, które zalecenia dotyczące wskaźników należy uwzględnić w ich linii bazowej. | Firma Contoso tworzy linię bazową zabezpieczeń, wybierając wymagać określonych zaleceń w usłudze Azure Security Benchmark. |

Czekamy na Wasze opinie na temat testu porównawczego zabezpieczeń platformy Azure! Zachęcamy do przedstawienia uwag w obszarze opinii poniżej. Jeśli wolisz udostępnić swoje dane wejściowe bardziej prywatnie zespołowi usługi Azure Security Benchmark, możesz wypełnić formularz nahttps://aka.ms/AzSecBenchmark 
