---
title: Informacje o laboratoriach szkolnych w usługach Azure Lab | Dokumenty firmy Microsoft
description: Dowiedz się, jak szybko skonfigurować środowisko laboratorium w klasie w chmurze — skonfiguruj laboratorium z szablonem maszyny Wirtualnej z oprogramowaniem wymaganym dla klasy i udostępnij kopię maszyny Wirtualnej każdemu uczniowi w klasie.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: bbbea0c943758c2682f7b166e461807d2914f790
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74561680"
---
# <a name="introduction-to-classroom-labs"></a>Wprowadzenie do laboratoriów na potrzeby zajęć
Usługa Azure Lab Services umożliwia szybkie skonfigurowanie środowiska laboratorium na potrzeby zajęć w chmurze. Nauczyciel tworzy laboratorium na potrzeby zajęć, aprowizuje maszyny wirtualne z systemem Windows lub Linux, instaluje niezbędne oprogramowanie i narzędzia wymagane podczas zajęć oraz udostępnia je studentom. Studenci na zajęciach łączą się z maszynami wirtualnymi w laboratorium, a następnie używają ich na potrzeby projektów, przydziałów i ćwiczeń wykonywanych na zajęciach. 

Laboratoria na potrzeby zajęć to typy laboratoriów zarządzanych przez platformę Azure. Sama usługa obsługuje całe zarządzanie infrastrukturą dla typu laboratorium zarządzanego, od zwiększania możliwości maszyn wirtualnych do obsługi błędów i skalowania infrastruktury. Użytkownik określa, jakiego rodzaju infrastruktury potrzebuje, i instaluje narzędzia lub oprogramowanie wymagane na zajęciach. 

## <a name="automatic-management-of-azure-infrastructure-and-scale"></a>Automatyczne zarządzanie skalą i infrastrukturą platformy Azure 
Azure Lab Services to usługa zarządzana, więc automatycznie obsługuje aprowizację podstawowej infrastruktury laboratorium oraz zarządzanie nią. Możesz się skupić wyłącznie na przygotowaniu odpowiedniego środowiska laboratorium dla swoich użytkowników. Pozwól usłudze zająć się resztą i wdrożyć maszyny wirtualne środowiska laboratoryjnego dla grona użytkowników. Skalowanie laboratorium do setek maszyn wirtualnych za pomocą jednego kliknięcia.

## <a name="simple-experience-for-your-lab-users"></a>Proste środowisko dla użytkowników laboratorium 
Użytkownicy, którzy są zapraszani do Twojego środowiska laboratoryjnego, uzyskują natychmiastowy dostęp do zasobów, które im przekażesz w swoich laboratoriach. Muszą tylko zalogować się, aby zobaczyć pełną listę maszyn wirtualnych, do których mają dostęp w wielu laboratoriach. Mogą kliknięciem jednego przycisku nawiązać połączenie z maszynami wirtualnymi i rozpocząć pracę. Nie muszą mieć subskrypcji platformy Azure, aby korzystać z usługi. 

## <a name="cost-optimization-and-tracking"></a>Optymalizacja i śledzenie kosztów  
Ogranicz zwiększanie budżetu, kontrolując dokładną liczbę godzin, przez które użytkownicy laboratorium mogą używać maszyn wirtualnych. Skonfiguruj harmonogramy w środowisku laboratoryjnym, aby pozwolić użytkownikom używać maszyn wirtualnych tylko w wyznaczonym czasie, lub skonfiguruj cykliczne czasy automatycznego zamykania i uruchamiania. Śledź użycie poszczególnych użytkowników i ustaw limity.

## <a name="example-class-types"></a>Przykładowe typy klas
Można skonfigurować laboratoria dla kilku typów klas za pomocą usługi Azure Lab Services. Zobacz [przykładowe typy klas w usłudze Azure Lab Services](class-types.md) artykuł na kilka przykładowych typów klas, dla których można skonfigurować laboratoria z usługami Azure Lab Services. 

## <a name="next-steps"></a>Następne kroki
Rozpocznij pracę od skonfigurowania konta laboratorium wymaganego do utworzenia laboratorium na potrzeby zajęć przy użyciu usługi Azure Lab Services:

- [Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md)
