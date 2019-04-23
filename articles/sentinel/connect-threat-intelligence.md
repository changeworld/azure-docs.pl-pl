---
title: Połącz dane analizy zagrożeń przez wartownika platformy Azure w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak połączyć dane analizy zagrożeń na platformie Azure przez wartownika.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 56412543-5664-44c1-b026-2dbaf78a9a50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: a33b00e4e553824f1f719be2c8518487241f0a11
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786188"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Połącz dane z dostawców analizy zagrożeń 

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Po obejmuje strumieniowe przesyłanie danych do platformy Azure przez wartownika możesz wzbogacić go za pomocą analizy zagrożeń, źródła danych używanego w organizacji. 

Umożliwia wielu sprawdzić alerty i reguły z analizy zagrożeń wartość true, jeśli alert zostanie wygenerowany z określonego adresu IP, na przykład integracją dostawcy analizy zagrożeń będą mogli Cię powiadomić, jeśli ten adres IP ostatnio stwierdzono, być złośliwy , Wartownik platformy azure umożliwia integrację z [dostawców analizy zagrożeń](https://aka.ms/graphsecuritytips). 

Można przesyłać strumieniowo dzienniki z dostawców analizy zagrożeń na platformie Azure przez wartownika, za pomocą jednego kliknięcia. To połączenie umożliwia dołączenie wskaźniki zawierające różne rodzaje dostrzegalne elementy, takie jak adres IP, domeny, adres URL i skrótu pliku, wyszukiwanie i tworzenie niestandardowych alertów reguły w przez wartownika platformy Azure.  
> [!NOTE]
> Można wpisać wskaźniki dostosowane przed zagrożeniami na platformie Azure przez wartownika do użycia w reguł alertów, pulpitów nawigacyjnych i myślistwo scenariuszy dzięki integracji z usługą [tiIndicator Microsoft Graph Security](https://aka.ms/graphsecuritytiindicators) jednostki lub za pomocą [firmy Microsoft Wykres zabezpieczenia zintegrowane platformy analizy zagrożeń](https://aka.ms/graphsecuritytips).

## <a name="prerequisites"></a>Wymagania wstępne  

- Użytkownik z administratorem globalnym lub uprawnienia administratora zabezpieczeń 

- Zintegrowana z usługą Microsoft Intelligent Security Graph aplikacji analizy zagrożeń 

## <a name="connect-to-threat-intelligence"></a>Nawiązać połączenie z analizy zagrożeń 

1. Jeśli już używasz dostawcy analizy zagrożeń, pamiętaj przejść do aplikacji PORADĘ, a następnie przyznać uprawnienia do wskaźników do firmy Microsoft i określić usługę jako platformy Azure przez wartownika.  

2. Na platformie Azure przez wartownika, wybierz **łączników danych** a następnie kliknij przycisk **analizy zagrożeń** kafelka.

3. Kliknij przycisk **Połącz**. 

4. Aby użyć odpowiednich schematu w usłudze Log Analytics dla źródła analizy zagrożeń, możesz wyszukać **ThreatIntelligenceIndicator**. 

 
## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie przedstawiono sposób nawiązać połączenie z dostawcą analizy zagrożeń usługi Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły.

- Aby rozpocząć korzystanie z platformy Azure przez wartownika, musisz mieć subskrypcji platformy Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak [dołączyć dane do platformy Azure przez wartownika](quickstart-onboard.md), i [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
