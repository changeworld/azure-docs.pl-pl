---
title: Zagrożenia — narzędzie Microsoft Threat Modeling Tool — Azure | Dokumenty firmy Microsoft
description: Strona kategorii zagrożeń dla narzędzia Microsoft Threat Modeling Tool zawierająca kategorie dla wszystkich narażonych wygenerowanych zagrożeń.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727827"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Zagrożenia związane z narzędziem do modelowania zagrożeń firmy Microsoft

Narzędzie do modelowania zagrożeń jest podstawowym elementem cyklu życia programu Microsoft Security Development (SDL). Umożliwia architektom oprogramowania wczesne identyfikowanie i łagodzenie potencjalnych problemów z zabezpieczeniami, gdy są one stosunkowo łatwe i opłacalne do rozwiązania. W rezultacie znacznie zmniejsza całkowity koszt rozwoju. Ponadto zaprojektowaliśmy narzędzie z myślą o ekspertach niezwiązanych z bezpieczeństwem, ułatwiając modelowanie zagrożeń wszystkim deweloperom, zapewniając jasne wskazówki dotyczące tworzenia i analizowania modeli zagrożeń.

> Odwiedź narzędzie do **[modelowania zagrożeń,](threat-modeling-tool.md)** aby rozpocząć już dziś!

Narzędzie do modelowania zagrożeń pomaga odpowiedzieć na niektóre pytania, takie jak poniższe:

* Jak osoba atakująca może zmienić dane uwierzytelniania?
* Jaki jest wpływ, jeśli osoba atakująca może odczytać dane profilu użytkownika?
* Co się stanie, jeśli odmówiono dostępu do bazy danych profilu użytkownika?

## <a name="stride-model"></a>Model KROK

Aby lepiej pomóc w formułowaniu tego rodzaju spiczastych pytań, firma Microsoft używa modelu STRIDE, który kategoryzuje różne typy zagrożeń i upraszcza ogólne konwersacje dotyczące zabezpieczeń.

| Kategoria | Opis |
| -------- | ----------- |
| **Fałszowanie zawartości** | Polega na nielegalnym dostępie, a następnie użyciu informacji uwierzytelniających innego użytkownika, takich jak nazwa użytkownika i hasło |
| **Manipulowanie** | Obejmuje złośliwą modyfikację danych. Przykłady obejmują nieautoryzowane zmiany wprowadzone w danych trwałych, takie jak dane przechowywane w bazie danych, oraz zmianę danych przesyłanych między dwoma komputerami za pośrednictwem otwartej sieci, takiej jak Internet |
| **Odrzucenie** | Skojarzony z użytkownikami, którzy odmawiają wykonywania akcji bez innych stron, które mają jakikolwiek sposób udowodnić inaczej — na przykład użytkownik wykonuje nielegalną operację w systemie, który nie ma możliwości śledzenia zabronionych operacji. Non-Repudiation odnosi się do zdolności systemu do przeciwdziałania zagrożeniom odrzucenia. Na przykład użytkownik, który kupuje element może być musiał podpisać dla towaru po otrzymaniu. Dostawca może następnie użyć podpisanego potwierdzenia jako dowodu, że użytkownik otrzymał pakiet |
| **Ujawnianie informacji** | Polega na ekspozycji informacji na osoby, które nie powinny mieć do nich dostępu , na przykład zdolność użytkowników do odczytu pliku, do którego nie otrzymali dostępu, lub zdolność intruza do odczytywania danych przesyłanych między dwoma komputerami. |
| **Odmowa usługi** | Ataki typu "odmowa usługi" (DoS) odmawiają usługi prawidłowym użytkownikom — na przykład przez tymczasowe lub bezużyteczne lub bezużyteczne dla serwera sieci Web. Należy chronić przed niektórymi rodzajami zagrożeń DoS po prostu w celu zwiększenia dostępności i niezawodności systemu |
| **Podniesienie uprawnień** | Nieuprzywilejowany użytkownik uzyskuje uprzywilejowany dostęp, a tym samym ma wystarczający dostęp do naruszenia lub zniszczenia całego systemu. Podniesienie uprawnień zagrożeń obejmują sytuacje, w których atakujący skutecznie przeniknął wszystkie mechanizmy obronne systemu i stał się częścią zaufanego systemu, niebezpieczna sytuacja rzeczywiście |

## <a name="next-steps"></a>Następne kroki

Przejdź do **[łagodzenia środków ograniczających narzędzia do modelowania zagrożeń,](threat-modeling-tool-mitigations.md)** aby dowiedzieć się, jak można ograniczyć te zagrożenia za pomocą platformy Azure.