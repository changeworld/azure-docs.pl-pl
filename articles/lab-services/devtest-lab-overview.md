---
title: O usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługa DevTest Labs może znacznie ułatwiają tworzenie, zarządzanie i monitorowanie maszyn wirtualnych platformy Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: 599b4325e0eb9aa7f6ccca2626c6d4b14f38149d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339333"
---
# <a name="about-azure-devtest-labs"></a>O usłudze Azure DevTest Labs
Usługa Azure DevTest Labs to usługa, która umożliwia deweloperom w zespole, aby efektywnie samoobsługi maszyn wirtualnych i/lub zasobów PaaS, które są im niezbędne do tworzenia, testowania, szkolenia i pokazów itp., bez konieczności oczekiwania na stałe zatwierdzeń w menu Narzędzia, które są im niezbędne. 

Laboratorium już składa się z wstępnie skonfigurowanych zasad lub szablonów usługi Resource Manager za pomocą wszystkie niezbędne narzędzia i oprogramowanie, które deweloperzy mogą używać do tworzenia środowisk. Deweloperzy mogą tworzyć ich środowisk w ciągu kilku minut, a nie godzin lub dni. 

Za pomocą usługi DevTest Labs, możesz przetestować najnowszą wersję aplikacji, wykonując następujące czynności:

- Szybkie Inicjowanie obsługi administracyjnej środowisk Windows i Linux przy użyciu szablonów i artefaktów
- Łatwo Zintegruj swój potok wdrażania za pomocą usługi DevTest Labs, aby aprowizować środowiska na żądanie
- Skalowanie w górę testy obciążeniowe, aprowizując wielu agentów testowych i tworząc wstępnie aprowizowane środowiska do szkolenia i pokazów.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>Możliwości
DevTest Labs oferuje następujące funkcje dla deweloperów, pracy z maszynami wirtualnymi:

- Szybko utworzyć maszynę wirtualną, postępując zgodnie z mniej niż pięć prostych kroków.
- Wybierz z listy wyselekcjonowanych bazami maszyn wirtualnych, które są skonfigurowane, zatwierdzone i autoryzowane przez lider zespołu lub centralnej IT.
- Tworzenie maszyn ze wstępnie utworzonych obrazów niestandardowych, zawierających całe oprogramowanie i narzędzia są zainstalowane w obrazie. 
- Tworzenie maszyn z formuł, które są zasadniczo niestandardowych obrazów i najnowszą kompilację oprogramowania, które jest zainstalowane w czasie tworzenia maszyny wirtualnej.
- Instalowanie artefaktów, które są wdrożone na maszynie Wirtualnej po jej zaaprowizowaniu rozszerzenia.
- Skonfiguruj automatyczne zamknięcie i automatycznie harmonogramy uruchamiania na komputerach, aby mieć zamknięcia na koniec dnia i w górę i uruchamianie następnego dnia rano.
- Po prostu oświadczeń wstępnie utworzonej maszyny wirtualnej bez konieczności przechodzenia przez proces tworzenia maszyny. 

DevTest Labs oferuje następujące funkcje dla deweloperów, Praca ze środowiskami PaaS:

- Tworzenie środowisk PaaS usługi Azure Resource Manager, szybko zgodnie z mniej niż trzech prostych krokach.
- Wybierz z wyselekcjonowaną listę szablonów usługi Resource Manager, które są skonfigurowane, zatwierdzone i autoryzowane przez lider zespołu lub centralnej IT.
- Przyspiesz pustą grupę zasobów (piaskownicy) przy użyciu szablonu usługi Resource Manager do eksplorowania całej platformy Azure, pozostając nadal w ramach laboratorium.
- DevTest Labs oferuje następujące korzyści podczas tworzenia, konfigurowania i zarządzania środowiskami dla deweloperów i testowania w chmurze

Oprócz modelu samoobsługi dla deweloperów w zespole usługi umożliwia centralne IT, aby kontrolować odpady, optymalizować koszty zasobów i pozostają w ramach budżetu, wykonując następujące czynności: 

