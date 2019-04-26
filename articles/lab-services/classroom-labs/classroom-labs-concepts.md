---
title: Klasa Labs pojęcia — usługi Azure Lab Services | Dokumentacja firmy Microsoft
description: Podstawowe informacje na temat Lab Services i jak może być łatwo tworzyć i zarządzać nimi labs.
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
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 82f9d7090d7283e0b00b36e0928fffb3395ca4c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679090"
---
# <a name="classroom-labs-concepts"></a>Pojęcia związane z laboratoriami na potrzeby zajęć
Poniższa lista zawiera podstawowe pojęcia Lab Services i definicje:

## <a name="quota"></a>Przydział
Przydział to limit czasu (w godzinach), który nauczyciel można ustawić dla uczniów lub studentów użyć maszynę Wirtualną laboratorium. Można go ustawić na 0, nieograniczone, lub określoną liczbę godzin. Jeśli limit przydziału jest równa 0, uczniem/uczennicą można używać maszyny wirtualnej tylko, gdy działa zgodnie z harmonogramem lub nauczyciel ręcznie włącza maszyny wirtualnej dla uczniów lub studentów.
 
## <a name="schedules"></a>Harmonogramy
Harmonogramy są odstępów czasu (jednorazowo lub cykliczne), tworzonych przez uczniów w klasie. Wszystkie maszyny wirtualne w laboratorium są uruchamiane automatycznie na początku z harmonogramem i pozostaną one zatrzymane na końcu harmonogramu. Godz. limit przydziału nie są używane, gdy działa zgodnie z harmonogramem.

## <a name="template-virtual-machine"></a>Szablon maszyny wirtualnej
Szablon maszyny wirtualnej w laboratorium jest obrazem podstawowa maszyna wirtualna, z której wszyscy użytkownicy, maszyny wirtualne są tworzone. Twórcy Instruktorzy/lab skonfigurować szablon maszyny wirtualnej i skonfiguruj je przy użyciu oprogramowania, które mają być zapewnienie uczestnicy szkolenia celu labs. Podczas publikowania szablonu maszyny Wirtualnej usługi Azure Lab Services tworzy lub aktualizuje laboratorium maszyn wirtualnych na podstawie szablonu maszyny Wirtualnej. 


## <a name="user-profiles"></a>Profile użytkowników
W tym artykule opisano różne profile użytkowników w usłudze Azure Lab Services. 

### <a name="lab-account-owner"></a>Właściciel konta laboratorium
Zazwyczaj administrator IT zasobów chmury organizacji, który jest właścicielem subskrypcji platformy Azure działa jako właściciel konta laboratorium i wykonuje następujące zadania:   

- Konfiguruje konto laboratorium dla Twojej organizacji.
- Konfiguruje zasady dla wszystkich laboratoriów i zarządza nimi.
- Udziela osobom w organizacji uprawnień do tworzenia laboratorium w ramach konta laboratorium.

### <a name="professor"></a>Profesor
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

## <a name="next-steps"></a>Kolejne kroki
Rozpocznij pracę od skonfigurowania konta laboratorium wymaganego do utworzenia laboratorium na potrzeby zajęć przy użyciu usługi Azure Lab Services:

- [Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md)
