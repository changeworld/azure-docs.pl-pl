---
title: Dodatkowe linki dotyczące vFXT Avere dla platformy Azure
description: Łącza do dodatkowych informacji na temat Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: v-erkell
ms.openlocfilehash: 2efbe7ddc39b8bde76ee4a135f3f44af0864a374
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515598"
---
# <a name="additional-documentation"></a>Dodatkowa dokumentacja

Ten artykuł zawiera linki do dodatkowej dokumentacji dotyczącej interfejsu zarządzania Avere Panelu sterowania i Tematy pokrewne. 

## <a name="avere-cluster-documentation"></a>Dokumentacja usługi Avere klastra

Dodatkową dokumentację klastra Avere można znaleźć w witrynie sieci Web w <https://azure.github.io/Avere/>. Te dokumenty mogą pomóc zrozumieć możliwości klastra i konfigurowania jej ustawień. 

* [Przewodnik tworzenia klastra FXT](<https://azure.github.io/Avere/#fxt_cluster>) jest przeznaczony dla klastrów składają się z węzłami sprzętu fizycznego, ale niektóre informacje w dokumencie jest odpowiednie w przypadku klastrów vFXT także. W szczególności nowi Administratorzy klastra vFXT może być korzystne odczytywanie następujące sekcje:
  * [Dostosowywanie pomocy technicznej i monitorowania ustawień](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) wyjaśnia, jak dostosować ustawienia przekazywania pomocy technicznej i Włącz monitorowanie zdalne. 
  * [Konfigurowanie VServers i globalne Namespace](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) zawiera informacje o tworzeniu nazw ukierunkowane na klienta.
  * [Konfigurowanie usługi DNS dla klastra Avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) wyjaśnia, jak skonfigurować usługę DNS okrężnego.
  * [Dodawanie magazynu zaplecza](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) dokumenty, jak dodać filtrach core.

* [Przewodnik konfiguracji klastra](<https://azure.github.io/Avere/#operations>) jest pełną dokumentację ustawień i opcji Avere klastra. Klaster vFXT korzysta z podzbioru tych opcji, ale większość tych samych stron konfiguracji mają zastosowanie.

* [Przewodnik pulpit nawigacyjny](<https://azure.github.io/Avere/#operations>) wyjaśnia, jak używać klastra funkcje Avere Panelu sterowania.

## <a name="vfxt-creation-and-management-documentation"></a>Dokumentacja vFXT tworzenie i zarządzanie nimi

Pełnego przewodnika przy użyciu vfxt.py, tworzenie klastra w chmurze i narzędzia zarządzania znajduje się w witrynie GitHub: [Zarządzanie klastrem przy użyciu vfxt.py w chmurze](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
