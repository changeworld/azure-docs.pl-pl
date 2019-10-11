---
title: Dostrajanie klastra avere vFXT — platforma Azure
description: Omówienie ustawień niestandardowych w celu zoptymalizowania wydajności w programie avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 274a20f84b978b38a284d1feb315d6621b7d3ccd
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256154"
---
# <a name="cluster-tuning"></a>Dostrajanie klastra


Większość klastrów vFXT może korzystać z niestandardowych ustawień wydajności. Te ustawienia pomagają, aby klaster działał najlepiej z określonym przepływem pracy, zestawem danych i narzędziami. 

Takie dostosowanie powinno odbywać się wraz z przedstawicielem pomocy technicznej, ponieważ zazwyczaj obejmuje Konfigurowanie funkcji, które nie są dostępne w panelu sterowania avere.

W tej sekcji opisano niektóre niestandardowe dostrajania, które można wykonać.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Optymalizacje ogólne

Te zmiany mogą być zalecane na podstawie jakości zestawu danych lub stylu przepływu pracy.

* Jeśli obciążenie ma duże możliwości zapisu, należy zwiększyć rozmiar pamięci podręcznej zapisu z wartości domyślnej 20%. 
* Jeśli zestaw danych zawiera wiele małych plików, zwiększ limit liczby plików pamięci podręcznej klastra. 
* Jeśli prace obejmują kopiowanie lub przeniesienie danych między dwoma repozytoriami, Dostosuj liczbę wątków używanych do przeniesienia danych: 
  * Aby zwiększyć szybkość, można zwiększyć liczbę używanych wątków równoległych.
  * Jeśli wolumin magazynu zaplecza zostanie przeciążony, może być konieczne zmniejszenie liczby użytych wątków równoległych.
* Jeśli klaster buforuje dane dla podstawowego pliku, który używa list ACL NFSv4, Włącz buforowanie trybu dostępu, aby usprawnić autoryzację plików dla określonych klientów.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Optymalizacje NAS lub Cloud Gateway w chmurze

Aby korzystać z wyższych szybkości danych między klastrem vFXT i magazynem w chmurze w scenariuszu usługi NAS lub bramy w chmurze (gdzie klaster vFXT zapewnia dostęp w stylu NAS do kontenera chmury), przedstawiciel może zalecić zmianę ustawień takich jak te agresywne wypychanie danych do woluminu magazynu z pamięci podręcznej:

* Zwiększ liczbę połączeń TCP między klastrem a kontenerem magazynu

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Przenoszenie w chmurze lub hybrydowe optymalizacje sieci WAN

W scenariuszu obejmującym scalanie w chmurze lub hybrydowe scenariusze optymalizacji sieci równorzędnej (w przypadku których klaster vFXT zapewnia integrację między magazynem w chmurze i lokalnym magazynu sprzętu) te zmiany mogą być przydatne:

* Zwiększ liczbę połączeń TCP dozwolonych między klastrem a podstawowym plikiem
* Włącz ustawienie optymalizacji sieci WAN dla zdalnego pliku podstawowego. to ustawienie może być używane w przypadku zdalnego lokalnego pliku lub pliku w chmurze w innym regionie świadczenia usługi Azure.
* Zwiększ rozmiar buforu gniazda TCP (w zależności od potrzeb obciążenia i wydajności)
* Włącz ustawienie "Zawsze przesyłaj dalej", aby zmniejszyć ilość plików buforowanych w pamięci podręcznej (w zależności od potrzeb obciążenia i wydajności)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Pomóż zoptymalizować swój avere vFXT dla platformy Azure

Skorzystaj z procedury opisanej w artykule [Uzyskaj pomoc](avere-vfxt-open-ticket.md) dotyczącą systemu, aby skontaktować się z personelem pomocy technicznej na temat tych optymalizacji. 