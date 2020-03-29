---
title: Dodatkowe łącza dotyczące usługi Avere vFXT for Azure
description: Łącza do dodatkowych informacji o avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78e0500057baf407f1d26afe29d9df1e525cbf3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153772"
---
# <a name="additional-documentation"></a>Dodatkowa dokumentacja

W tym artykule znajdują się łącza do dodatkowej dokumentacji dotyczącej interfejsu zarządzania panelu sterowania Avere i powiązanych tematów.

## <a name="avere-cluster-documentation"></a>Dokumentacja klastra Avere

Dodatkową dokumentację klastra Avere można <https://azure.github.io/Avere/>znaleźć na stronie internetowej pod adresem . Dokumenty te mogą pomóc w zrozumieniu możliwości klastra i sposobu konfigurowania jego ustawień.

* [Przewodnik tworzenia klastrów FXT](<https://azure.github.io/Avere/#fxt_cluster>) jest przeznaczony dla klastrów składających się z fizycznych węzłów sprzętowych, ale niektóre informacje w dokumencie są istotne również dla klastrów vFXT. W szczególności nowi administratorzy klastrów vFXT mogą korzystać z następujących sekcji:
  * [Dostosowywanie ustawień pomocy technicznej i monitorowania](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) wyjaśnia, jak dostosować ustawienia przekazywania pomocy technicznej i włączyć zdalne monitorowanie.
  * [Konfigurowanie serwerów VServers i globalnej przestrzeni nazw](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) ma informacje o tworzeniu obszaru nazw skierowanego do klienta.
  * [Konfigurowanie systemu DNS dla klastra Avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) wyjaśnia, jak skonfigurować system DNS okrężny.
  * Dodawanie dokumentów [magazynu zaplecza](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) sposób dodawania podstawowych filers.

* [Przewodnik konfiguracji klastra](<https://azure.github.io/Avere/#operations>) jest kompletnym odniesieniem do ustawień i opcji klastra Avere. Klaster vFXT używa podzbioru tych opcji, ale większość tych samych stron konfiguracji ma zastosowanie.

* W [przewodniku pulpitu nawigacyjnego](<https://azure.github.io/Avere/#operations>) wyjaśniono, jak korzystać z funkcji monitorowania klastra w Panelu sterowania Avere.

## <a name="vfxt-creation-and-management-documentation"></a>Dokumentacja tworzenia i zarządzania vFXT

Pełny przewodnik dotyczący korzystania z vfxt.py, narzędzia do tworzenia i zarządzania klastrami w chmurze opartym na skryptach, znajduje się w witrynie GitHub: [Cloud cluster management with vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).
