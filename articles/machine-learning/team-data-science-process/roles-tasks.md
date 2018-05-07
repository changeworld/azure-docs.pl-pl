---
title: Zespół ról w procesie nauki danych oraz zadania - Azure | Dokumentacja firmy Microsoft
description: Zarys najważniejsze składniki, role pracownikami i skojarzonych zadań dla projektu zespołowego analizy danych.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: deguhath
ms.openlocfilehash: 00d8761a7afe4b727c04626c0ba7c032b2277f48
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="team-data-science-process-roles-and-tasks"></a>Zespół proces nauki danych ról i zadań

Proces nauki danych zespołu jest opracowanym przez firmę Microsoft, zapewniająca strukturalnych metodologii do tworzenia rozwiązania analizy predykcyjnej i aplikacje inteligentnego wydajnie. W tym artykule opisano role personelu klucza i ich skojarzonych zadań, które są obsługiwane przez funkcję analizy danych zespołu standaryzacji tego procesu. 

Łącze wprowadzenie do samouczków, które zawierają informacje na temat sposobu konfigurowania środowiska TDSP grupy nauki danych, danych nauki zespołów i projektów. Firma Microsoft udostępnia szczegółowe wytyczne, używając programu Visual Studio Team Services (VSTS) w samouczków jako naszych hosting kod platformy i elastyczne narzędzia planowania do zarządzania zadaniami zespołu, kontroli dostępu i zarządzania repozytoriów. 

Można również użyć tych informacji do zaimplementowania TDSP własne hosting kodu i elastyczne narzędzia planowania. 

## <a name="structures-of-data-science-groups-and-teams"></a>Struktury danych nauki grup i zespołów
Funkcje analizy danych w przedsiębiorstwach często mogą być uporządkowane w następujących hierarchii:

1. ***Grupy nauki danych/s***

2. ***Dane nauki zespołu/s w obrębie grupy/s***

W strukturze o nie będzie są grupy i zespołowych potencjalnych klientów. Zazwyczaj projektu nauki danych jest realizowane przez zespół nauki danych, której może się składać potencjalnych projektu (dla zadań zarządzania i nadzór nad projektu) i analityków danych i inżynierów (pojedynczych współpracowników / techniczne personelu) kto będzie wykonywać analizy danych dane techniczne części projektu i. Przed wykonaniem konfigurację i zarządzanie odbywa się przez grupę, zespołu lub projektu potencjalnych klientów.

## <a name="definition-of-four-tdsp-roles"></a>Definicja cztery role TDSP
Przy założeniu powyżej cztery różne role została określona dla personel firmy Microsoft zespołu:

1. ***Menedżer grupy***. Menedżer grupy jest Menedżer jednostki analizy danych w przedsiębiorstwie. Jednostka nauki danych może mieć kilka zespołów, z których każdy działa na wielu projektów analizy danych w przypadkach biznesowych. Menedżer grupy mogą delegować ich zadań surogatu, ale nie należy zmieniać zadania związane z rolą.

2. ***Zespół realizacji***. Realizacji zespołu zarządza zespołu w jednostce nauki danych przedsiębiorstwa. Zespół składa się z wielu analityków danych. Dla danych nauki jednostki z mniejszą liczbą analityków danych menedżera grupy i prowadzić zespołu może być ta sama osoba.

3. ***Realizacji projektu***. Realizacji projektu zarządza codzienne działania analityków danych poszczególnych projektu nauki określonych danych.

4. ***Projekt poszczególnych współautora***. Data Scientist, Business Analyst, Data Engineer, Architect, etc. Poszczególne współautorem projektu wykonuje projektu analizy danych. 


**[AZURE.NOTE]**: W zależności od struktury w przedsiębiorstwie jedna osoba może odtworzyć więcej niż jedna rola lub może być więcej niż jedna osoba, pracy w roli. Może to być często w przypadku małych przedsiębiorstw lub przedsiębiorstwa z mniejszą liczbą personelu w organizacji analizy danych.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Zadań do wykonania przez personel cztery

Poniżej przedstawia najwyższego poziomu zadania dla personelu przez rolę w przyjęcia i wdrożenia proces nauki zespołu danych jako conceptualized przez firmę Microsoft. 

