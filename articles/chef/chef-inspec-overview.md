---
title: Użyj InSpec zgodności automatyzacji infrastruktury platformy Azure
description: Dowiedz się, jak używać InSpec do wykrywania problemów z wdrożeń platformy Azure
keywords: Azure, programu chef, metodyki devops, maszyny wirtualne, przegląd, automatyzacja, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: a5de2ca04a193f97a2a65a043f744abb8e0ea758
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359225"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Użyj InSpec zgodności automatyzacji infrastruktury platformy Azure
[InSpec](https://www.chef.io/inspec/) jest związane z programem Chef typu open-source języka opisu reguły zabezpieczeń i zgodności, które mogą być współużytkowane między programistów, operacji i inżynierowie zabezpieczeń. InSpec działa przez porównanie rzeczywisty stan infrastruktury za pomocą żądanego stanu, który można wyrazić w łatwych do zrozumienia i łatwe w zapisie InSpec kodu. InSpec wykrywa naruszeń i wyświetla wyniki w postaci raportu, ale umożliwia kontrolę korygowania.

InSpec służy do sprawdzania stanu zasobów i grup zasobów w subskrypcji, w tym maszyn wirtualnych, konfiguracje sieci, ustawienia usługi Azure Active Directory i inne.

W tym artykule opisano zalety używania InSpec, aby ułatwić zabezpieczeń i zgodności na platformie Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Wprowadź łatwy do zrozumienia i ocena zgodności
Dokumentacja zgodności w języku arkuszy kalkulacyjnych lub dokumentów programu Word zamykaj wymagania do interpretacji. Za pomocą InSpec przekształcasz wymagań kod numerów wersji, pliku wykonywalnego, czytelny dla człowieka. Kod zastępuje rozmowy dotyczące powinien być oceniany na rzecz testów zawierają wymiernych z zamiarem Wyczyść.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Wykrywanie problemów w całej flocie i ustalić ich priorytety ich korygowania
InSpec użytkownika bez wykorzystania agentów wykrywa tryb umożliwiają szybko ocenić - skali — poziom usługi ekspozycji. Wbudowanych metadanych do oceniania wpływ/ważność pomaga ustalić, jakie obszary, aby skoncentrować się na dotyczącym korygowania. Można również pisać reguły szybko w odpowiedzi na nowych luk w zabezpieczeniach lub regulacji prawnych i wprowadzane natychmiast.

## <a name="satisfy-audits"></a>Spełniają inspekcji
Dzięki InSpec możesz reagować z inspekcji pytania w dowolnym momencie — nie tylko na poziomie wcześniej określonych przedziałach czasowych, np. kwartalnych lub co rok. Według działających nieprzerwanie InSpec testów, możesz wprowadzić przechodzić audyt wiedząc swoją ocenę stanu zgodności dokładne i historii, zamiast oczekiwano tak, przez ustalenia audytora.

## <a name="next-steps"></a>Kolejne kroki

* Spróbuj InSpec w usłudze Azure Cloud Shell [ ![uruchomienie usługi Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "uruchomienie usługi Cloud Shell")](https://shell.azure.com)
