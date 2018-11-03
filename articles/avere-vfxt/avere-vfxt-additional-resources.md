---
title: Dodatkowe linki dotyczące vFXT Avere dla platformy Azure
description: Łącza do dodatkowych informacji na temat Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 65e764d843f9e87adee4cf94c1d22b02db80eda0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958844"
---
# <a name="additional-documentation"></a>Dodatkowa dokumentacja

Ten artykuł zawiera linki do dodatkowej dokumentacji dotyczącej interfejsu zarządzania Avere Panelu sterowania i Tematy pokrewne. 

## <a name="avere-cluster-documentation"></a>Dokumentacja usługi Avere klastra

Dodatkową dokumentację klastra Avere można znaleźć w witrynie sieci Web w <http://library.averesystems.com/>. Te dokumenty mogą pomóc zrozumieć możliwości klastra i konfigurowania jej ustawień. 

* [Przewodnik tworzenia klastra FXT](<http://library.averesystems.com/#fxt_cluster>) jest przeznaczony dla klastrów składają się z węzłami sprzętu fizycznego, ale niektóre informacje w dokumencie jest odpowiednie w przypadku klastrów vFXT także. W szczególności nowi Administratorzy klastra vFXT może być korzystne odczytywanie następujące sekcje:
  * [Dostosowywanie pomocy technicznej i monitorowania ustawień](<http://library.averesystems.com/create_cluster/4_8/html/config_support.html#config-support>) wyjaśnia, jak dostosować ustawienia przekazywania pomocy technicznej i Włącz monitorowanie zdalne. 
  * [Konfigurowanie VServers i globalne Namespace](<http://library.averesystems.com/create_cluster/4_8/html/config_vserver.html#config-vserver>) zawiera informacje o tworzeniu nazw ukierunkowane na klienta.
  * [Konfigurowanie usługi DNS dla klastra Avere](<http://library.averesystems.com/create_cluster/4_8/html/config_network.html#dns-overview>) wyjaśnia, jak skonfigurować usługę DNS okrężnego.
  * [Dodawanie magazynu zaplecza](<http://library.averesystems.com/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) dokumenty, jak dodać filtrach core.

* [Przewodnik konfiguracji klastra](<http://library.averesystems.com/#operations>) jest pełną dokumentację ustawień i opcji Avere klastra. Klaster vFXT korzysta z podzbioru tych opcji, ale większość tych samych stron konfiguracji mają zastosowanie.

* [Przewodnik pulpit nawigacyjny](<http://library.averesystems.com/#operations>) wyjaśnia, jak używać klastra funkcje Avere Panelu sterowania.

## <a name="vfxt-creation-and-management-documentation"></a>Dokumentacja vFXT tworzenie i zarządzanie nimi

Pełnego przewodnika przy użyciu vfxt.py, tworzenie klastra w chmurze i narzędzia zarządzania znajduje się w witrynie GitHub: [chmury Zarządzanie klastrem przy użyciu vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
