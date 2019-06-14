---
title: Dostosowywanie klastra vFXT Avere — platformy Azure
description: Przegląd ustawień niestandardowych w celu optymalizacji wydajności w Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f5e780dcab20befe19ca34020908eee93c290516
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409170"
---
# <a name="cluster-tuning"></a>Dostosowywanie klastra


Większość klastrów vFXT mogą korzystać z ustawień niestandardowych wydajności. Te ustawienia ułatwiają klastra by najlepiej pracować z określonego przepływu pracy, zestaw danych i narzędzia. 

To dostosowanie powinna być podejmowana wraz z przedstawicielem pomocy technicznej, ponieważ obejmuje ona zazwyczaj, konfigurując funkcje, które nie są dostępne z poziomu Panelu sterowania Avere.

W tej sekcji wyjaśniono, że niektóre niestandardowe dostrojenia, które można zrobić.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Optymalizacje ogólne

Te zmiany mogą zalecane, na podstawie właściwości zestawu danych lub stylu przepływu pracy.

* Jeżeli obciążenie jest procesów, należy zwiększyć rozmiar pamięci podręcznej zapisu z domyślnego z 20%. 
* Jeśli zestaw danych obejmuje wiele małych plików, należy zwiększyć limit liczby plików w klastrze pamięci podręcznej. 
* Jeśli praca obejmuje kopiowanie lub przenoszenie danych między dwoma repozytoriów, Dostosuj liczbę wątków używanych do przenoszenia danych: 
  * Aby zwiększyć szybkość, może zwiększyć liczby wątków równoległych, używane.
  * Jeśli wolumin magazynu zaplecza jest przeciążona, konieczne może zmniejszyć liczbę równoległych wątków używanych.
* Jeśli klaster buforuje dane przez filtr core, używającej NFSv4 listy kontroli dostępu, należy włączyć tryb dostępu do pamięci podręcznej w celu usprawnienia autoryzacji pliku dla konkretnej klientów.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Cloud NAS lub optymalizacje bramy w chmurze

Aby móc korzystać z wyższej szybkości danych między vFXT magazynu klastra i chmury w scenariuszu NAS lub brama chmury (gdzie klastra vFXT zapewnia stylu dla NAS dostępu do kontenera w chmurze), przedstawicielem może zalecić zmianę ustawienia, takie jak te ułatwiają agresywne wypychanie danych do woluminu magazynu z pamięci podręcznej:

* Zwiększ liczbę połączeń TCP między klastrem i kontener magazynu
* Zmniejsz wartość limitu czasu REST do komunikacji między klastrem, a aby ponowić próbę zapisu w magazynie wcześniej jeśli one nie natychmiast  
* Zwiększ rozmiar segmentu, dzięki czemu poszczególnych pól zaplecza tworzyć segmentami 8 MB fragmentów danych, a nie 1 MB

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Optymalizacji WAN hybrydowego lub dużego ruchu do chmury

W chmurze, przenoszenie obsługi dużego ruchu scenariusza lub scenariusza hybrydowego magazynu Optymalizacja sieci WAN (gdzie vFXT klastra zapewnia integrację między chmury i sprzętu magazynu lokalnego) te zmiany mogą być pomocne:

* Zwiększ liczbę dozwolonych między klastrem i filtr core połączeń TCP
* Ustawienie Optymalizacja sieci WAN filtr zdalnego core (to ustawienie może być używane filtr zdalnego w środowisku lokalnym lub filtr core chmury, w innym regionie platformy Azure).
* Zwiększ rozmiar buforu gniazda TCP (w zależności od potrzeb obciążenia i wydajności)
* Włącz ustawienie "zawsze Prześlij dalej" zmniejszyć nadmiarowo buforowanych plików (w zależności od potrzeb obciążenia i wydajności)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Pomoc optymalizacji Twojego vFXT Avere dla platformy Azure

Procedury opisane w [Uzyskaj pomoc dotyczącą systemu](avere-vfxt-open-ticket.md) do kontaktowania się z pracownikami działu pomocy technicznej o tych optymalizacjach. 