---
title: Rozwiązanie Azure VMware według cloudsimple — uprawnienia Eskalacja cloudsimple
description: W tym artykule opisano sposób eskalacji uprawnień CloudSimple do wykonywania funkcji administracyjnych w centrum vCenter private cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025337"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Eskalacja cloudSimple uprawnienia do wykonywania funkcji administracyjnych w private cloud vCenter

Podejście CloudSimple uprawnienia ma na celu nadadać użytkownikom vCenter uprawnienia, których potrzebują do wykonywania normalnych operacji. W niektórych przypadkach użytkownik może wymagać dodatkowych uprawnień do wykonania określonego zadania.  Można eskalować uprawnienia użytkownika SSO vCenter przez ograniczony okres.

Przyczyny eskalacji uprawnień mogą być następujące:

* Konfiguracja źródeł tożsamości
* Zarządzanie użytkownikami
* Usuwanie rozproszonej grupy portów
* Instalowanie rozwiązań vCenter (takich jak aplikacje do tworzenia kopii zapasowych)
* Tworzenie kont usług

> [!WARNING]
> Akcje podejmowane w stanie uprzywilejowanym może niekorzystnie wpłynąć na system i może spowodować, że system stanie się niedostępny. Wykonaj tylko niezbędne akcje w okresie eskalacji.

Z portalu CloudSimple, [eskalacji uprawnień](escalate-private-cloud-privileges.md) dla użytkownika lokalnego CloudOwner na vCenter SSO.  Uprawnienia użytkownika zdalnego można eskalować tylko wtedy, gdy dodatkowy dostawca tożsamości jest skonfigurowany w centrum vCenter.  Eskalacja uprawnień polega na dodaniu wybranego użytkownika do wbudowanej grupy Administratorzy vSphere.  Tylko jeden użytkownik może mieć eskalacji uprawnień.  Jeśli musisz eskalować uprawnienia innego użytkownika, najpierw de-eskalacji uprawnień bieżących użytkowników.

Użytkownicy z dodatkowych źródeł tożsamości muszą zostać dodana jako członkowie grupy CloudOwner.

> [!CAUTION]
> Nowi użytkownicy muszą być dodani tylko do *grupy Cloud-Owner,* *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* lub *Cloud-Global-VM-Admin-Group*.  Użytkownicy dodani do grupy *Administratorzy* zostaną automatycznie usunięci.  Tylko konta usług muszą być dodawane do grupy *administratorów* i kont usług nie mogą być używane do logowania się do interfejsu użytkownika sieci web vSphere.

W okresie eskalacji CloudSimple używa automatycznego monitorowania z skojarzonymi powiadomieniami o alertach, aby zidentyfikować wszelkie niezamierzone zmiany w środowisku.
