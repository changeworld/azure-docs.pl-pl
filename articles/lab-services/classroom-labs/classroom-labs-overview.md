---
title: Informacje o usłudze Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak usługa Lab Services może ułatwić tworzenie i zabezpieczanie laboratoriów z maszynami wirtualnymi, które mogą być używane przez deweloperów, testerów, nauczycieli, uczniów itd., a także zarządzanie tymi laboratoriami.
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
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: e9c3cae7c7129cc489ddd38b5b2de18dd6f52e58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660593"
---
# <a name="introduction-to-classroom-labs"></a>Wprowadzenie do laboratoriów na potrzeby zajęć
Usługa Azure Lab Services umożliwia szybkie skonfigurowanie środowiska laboratorium na potrzeby zajęć w chmurze. Nauczyciel tworzy laboratorium na potrzeby zajęć, aprowizuje maszyny wirtualne z systemem Windows lub Linux, instaluje niezbędne oprogramowanie i narzędzia wymagane podczas zajęć oraz udostępnia je studentom. Studenci na zajęciach łączą się z maszynami wirtualnymi w laboratorium, a następnie używają ich na potrzeby projektów, przydziałów i ćwiczeń wykonywanych na zajęciach. 

Laboratoria na potrzeby zajęć to laboratoria zarządzane przez platformę Azure. Sama usługa obsługuje całe zarządzanie infrastrukturą w laboratoriach zarządzanych, od zwiększania możliwości maszyn wirtualnych do obsługi błędów i skalowania infrastruktury. Użytkownik określa, jakiego rodzaju infrastruktury potrzebuje, i instaluje narzędzia lub oprogramowanie wymagane na zajęciach. Laboratoria zarządzane są obecnie w wersji zapoznawczej.  

## <a name="scenarios"></a>Scenariusze
Oto główny scenariusz obsługiwany przez laboratoria na potrzeby zajęć w usłudze Azure Lab Services: 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Konfigurowanie laboratorium komputerowego o zmiennym rozmiarze w chmurze na potrzeby zajęć  

- Utwórz laboratorium zarządzane na potrzeby zajęć. Po prostu poinformuj usługę, czego dokładnie potrzebujesz, a ona utworzy infrastrukturę laboratorium i będzie nią zarządzać, dzięki czemu można będzie się skupić na nauczaniu, a nie na technicznych szczegółach laboratorium. 
- Zapewnij uczniom laboratorium maszyn wirtualnych, na których skonfigurowano dokładnie to, co jest potrzebne na zajęciach. Przyznaj każdemu uczniowi ograniczoną liczbę godzin używania maszyn wirtualnych do pracy na zajęciach.  
- Przenieś fizyczne laboratorium komputerowe swojej szkoły do chmury. Automatycznie skaluj liczbę maszyn wirtualnych tylko do maksymalnego progu użycia i kosztów ustawionych dla laboratorium. 
- Gdy skończysz pracę, usuń laboratorium jednym kliknięciem. 

## <a name="user-profiles"></a>Profile użytkowników
W tym artykule opisano różne profile użytkowników w usłudze Azure Lab Services. 

### <a name="lab-account-owner"></a>Właściciel konta laboratorium
Zazwyczaj administrator IT zasobów chmury organizacji, który jest właścicielem subskrypcji platformy Azure działa jako właściciel konta laboratorium i wykonuje następujące zadania:   

- Konfiguruje konto laboratorium dla Twojej organizacji.
- Konfiguruje zasady dla wszystkich laboratoriów i zarządza nimi.
- Udziela osobom w organizacji uprawnień do tworzenia laboratorium w ramach konta laboratorium.

### <a name="educator"></a>Nauczyciel
Zazwyczaj użytkownicy, tacy jak nauczyciel lub instruktor w trybie online, tworzą laboratoria na potrzeby zajęć w ramach konta laboratorium. Nauczyciel wykonuje następujące zadania: 

- Tworzy laboratorium na potrzeby zajęć.
- Tworzy maszyny wirtualne w laboratorium. 
- Instaluje odpowiednie oprogramowanie na maszynach wirtualnych.
- Określa, kto ma dostęp do laboratorium.
- Udostępnia studentom link umożliwiający rejestrację w laboratorium.

### <a name="student"></a>Student
Student wykonuje następujące zadania:

- Używa linku rejestracji, który otrzymuje od twórcy laboratorium w celu zarejestrowania się w laboratorium. 
- Nawiązuje połączenie z maszyną wirtualną w laboratorium i używa jej do wykonywania prac, przydziałów i projektów na zajęciach. 

## <a name="next-steps"></a>Następne kroki
Rozpocznij pracę od skonfigurowania konta laboratorium wymaganego do utworzenia laboratorium na potrzeby zajęć przy użyciu usługi Azure Lab Services:

- [Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md)
