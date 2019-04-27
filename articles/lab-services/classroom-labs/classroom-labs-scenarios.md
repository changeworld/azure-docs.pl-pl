---
title: Laboratorium na potrzeby zajęć na użytek szkoleniach — usługi Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure DevTest Labs na potrzeby scenariuszy szkoleniowych.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 4d2ba11181977f1976b5ae933e8b93a92424fa96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695293"
---
# <a name="use-classroom-labs-for-trainings"></a>Laboratorium na potrzeby zajęć na użytek szkoleniach
Można skonfigurować laboratorium na potrzeby szkolenia. Klasa laboratoria usługi Azure Lab Services pozwalają na tworzenie laboratorium na potrzeby szkolenia gdzie każdy szkoleniowych używa identyczne i wyizolowanego środowiska do szkolenia. Można zastosować zasad w celu zapewnienia środowiska szkoleń są dostępne dla każdego szkoleniowych, tylko wtedy, gdy są potrzebne i zawiera za mało zasobów — takich jak virtual machines — wymagana na szkolenia. 

![Laboratorium na potrzeby zajęć](../media/classroom-labs-scenarios/classroom.png)

Laboratorium na potrzeby zajęć, spełnia następujące wymagania, które są wymagane do prowadzenia szkoleń w dowolnym środowisku wirtualnym: 

- Zaleć można szybko inicjować obsługę środowisk szkolenia
- Do każdej maszyny szkolenia powinny być identyczne
- Maszyny wirtualne utworzone przez innych Zaleć Zaleć nie są widoczne.
- Kontrolowanie kosztów przez zapewnienie, że Zaleć nie można pobrać więcej maszyn wirtualnych, niż jest to wymagane dla szkoleń, a także zamykania maszyn wirtualnych, jeśli ich nie używają
- Łatwe udostępnianie laboratorium szkolenia każda szkoleniowych
- Ponowne użycie laboratorium szkolenia ponownego

W tym artykule poznasz różne funkcje usługi Azure Lab Services, których można użyć w celu spełnienia wymagań dotyczących szkoleń opisany wcześniej i szczegółowy opis kroków, które można wykonać, aby skonfigurować laboratorium na potrzeby szkolenia.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Utwórz konto laboratorium jako administratora konta laboratorium
Pierwszym krokiem przy użyciu usługi Azure Lab Services jest utworzenie konta laboratorium w witrynie Azure portal. Gdy administrator konta laboratorium utworzy konto laboratorium, administrator dodaje użytkowników, którzy chcą tworzyć laboratoriów **twórca laboratorium** roli. Wykładowców utworzyć laboratoria z maszynami wirtualnymi dla uczniów lub studentów w celu ćwiczenia dotyczące kursów, które są one przeznaczone. Aby uzyskać więcej informacji, zobacz [tworzenie i zarządzanie kontem laboratorium](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Tworzenie i zarządzanie laboratorium na potrzeby zajęć
Trainer, będący członkiem roli Twórca laboratorium na koncie laboratorium, można utworzyć przynajmniej jednego laboratoriów w ramach konta laboratorium. Możesz utworzyć i skonfigurować szablon maszyny Wirtualnej za pomocą całe oprogramowanie wymagane do wykonywania ćwiczeniami z kursu. Wybierz obraz gotowych do użycia z dostępnych obrazów do tworzenia laboratorium na potrzeby zajęć, a następnie dostosuj je, instalując oprogramowanie wymagane do laboratorium. Aby uzyskać więcej informacji, zobacz [tworzenie i zarządzanie laboratorium na potrzeby zajęć](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Konfigurowanie zasad i ustawień użycia
Twórca laboratorium można dodawać lub usuwać użytkowników z laboratorium, uzyskać łącze, aby wysłać do użytkowników w laboratorium, skonfiguruj zasady, takie jak przydziały poszczególne ustawienia dla każdego użytkownika, aktualizacji liczbę maszyn wirtualnych dostępnych w laboratorium i nie tylko. Aby uzyskać więcej informacji, zobacz [konfigurowania ustawień użycia i zasad](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Tworzenie harmonogramów i zarządzanie nimi
Harmonogramy umożliwiają konfigurowanie laboratorium na potrzeby zajęć w taki sposób, że maszyny wirtualne w laboratorium automatycznie uruchom i zamknij o określonej godzinie. Można zdefiniować jednorazowe harmonogramu lub Harmonogram cykliczny. Aby uzyskać więcej informacji, zobacz [tworzenie i zarządzanie nimi harmonogramy dla laboratorium na potrzeby zajęć](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Konfigurowanie i publikowanie szablonu maszyny Wirtualnej
Szablon w laboratorium to podstawowy obraz maszyny wirtualnej, który służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Szablon maszyny Wirtualnej należy skonfigurować tak, że jest ona skonfigurowana przy użyciu dokładnie co chcesz udostępnić uczestnikom szkolenia. Możesz podać nazwę i opis szablonu, które będą widoczne dla użytkowników laboratorium. Następnie możesz opublikować szablonu, aby udostępnić użytkownikom laboratorium wystąpień szablonu maszyny Wirtualnej. Gdy opublikujesz szablon, usługa Azure Lab Services utworzy maszyny wirtualne w laboratorium przy użyciu tego szablonu. Liczba maszyn wirtualnych utworzonych w ramach tego procesu jest taka sama jak maksymalna liczba użytkowników, którzy mogą korzystać z laboratorium. Tę liczbę można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon. Aby uzyskać więcej informacji, zobacz [Ustaw Konfigurowanie i publikowanie szablonów maszyn wirtualnych](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Używanie maszyn wirtualnych w laboratorium na potrzeby zajęć
Uczniem, studentem lub uczestnik szkolenia rejestruje na laboratorium, a następnie nawiązuje połączenie z maszyną wirtualną w celu ćwiczenia kurs. Aby uzyskać więcej informacji, zobacz [jak dostęp do laboratorium na potrzeby zajęć](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Kolejne kroki
Rozpocznij od utworzenia konta laboratorium w laboratorium na potrzeby zajęć, postępując zgodnie z instrukcjami opisanymi w artykule: [Samouczek: Konfigurowanie konta laboratorium przy użyciu usługi Azure Lab Services](tutorial-setup-lab-account.md).