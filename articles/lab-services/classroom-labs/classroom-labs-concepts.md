---
title: Koncepcje Classroom Labs - Usługi Azure Lab | Dokumenty firmy Microsoft
description: Poznaj podstawowe pojęcia dotyczące usług laboratoryjnych i jak można je łatwo tworzyć laboratoria i zarządzać nimi.
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
ms.openlocfilehash: 15fd3c18d059466c2b2bd5e2431013f393092b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526233"
---
# <a name="classroom-labs-concepts"></a>Pojęcia związane z laboratoriami na potrzeby zajęć

Poniższa lista zawiera kluczowe pojęcia i definicje usług lab:

## <a name="quota"></a>Limit przydziału

Przydział to limit czasu (w godzinach), który nauczyciel może ustawić dla ucznia, aby użyć maszyny Wirtualnej laboratorium. Można go ustawić na 0 lub określoną liczbę godzin. Jeśli przydział jest ustawiony na 0, uczeń może używać maszyny wirtualnej tylko wtedy, gdy harmonogram jest uruchomiony lub gdy nauczyciel ręcznie włącza maszynę wirtualną dla ucznia.  

Godziny przydziału są liczone, gdy uczeń sam uruchamia maszynę wirtualną laboratorium.  Jeśli nauczyciel ręcznie uruchamia maszynę wirtualną laboratorium dla ucznia, godziny przydziału nie są używane dla tego ucznia.

## <a name="schedules"></a>Harmonogramy

Harmonogramy to przedziały czasowe, które nauczyciel może utworzyć dla klasy, dzięki czemu maszyny wirtualne uczniów są dostępne dla czasu zajęć.  Harmonogramy mogą być jednorazowe lub cykliczne.  Godziny przydziału nie są używane, gdy harmonogram jest uruchomiony.

Istnieją trzy typy harmonogramów: Standardowy, Tylko start i Stop tylko.

- **Standard**.  Ten harmonogram rozpocznie wszystkie maszyny wirtualne dla uczniów o określonej godzinie rozpoczęcia i zamknie wszystkie maszyny wirtualne uczniów o określonej godzinie zatrzymania.
- **Tylko start**.   Ten harmonogram rozpocznie wszystkie maszyny wirtualne dla uczniów o określonej godzinie.  Maszyny wirtualne uczniów nie zostanie zatrzymana, dopóki student nie zatrzyma ich maszyny wirtualnej za pośrednictwem portalu usług Azure Lab Services lub harmonogram tylko zatrzymania.
- **Zatrzymaj się tylko**.  Ten harmonogram zatrzyma wszystkie maszyny wirtualne uczniów w określonym czasie.  

## <a name="template-virtual-machine"></a>Maszyna wirtualna szablonu

Maszyna wirtualna szablonu w laboratorium to podstawowy obraz maszyny wirtualnej, z którego tworzone są maszyny wirtualne wszystkich użytkowników. Trenerzy/twórcy laboratorium skonfigurować szablon maszyny wirtualnej i skonfigurować go z oprogramowaniem, które chcą zapewnić do szkolenia uczestników do laboratoriów. Podczas publikowania szablonu maszyny Wirtualnej usługi Azure Lab Services tworzy lub aktualizuje maszyny wirtualne laboratorium na podstawie szablonu maszyny Wirtualnej.

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

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę od skonfigurowania konta laboratorium wymaganego do utworzenia laboratorium na potrzeby zajęć przy użyciu usługi Azure Lab Services:

- [Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md)