![Przegląd ról i zadań](./media/roles-tasks/overview-tdsp-top-level.png)

Ten schemat i konspekt następujących, bardziej szczegółowych zadań, które są przypisane do każdej roli w TDSP powinna ułatwić wybierz odpowiedni samouczek, oparte na Twoje obowiązki w organizacji.

>[AZURE.NOTE] W poniższych instrukcjach zostanie przedstawiony kroki sposobu konfigurowania środowiska TDSP i wykonywanie innych zadań nauki danych w Visual Studio Team Services (VSTS). Określono sposobu wykonywania tych zadań z programu VSTS, ponieważ jest to, co jest używany do wykonania TDSP firmy Microsoft. VSTS ułatwia współpracy przez integrację zarządzania elementów roboczych, które śledzą zadania i kodu usługi hostingu, używany do udostępniania utilities, organizowanie wersji i zabezpieczenia oparte na rolach. Będą mogli wybrać innych platform, jeśli wolisz, implementowanie zadań określone przez TDSP. Jednak w zależności od platformy, niektóre funkcje, które możemy korzystać z programu VSTS mogą być niedostępne. 
>
>Ponadto firma Microsoft używa [danych nauki maszyny wirtualnej (DSVM)](http://aka.ms/dsvm) na platformie Azure w chmurze jako pulpitu analytics z kilku popularnych danych narzędzia nauki wstępnie skonfigurowanych i zintegrowane z różnych oprogramowania i usług Azure. DSVM lub inne środowisko projektowe służy do implementowania TDSP. 


## <a name="group-manager-tasks"></a>Menedżer grupy zadań

Następujące zadania są wykonywane przez menedżera grupy lub określonego administratora systemu TDSP przyjęcie TDSP:

- Utwórz **konta grupy** na kod obsługujący platformy (na przykład Github, Git, VSTS lub innych użytkowników)
- Utwórz **repozytorium szablonu projektu** na konto grupy i go z repozytorium szablonu projektu opracowywanym przez zespół Microsoft TDSP inicjatora. Repozytorium szablonu projektu TDSP firmy Microsoft zapewnia **standaryzowane struktury katalogów** tym katalogi danych, kodu i dokumenty i zawiera zestaw **standardowych szablonów dokumentów** pomocny w procesie nauki wydajne danych. 
- Utwórz **repozytorium narzędzia**i inicjatora go z repozytorium narzędzia opracowywanym przez zespół TDSP firmy Microsoft. Repozytorium narzędzia TDSP firmy Microsoft zawiera zestaw narzędzi przydatne dla zapewnienia działania naukowca danych bardziej wydajne, w tym narzędzi dla interakcyjną eksplorację, analizy i raportowania i dla linii bazowej modelowania i raportowania.
- Konfigurowanie **zasady kontroli zabezpieczeń** tych dwóch repozytoriów Twojego konta grupy.  

Aby uzyskać szczegółowe instrukcje krok po kroku, zobacz [grupy Menedżera zadań dla zespołu nauki danych](group-manager-tasks.md). 


## <a name="team-lead-tasks"></a>Zespół realizacji zadań

Następujące zadania są wykonywane przez prowadzić zespołu (lub administratorem projektu zespołowego wyznaczonych) do przyjęcia TDSP:

- Jeśli VSTS jest wybrany jako Platforma macierzysta kodu dla wersji i współpracy, Utwórz **projektu zespołowego** na serwerze programu VSTS grupy. W przeciwnym razie to zadanie można pominięte.
- Utwórz **repozytorium szablonu projektu zespołowego** w ramach projektu zespołowego i inicjatora z repozytorium szablonu projektu grupy Konfigurowanie przez menedżera grupy lub obiektu delegowanego menedżera. 
- Utwórz **repozytorium narzędzia team**i Dodaj do repozytorium narzędzia specyficzne dla zespołu. 
- (Opcjonalnie) Utwórz **[usługi Azure file storage](https://azure.microsoft.com/services/storage/files/)** ma być używany do przechowywania zasobów danych, które mogą być przydatne dla całego zespołu. Innych członków zespołu można zainstalować tej udostępnionej chmury magazyn plików na komputerach stacjonarnych analytics.
- (Opcjonalnie) Zainstaluj usługi Azure file storage do **maszyny wirtualnej nauki danych** (DSVM) zespołu prowadzić i dodać zasobów danych.
- Konfigurowanie **zabezpieczeniem** przez dodawanie członków zespołu i skonfiguruj swoje uprawnienia. 

Aby uzyskać szczegółowe instrukcje krok po kroku, zobacz [prowadzić zespołu zadania dla zespołu nauki danych](team-lead-tasks.md).  


## <a name="project-lead-tasks"></a>Zadania realizacji projektu

Następujące zadania są wykonywane przez projekt prowadzić do przyjęcia TDSP:

- Utwórz **repozytorium projektu** zgodnie z projektem zespołowym i inicjatora jej przez zespół projektu szablonu repozytorium. 
- (Opcjonalnie) Utwórz **usługi Azure file storage** ma być używany do przechowywania zasobów danych projektu. 
- (Opcjonalnie) Zainstaluj usługi Azure file storage do **maszyny wirtualnej nauki danych** (DSVM) projektu prowadzić i dodać zasoby danych projektu.
- Konfigurowanie **zabezpieczeniem** przez dodawanie członków projektu i skonfiguruj swoje uprawnienia. 

Aby uzyskać szczegółowe instrukcje krok po kroku, zobacz [projektu zadania dla zespołu nauki danych](project-lead-tasks.md). 

## <a name="project-individual-contributor-tasks"></a>Współautor poszczególne zadania w projekcie

Następujące zadania są wykonywane przez poszczególne współautora projektu (zwykle naukowca danych) do prowadzenia projektu nauki danych przy użyciu TDSP:

- Klonowanie **repozytorium projektu** przez realizacji projektu. 
- (Opcjonalnie) Zainstaluj udostępniony **usługi Azure file storage** zespołu i projektu na ich **maszyny wirtualnej nauki danych** (DSVM).
- Umożliwia wykonanie projektu. 

 
Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące dołączania do projektu, zobacz [pojedynczych współpracowników projektu dla zespołu nauki danych](project-ic-tasks.md). 


## <a name="data-science-project-execution"></a>Wykonanie projektu nauki danych
 
Wykonując odpowiedni zestaw instrukcji realizacji projektu, zespołu potencjalnych klientów i analityków danych można utworzyć elementów roboczych do śledzenia wszystkich zadań i etapy, które projekt wymaga od początku do końca. Również przy użyciu narzędzia git wspiera współpracę analityków danych i zapewnia, że artefakty generowane podczas wykonywania projektu są wersji kontrolowane i wspólne dla wszystkich członków projektu.

Instrukcjami wykonywania projektu zostały opracowane na podstawie przy założeniu, oba elementy robocze i projektu git repozytoria są na VSTS. Za pomocą programu VSTS zarówno pozwala połączyć elementy robocze z gałęzi Git repozytoriami projektu. Dzięki temu można łatwo śledzić, co zostało wykonane dla elementu roboczego. 

Poniższa ilustracja przedstawia ten przepływ pracy dla realizacji projektu przy użyciu TDSP.

![Wykonanie projektu nauki typowych danych](./media/roles-tasks/overview-project-execute.png)

Przepływ pracy zawiera kroki, które można podzielić na trzy czynności:

- Planowanie (prowadzić projektu) przebiegu
- Tworzenie artefaktów na gałęzi git adresów elementów roboczych (naukowca danych)
- Przegląd kodu i scalanie oddziałów z gałęzi głównej (projektu lub innych członków zespołu)

Aby uzyskać szczegółowe instrukcje krok po kroku na przepływ wykonania projektu, zobacz [wykonywania projektów analizy danych](project-execution.md).

## <a name="next-steps"></a>Kolejne kroki

Oto łącza do bardziej szczegółowe opisy ról i zadań zdefiniowanych przez proces nauki danych zespołu:

- [Zadania menedżera grupy dla zespołu nauki danych](group-manager-tasks.md)
- [Zespół realizacji zadania dla zespołu nauki danych](team-lead-tasks.md)
- [Zadania realizacji projektu dla zespołu nauki danych](project-lead-tasks.md)
- [Współautorzy poszczególnych projektu dla zespołu nauki danych](project-ic-tasks.md)