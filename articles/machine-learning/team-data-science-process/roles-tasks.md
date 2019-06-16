---
title: Zespół danych dla celów naukowych role i zadania
description: Zarys najważniejsze składniki, role personelu i skojarzonych zadań dla projektu zespołowego do nauki o danych.
author: marktab
manager: cgronlun
editor: cgronlun
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 05fc742bba535ea3968e60cd0f40c80b812c09fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61043071"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Zespół danych dla celów naukowych role i zadania

Proces analizy danych zespołu jest opracowanym przez firmę Microsoft, który zawiera structured metodologii do wydajnego tworzenia rozwiązań analizy predykcyjnej i inteligentnych aplikacji. W tym artykule opisano role kluczy, personelu i ich skojarzonych zadań, które są obsługiwane przez usługę do nauki o danych zespołu, standaryzacji na temat tego procesu.

Ten zawiera wprowadzenie linki do samouczków, które zawierają instrukcje dotyczące sposobu konfigurowania środowiska TDSP dla grupy do nauki o danych, zespoły do nauki o danych i projektów.
Zawiera on szczegółowe wskazówki, które przy użyciu DevOps platformy Azure w ramach samouczków. Azure DevOps zapewnia hosting kodu platformy, a narzędzie planowania agile do zarządzania zadaniami zespołu, kontroli dostępu i Zarządzaj repozytoriami.

Można użyć tych informacji do zaimplementowania przetwarzania TDSP w własne narzędzie planowania agile i hosting kodu.

## <a name="structures-of-data-science-groups-and-teams"></a>Struktury grupy do nauki o danych i zespołów

Funkcje analizy danych w przedsiębiorstwach często może być uporządkowane w następującej hierarchii:

1. ***Grupy do nauki o danych na sekundę***

2. ***Data science zespołu/s w obrębie grupy/s***

W takiej struktury są potencjalnymi klientami grupy i zespół. Zazwyczaj projektu nauki o danych jest realizowane przez zespół do nauki o danych, co może się składać z Liderzy projektu (dla zadań zarządzania i nadzoru projektu) i analityków danych lub inżynierów (pojedynczych współpracowników techniczne personelu) który będzie wykonywać analizy danych i dane inżynierii części projektu. Przed wykonaniem Konfiguracja i zarządzanie odbywa się przez grupę, zespołu lub projektu potencjalnych klientów.

## <a name="definition-of-four-tdsp-roles"></a>Definicja cztery role środowiska TDSP
Przy założeniu powyżej istnieją cztery różne role dla pracowników zespołu:

1. ***Menedżer grupy***. Menedżer grupy jest kierownikiem jednostki do nauki o danych w przedsiębiorstwie. Jednostka analizy danych może mieć wielu zespołach, z których każdy działa w wielu projektach do nauki o danych w różnych firm branżowych. Menedżer grupy może delegować ich zadań, aby zastępczy, ale nie zmieniaj zadania związane z rolą.

2. ***Zespół potencjalny klient***. Lider zespołu jest zarządzanie zespołem w jednostce do nauki o danych przedsiębiorstwa. Zespół składa się z wielu analityków danych. Jednostka do nauki o danych z mniejszą liczbą analityków danych Kierownik grupy i lider zespołu może być ta sama osoba.

3. ***Kierownik projektu***. Lider projektu zarządza codziennych działań poszczególnych naukowców pracujących nad projektem nauki o danych z konkretnych.

4. ***Pojedynczy Współautor projektu***. Data Scientist, Business Analyst, Data Engineer, Architect, etc. Pojedynczy Współautor projektu wykonuje projektu nauki o danych.


> [!NOTE]
> W zależności od struktury w przedsiębiorstwie jedna osoba może pełnić więcej niż jednej roli, lub może być więcej niż jedna osoba działa w roli. Może to być często w przypadku małych przedsiębiorstw i przedsiębiorstw z mniejszą liczbą personelu w swojej organizacji do nauki o danych.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Zadań do wykonania przez cztery personel

Poniższy obraz przedstawia zadania najwyższego poziomu dla pracowników według roli w przyjęcia i wdrożenia procesu do nauki o danych zespołu jako conceptualized przez firmę Microsoft.

![Omówienie ról i zadań](./media/roles-tasks/overview-tdsp-top-level.png)

Ten schemat i zarys następujących, bardziej szczegółowe zadania, które są przypisane do każdej roli w przetwarzania TDSP powinien pomogą w wybraniu w odpowiednim samouczku, w oparciu o Twoje obowiązki w organizacji.

