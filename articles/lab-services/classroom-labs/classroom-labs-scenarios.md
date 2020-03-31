---
title: Korzystanie z classroom labs do szkoleń — Usługi Azure Lab
description: W tym artykule opisano sposób używania laboratoriów DevTest Platformy Azure do tworzenia laboratoriów na platformie Azure w scenariuszach szkoleniowych.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 155806222f9e11fec177487b7147d81054ac06ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717988"
---
# <a name="use-classroom-labs-for-trainings"></a>Korzystanie z Classroom Labs do szkoleń
Można skonfigurować laboratorium do treningów. Classroom Labs of Azure Lab Services umożliwiają utworzenie laboratorium dla szkolenia, w którym każdy stażysta używa identycznych i odizolowanych środowisk do szkolenia. Można zastosować zasady, aby upewnić się, że środowiska szkoleniowe są dostępne dla każdego stażysty tylko wtedy, gdy ich potrzebują i zawierają wystarczającą ilość zasobów — takich jak maszyny wirtualne — wymaganych do szkolenia. 

![Laboratorium w klasie](../media/classroom-labs-scenarios/classroom.png)

Classroom Labs spełnia następujące wymagania wymagane do prowadzenia szkoleń w dowolnym środowisku wirtualnym: 

- Stażyści mogą szybko zapewniać swoje środowisko szkoleniowe
- Każda maszyna treningowa powinna być identyczna
- Stażyści nie widzą maszyn wirtualnych utworzonych przez innych stażystów
- Kontroluj koszty, zapewniając, że stażyści nie mogą uzyskać większej liczby maszyn wirtualnych niż potrzebują do szkolenia, a także zamykać maszyny wirtualne, gdy ich nie używają
- Łatwe udostępnianie laboratorium szkoleniowego każdemu stażyście
- Ponowne wykorzystanie laboratorium szkoleniowego

W tym artykule dowiesz się o różnych funkcjach usługi Azure Lab Services, które mogą służyć do spełnienia wcześniej opisanych wymagań szkoleniowych i szczegółowych kroków, które można wykonać, aby skonfigurować laboratorium do szkolenia.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Tworzenie konta laboratorium jako administratora konta laboratorium
Pierwszym krokiem w użyciu usługi Azure Lab Services jest utworzenie konta laboratorium w witrynie Azure portal. Gdy administrator konta laboratorium utworzy konto laboratorium, administrator dodaje użytkowników, którzy chcą utworzyć laboratoria do roli **Twórca laboratorium.** Trenerzy tworzą laboratoria z maszynami wirtualnymi dla studentów do wykonywania ćwiczeń na kurs, który nauczają. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta laboratorium i zarządzanie nim](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Tworzenie laboratoriów na potrzeby zajęć i zarządzanie nimi
Trener, który jest członkiem roli Twórca laboratorium na koncie laboratorium, można utworzyć jeden lub więcej laboratoriów na koncie laboratorium. Tworzenie i konfigurowanie szablonu maszyny Wirtualnej ze wszystkimi wymaganymi programami do wykonywania ćwiczeń w trakcie kursu. Możesz wybrać gotowy obraz z dostępnych obrazów do utworzenia laboratorium w klasie, a następnie dostosować go, instalując oprogramowanie wymagane dla laboratorium. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie laboratoriów w klasie i zarządzanie nimi.](how-to-manage-classroom-labs.md)

## <a name="configure-usage-settings-and-policies"></a>Konfigurowanie ustawień i zasad użycia
Twórca laboratorium może dodawać lub usuwać użytkowników do laboratorium, uzyskać łącze rejestracyjne do wysyłania do użytkowników laboratorium, skonfigurować zasady, takie jak ustawianie poszczególnych przydziałów na użytkownika, aktualizowanie liczby maszyn wirtualnych dostępnych w laboratorium i inne. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie ustawień i zasad użytkowania](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Tworzenie harmonogramów i zarządzanie nimi
Harmonogramy umożliwiają skonfigurowanie laboratorium w klasie w taki sposób, aby maszyny wirtualne w laboratorium automatycznie uruchamiane i zamykane w określonym czasie. Można zdefiniować harmonogram jednorazowy lub harmonogram cykliczny. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie harmonogramów dla laboratoriów w klasie i zarządzanie nimi.](how-to-create-schedules.md)

## <a name="set-up-and-publish-a-template-vm"></a>Konfigurowanie i publikowanie szablonu maszyny Wirtualnej
Szablon w laboratorium to podstawowy obraz maszyny wirtualnej, który służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Skonfiguruj szablon maszyny Wirtualnej, tak aby była skonfigurowana z dokładnie tym, co chcesz zapewnić uczestnikom szkolenia. Możesz podać nazwę i opis szablonu, które będą widoczne dla użytkowników laboratorium. Następnie należy opublikować szablon, aby wystąpienia szablonu maszyny Wirtualnej dostępne dla użytkowników laboratorium. Gdy opublikujesz szablon, usługa Azure Lab Services utworzy maszyny wirtualne w laboratorium przy użyciu tego szablonu. Liczba maszyn wirtualnych utworzonych w ramach tego procesu jest taka sama jak maksymalna liczba użytkowników, którzy mogą korzystać z laboratorium. Tę liczbę można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie i publikowanie maszyn wirtualnych szablonów](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Używanie maszyn wirtualnych w laboratorium w klasie
Student lub uczestnik szkolenia rejestruje się w laboratorium i łączy się z maszyną wirtualną, aby wykonywać ćwiczenia na kurs. Aby uzyskać szczegółowe informacje, zobacz [Jak uzyskać dostęp do laboratorium w klasie](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Następne kroki
Zacznij od utworzenia konta laboratorium w Classroom Labs, wykonując instrukcje zawarte w artykule: [Samouczek: Konfigurowanie konta laboratoryjnego za pomocą usługi Azure Lab Services](tutorial-setup-lab-account.md).