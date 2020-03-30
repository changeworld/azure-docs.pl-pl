---
title: Lokalizacje danych dla pulpitu wirtualnego systemu Windows — platforma Azure
description: Krótki przegląd lokalizacji danych i metadanych pulpitu wirtualnego systemu Windows są przechowywane.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128042"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Lokalizacje danych dla pulpitu wirtualnego systemu Windows

Pulpit wirtualny systemu Windows jest obecnie dostępny dla wszystkich lokalizacji geograficznych. Początkowo metadane usługi mogą być przechowywane tylko w lokalizacji geograficznej Stanów Zjednoczonych (USA). Administratorzy mogą wybrać lokalizację do przechowywania danych użytkownika podczas tworzenia maszyn wirtualnych puli hostów i skojarzonych usług, takich jak serwery plików. Dowiedz się więcej o lokalizacjach geograficznych platformy Azure na [mapie centrum danych platformy Azure.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>Firma Microsoft nie kontroluje ani nie ogranicza regionów, w których użytkownik lub użytkownicy mogą uzyskiwać dostęp do danych użytkownika i aplikacji.

>[!IMPORTANT]
>Pulpit wirtualny systemu Windows przechowuje globalne informacje o metadanych, takie jak nazwy dzierżaw, nazwy puli hostów, nazwy grup aplikacji i główne nazwy użytkowników w centrum danych znajdującym się w Stanach Zjednoczonych. Przechowywane metadane są szyfrowane w spoczynku, a geograficznie nadmiarowe serwery lustrzane są obsługiwane w Stanach Zjednoczonych. Wszystkie dane klienta, takie jak ustawienia aplikacji i dane użytkownika, znajdują się w lokalizacji, którą klient wybiera i nie jest zarządzana przez usługę.

Metadane usługi są replikowane w Stanach Zjednoczonych do celów odzyskiwania po awarii.