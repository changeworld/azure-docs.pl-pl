---
title: Testowanie penetracyjne | Dokumentacja firmy Microsoft
description: Artykuł zawiera omówienie procesu (pentest) penetracyjne i działanie pentest względem aplikacje działające w infrastrukturze platformy Azure.
services: security
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: barclayn
ms.openlocfilehash: a64316eda25bd02f89b5afdd7b98c0193381d023
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970823"
---
# <a name="pen-testing"></a>Testowanie penetracyjne
Jest jedną z korzyści z używania platformy Azure do testowania aplikacji i wdrażania, można szybko uzyskać środowisk utworzonych.  Nie trzeba martwić się o tworzenia zapotrzebowania, pobieranie i "pracy i zestawianie" sprzętu w środowisku lokalnym.

Jest to doskonałe rozwiązanie —, ale nadal musisz upewnić się, wykonaj bezpieczeństwa normalne należytą starannością. Jedną z rzeczy, konieczne jest penetracji testowanie aplikacji można wdrożyć na platformie Azure.

Może już wiesz, że firma Microsoft wykona [testów penetracyjnych naszego środowiska Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Dzięki temu Dbaj o ulepszenia systemu Azure. 

Firma Microsoft nie piórem testować swoją aplikację dla Ciebie, ale wiemy, będzie ma i trzeba będzie wykonać pióra testowania na potrzeby własnych aplikacji. Który jest to przydatne, ponieważ gdy można zwiększyć bezpieczeństwo aplikacji, Pomożesz zapewnić większe bezpieczeństwo całego ekosystemu platformy Azure.

Co można zrobić w takiej sytuacji?

Od 15 czerwca 2017 r. firmy Microsoft nie wymaga już wstępnej akceptacji do prowadzenia penetracji testy względem zasobów platformy Azure. Zachęcamy klientów, którzy chcą formalnie dokumentu przyszłych testów penetracyjnych engagements względem Microsoft Azure, aby wypełnić [formularza testowania powiadomienia usługi Azure Service penetracji](https://portal.msrc.microsoft.com/en-us/engage/pentest). Ten proces dotyczy tylko Microsoft Azure i nie dotyczy innych usługi w chmurze firmy Microsoft. 

>[!IMPORTANT] 
>Podczas powiadamiania Microsoft pióra testowania działania nie jest już wymagany klienci nadal muszą być zgodne z [Microsoft Cloud Unified penetracji testowania zasad zaangażowania](https://technet.microsoft.com/mt784683). 

Standardowe testy, które można wykonać obejmują:

* Testy w punktach końcowych, aby odkryć [Otwórz sieci Web aplikacji Security Project (OWASP) top 10 luk w zabezpieczeniach](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Testowanie argumentu rozmycie](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) punktów końcowych sieci
* [Skanowanie portów](https://en.wikipedia.org/wiki/Port_scanner) punktów końcowych sieci

Jeden typ testu, który nie może wykonać jest dowolnym rodzaju [przeprowadzenie ataku typu "odmowa usługi" (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) ataku. Obejmuje to inicjowanie ataków DoS, samego lub wykonywania powiązanych testów, które mogą określić, pokazują lub symulacji wszelkiego rodzaju atak DoS.

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę przy użyciu pióra testujesz aplikacji hostowanych na platformie Microsoft Azure? Jeśli tak, a następnie przejdź na do [omówienie testów Penetracyjnych](https://technet.microsoft.com/library/mt784683.aspx) strony (i kliknij polecenie Utwórz testy żądania w dolnej części strony. 
