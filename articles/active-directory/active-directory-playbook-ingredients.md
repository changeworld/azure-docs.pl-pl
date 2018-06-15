---
title: Składniki fazy weryfikacji koncepcji podręcznika dotyczącego usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Eksploruj i szybkie rozpoczęcie scenariusze Zarządzanie tożsamościami i dostępem
services: active-directory
keywords: Usługa Azure active directory, podręcznika dotyczącego koncepcji, aby zapewnić
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: ff4a8601b619837d835ec25c26b1f7e69b46ae85
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26602543"
---
# <a name="azure-active-directory-proof-of-concept-playbook-ingredients"></a>Usługa Azure Active Directory dowód koncepcji podręcznika dotyczącego składników 

## <a name="theme"></a>Motyw
Usługa Azure AD zapewnia rozwiązania tożsamościami i dostępem w wielu obszarach w przedsiębiorstwie. Firma Microsoft klasyfikowania scenariusze w następujących obszarach: 

* [Wiele aplikacji i jednej tożsamości](active-directory-playbook-implementation.md#theme---lots-of-apps-one-identity) 
* [Zwiększające bezpieczeństwo](active-directory-playbook-implementation.md#theme---increase-your-security) 
* [Skalowalność samoobsługi](active-directory-playbook-implementation.md#theme---scale-with-self-service) 

Definiowanie motyw do ramki fazy weryfikacji koncepcji pozwala skupić się wysiłków o korzyściach z cele biznesowe, które często są wyzwalacze zainteresowanie weryfikacji koncepcji w pierwszej kolejności. 

## <a name="environment"></a>Środowisko

Należy określić szczegóły środowisko, w którym będzie dostarczać fazy weryfikacji koncepcji. W idealnym przypadku można tworzyć na niej po zakończeniu weryfikacji koncepcji. Środowisko docelowe odgrywa kluczową rolę i powinna być widoczna kompromisu między go jako prawdziwe, jak to możliwe i koszty ograniczeń lub dodatkowe zagadnienia. Środowisk do weryfikacji koncepcji są:
* **Produkcja:** scenariusze są realizowane w środowisku na żywo i już wdrożone usługi Microsoft Cloud (środowisko produkcyjne AD, Office 365, rozwiązanie SSO/dzierżawy usługi Azure AD). 
* **Użytkownik akceptacji testu Akceptacyjne / środowiska deweloperskiego:** infrastruktury testu (równoległe AD i potencjalnie Azure AD rozwiązania dzierżawy/logowania jednokrotnego) z danych testowych, podobny do produkcji. Zazwyczaj środowiska testowego jest współużytkowana przez wielu projektów w przedsiębiorstwie.

Większość scenariuszy, w tym przewodniku są addytywne charakter. W związku z tym ich można wdrożyć w dzierżawie produkcyjnego bez wpływu na użytkowników spoza fazy weryfikacji koncepcji. W tym dokumencie Zadzwonimy limit scenariusze, które ma wpływ na poziomie dzierżawy. W takich przypadkach można wziąć pod uwagę środowiskach nieprodukcyjnych. 


## <a name="target-users"></a>Użytkownicy docelowi

Należy ustalić docelowy zbiór użytkowników, które wykonują scenariuszy, szczególnie w przypadku, gdy środowisko jest środowisko produkcyjne lub testu. Kategorie użytkowników docelowych do weryfikacji koncepcji są:
* **Użytkownicy pilotażowi:** rzeczywistych użytkowników w środowisku, który będzie używany w rozwiązaniu przy użyciu konta, które używają w swoich funkcji codzienne zadania
* **Użytkowników testowych:** konta tworzone w środowisko testowe 

Większość scenariuszy, w tym przewodniku może być wykonywane przez użytkowników pilotażowych. W tym dokumencie Zadzwonimy limit uwagi dotyczące użytkownika docelowego w razie potrzeby.


[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]