> [!NOTE]
> Poniżej przedstawiono kroki konfigurowania środowiska TDSP i wykonuj inne zadania do nauki o danych w infrastrukturze DevOps platformy Azure. Firma Microsoft umożliwia określenie sposobu wykonywania tych zadań, za pomocą DevOps platformy Azure, ponieważ używamy do zaimplementowania przetwarzania TDSP w firmie Microsoft. Azure DevOps usprawnia współpracę dzięki integracji zarządzania elementów roboczych, które śledzą zadania i Usługa hostingu kod używany do udostępniania programów narzędziowych, organizowanie wersji i zapewnienia opartej na rolach zabezpieczeń. Masz możliwość wybrania innych platform, jeśli wolisz, implementowanie zadań opisane przez przetwarzania TDSP. Jednak w zależności od platformy, niektóre funkcje, z której korzystać z DevOps platformy Azure nie mogą być dostępne.
>
>Instrukcje w tym miejscu jest również użyć [maszyny wirtualnej do nauki o danych (DSVM)](https://aka.ms/dsvm) na platformie Azure w chmurze jako pulpit analityczny za pomocą kilku narzędzi do analizy danych popularnych wstępnie skonfigurowanych i zintegrowane z różnymi oprogramowania firmy Microsoft i platformy Azure usługi. Maszyny DSVM lub dowolnym innym środowisku programowania można użyć do zaimplementowania przetwarzania TDSP.


## <a name="group-manager-tasks"></a>Menedżer grupy zadań

Następujące zadania są wykonywane przez menedżera grupy (lub określonego administratora systemu przetwarzania TDSP) podjęcie przetwarzania TDSP:

- Tworzenie **konta grupy** na kod hostingu platformy (np. usługi GitHub, Git, DevOps platformy Azure lub inne)
- Tworzenie **repozytorium szablonów projektu** na konto grupy i go z repozytorium szablonów projektu opracowany przez zespół Microsoft TDSP inicjatora. Repozytorium szablonów projektu przetwarzania TDSP od firmy Microsoft
    - udostępnia **standaryzowane strukturę katalogów** katalogów dla danych, kodu i dokumenty, w tym
    - zawiera zestaw **standardowych szablonów dokumentów** przeprowadzenie procesu do nauki o danych wydajne.
- Tworzenie **repozytorium narzędzia**i umieszczenia go z repozytorium narzędzia opracowane przez zespół TDSP firmy Microsoft. Zapewnia repozytorium narzędzia przetwarzania TDSP od firmy Microsoft
    - zestaw przydatne narzędzia, aby uczynić pracę analitykiem danych, bardziej wydajne, w tym programy narzędziowe interakcyjna Eksploracja danych, analizy i raportowania oraz dla linii bazowej, modelowania i raportowania.
- Konfigurowanie **zasady kontroli zabezpieczeń** tych dwóch repozytoriów na Twoim koncie grupy.

Aby uzyskać szczegółowe instrukcje krok po kroku, zobacz [menedżera grupy zadań dla zespołu do nauki o danych](group-manager-tasks.md).


## <a name="team-lead-tasks"></a>Zadania kierownik zespołu

Lider zespołu (lub administratorem projektu wyznaczonym) podjęcie przetwarzania TDSP wykonywane są następujące zadania:

- Zaznaczenie DevOps platformy Azure jako kod hostingu platforma współpracy i przechowywania wersji, należy utworzyć **projektu** w usługach infrastruktury DevOps platformy Azure w grupie. W przeciwnym razie można pominąć to zadanie.
- Tworzenie **repozytorium szablonów projektu** w ramach projektu i inicjatora z repozytorium szablonów projektu grupy, o których tworzone przez menedżera grupy lub delegata menedżera.
- Tworzenie **repozytorium narzędzia zespołu**i Dodaj narzędzia specyficzne dla zespołu do repozytorium.
- (Opcjonalnie) Tworzenie **[usługi Azure file storage](https://azure.microsoft.com/services/storage/files/)** ma być używany do przechowywania zasobów danych, które mogą być przydatne dla całego zespołu. Inni członkowie zespołu mogą zainstalować ten magazyn plików udostępnionych chmury na swoich komputerach stacjonarnych analizy.
- (Opcjonalnie) Usługa Azure file storage, aby zainstalować **maszyny wirtualnej do nauki o danych** (DSVM) zespołu potencjalny klient i Dodaj zasoby danych na nim.
- Konfigurowanie **zabezpieczeniem** przez dodawanie członków zespołu i skonfiguruj swoje uprawnienia.

Aby uzyskać szczegółowe instrukcje krok po kroku, zobacz [lider zespołu zadań dla zespołu do nauki o danych](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Zadania potencjalnego klienta w projekcie

Następujące zadania są wykonywane przez projekt prowadzić do przyjęcia przetwarzania TDSP:

- Tworzenie **repozytorium projektu** w ramach projektu i umieszczenia go z repozytorium szablonów projektu.
- (Opcjonalnie) Tworzenie **usługi Azure file storage** ma być używany do przechowywania zasobów danych projektu.
- (Opcjonalnie) Usługa Azure file storage, aby zainstalować **maszyny wirtualnej do nauki o danych** (DSVM) projektu potencjalny klient i Dodaj zasoby danych projektu na nim.
- Konfigurowanie **zabezpieczeniem** przez dodawanie członków projektu i skonfiguruj swoje uprawnienia.

Aby uzyskać szczegółowe instrukcje krok po kroku, zobacz [projektu zadań dla zespołu do nauki o danych](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Pojedynczy Współautor zadania w projekcie

Następujące zadania są wykonywane przez poszczególne Współautor projektu (zwykle analitykiem danych) do prowadzenia projektu nauki o danych, korzystanie z przetwarzania TDSP:

- Klonuj **repozytorium projektu** przez kierownika projektu.
- (Opcjonalnie) Instalowanie udostępnionego **usługi Azure file storage** zespołu i projektu na ich **maszyny wirtualnej do nauki o danych** (DSVM).
- Wykonywanie projektu.


Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące wdrażania na projekt, zobacz [poszczególnych uczestników projektu dla zespołu do nauki o danych](project-ic-tasks.md).


## <a name="data-science-project-execution"></a>Wykonywanie projektu nauki o danych

Postępując zgodnie z odpowiednimi zestaw instrukcji, analityków danych, kierownika projektu i zespołów mogą tworzyć elementy robocze do śledzenia wszystkich zadań i etapów, które projekt wymaga od jego początku do końca. Ponadto za pomocą narzędzia git promuje współpracę między analitykami danych i zapewnia, że artefakty generowane podczas wykonywania projektów wersji kontrolowany i udostępnionych przez wszystkich członków projektu.

Z instrukcjami dla wykonywanie projektu zostały opracowane na podstawie przy założeniu, oba elementy robocze i projekt usługi git, które repozytoriów znajdują się w DevOps platformy Azure. Za pomocą usługi Azure DevOps dla obu pozwala połączyć elementy robocze z gałęziami usługi Git z repozytoriami projektu. W ten sposób można łatwo śledzić, co zostało zrobione dla elementu roboczego.

Poniższa ilustracja przedstawia ten przepływ pracy do wykonania projektu przy użyciu przetwarzania TDSP.

![Wykonywanie projektu nauki typowych danych](./media/roles-tasks/overview-project-execute.png)

Przepływ pracy zawiera kroki, które można podzielić na trzy czynności:

- Planowanie (projekt prowadzić) przebiegu
- Tworzenie artefaktów na odgałęzień git, aby rozwiązać elementy robocze (analityk danych)
- Przegląd kodu i scalanie gałęzi z głównej gałęzi (prowadzić projektów lub inni członkowie zespołu)

Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące przepływ wykonania projektu, zobacz [wykonywania projekty do nauki o danych](project-execution.md).

## <a name="project-structure"></a>Struktura projektu

Użyj tego [repozytorium szablonów projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate) umożliwiających wykonywanie wydajne projektu i współpracy. To repozytorium zawiera standardowy katalog struktury i szablony dokumentów używanych w projekcie środowiska TDSP.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z bardziej szczegółowy opis ról i zadań zdefiniowanych przez zespół danych dla celów naukowych:

- [Menedżer grupy zadań dla zespołu do nauki o danych](group-manager-tasks.md)
- [Zadania kierownik zespołu dla zespołu do nauki o danych](team-lead-tasks.md)
- [Zadania potencjalnych klientów dla zespołu do nauki o danych w projekcie](project-lead-tasks.md)
- [Poszczególnych uczestników projektu dla zespołu do nauki o danych](project-ic-tasks.md)