- Ustawienia automatycznego zamykania i automatycznie uruchomione harmonogramy, na maszynach wirtualnych.
- Ustawianie zasad na liczbie użytkowników maszyn wirtualnych można utworzyć.
- Ustawianie zasad na rozmiary maszyn wirtualnych i Galeria obrazów użytkowników można wybierać.
- Śledzenie kosztów i ustawianie elementów docelowych dla laboratorium.
- Otrzymywanie powiadomień o przewidywanych koszty dla laboratorium, tak, aby można podjąć odpowiednie działania. 

DevTest Labs zapewnia następujące korzyści podczas tworzenia, konfigurowania i zarządzania środowiskami w chmurze:

## <a name="cost-control-and-governance"></a>Kontrola kosztów i nadzór
DevTest Labs ułatwia kontrolowanie kosztów, co umożliwia wykonywanie następujących zadań:

- Ustawianie zasad dla laboratorium — takie jak liczba maszyn wirtualnych (VM) na użytkownika i liczba maszyn wirtualnych na laboratorium. 
- Utwórz zasady, aby automatycznie zamykać i uruchamiać maszyny wirtualne.
- Umożliwia śledzenie kosztów na maszynach wirtualnych i zasobów PaaS przetworzyliśmy wewnątrz środowiska laboratoryjnego, dzięki czemu możesz można w ramach budżetu wstępnie zdefiniowane. 
- Pomaga deweloperom pozostają w kontekście laboratorium, tak aby nie znajdą uruchamiając żadnych zasobów poza jej używać w źródłowej grupy zasobów lub subskrypcji.

## <a name="quickly-get-to-ready-to-test"></a>Szybki dostęp do gotowych do testowania
DevTest Labs umożliwia utworzenie wstępnie zainicjowana obsługa środowisk wszystko, czego potrzebuje Twój zespół, aby rozpocząć tworzenie i testowanie aplikacji. Po prostu oświadczenie środowisk zainstalowaną ostatnią dobrą kompilacją aplikacji i rozpocząć pracę już teraz. Możesz też użyć kontenerów, aby jeszcze szybciej i sprawniej utworzyć środowiska.

## <a name="create-once-use-everywhere"></a>Utwórz raz, używaj wszędzie
Przechwytuj i udostępniaj szablony środowisk PaaS i artefakty w ramach zespołu lub organizacji — wszystko w kontroli źródła — Aby utworzyć dla deweloperów i środowisk testowych w prosty sposób.

## <a name="worry-free-self-service"></a>Samoobsługa bez zmartwień
DevTest Labs umożliwia Twojej deweloperom i testerom szybko i łatwo tworzy maszyn wirtualnych (IaaS) i zasobów PaaS po kilku kliknięciach przy użyciu zestawu wstępnie skonfigurowany dla nich zasobów.

## <a name="use-iaas-and-paas-resources"></a>Użyj zasobów IaaS i PaaS 
DevTest Labs umożliwia również deweloperom korzystać z zasobów PaaS, takich jak aplikacje sieci Web, klastry usługi Service Fabric farmy programu SharePoint i tak dalej. w środowisku laboratoryjnym przy użyciu szablonu usługi Resource Manager. Korzystanie z szablonów usługi Resource Manager z repozytorium publicznego środowiska, lub Połącz laboratorium z własnym repozytorium Git, aby rozpocząć pracę w usłudze PaaS w laboratorium. Można także śledzić koszty w tych zasobach w celu pozostania w budżetów wstępnie zdefiniowane. 

## <a name="integrate-with-your-existing-toolchain"></a>Integracja z istniejącym łańcuchem narzędzi
Użyj wstępnie utworzonych wtyczki lub nasze interfejsy API, aby aprowizować środowiska deweloperskie i testowe bezpośrednio z narzędzia do preferowanego ciągłej integracji (CI) to zintegrowane środowisko projektowe (IDE) lub zautomatyzowanego potoku wersji. Można również użyć naszego kompleksowe narzędzie wiersza polecenia.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły: 

- Aby dowiedzieć się więcej o usłudze DevTest Labs, zobacz [DevTest Labs — pojęcia](devtest-lab-concepts.md)
- Aby uzyskać przewodnik z instrukcjami krok po kroku, zobacz [samouczka: Konfigurowanie laboratorium przy użyciu usługi Azure DevTest Labs](tutorial-create-custom-lab.md)


