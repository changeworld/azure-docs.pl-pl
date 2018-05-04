---
title: Testowanie pióra | Dokumentacja firmy Microsoft
description: Artykuł zawiera omówienie penetracji testowanie procesu (pentest) oraz jak wykonywać pentest względem aplikacji uruchomionych w infrastrukturze Azure.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: barclayn
ms.openlocfilehash: 1b7b6604de80beadf15dabe0d2da1d7a2e5fabd6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="pen-testing"></a>Testowanie pióra
Jest jedną z korzyści wynikające ze stosowania Azure do testowania aplikacji i wdrażania, można szybko uzyskać środowisk utworzony.  Nie trzeba martwić tworzenia zapotrzebowania, pobierania, a "rozlania i układania" sprzętu lokalnymi.

Jest to doskonały —, ale nadal konieczne upewnij się, że należy wykonać normalnego zabezpieczeń z powodu starannością. Jest jednym z elementów, należy wykonać penetracji Przetestuj aplikacje można wdrożyć na platformie Azure.

Może już wiedzieć, że Microsoft wykonuje [penetracji testowania naszym środowisku Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Dzięki temu dysków Azure ulepszenia. 

Nie możemy pióra przetestować aplikację dla Ciebie, ale Rozumiemy będzie mają i konieczne jest wykonanie pióra testowania na własnych aplikacji. Że jest to przydatne, ponieważ po podniesieniu bezpieczeństwa aplikacji, należy zabezpieczyć cały ekosystem platformy Azure.

Co można zrobić w takiej sytuacji?

Począwszy od 15 czerwca 2017 Microsoft nie wymaga już wstępne zatwierdzenie przeprowadzenie penetracji testy względem zasobów platformy Azure. Zachęcamy klientów, którzy chcą formalnie dokumentu nadchodzących penetracji testowania promujących zaangażowanie przed Microsoft Azure, aby wypełnić [formularza powiadomienia testowania penetracji usługi Azure](https://portal.msrc.microsoft.com/en-us/engage/pentest). Ten proces dotyczy tylko Microsoft Azure i nie ma zastosowania do inne usługi w chmurze firmy Microsoft. 

>[!IMPORTANT] 
>Podczas wysyłania powiadomień Microsoft pióra testowania czynności nie jest już wymagane klienci nadal muszą być zgodne z [Microsoft Cloud Unified penetracji testowania zasad zaangażowania](https://technet.microsoft.com/mt784683). 

Standardowych testów, które można wykonać obejmują:

* Testy na punktów końcowych do ujawniania [Otwórz sieci Web aplikacji zabezpieczeń projektu (OWASP) pierwszych 10 luk w zabezpieczeniach](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Testowanie argumentu rozmycie](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) z punktami końcowymi
* [Skanowanie portów](https://en.wikipedia.org/wiki/Port_scanner) z punktami końcowymi

Jeden typ testu, który nie może wykonać jest dowolnego rodzaju [przeprowadzenie ataku typu "odmowa usługi" (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) ataku. Dotyczy to również inicjowanie sam atak DoS lub przy użyciu powiązanych testy, które może ustalić, pokazują lub symulować dowolnego typu atak DoS.

## <a name="next-steps"></a>Kolejne kroki

- Czy chcesz rozpocząć pracę za pomocą pióra testowanie aplikacji hostowanej na platformie Microsoft Azure Jeśli tak, a następnie przejdź na za pośrednictwem do [omówienie testów penetracji](https://technet.microsoft.com/library/mt784683.aspx) (i kliknij przycisk Utwórz testowania żądania w dolnej części strony. 
