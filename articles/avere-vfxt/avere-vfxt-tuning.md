---
title: Dostrajanie klastra Avere vFXT — platforma Azure
description: Omówienie ustawień niestandardowych w celu optymalizacji wydajności w aplikacji Avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152939"
---
# <a name="cluster-tuning"></a>Dostosowywanie klastra

Większość klastrów vFXT może korzystać z niestandardowych ustawień wydajności. Te ustawienia ułatwiają klastrowi najlepszą pracę z określonym przepływem pracy, zestawem danych i narzędziami.

To dostosowanie należy wykonać z pomocą przedstawiciela pomocy technicznej, ponieważ może to obejmować konfigurowanie funkcji, które nie są dostępne w Panelu sterowania Avere.

W tej sekcji opisano niektóre strojenie niestandardowe, które można wykonać.

## <a name="general-optimizations"></a>Optymalizacje ogólne

Te zmiany mogą być zalecane na podstawie jakości zestawu danych lub stylu przepływu pracy.

* Jeśli obciążenie jest dużo zapisu, należy zwiększyć rozmiar pamięci podręcznej zapisu z domyślnej 20%.
* Jeśli zestaw danych obejmuje wiele małych plików, zwiększ limit liczby plików w pamięci podręcznej klastra.
* Jeśli praca polega na kopiowaniu lub przenoszeniu danych między dwoma repozytoriami, dostosuj liczbę wątków używanych do przenoszenia danych:
  * Aby zwiększyć szybkość, można zwiększyć liczbę równoległych wątków używanych.
  * Jeśli wolumin magazynu zaplecza staje się przeciążony, może być konieczne zmniejszenie liczby używanych równoległych wątków.
* Jeśli klaster buforuje dane dla głównego filera korzystającego z list ACL NFSv4, włącz buforowanie trybu dostępu, aby usprawnić autoryzację plików dla określonych klientów.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Optymalizacje serwera NAS w chmurze lub bramy w chmurze

W scenariuszu serwera NAS lub bramy w chmurze klaster vFXT zapewnia dostęp do kontenera w chmurze w stylu NAS. Aby korzystać z wyższych szybkości danych między klastrem vFXT a magazynem w chmurze, przedstawiciel może zalecić zmianę ustawień, aby bardziej agresywnie wypychać dane do woluminu magazynu z pamięci podręcznej. Przykład:

* Zwiększenie liczby połączeń TCP między klastrem a kontenerem magazynu

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Chmura pęknięcie lub hybrydowe optymalizacje WAN

W scenariuszu tworzenia warstwy w chmurze lub scenariuszu optymalizacji sieci WAN magazynu hybrydowego klaster vFXT zapewnia integrację między chmurą a lokalnym magazynem sprzętu. Te zmiany mogą być pomocne:

* Zwiększenie liczby dozwolonych połączeń TCP między klastrem a głównym filerem
* Włącz ustawienie Optymalizacji sieci WAN dla zdalnego rejestratora rdzenia (to ustawienie może być używane dla zdalnego lokalnego filera lub filera z rdzeniem chmury w innym regionie platformy Azure).
* Zwiększanie rozmiaru buforu gniazda TCP<sup>*</sup>
* Włącz ustawienie "zawsze do przodu", aby zmniejszyć nadmiarowo buforowane pliki<sup>*</sup>

<sup>*</sup>Te korekty mogą nie dotyczyć wszystkich systemów, w zależności od obciążenia i wymagań dotyczących wydajności.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Pomóż zoptymalizować avere vFXT na platformie Azure

Aby skontaktować się z pracownikami pomocy technicznej w sprawie tych optymalizacji, skorzystaj z procedury [opisanej](avere-vfxt-open-ticket.md)w temacie Uzyskaj pomoc dotyczącą systemu .
