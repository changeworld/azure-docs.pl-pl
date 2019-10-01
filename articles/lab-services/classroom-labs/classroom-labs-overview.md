---
title: Informacje o Azure Lab Services | Microsoft Docs
description: Dowiedz się, w jaki sposób usługi laboratoryjne mogą ułatwić tworzenie, zarządzanie i zabezpieczanie laboratoriów z maszynami wirtualnymi, które mogą być używane przez deweloperów, testerów, nauczycieli, uczniów i innych.
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
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 67ebce2130bf5594d3c8c671934f7141a02eb60d
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694667"
---
# <a name="introduction-to-classroom-labs"></a>Wprowadzenie do laboratoriów zajęć
Azure Lab Services umożliwia szybkie skonfigurowanie środowiska laboratoryjnego do zajęć w chmurze. Nauczycieli tworzy laboratorium zajęć, Inicjuje obsługę systemu Windows lub maszyny wirtualne z systemem Linux, instaluje niezbędne laboratoria oprogramowania i narzędzi w klasie oraz udostępnia je uczniom. Studenci w klasie nawiązują połączenie z maszynami wirtualnymi w laboratorium i używają ich do swoich projektów, przydziałów, ćwiczeń. 

Laboratoria zajęć to zarządzane typy laboratorium zarządzane przez platformę Azure. Sama usługa obsługuje wszystkie zarządzanie infrastrukturą dla zarządzanego typu laboratorium, od nawirowania maszyn wirtualnych do obsługi błędów i skalowania infrastruktury. Należy określić rodzaj potrzebnej infrastruktury i zainstalować wszystkie narzędzia lub oprogramowanie wymagane przez klasę. 

## <a name="automatic-management-of-azure-infrastructure-and-scale"></a>Automatyczne zarządzanie infrastrukturą i skalą platformy Azure 
Azure Lab Services jest usługą zarządzaną, co oznacza, że inicjowanie obsługi i zarządzanie infrastrukturą podstawową laboratorium jest obsługiwane automatycznie przez usługę. Możesz po prostu skupić się na przygotowaniu odpowiedniego środowiska laboratoryjnego dla użytkowników. Zezwól usłudze na obsługę reszty i przewinięcie maszyn wirtualnych laboratorium do odbiorców. Skaluj swoje laboratorium do setek maszyn wirtualnych jednym kliknięciem.

## <a name="simple-experience-for-your-lab-users"></a>Proste środowisko dla użytkowników laboratorium 
Użytkownicy, którzy są zapraszani do laboratorium, uzyskują natychmiastowy dostęp do zasobów, które są umieszczane w laboratoriach. Wystarczy zalogować się, aby wyświetlić pełną listę maszyn wirtualnych, do których mają dostęp w wielu laboratoriach. Mogą oni kliknąć jeden przycisk, aby połączyć się z maszynami wirtualnymi i rozpocząć pracę. Użytkownicy nie potrzebują subskrypcji platformy Azure do korzystania z usługi. 

## <a name="cost-optimization-and-tracking"></a>Optymalizacja i śledzenie kosztów  
Kontynuuj sprawdzanie budżetu, kontrolując dokładnie tyle godzin, w których użytkownicy laboratorium mogą korzystać z maszyn wirtualnych. Skonfiguruj harmonogramy w laboratorium, aby umożliwić użytkownikom korzystanie z maszyn wirtualnych tylko w wyznaczonych godzinach czasu, lub skonfiguruj automatyczne zamykanie i godziny rozpoczęcia. Śledź użycie poszczególnych użytkowników i Ustaw limity.

## <a name="example-class-types"></a>Przykładowe typy klas
Laboratoria można skonfigurować dla kilku typów klas z Azure Lab Services. Zapoznaj się z [przykładowymi typami klas w Azure Lab Services](class-types.md) artykule dla kilku przykładowych typów klas, dla których można skonfigurować laboratoria z Azure Lab Services. 

## <a name="next-steps"></a>Następne kroki
Wprowadzenie do konfigurowania konta laboratorium, które jest wymagane do utworzenia laboratorium klasy, przy użyciu Azure Lab Services:

- [Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md)
