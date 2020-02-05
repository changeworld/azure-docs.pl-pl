---
title: Azure VMware Solutions (Automatyczna synchronizacja) — eskalacja uprawnień automatycznej synchronizacji
description: Opisuje sposób eskalacji uprawnień automatycznej synchronizacji w celu wykonywania funkcji administracyjnych w programie vCenter w chmurze prywatnej do automatycznej synchronizacji
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 90dd61fc9856978bab0b68de19d48493a8e0c5fd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025337"
---
# <a name="escalate-avs-privileges-to-perform-administrative-functions-in-avs-private-cloud-vcenter"></a>Eskalacja uprawnień automatycznej synchronizacji w celu wykonywania funkcji administracyjnych w programie do automatycznej synchronizacji w programie vCenter w chmurze prywatnej

Podejście do automatycznej synchronizacji ma na celu umożliwienie użytkownikom programu vCenter wymaganych uprawnień do wykonywania normalnych operacji. W niektórych przypadkach użytkownik może wymagać dodatkowych uprawnień do wykonania określonego zadania. Można eskalować uprawnienia użytkownika usługi vCenter SSO przez ograniczony okres.

Przyczyny eskalacji uprawnień mogą być następujące:

* Konfiguracja źródeł tożsamości
* Zarządzanie użytkownikami
* Usuwanie rozproszonej grupy portów
* Instalowanie rozwiązań vCenter (takich jak aplikacje do tworzenia kopii zapasowych)
* Tworzenie kont usług

> [!WARNING]
> Akcje podjęte w stanie eskalacji uprzywilejowanej mogą mieć negatywny wpływ na system i mogą spowodować, że system stanie się niedostępny. Wykonaj tylko niezbędne akcje w okresie eskalacji.

W portalu automatycznej synchronizacji [zaeskalacjnie uprawnień](escalate-private-cloud-privileges.md) dla lokalnego użytkownika CloudOwner na serwerze vCenter SSO. Możesz eskalować uprawnienia użytkownika zdalnego tylko wtedy, gdy na serwerze vCenter jest skonfigurowany dodatkowy dostawca tożsamości. Eskalacja uprawnień obejmuje dodanie wybranego użytkownika do wbudowanej grupy administratorów vSphere. Tylko jeden użytkownik może mieć uprawnienia eskalacji. Jeśli musisz eskalować uprawnienia innego użytkownika, najpierw Anuluj eskalację uprawnień bieżących użytkowników.

Użytkownicy z dodatkowych źródeł tożsamości muszą zostać dodani jako członkowie grupy CloudOwner.

> [!CAUTION]
> Nowi użytkownicy muszą zostać dodani tylko *do chmury-właściciel-Grupa*, *chmura-Global-Cluster-admin-* Group, Cloud- *Global-Storage-Administrator-* Group, *Cloud-Global-Network-admin* -Group  Użytkownicy dodani do grupy *administratorzy* zostaną usunięci automatycznie.  Tylko konta usług należy dodać do grupy *administratorzy* , a konta usług nie mogą być używane do logowania się do interfejsu użytkownika sieci Web vSphere.
W okresie eskalacji automatyczna synchronizacja ze skojarzonymi powiadomieniami o alertach umożliwia zidentyfikowanie wszelkich niezamierzonych zmian w środowisku.
