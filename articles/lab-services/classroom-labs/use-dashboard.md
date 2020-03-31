---
title: Używanie pulpitu nawigacyjnego dla laboratorium w klasie w usłudze Azure Lab Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak korzystać z pulpitu nawigacyjnego w laboratorium w klasie w usłudze Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 363e5705e9ccf4aa207ff7e5cafb615bc01bc7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538944"
---
# <a name="dashboard-for-classroom-labs"></a>Deska rozdzielcza do laboratoriów szkolnych
W tym artykule opisano widok pulpitu nawigacyjnego laboratorium w klasie w usłudze Azure Lab Services. 

![Pulpit nawigacyjny](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Koszty i kafelek rozliczeniowe
Ten kafelek zawiera następujące szczegóły szacowania kosztów:

| Ustawienie | Wartość | 
| ------- | ----- | 
| Godziny przydziału | Maksymalna liczba godzin, przez które użytkownik może korzystać z maszyny Wirtualnej poza zaplanowanymi godzinami. |
| Godziny planowe | Godziny, które będą ponoszone na podstawie harmonogramu ustawionego w laboratorium. Ta wartość jest dostępna tylko wtedy, gdy istnieje od/do daty ustawiona na wszystkich zdarzeniach harmonogramu. |
| Godziny/użytkownik | Suma godzin przydziału i godzin planowych. |
| Maksymalna liczba użytkowników | Maksymalna liczba użytkowników w laboratorium na podstawie wszystkich maszyn wirtualnych, które mają być zgłoszone. |
| Godziny x użytkownicy | Godziny/użytkownik pomnożona przez liczbę użytkowników. |
| Skorygowany przydział | Suma godzin przydziału dodanych do określonych użytkowników. |
| Łączna liczba godzin * $/godz. | Koszt na godzinę na podstawie wybranego rozmiaru maszyny Wirtualnej. Jest to oparte na regularnej płacy w miarę jak idziesz cena. |
| Całkowity szacowany koszt | Jest to maksymalna cena dla tego laboratorium na podstawie bieżących ustawień. |

## <a name="template-tile"></a>Kafelek szablonu
Na tym kafelku są widoczne następujące informacje:

- Data utworzenia szablonu 
- Data ostatniej publikacji szablonu 

Ma również łącze, aby przejść do **szablonu** strony, gdzie można [zarządzać szablonu maszyny Wirtualnej](how-to-create-manage-template.md) dla klasy. 

## <a name="virtual-machine-pool-tile"></a>Kafelek puli maszyn wirtualnych

Na tym kafelku są widoczne następujące informacje:

- Liczba maszyn wirtualnych przypisanych do studentów (użytkowników)
- Liczba maszyn wirtualnych, które nie zostały jeszcze przypisane do studentów

Zawiera również łącze do strony **puli maszyn wirtualnych,** gdzie można [zarządzać pulą maszyn wirtualnych](how-to-set-virtual-machine-passwords.md) w laboratorium. 

## <a name="users-tile"></a>Kafelek Użytkownicy

Na tym kafelku są widoczne następujące informacje:

- Liczba użytkowników zarejestrowanych w klasie
- Liczba użytkowników, którzy są dodani do laboratorium, ale nie zarejestrowani w klasie 

Zawiera również łącze umożliwiające przejście do strony **Użytkownicy,** na której można [zarządzać użytkownikami](how-to-configure-student-usage.md) w laboratorium. 

## <a name="schedules-tile"></a>Kafelek Harmonogramy
Widoczne są bieżące zaplanowane zdarzenia dla laboratorium na kafelku. Zawiera również łącze umożliwiające przejście do strony **Harmonogram,** na której można [tworzyć harmonogramy i zarządzać nimi.](how-to-create-schedules.md) Kafelek zawiera szczegółowe informacje dotyczące tylko dwóch zaplanowanych zdarzeń i liczby pozostałych zaplanowanych zdarzeń w laboratorium. 

![Zaplanowane zdarzenia](../media/use-dashboard/scheduled-events.png)

