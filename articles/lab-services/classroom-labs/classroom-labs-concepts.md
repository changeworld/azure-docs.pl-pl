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
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 255cbff94e954dd05acfe77b97929c313b1dd3cf
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808580"
---
# <a name="classroom-labs-concepts"></a>Klasa Labs — pojęcia
Poniższa lista zawiera podstawowe pojęcia Lab Services i definicje:

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
